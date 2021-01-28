<template>
  <div class="container">
    <div class="form">
      <label for="text">Enter Amount</label>
      <input v-model="amount" type="text" placeholder="enter amount here" />
      <button @click="createPayment">Pay</button>
    </div>
  </div>
</template>

<script>
export default {
  head() {
    return {
      script: [
        {
          src: "https://checkout.razorpay.com/v1/checkout.js"
        }
      ]
    };
  },
  data() {
    return {
      amount: "",
      orderId: ""
    };
  },

  methods: {
    async createPayment() {
      try {
        const res = await this.$axios.$post("/order", {
          amount: this.amount
        });
        console.log(res);
        this.orderId = res.id;
        const options = {
          key: "rzp_test_s80zzTHF2x596N",
          amount: res.amount,
          currency: res.currency,
          order_id: res.id,
          handler: handlerResponse => {
            this.verifyPayment();
          },
          notes: {
            address: "Razorpay Corporate Office"
          },
          theme: {
            color: "#3399cc"
          }
        };
        var rzp1 = new Razorpay(options);
        rzp1.open();
      } catch (error) {
        console.log(error.message);
      }
    },
    async verifyPayment() {
      try {
        const result = await this.$axios.$get(`/${this.orderId}`);
        if ((result.status = "paid")) {
          console.log("payment successfull");
        }
      } catch (error) {
        console.log(error.message);
      }
    }
  }
};
</script>
<style lang="scss" scopped>
.container {
  position: relative;
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  .form {
    width: 400px;
    padding: 20px;
    border: 0.5px solid;
    text-align: center;
    label {
      width: 100%;
      font-size: 18px;
      font-weight: bold;
    }
    input {
      width: 100%;
      outline: none;
      height: 35px;
      border-radius: 20px;
      border: 1px solid rgb(102, 102, 102);
      margin: 25px 0;
      text-align: center;
    }
    button {
      height: 35px;
      width: 50%;
      border-radius: 20px;
      background: rgb(66, 66, 221);
      outline: none;
      border: none;
      color: #fff;
      cursor: pointer;
    }
  }
}
</style>
