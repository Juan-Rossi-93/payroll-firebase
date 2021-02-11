<template>
  <template v-if="user">
    <router-view />
  </template>
  <Auth v-if="!user && user !== undefined" />
</template>

<script>
import { onMounted, computed } from "vue";
import { auth } from "./utils/firebase";
import { useStore } from "vuex";
import Auth from "./views/Auth";
export default {
  components: { Auth },
  name: "App",
  setup() {
    const store = useStore();
    const user = computed(() => store.state.user);

    onMounted(() => {
      auth.onAuthStateChanged((user) => {
        store.commit("setUser", user);
      });
    });

    return {
      user,
    };
  },
};
</script>
<style></style>
