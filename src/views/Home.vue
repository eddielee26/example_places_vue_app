<template>
  <body>
    <div class="home">
      <h1>Places</h1>

      <ul>
          <li v-for="error in errors">{{ error }}</li>
      </ul>
      
      <div>
        Name: <input type="text" v-model="newPlaceName" /><br>
        Address: <input type="text" v-model="newPlaceAddress" /><br>
      </div>

      <button v-on:click="createPlace()">Create Place</button>

      <h1>All Places</h1>

      <div v-for="place in places">
        <h2>Name: {{ place.name }}</h2>

        <div>
          <button v-on:click="showPlace(place)">See Address</button>
        </div>

        <div v-if="currentPlace === place">
          <p>Address: {{ place.address }}</p>
          <h4>Edit Place</h4>
          <div>
            Name: <input type="text" v-model="place.name" /><br>
            Address: <input type="text" v-model="place.address" /><br>
            <button v-on:click="updatePlace(place)">Update Place</button><br>
            <button v-on:click="destroyPlace(place)">Destroy Place</button><br>
          </div>
        </div>
      </div>
    </div>
  </body>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      places: [],
      errors: [],
      currentPlace: {},
      newPlaceName: "",
      newPlaceAddress: ""
    };
  },
  created: function() {
    axios.get("/api/places").then(response => {
      this.places = response.data;
    });
  },
  methods: {
    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };
      axios
        .post("/api/places", params)
        .then(response => {
          console.log("New Place Created", response.data);
          this.places.push(response.data);
          this.newPlaceName = "";
          this.newPlaceAddress = "";
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function(place) {
      if (this.currentPlace === place) {
        this.currentPlace = {};
      } else {
        this.currentPlace = place;
      }
    },
    updatePlace: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios.patch("/api/places/" + place.id, params)
        .then(response => {
          console.log("Success", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function(place) {
      axios.delete("/api/places/" + place.id).then(response => {
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    }
  }
};
</script>