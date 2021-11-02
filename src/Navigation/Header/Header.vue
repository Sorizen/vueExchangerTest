<template>
  <header class="header">
    <router-link class="header__link" to="/">DLExchanger</router-link>
    <span class="header__check">Check id</span>
  </header>
</template>

<script>
export default {
  name: "Header"
};
</script>

<style scoped lang="scss">
    .header{
      padding:30px 50px;
      background: #120a8f;
      color: white;
      display: flex;
      align-items: center;
      justify-content: space-between;
      .header__link{
        text-decoration: none;
        color: white;
        font-weight: bold;
        font-size: 30px;
        transition: color 0.2s ease-in-out;
      }
      .header__link:hover{
        color: lightgrey;
      }
      .header__check{
        cursor: pointer;
        transition: color 0.2s ease-in-out;
      }
      .header__check:hover{
        color: lightgrey;
      }
    }
</style>