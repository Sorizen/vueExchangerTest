<template>
  <div class="home">
    <h1 class="home__title">Обменник</h1>
    <div class="home__text-info">
      <span>Отдаёте</span>
      <span :class="{ red: error.state }">Сумма {{ currencyForSale }}</span>
      <span>Получаете</span>
      <span :class="{ red: error.state }">Сумма {{ currencyToBuy }}</span>
    </div>
    <form class="home__exchange__wrapper">
      <template style="display: flex; justify-content: center; height: 40px">
        <exchangeSelect
          v-bind:currencyValue="currencyForSale"
          v-bind:currencies="currencies"
          v-on:changeCurrencyValue="changeCurrencyValueForSale"
          :key="currencyToBuy"
        />
        <exchangeInput
          class="home_exchange__input"
          :class="{ red: error.state }"
          v-on:countOfCurrency="countOfCurrencyFunc"
          v-bind:value="countOfCurrencyForSale"
          v-bind:typeOfInput="forSale"
        />
        <img
          v-on:click="$emit('switchValuesExchange')"
          class="home__exchange__wrapper_img"
          src="https://img.icons8.com/ios-glyphs/30/000000/refresh--v2.png"
        />
        <exchangeSelect
          v-bind:currencyValue="currencyToBuy"
          v-bind:currencies="currencies"
          v-on:changeCurrencyValue="changeCurrencyValueToBuy"
          :key="currencyToBuy"
        />
        <exchangeInput
          class="home_exchange__input"
          :class="{ red: error.state }"
          v-on:countOfCurrency="countOfCurrencyFunc"
          v-bind:value="countOfCurrencyToBuy"
          v-bind:typeOfInput="toBuy"
        />
      </template>

      <div v-if="error.state" class="home_exchange__red-text__wrapper">
        <span class="home_exchange__red-text">{{ error.massage }}</span>
      </div>
      <button
        type="button"
        @click="buySomeCurrency"
        :disabled="error.state"
        class="home__button"
      >
        <template v-if="error.state"> УКАЖИТЕ СУММУ</template>
        <template v-else> ОБМЕНЯТЬ </template>
      </button>
    </form>
    <exchangeInfo :info="info" :credit="credit" class="home__exchange-info" />
  </div>
</template>

<script>
import exchangeSelect from "../components/exchangeSelect/exchangeSelect";
import exchangeInput from "../components/exchangeInput/exchangeInput";
import ExchangeInfo from "../components/exchangeInfo/exchangeInfo";
import exchangeInfo from "../components/exchangeInfo/exchangeInfo";
// import exchangeSubmitButton from "../components/exchangeSubmitButton/exchangeSubmitButton";

export default {
  name: "Home",
  data() {
    return {
      forSale: "forSale",
      toBuy: "toBuy",
      error: { state: false, massage: null },
    };
  },
  components: {
    ExchangeInfo,
    exchangeSelect,
    exchangeInput,
    exchangeInfo,
    // exchangeSubmitButton,
  },
  props: [
    "inputError",
    "credit",
    "currencies",
    "setCurrency",
    "countOfCurrency",
    "countOfCurrencyToBuy",
    "countOfCurrencyForSale",
    "currencyForSale",
    "currencyToBuy",
    "info",
  ],
  methods: {
    changeCurrencyValueForSale(value) {
      this.$emit("changeForSaleCurrencyValue", value);
    },
    changeCurrencyValueToBuy(value) {
      this.$emit("changeToBuyCurrencyValue", value);
    },
    countOfCurrencyFunc(obj) {
      this.validateInput();
      console.log(this.error.state);
      this.$emit("countOfCurrencyFunc", obj);
    },
    buySomeCurrency() {
      this.$emit("buyCurrency");
      this.$router.push({
        path: "/exchange-passed",
      });
    },
    validateInput() {
      if (
        this.countOfCurrencyForSale < 0 ||
        this.countOfCurrencyToBuy < 0 ||
        this.countOfCurrency === ""
      ) {
        console.log(
          "this.countOfCurrencyForSale < 0 || this.countOfCurrencyToBuy < 0"
        );
        this.error.state = true;
        this.error.massage = "Вы не можете продать или купить 0 валюты";
        return;
      } else if (this.currencyForSale === this.currencyToBuy) {
        console.log("this.currencyForSale === this.currencyToBuy");
        this.error.state = true;
        this.error.massage = "Вы не можете обменивать валюту саму на себя";
        return;
      } else if (
        this.credit <
          this.countOfCurrencyToBuy *
            this.info.filter((el) => el.name === this.currencyToBuy)[0].price *
            this.info.filter((el) => el.name === "UAH")[0].price &&
        this.currencyToBuy !== "UAH"
      ) {
        console.log("this.countOfCurrencyToBuy");
        this.error.state = true;
        this.error.massage = "Недосточно средств на балансе";
        return;
      } else if (
        this.credit < this.countOfCurrencyToBuy &&
        this.currencyToBuy === "UAH"
      ) {
        console.log("this.countOfCurrencyToBuy");
        this.error.state = true;
        this.error.massage = "Недосточно средств на балансе";
        return;
      }
      this.error = { state: false, massage: null };
    },
  },
};
</script>
<style scoped lang="scss">
.home {
  padding-top: 100px;
  .home__title {
    color: #120a8f;
    padding-bottom: 50px;
  }
  .home__text-info {
    display: flex;
    width: 710px;
    margin: 0 auto;
    font-size: 12px;
    span:first-child {
      margin-right: 28px;
    }
    span:nth-child(2) {
      margin-right: 272px;
    }
    span:nth-child(3) {
      margin-right: 16px;
    }
    .red {
      color: red;
    }
  }
  .home__exchange__wrapper {
    padding-top: 20px;
    .home__exchange__wrapper_img {
      cursor: pointer;
      margin: 0 40px;
    }
    .home_exchange__input::-webkit-outer-spin-button,
    .home_exchange__input::-webkit-inner-spin-button {
      -webkit-appearance: none;
      margin: 0;
    }
    .home_exchange__input {
      border-top: none;
      border-left: none;
      border-right: none;
      border-color: #120a8f;
      padding-left: 10px;
      outline: none;
      color: black;
      margin-left: 20px;
    }
    .home_exchange__input.red {
      border-color: red;
      color: red;
    }
    .home_exchange__red-text__wrapper {
      display: flex;
      justify-content: flex-end;
      width: 710px;
      margin: 0 auto;
      padding-top: 10px;
      text-align: left;
      color: red;
      font-size: 12px;
      .home_exchange__red-text {
        display: block;
        width: 220px;
      }
    }
    .home__button {
      margin-top: 100px;
      border-radius: 10px;
      width: 200px;
      height: 40px;
    }
  }
}
</style>
