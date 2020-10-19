<template>
  <div class="home">

  <div v-for="place in places">
    <h4>Name: {{ place.name }}</h4>
    <h4>Address: {{ place.address }}</h4>
    <button v-on:click="showPlace(place)">Details</button>
  </div>
  
  <div>
    <h2>New Place</h2>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
    Name: <input type="text" v-model="newPlaceName"><br>
    Address: <input type="text" v-model="newPlaceAddress"><br>
    <button v-on:click="createPlace()">Submit</button>
  </div>
  
  <dialog id="place-details">
    <form method="dialog">
      <h2>Place Info</h2>
      <p>Name: {{ currentPlace.name }}</p>
      <p>Address: {{ currentPlace.address }}</p>
      <p>Name <input type="text" v-model="currentPlace.name"></p>
      <p>Address <input type="text" v-model="currentPlace.address"></p>
      <button v-on:click="updatePlace(currentPlace)">Update</button>
      <button v-on:click="destroyPlace(currentPlace)">Delete</button>

      <button>Close</button>
    </form>
  </dialog>

  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("api/places", params)
        .then((response) => {
          console.log("Success", response.data);
          this.places.push(response.data);
          this.newPlaceName = "";
          this.newPlaceAddress = "";
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place.name);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };
      axios
        .patch(`/api/places/${place.id}`, params)
        .then((response) => {
          console.log("Success", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function (place) {
      axios.delete(`/api/places/${place.id}`).then((response) => {
        console.log("Success", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>