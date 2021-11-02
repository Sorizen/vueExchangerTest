<template>
  <input type="number" v-model="inputValue" v-on:input="onInput" />
</template>

<script>
export default {
  name: "exchangeInput",
  props: ["typeOfInput", "countOfCurrencyValue"],
  data() {
    return {
      inputValue: "",
    };
  },
  methods: {
    onInput() {
      if (this.inputValue === "") {
        this.$emit("countOfCurrency", { input: 0, type: this.typeOfInput });
      } else {
        this.$emit("countOfCurrency", {
          input: this.inputValue,
          type: this.typeOfInput,
        });
      }
    },
  },
};
</script>

<style scoped></style>
