<template>
  <Header />
  <main>
    <router-view
      v-if="infoLoaded"
      :sumOfBought="sumOfBought"
      :nameOfBoughtCurrency="nameOfBoughtCurrency"
      :countOfBoughtCurrency="countOfBoughtCurrency"
      :info="info"
      :credit="credit"
      :currencies="currencies"
      :setCurrency="setCurrency"
      :countOfCurrency="countOfCurrency"
      :countOfCurrencyToBuy="countOfCurrencyToBuy"
      :countOfCurrencyForSale="countOfCurrencyForSale"
      :currencyForSale="currencyForSale"
      :currencyToBuy="currencyToBuy"
      :inputError="inputError"
      @changeForSaleCurrencyValue="changeForSaleCurrencyValue"
      @changeToBuyCurrencyValue="changeToBuyCurrencyValue"
      @countOfCurrencyFunc="countOfCurrencyFunc"
      @switchValuesExchange="switchValuesExchange"
      @buyCurrency="buyCurrency"
    />
    <Loader v-else />
  </main>
</template>

<script>
import Header from "./Navigation/Header/Header";
import Loader from "./views/Loader";
export default {
  components: {
    Header,
    Loader,
  },
  data() {
    return {
      info: [],
      currencies: ["UAH", "USD", "BTC", "ETH", "EUR"],
      currencyForSale: "UAH",
      currencyToBuy: "UAH",
      setCurrency: ["currencyForSale", "currencyToBuy"],
      countOfCurrency: 0,
      countOfCurrencyToBuy: 0,
      countOfCurrencyForSale: 0,
      infoLoaded: false,
      credit: 7000000,
      sumOfBought: 0,
      nameOfBoughtCurrency: "",
      countOfBoughtCurrency: 0,
      inputError: false,
    };
  },
  async mounted() {
    const axios = require("axios");
    await axios
      .get("https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd")
      .then((response) => (this.info = response.data))
      .then(() => this.filterArrInfo());
    await axios
      .get(
        "https://v6.exchangerate-api.com/v6/84151c916aabea918fea11e7/latest/USD"
      )
      .then((response) =>
        this.filterArrInfoFiat(response.data.conversion_rates)
      )
      .then(() => (this.infoLoaded = true));
    await console.log(this.info);
  },
  methods: {
    changeForSaleCurrencyValue(value) {
      this.currencyForSale = value;
    },
    changeToBuyCurrencyValue(value) {
      this.currencyToBuy = value;
    },
    countOfCurrencyFunc(obj) {
      if (obj.type === "forSale") {
        this.countOfCurrency = Number(obj.input);
        this.countOfCurrencyForSale = this.countOfCurrency;
        this.countOfCurrencyToBuy = this.editToBuyValue();
        this.inputError = false;
      } else if (obj.type === "toBuy") {
        this.countOfCurrency = Number(obj.input);
        this.countOfCurrencyToBuy = this.countOfCurrency;
        this.countOfCurrencyForSale = this.editForSaleValue();
        this.inputError = false;
      } else {
        this.inputError = true;
      }
    },
    editToBuyValue() {
      return Number(
        (this.countOfCurrency /
          this.info.filter((el) => el.name === this.currencyForSale)[0].price) *
          this.info.filter((el) => el.name === this.currencyToBuy)[0].price
      );
    },
    editForSaleValue() {
      return Number(
        (this.countOfCurrency /
          this.info.filter((el) => el.name === this.currencyToBuy)[0].price) *
          this.info.filter((el) => el.name === this.currencyForSale)[0].price
      );
    },
    filterArrInfo() {
      this.info = this.info.filter((el) => {
        for (let i = 0; i < this.currencies.length; i++) {
          if (el.symbol === this.currencies[i].toLowerCase()) {
            return true;
          }
        }
      });
      for (let i = 0; i < this.info.length; i++) {
        const obj = {};
        obj.name = this.info[i].symbol.toUpperCase();
        obj.price = Math.pow(Number(this.info[i].ath), -1);
        this.info[i] = obj;
      }
    },
    filterArrInfoFiat(obj) {
      for (let el in obj) {
        for (let i = 0; i < this.currencies.length; i++) {
          if (el === this.currencies[i]) {
            this.info.push({ name: el, price: obj[el] });
          }
        }
      }
    },
    switchValuesExchange() {
      const currencyForSaleDef = this.currencyForSale;
      const countOfCurrencyToBuyDef = this.countOfCurrencyToBuy;
      this.currencyForSale = this.currencyToBuy;
      this.currencyToBuy = currencyForSaleDef;
      this.countOfCurrencyToBuy = this.countOfCurrencyForSale;
      this.countOfCurrencyForSale = countOfCurrencyToBuyDef;
      console.log(this.currencyForSale + "  " + this.currencyToBuy);
    },
    buyCurrency() {
      if (this.currencyToBuy !== "UAH") {
        this.sumOfBought = this.credit;
        this.countOfBoughtCurrency = this.countOfCurrencyToBuy;
        this.nameOfBoughtCurrency = this.currencyToBuy;
        this.credit = this.credit - this.convertMoneyToUah();
        this.sumOfBought = this.sumOfBought - this.credit;
        if (this.credit < 0) {
          this.credit = 0;
        }
      } else if (this.currencyToBuy === "UAH") {
        this.sumOfBought = this.credit;
        this.countOfBoughtCurrency = this.countOfCurrencyForSale;
        this.nameOfBoughtCurrency = this.currencyForSale;
        this.credit = this.credit - this.countOfCurrencyToBuy;
        this.sumOfBought = this.sumOfBought - this.credit;
        if (this.credit < 0) {
          this.credit = 0;
        }
      }
    },
    convertMoneyToUah() {
      console.log(
        "Price " +
          this.info.filter((el) => el.name === this.currencyToBuy)[0].price
      );
      console.log(this.countOfCurrencyToBuy);
      console.log(this.info.filter((el) => el.name === "UAH")[0].price);
      let res = 0;
      if (this.currencyForSale !== "UAH") {
        res =
          this.info.filter((el) => el.name === this.currencyForSale)[0].price *
          this.countOfCurrencyForSale *
          this.info.filter((el) => el.name === "UAH")[0].price;
      } else {
        res = this.countOfCurrencyForSale;
      }
      return res;
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  *,
  *::before,
  *::after {
    box-sizing: border-box;
  }
  ul[class],
  ol[class] {
    padding: 0;
  }
  body,
  h1,
  h2,
  h3,
  h4,
  p,
  ul[class],
  ol[class],
  li,
  figure,
  figcaption,
  blockquote,
  dl,
  dd {
    margin: 0;
  }
  body {
    min-height: 100vh;
    scroll-behavior: smooth;
    text-rendering: optimizeSpeed;
    line-height: 1.5;
  }
  ul[class],
  ol[class] {
    list-style: none;
  }
  a:not([class]) {
    text-decoration-skip-ink: auto;
  }
  img {
    max-width: 100%;
    display: block;
  }
  article > * + * {
    margin-top: 1em;
  }
  input,
  button,
  textarea,
  select {
    font: inherit;
  }
  @media (prefers-reduced-motion: reduce) {
    * {
      animation-duration: 0.01ms !important;
      animation-iteration-count: 1 !important;
      transition-duration: 0.01ms !important;
      scroll-behavior: auto !important;
    }
  }
}
</style>
