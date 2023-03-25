<template>
  <div id="products">
    <div id="product" v-for="product in products" :key="product.id">
      <div id="details">
        <p>Subject: {{ product.title }}</p>
        <p>Location: {{ product.location }}</p>
        <p>Price(AED): {{ product.price }}</p>
        <p>Space: {{ product.displaySpace }}</p>
        <p>
          Rating:
          <i
            class="fa-solid fa-star"
            v-for="(star, index) in product.rating"
            :key="index"
          ></i
          ><i
            class="fa-regular fa-star"
            v-for="(star, index) in 5 - product.rating"
            :key="index + 'empty'"
          ></i>
        </p>
        <p v-if="product.space === cartCount(product.id)">Out Of Stock!</p>
        <p v-else-if="product.space - cartCount(product.id) < 5">
          Only {{ product.space - cartCount(product.id) }} left!
        </p>
        <p v-else>Buy Now!</p>
      </div>
      <div id="image">
        <img id="imgsize" v-bind:src="product.image" />
      </div>
      <button
        id="addB"
        @click="addItem(product)"
        v-bind:disabled="!canAdd(product)"
      >
        Add
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "ProductDetails",
  props: ["products", "cart", "displayCart"],
  data() {
    return {};
  },
  methods: {
    cartCount(id) {
      // this.$emit("cartCount", id);

      return this.$parent.cartCount(id);
    },
    addItem(product) {
      this.$emit("addItem", product);
    },
    canAdd(product) {
      // this.$emit("canAdd", product);

      return this.$parent.canAdd(product);
    },
  },

  //   created: function () {
  //     // retrieving data from the server
  //     const vm = this;
  //     fetch("http://localhost:3000/lessons").then(function (response) {
  //       response.json().then(function (json) {
  //         // storing the response
  //         vm.products = json;
  //       });
  //     });
  //   },
};
</script>

<style scoped>
@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.1/css/all.min.css");
@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.2.0/css/all.min.css");
</style>
