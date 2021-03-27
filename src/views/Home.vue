<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h1>Good day, {{ name }}!</h1>
    <div>
      Product Name:
      <input type="text" v-model="newProductName" />
      <br />
      Product Price:
      <input type="text" v-model="newProductPrice" />
      <br />
      Product Description:
      <input type="text" v-model="newProductDescription" />
      <br />
      Product Image URL:
      <input type="text" v-model="newProductImageURL" />
      <br />
      <button v-on:click="createProduct()">Create product!</button>
    </div>
    <!-- you should bind by "product.id" when using v-bind:key -->
    <ul v-for="product in products" v-bind:key="product.id">
      <h4>{{ product.name }}</h4>
      <p>{{ product.price }}</p>
      <img v-bind:src="product.image_url" v-bind:alt="product.name" />
      <br />
      <button v-on:click="showProduct(product)">More Info:</button>
      <hr />
    </ul>
    <dialog id="product-details">
      <!-- dialog element gets a form element inside of it. -->
      <!-- submit button closes dialog when inside form element. -->
      <form method="dialog">
        <h1>Product Info:</h1>
        Name:
        <input v-model="currentProduct.name" />
        Description:
        <input v-model="currentProduct.description" />
        Price:
        <input v-model="currentProduct.price" />
        Image URL:
        <input v-model="currentProduct.image_URL" />
        <p>Tax: {{ currentProduct.tax }}</p>
        <p>Total: {{ currentProduct.total }}</p>
        <button v-on:click="updateProduct(currentProduct)">Update Product!</button>
        <button v-on:click="destroyProduct(currentProduct)">Delete Product!</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style>
img {
  width: 200px;
}
::backdrop {
  background-color: #cadbd5;
  opacity: 0.85;
}
</style>
<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      name: "Mark",
      products: [],
      newProductName: this.newProductName,
      newProductPrice: this.newProductPrice,
      newProductDescription: this.newProductDescription,
      newProductImageURL: this.newProductImageURL,
      // errors: "",
      currentProduct: {},
    };
  },
  created: function () {
    // "this" is set to the VueComponent object
    this.indexProducts();
  },
  updated: function () {
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
    createProduct: function () {
      let params = {
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        image_url: this.newProductImageURL,
      };
      axios
        .post("/api/products", params)
        .then((response) => {
          console.log(response.data);
          this.products.push(response.data);
          this.newProductName = "";
          this.newProductPrice = "";
          this.newProductDescription = "";
          this.newProductImageURL = "";
        })
        .catch((error) => {
          console.log(error.response);
          // this.errors = error.response.statusText;
        });
    },
    showProduct: function (product) {
      // this.currentProduct is the var you'll be rendering in the pop-up modal of the Vue template.
      this.currentProduct = product;
      console.log(product);
      // open up the modal
      document.querySelector("#product-details").showModal();
    },
    updateProduct: function (product) {
      let params = {
        name: product.name,
        price: product.price,
        description: product.description,
        image_url: product.image_URL,
      };
      axios.patch(`/api/products/${product.id}`, params).then((res) => {
        console.log("product updated:", res.data);
      });
    },
    destroyProduct: function (product) {
      axios.delete(`/api/products/${product.id}`).then((res) => {
        console.log("product deleted:", res.data);
        let index = this.products.indexOf(product);
        this.products.splice(index, 1);
      });
    },
  },
};
</script>
