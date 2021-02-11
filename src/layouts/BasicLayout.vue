<template>
  <div class="basic-layout">
    <!-- <h2>Menu</h2> -->
    <Menu />
    <div class="ui container">
      <slot />
    </div>
  </div>
</template>

<script>
import Menu from "../components/Auth/Menu";
export default {
  components: {
    Menu,
  },
};
</script>

<style></style>
