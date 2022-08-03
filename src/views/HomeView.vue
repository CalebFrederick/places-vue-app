<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Places App",
      places: [],
      currentPlace: {},
      editPlaceParams: {},
      newPlaceParams: {},
      showErrorMessage: false,
      errorMessage: "",
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        this.places = response.data;
        console.log("All Places: ", this.places);
      });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places", this.newPlaceParams)
        .then((response) => {
          console.log("Address Added ", response.data);
          this.places.push(response.data);
          this.newPlaceParams = {};
          this.showErrorMessage = false;
        })
        .catch((error) => (this.errorMessage = error))
        .then((this.showErrorMessage = true));
    },
    showPlace: function (place) {
      console.log(place);
      document.querySelector("#place-details").showModal();
      this.currentPlace = place;
      this.editPlaceParams = place;
    },
    updatePlace: function (place) {
      axios
        .patch("http://localhost:3000/places/" + place.id, this.editPlaceParams)
        .then((response) => {
          console.log("We done it!!!", response.data);
        })
        .catch((error) => (this.errorMessage = error))
        .then((this.showErrorMessage = true));
    },
    destroyPlace: function (place) {
      axios
        .delete("http://localhost:3000/places/" + place.id)
        .then((response) => {
          console.log("Success!", response.data);
          var index = this.places.indexOf(place);
          this.places.splice(index, 1);
        })
        .catch((error) => (this.errorMessage = error))
        .then((this.showErrorMessage = true));
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h3>Add a Place</h3>
    <div>
      <label for="name">Name:</label>
      <input type="text" v-model="newPlaceParams.name" id="name" />
    </div>
    <div>
      <label for="address">Address:</label>
      <input type="text" v-model="newPlaceParams.address" id="address" />
    </div>
    <div>
      <button v-on:click="createPlace()">Create</button>
    </div>
    <div v-for="place in places" v-bind:key="place.id">
      <h5>{{ place.name }}</h5>
      <h6>{{ place.price }}</h6>
      <div>
        <button v-on:click="showPlace(place)">More Info!</button>
      </div>
      <hr />
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <div>
          <label for="editName">Name:</label>
          <input type="text" v-model="editPlaceParams.name" id="editName" />
        </div>
        <br />
        <div>
          <label for="editAddress">Address:</label>
          <input
            type="text"
            v-model="editPlaceParams.address"
            id="editAddress"
          />
        </div>
        <br />
        <button v-on:click="updatePlace(currentPlace)">Update Place</button>
        <button>Close</button>
        <button v-on:click="destroyPlace(currentPlace)">Destroy Place</button>
      </form>
    </dialog>
  </div>
</template>
