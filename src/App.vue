<template>
  <!-- Main Component -->
  <div id="app">
    <div id="header">
      <h2>LESSONS STORE</h2>
    </div>
    <div id="checkout">
      <button
        id="checkB"
        @click="showCheckout"
        v-bind:disabled="!canCheckOut"
        v-if="showProduct"
      >
        <img id="cart" src="images/cart.png" /> {{ cartItemCount }}
      </button>
      <button
        id="checkB"
        @click="showCheckout"
        v-bind:disabled="showPlaceOrder"
        v-else
      >
        Go Back
      </button>
    </div>
    <div v-if="showProduct">
      <!-- Creating search bar -->
      <div id="search-bar">
        <input type="text" placeholder="Search.." v-model.trim="search" />
      </div>
      <!-- Creating sort area -->
      <div id="sort">
        <h2>Sort</h2>
        <p>By</p>
        <input type="radio" id="subject" value="Subject" v-model="sort.by" />
        <label for="subject">Subject</label><br />
        <input type="radio" id="location" value="Location" v-model="sort.by" />
        <label for="location">Location</label><br />
        <input type="radio" id="price" value="Price" v-model="sort.by" />
        <label for="price">Price</label><br />
        <input type="radio" id="space" value="Space" v-model="sort.by" />
        <label for="space">Space</label>
        <p>Order</p>
        <input type="radio" id="ascending" value="a" v-model="sort.order" />
        <label for="ascending">Ascending</label><br />
        <input type="radio" id="descending" value="d" v-model="sort.order" />
        <label for="descending">Descending</label>
      </div>
      <product
        :products="products"
        :cart="cart"
        :displayCart="displayCart"
        :sortedProducts="sortedProducts"
        :results="results"
        @addItem="addItem"
        @canAdd="canAdd"
      />
    </div>
    <div v-else-if="showPlaceOrder">
      <div id="order-info">
        <h2>Order Placed Successfully!</h2>
        <div>Courses Taken : {{ order.cart }}</div>
        <p>Total Courses : {{ cart.length }}</p>
        <p>First Name : {{ order.firstName }}</p>
        <p>Last Name : {{ order.lastName }}</p>
        <p>Address : {{ order.address }}</p>
        <p>City : {{ order.city }}</p>
        <p>Phone Number : {{ order.phone }}</p>
        <p>Emirate : {{ order.state }}</p>
        <p>Gift : {{ order.gift }}</p>
        <p>Method : {{ order.method }}</p>
      </div>
    </div>
    <div v-else>
      <cart
        :displayCart="displayCart"
        :order="order"
        :states="states"
        @removeItem="removeItem"
        @submitForm="submitForm"
      />
    </div>
  </div>

  <!-- Creating cart page button -->
</template>

