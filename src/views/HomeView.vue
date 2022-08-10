<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Some of LA's best places to visit!",
      greeting: "Feel free to add more suggestions to this list.",
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      editPlaceParams: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places.json").then((response) => {
        this.places = response.data;
        console.log("All places", this.places);
      });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places.json", this.newPlaceParams)
        .then((response) => {
          console.log("Place succesfully created", response.data);
          this.places.push(response.data);
        })
        .catch((error) => console.log(error.response));
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      this.editPlaceParams = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      axios.patch("http://localhost:3000/places/" + place.id, this.editPlaceParams).then((response) => {
        console.log("Successfully updated place!", response.data);
      });
    },
    destroyPlace: function (place) {
      axios.delete("http://localhost:3000/places/" + place.id).then((response) => {
        console.log("Successfully deleted place!", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <p>{{ greeting }}</p>

    <div>
      Name:
      <input type="text" v-model="newPlaceParams.name" />
      Adress:
      <input type="text" v-model="newPlaceParams.adress" />
    </div>
    <button v-on:click="createPlace()">Create Place</button>
    <div v-for="place in places" v-bind:key="place.id">
      <h3>{{ place.name }}</h3>
      <p>{{ place.adress }}</p>
      <p><button v-on:click="showPlace(place)">More Information</button></p>
      <p>__________________________________________________</p>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h2>Place Info</h2>
        <p>
          Name:
          <input type="text" v-model="editPlaceParams.name" />
        </p>
        <p>
          Adress:
          <input type="text" v-model="editPlaceParams.adress" />
        </p>
        <button v-on:click="updatePlace(currentPlace)">Update Place</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete Place</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
