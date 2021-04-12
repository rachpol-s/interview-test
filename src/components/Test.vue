<template
  ><div style="background-color:whitesmoke">
    <div class="container">
      <div
        class="btn-group"
        role="group"
        aria-label="Basic example"
        style="width:100%; margin-bottom:20px"
      >
        <button
          type="button"
          class="btn"
          :class="{ buttonblue: btn === 1 }"
          @click="btn = 1"
        >
          All
        </button>
        <button type="button" class="btn btn-primary" @click="btn = 2">
          Launched
        </button>
        <button type="button" class="btn btn-primary" @click="btn = 3">
          Upcoming
        </button>
      </div>

      <div v-if="btn === 1">
        <b-card
          class="text-center"
          v-for="data in apiLaunch"
          :key="data.index"
          style="margin-bottom:15px"
          @click="showModal(data)"
        >
          <div class="row">
            <div class="col">
              <img
                :src="data.links.patch.small"
                style="width:30%"
                v-if="data.links.patch.small !== null"
              />
              <img src="@/assets/default-img.png" v-else style="width:30%" />
            </div>
            <div class="col">
              {{ data.name }}
            </div>
            <div class="col-5">
              <div class="row">
                <div class="col-3" v-if="data.crew.length">
                  <p class="text-white crew">{{ data.crew.length }} crews</p>
                </div>
                <div class="col">
                  {{ new Date(data.date_local).toString().slice(0, 15) }}
                </div>
              </div>
            </div>
            <div v-if="new Date(data.date_local) < new Date()" class="col">
              <p style="color:blue">
                Launched
              </p>
            </div>
            <div v-else>
              <p style="color:blue">
                Upcoming
              </p>
            </div>
          </div>
        </b-card>
      </div>
      <div v-if="btn === 2">
        <b-card
          class="text-center"
          v-for="data in apiLaunch.filter(
            (x) => new Date(x.date_local) < new Date()
          )"
          :key="data.index"
          style="margin-bottom:15px"
        >
          <div class="row">
            <div class="col">
              <img
                :src="data.links.patch.small"
                style="width:30%"
                v-if="data.links.patch.small !== null"
              />
              <img src="@/assets/default-img.png" v-else style="width:30%" />
            </div>
            <div class="col">
              <h4>{{ data.name }}</h4>
              
            </div>
            <div class="col-5">
              <div class="row">
                <div class="col-3" v-if="data.crew.length">
                  <p class="text-white crew">{{ data.crew.length }} crews</p>
                </div>
                <div class="col">
                  {{ new Date(data.date_local).toString().slice(0, 15) }}
                </div>
              </div>
            </div>
            <div class="col">
              <p style="color:blue">
                Launched
              </p>
            </div>
          </div>
        </b-card>
      </div>
      <div v-if="btn === 3">
        <b-card
          class="text-center"
          v-for="data in apiLaunch.filter(
            (x) => new Date(x.date_local) > new Date()
          )"
          :key="data.index"
          style="margin-bottom:15px"
        >
          <div class="row">
            <div class="col">
              <img
                :src="data.links.patch.small"
                style="width:30%"
                v-if="data.links.patch.small !== null"
              />
              <img src="@/assets/default-img.png" v-else style="width:30%" />
            </div>
            <div class="col">
              {{ data.name }}
            </div>
            <div class="col-5">
              <div class="row">
                <div class="col-3" v-if="data.crew.length">
                  <p class="text-white crew">{{ data.crew.length }} crews</p>
                </div>
                <div class="col">
                  {{ new Date(data.date_local).toString().slice(0, 15) }}
                </div>
              </div>
            </div>
            <div class="col">
              <p style="color:blue">
                Upcoming
              </p>
            </div>
          </div>
        </b-card>
      </div>

      <div class="row">
        <div class="col"></div>
      </div>
    </div>

    <div>
      <b-modal ref="my-modal" hide-footer hide-header>
        <div class="d-block text-center">
          <h3>{{ modal.name }}</h3>
          <p>
            {{ modal.date }}
          </p>
          <hr />
          <div class="row justify-content-center">
            <div class="col-3">
              <p class="text-white crew">Crews</p>
            </div>
          </div>
          <hr />
          <div class="row justify-content-center">
            <div class="col-3">
              <p class="text-white crew">Rocket</p>
            </div>
          </div>
          <hr />
          <div class="row justify-content-center">
            <div class="col-3">
              <p class="text-white crew">Launchpad</p>
            </div>
          </div>
        </div>
        <b-button class="mt-3" variant="outline-danger" block @click="hideModal"
          >Close Me</b-button
        >
      </b-modal>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      apiLaunch: "",
      apiCrew: "",
      apiRocket: "",
      btn: 1,
      modal: {
        name: "",
        date: "",
        crew: {
          crewImg: [],
          crewName: [],
        },
        rocket: {
          rocketImg: [],
          rocketName: "",
        },
      },
    };
  },
  methods: {
    async showModal(data) {
      console.log(this.apiLaunch);
      console.log(data);

      await data.crew.forEach((element) => {
        axios
          .get("https://api.spacexdata.com/v4/crew/" + element)
          .then((response) => (this.apiCrew = response.data));
        // console.log(this.apiCrew);
        // this.modal.crew.crewImg.push(this.apiCrew.image);
        // this.modal.crew.crewName.push(this.apiCrew.name);
      });

      await axios
        .get("https://api.spacexdata.com/v4/rockets/" + data.rocket)
        .then((response) => (this.apiRocket = response.data));
      console.log(this.apiRocket);
      this.modal.rocket.rocketImg.push(this.apiRocket.image);
      this.modal.rocket.rocketName = this.apiRocket.name;

      this.modal.name = data.name;
      this.modal.date = new Date(data.date_local).toString().slice(0, 24);
      console.log(this.modal);
      this.$refs["my-modal"].show();
    },

    hideModal() {
      console.log(this.apiCrew);
      console.log(this.apiRocket);
      this.$refs["my-modal"].hide();
    },
  },
  mounted() {
    axios
      .get("https://api.spacexdata.com/v4/launches")
      .then((response) => (this.apiLaunch = response.data));
  },
};
</script>

<style>
.buttonblue {
  background-color: blue;
}
.crew {
  background-color: blue;
  border-radius: 15px;
}
</style>