<script>
import product from "./components/Product.vue";
import cart from "./components/Cart.vue";
export default {
  name: "App",
  components: { product, cart },
  data() {
    return {
      sitename: "activitystore", // defines sitename
      products: [],
      order: {
        //order details
        firstName: "",
        lastName: "",
        address: "",
        city: "",
        state: "",
        phone: "",
        method: "Home",
        gift: "Send as a gift",
        sendGift: "Send as a gift",
        dontSendGift: "Do not send as a gift",
        cart: "",
      }, //order locations
      states: {
        DXB: "Dubai",
        SHJ: "Sharjah",
        ADH: "Abu Dhabi",
      }, //sort options
      sort: {
        by: "Price",
        order: "a",
      },
      search: "",
      cart: [], //creating cart array
      showProduct: true,
      showPlaceOrder: false,
      displayCart: [],
      results: [],
    };
  },
  methods: {
    addItem(product) {
      if (product.space > 0) {
        product.displaySpace -= 1;
        this.cart.push(product.id);
        this.displayCart.push({
          id: product.id,
          title: product.title,
          location: product.location,
          price: product.price,
          image: product.image,
          rating: product.rating,
        });
      }
    },
    sortedProducts() {
      let x, y;
      if (this.search !== "") {
        //to search the lessons
        const query = this.search.toLowerCase();
        return this.products.filter(
          (product) =>
            product.title.toLowerCase().includes(query) ||
            product.location.toLowerCase().includes(query) ||
            product.price.toString().includes(query)
        );
      } else {
        switch (this.sort.order) {
          case "a":
            x = 1;
            y = -1;
            break;
          case "d":
            x = -1;
            y = 1;
            break;
        }

        let compare;

        if (this.sort.by == "Subject") {
          compare = function (a, b) {
            if (a.title > b.title) return x;
            if (a.title < b.title) return y;
            return 0;
          };
        } else if (this.sort.by == "Location") {
          compare = function (a, b) {
            if (a.location > b.location) return x;
            if (a.location < b.location) return y;
            return 0;
          };
        } else if (this.sort.by == "Price") {
          compare = function (a, b) {
            if (a.price > b.price) return x;
            if (a.price < b.price) return y;
            return 0;
          };
        } else if (this.sort.by == "Space") {
          compare = function (a, b) {
            if (a.displaySpace > b.displaySpace) return x;
            if (a.displaySpace < b.displaySpace) return y;
            return 0;
          };
        }

        return this.products.sort(compare);
      }
    },
    showCheckout() {
      this.showProduct = this.showProduct ? false : true;
    },
    canAdd(product) {
      return product.space > this.cartCount(product.id);
    },
    cartCount(id) {
      let count = 0;
      for (let i = 0; i < this.cart.length; i++) {
        if (this.cart[i] === id) {
          count++;
        }
      }
      return count;
    },
    submitForm() {
      let order = [];
      var count = {};

      for (var i = 0; i < this.displayCart.length; i++) {
        if (
          this.displayCart[i].title + "[" + this.displayCart[i].id + "]" in
          count
        ) {
          count[
            this.displayCart[i].title + "[" + this.displayCart[i].id + "]"
          ]++;
        } else {
          count[
            this.displayCart[i].title + "[" + this.displayCart[i].id + "]"
          ] = 1;
        }
      }

      for (var title in count) {
        order.push(title + " X " + count[title]);
      }

      let text = "";

      for (let i = 0; i < order.length; i++) {
        text += order[i] + ", ";
      }

      this.order.cart = text.slice(0, -2);

      this.showPlaceOrder = true;
    },
    removeItem(product) {
      const objWithIdIndex = this.displayCart.findIndex(
        (obj) => obj.id === product.id
      );
      this.displayCart.splice(objWithIdIndex, 1);

      const index = this.cart.indexOf(product.id);
      if (index > -1) {
        // only splice array when item is found
        this.cart.splice(index, 1); // 2nd parameter means remove one item only
      }

      for (let j = 0; j < this.products.length; j++) {
        if (product.id === this.products[j].id) {
          this.products[j].displaySpace += 1;
        }
      }

      return this.displayCart;
    },
  },
  computed: {
    cartItemCount() {
      return this.cart.length || "";
    },
    canCheckOut() {
      return this.cart.length != 0;
    },
    canPlaceOrder() {
      return (
        this.order.firstName.match(/^[A-Za-z]+$/) &&
        this.order.lastName.match(/^[A-Za-z]+$/) &&
        this.order.address != "" &&
        this.order.city.match(/^[A-Za-z]+$/) &&
        this.order.phone != "" &&
        this.order.state != ""
      );
    },
  },
  created: function () {
    // retrieving data from the server
    const vm = this;
    fetch("http://localhost:3000/lessons").then(function (response) {
      response.json().then(function (json) {
        // storing the response
        vm.products = json;
      });
    });
  },
};
</script>

<style>
/* styling the body */
body {
  font-family: sans-serif;
  background-color: white;
  margin: 0px;
}

/* styling the lessons area */
#products {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  align-items: center;
  margin-left: 30%;
}

/* styling each lesson */
#product {
  width: 370px;
  height: 280px;
  border: 5px solid white;
  border-radius: 15px;
  padding: 10px;
  background-color: #f9b931;
  margin: 10px;
}

/* setting hover style for each lesson */
#product:hover {
  border: 5px solid #677189;
  font-weight: bold;
}

/* styling the lesson details */
#details {
  width: 170px;
  height: 200px;
  display: inline-block;
  padding-left: 10px;
  position: absolute;
}

/* positioning and styling the image area */
#image {
  width: 150px;
  height: 150px;
  padding-top: 10px;
  margin-bottom: 20px;
  display: inline-block;
}

/* setting the image size */
#imgsize {
  width: 150px;
  height: 150px;
  padding-left: 200px;
}

/* styling the add button */
#addB {
  background-color: #202d40;
  color: #e2e2e2;
  border: none;
  border-radius: 15px;
  display: inline-block;
  font-family: Roobert, -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica,
    Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-size: 16px;
  font-weight: 600;
  line-height: normal;
  margin: 0;
  margin-top: 37px;
  min-height: 60px;
  min-width: 0;
  padding: 16px 24px;
  text-align: center;
  text-decoration: none;
  transition: all 300ms cubic-bezier(0.23, 1, 0.32, 1);
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  width: 100%;
  will-change: transform;
}

