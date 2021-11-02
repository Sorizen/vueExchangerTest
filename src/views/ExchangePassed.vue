<template>
  <div class="exchange-passed__wrapper">
    <h2 class="exchange-passed__wrapper__title">Удачная покупка!</h2>
    <p v-if="nameOfBoughtCurrency !== 'UAH'" class="exchange-passed__info">
      Вы приобрели {{ countOfBoughtCurrency }} {{ nameOfBoughtCurrency }} за
      {{ sumOfBought.toFixed(2) }} UAH
    </p>
    <p v-else class="exchange-passed__info">
      Вы приобрели {{ countOfBoughtCurrency }} {{ nameOfBoughtCurrency }} за
      {{
        (
          sumOfBought *
          this.info.filter((el) => el.name === this.currencyForSale)[0].price
        ).toFixed(2)
      }}
      UAH
    </p>
    <router-link to="/" class="exchange-passed__wrapper__link"
      >Home</router-link
    >
  </div>
</template>
<script>
export default {
  name: "ExchangePassed",
  props: [
    "sumOfBought",
    "nameOfBoughtCurrency",
    "countOfBoughtCurrency",
    "currencyForSale",
    "info",
  ],
};
</script>

<style scoped lang="scss">
.exchange-passed__wrapper {
  padding-top: 100px;
  color: #120a8f;
  .exchange-passed__wrapper__title {
    font-size: 40px;
  }
  .exchange-passed__info {
    padding-top: 20px;
  }
  .exchange-passed__wrapper__link {
    text-decoration: none;
    color: white;
    display: inline-block;
    margin-top: 20px;
    font-size: 20px;
    padding: 10px 20px;
    background: #120a8f;
    border-radius: 20px;
    transition: background-color 0.2s ease-in-out;
  }
  .exchange-passed__wrapper__link:hover {
    background: #170db3;
  }
}
</style>
