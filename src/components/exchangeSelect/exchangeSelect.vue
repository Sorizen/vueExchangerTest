<template>
 <select v-model="selectValue" v-on:change="$emit('changeCurrencyValue', selectValue)">
   <option v-for="(currency, index) of currencies" :key="index" :value="currency">{{currency}}</option>
 </select>
</template>

<script>
export default {
  name: "exchangeSelect",
  props: ['currencies', 'currencyValue'],
  data(){
    return{
      selectValue: this.currencyValue,
    }
  },
};
</script>

<style scoped>

</style>