/* setting the effects for add button */
#addB:active {
  position: relative;
  top: -1px;
  background-color: #202d40;
}

#addB:disabled {
  position: relative;
  top: 1px;
  border: none;
  background-color: #677189;
}

/* styling the cart page button */
#checkB {
  background-color: #779a91;
  border: solid 5px #0d3580;
  border-radius: 15px;
  display: inline-block;
  font-family: Roobert, -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica,
    Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-size: 16px;
  font-weight: 600;
  line-height: normal;
  margin-top: 10px;
  margin-bottom: 0px;
  margin-left: 72%;
  min-height: 60px;
  min-width: 0;
  padding: 16px 24px;
  text-align: center;
  text-decoration: none;
  transition: all 300ms cubic-bezier(0.23, 1, 0.32, 1);
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  width: 20%;
  will-change: transform;
}

#checkB:active {
  position: relative;
  top: -1px;
  background-color: #779a91;
}

/* styling the header */
#header {
  background-color: #3f63b5;
  align-items: center;
  margin: 0%;
  padding: 0.05px;
}

#header > h2 {
  padding-left: 41%;
  color: white;
}

#cart {
  width: 22px;
  height: 22px;
}

/* styling the order information */
#order-info {
  border: 5px solid #0d3580;
  margin: 10px;
  padding: 10px;
  padding-left: 5%;
  background-color: #3f63b5;
  width: 75%;
  border-radius: 7px;
  margin-left: 10%;
  color: white;
}

input {
  border-radius: 10px;
  padding-left: 10px;
}

#order-table {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  align-items: center;
}

#cart-items {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  margin-left: 10%;
  margin-right: 8%;
}

#bucket > h2 {
  color: #3f63b5;
  padding-left: 48%;
}

/* styling the sort area */
#sort {
  position: absolute;
  width: 150px;
  height: 300px;
  border: 5px solid #677189;
  border-radius: 10px;
  padding: 10px;
  background-color: #f9b931;
  margin: 10px;
  padding-left: 20px;
  margin-left: 100px;
  margin-top: 100px;
}

/* styling the search bar */
#search-bar {
  position: absolute;
  margin-left: 100px;
}

#search-bar input[type="text"] {
  float: right;
  padding: 6px;
  margin-top: 8px;
  margin-right: 16px;
  font-size: 17px;
  border: 5px solid #0d3580;
  border-radius: 10px;
  background-color: #779a91;
}

/* styling the remove from cart button */
#removeB {
  background-color: red;
  color: white;
  border: none;
  border-radius: 15px;
  display: inline-block;
  font-family: Roobert, -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica,
    Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-size: 16px;
  font-weight: 600;
  line-height: normal;
  margin: 0;
  min-height: 60px;
  min-width: 0;
  padding: 16px 24px;
  text-align: center;
  text-decoration: none;
  transition: all 300ms cubic-bezier(0.23, 1, 0.32, 1);
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  width: 100%;
  will-change: transform;
}

/* setting the effects for remove from cart button */
#removeB:active {
  position: relative;
  top: -1px;
  background-color: red;
}

/* styling the cart items */
#cartI {
  width: 370px;
  height: 250px;
  border: 5px solid #677189;
  border-radius: 15px;
  padding: 10px;
  background-color: #f9b931;
  margin-bottom: 10px;
}

/* setting hover style for cart items */
#cartI:hover {
  font-weight: bold;
}

/* styling the place order button */
#placeB {
  background-color: white;
  border: solid 5px #0d3580;
  border-radius: 15px;
  display: inline-block;
  font-family: Roobert, -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica,
    Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-size: 16px;
  font-weight: 600;
  line-height: normal;
  margin-top: -70px;
  margin-bottom: 0px;
  margin-left: 77%;
  min-height: 60px;
  min-width: 0;
  padding: 16px 24px;
  text-align: center;
  text-decoration: none;
  transition: all 300ms cubic-bezier(0.23, 1, 0.32, 1);
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  width: 20%;
  will-change: transform;
  position: relative;
}

#placeB:active {
  position: relative;
  top: -1px;
  background-color: white;
}
</style>

<head>
     <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.1/css/all.min.css"
    />
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.2.0/css/all.min.css"
      integrity="sha512-xh6O/CkQoPOWDdYTDqeRdPCVd1SpvCA9XXcUnZS2FmJNp1coAFzvtCN9BmamE+4aHK8yyUHUSCcJHgXloTyT2A=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    />
</head>
