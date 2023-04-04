<template>
  <div>
    <div id="bucket">
      <h2>Cart</h2>
      <div id="cart-items">
        <div id="cartI" v-for="(product, index) in displayCart" :key="index">
          <div id="details">
            <p>Subject: {{ product.title }}</p>
            <p>Location: {{ product.location }}</p>
            <p>Price(AED): {{ product.price }}</p>
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
          </div>
          <!-- Creating div for image -->
          <div id="image">
            <img id="imgsize" v-bind:src="product.image" />
          </div>
          <!-- Creating remove from cart button -->
          <button id="removeB" @click="removeItem(product)">Remove</button>
        </div>
      </div>
    </div>
    <div id="order-info">
      <h2>Enter Order Information</h2>
      <div id="order-table">
        <p>
          <strong>First Name:</strong>
          <input v-model.trim="order.firstName" />
        </p>
        <p>
          <strong>Last Name:</strong>
          <input v-model.trim="order.lastName" />
        </p>
        <p>
          <strong>Address:</strong>
          <input v-model="order.address" />
        </p>
        <p>
          <strong>City:</strong>
          <input v-model="order.city" />
        </p>
        <p>
          <strong>Emirate:</strong>
          <select v-model="order.state">
            <option disabled value="">Emirate</option>
            <option
              v-for="(state, key) in states"
              v-bind:value="state"
              :key="key"
            >
              {{ key }}
            </option>
          </select>
        </p>
        <p>
          <strong>Phone Number:</strong>
          <input v-model="order.phone" type="number" />
        </p>
      </div>
      <p>
        <input
          type="checkbox"
          id="gift"
          value="true"
          v-model="order.gift"
          v-bind:true-value="order.sendGift"
          v-bind:false-value="order.dontSendGift"
        /><label for="gift">Ship As Gift?</label>
      </p>
      <p>
        <input
          type="radio"
          id="home"
          value="Home"
          v-model="order.method"
        /><label for="home">Home</label
        ><input
          type="radio"
          id="business"
          value="Business"
          v-model="order.method"
        /><label for="business">Business</label>
      </p>
      <button id="placeB" @click="submitForm" v-if="canPlaceOrder(order)">
        Place Order
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "ViewCart",
  props: ["displayCart", "order", "states"],
  data() {
    return {};
  },
  methods: {
    removeItem(product) {
      this.$emit("removeItem", product);
    },
    submitForm() {
      this.$emit("submitForm");
    },
    canPlaceOrder(order) {
      return (
        order.firstName.match(/^[A-Za-z]+$/) &&
        order.lastName.match(/^[A-Za-z]+$/) &&
        order.address != "" &&
        order.city.match(/^[A-Za-z]+$/) &&
        order.phone != "" &&
        order.state != ""
      );
    },
  },
};
</script>

<style scoped>
@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.1/css/all.min.css");
@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.2.0/css/all.min.css");
</style>
