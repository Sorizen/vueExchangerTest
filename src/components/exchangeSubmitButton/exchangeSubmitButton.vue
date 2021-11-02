<template>
    <button type="button" v-if="inputError" @click="$emit('buyingCurrency')">ОБМЕНЯТЬ</button>
    <button type="button" disabled v-else>УКАЖИТЕ СУММУ</button>
</template>

<script>
export default {
  name: "exchangeSubmitButton",
  props: ['inputError'],
};
</script>

<style scoped>

</style>