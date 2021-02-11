<template>
  <div class="Account">
    <BasicLayout>
      <h1>Ajustes</h1>
      <div class="account__section">
        <p class="tittle">Cambiar contraseña</p>
        <span v-if="user.displayName">Nombre: {{ user.displayName }}</span>
        <ChangeName />
      </div>

      <div class="account__section">
        <p class="tittle">Cambiar Email</p>
        <span>Email: {{ user.email }}</span>
        <ChangeEmail />
      </div>
      <div class="account__section">
        <p class="tittle">Cambiar Constraseña</p>
        <ChangePassword />
      </div>
    </BasicLayout>
  </div>
</template>

<script>
import BasicLayout from "../layouts/BasicLayout";
import { useStore } from "vuex";
import { computed } from "vue";
import ChangeName from "../components/Account/ChangeName";
import ChangeEmail from "../components/Account/ChangeEmail";
import ChangePassword from "../components/Account/ChangePassword";

export default {
  name: "Account",
  components: {
    BasicLayout,
    ChangeEmail,
    ChangeName,
    ChangePassword,
  },
  setup() {
    const store = useStore();
    const user = computed(() => store.state.user);
    return { user };
  },
};
</script>

<style lang="scss">
.account {
  h1 {
    margin: 50px 0;
    text-align: center;
  }
  &__section {
    position: relative;
    padding: 10px;
    border: 1px solid #16212b;
    padding-top: 25px;
    margin-top: 40px;
    .tittle {
      position: absolute;
      top: -12px;
      background-color: #16212b;
      color: #fff;
      padding: 2px 10px;
    }
  }
}
</style>
