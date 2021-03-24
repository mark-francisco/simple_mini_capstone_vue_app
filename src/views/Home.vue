<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h1>Good day, {{ name }}!</h1>
    <!-- <button>Submit!</button> -->

    <!-- you should bind by "product.id" when using v-bind:key -->
    <ul v-for="product in products" v-bind:key="product.id">
      <h4>{{ product.name }}</h4>
      <p>{{ product.price }}</p>
      <img v-bind:src="product.image_url" v-bind:alt="product.name" />
    </ul>
  </div>
</template>

<style></style>
<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      name: "Mark",
      products: [],
    };
  },
  created: function () {
    // "this" is set to the VueComponent object
    this.indexProducts();
  },
  methods: {
    // use the traditional fn syntax for defining fn's that contain axios HTTP requests
    indexProducts: function () {
      axios.get("/api/products").then((response) => {
        // populate the products array defined in this Vue file
        // you're returning the json data from your simple_mini_capstone Rails views.
        this.products = response.data;
        // "this" is set to the VueComponent object
        console.log("all of your products:", this.products);
      });
    },
  },
};
</script>
