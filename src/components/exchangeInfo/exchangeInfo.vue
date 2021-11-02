<template>
  <div class="exchange-info__wrapper">
    <div class="exchange-info__convert_info_wrapper">
      <p class="exchange-info__convert_info__text">Курс</p>
      <p class="exchange-info__convert_info__text">
        1 BTC = {{ uahToBtc }} UAH
      </p>
    </div>
    <div class="exchange-info__balance__info__wrapper">
      <p class="exchange-info__balance__info__text">Резерв</p>
      <p class="exchange-info__balance__info__text">{{ credit }} UAH</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "exchangeInfo",
  props: ["info", "credit"],
  data() {
    return {
      uahToBtc: Number(
        this.info.filter((el) => el.name === "UAH")[0].price /
          this.info.filter((el) => el.name === "BTC")[0].price
      ).toFixed(2),
    };
  },
};
</script>

<style scoped lang="scss">
.exchange-info__wrapper {
  display: flex;
  margin-top: 100px;
  justify-content: space-between;
  width: 80%;
  margin-left: auto;
  margin-right: auto;
  color: grey;
}
</style>
