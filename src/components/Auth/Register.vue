<template>
  <div class="register">
    <h1>Crear Cuenta</h1>
    <form class="ui form" @submit.prevent="onRegsiter">
      <div class="field">
        <input
          type="text"
          placeholder="Correo Electronico"
          v-model="formData.email"
          :class="{ error: formError.email }"
        />
      </div>
      <div class="field">
        <input
          type="password"
          placeholder="Contraseña"
          v-model="formData.password"
          :class="{ error: formError.password }"
        />
      </div>
      <div class="field">
        <input
          type="password"
          placeholder="Repetir Contraseña"
          v-model="formData.repeatPassword"
          :class="{ error: formError.repeatPassword }"
        />
      </div>

      <button type="submit" class="ui button violet fluid" :class="{ loading }">
        Registrar
      </button>
    </form>
    <p @click="changeForm">Loguear Cuenta</p>
  </div>
</template>
<script>
import { auth } from "../../utils/firebase";
import { ref } from "vue";
import * as Yup from "yup";

export default {
  name: "Register",
  props: {
    changeForm: Function,
  },
  setup() {
    let formData = {};
    let formError = ref({});
    let loading = ref(false);

    const schemaForm = Yup.object().shape({
      email: Yup.string()
        .email(true)
        .required(true),
      password: Yup.string().required(true),
      repeatPassword: Yup.string()
        .required(true)
        .oneOf([Yup.ref("password")], true),
    });

    const onRegsiter = async () => {
      loading.value = true;
      console.log("Registrando Cuenta");
      console.log(formData);
      formError.value = {};
      try {
        await schemaForm.validate(formData, { abortEarly: false });
        console.log("Todo OK");
        try {
          const { email, password } = formData;
          await auth.createUserWithEmailAndPassword(email, password);
        } catch (error) {
          console.log(error);
        }
      } catch (err) {
        err.inner.forEach((error) => {
          formError.value[error.path] = error.message;
        });
        console.log(err);
      }
      loading.value = false;
    };
    return { formData, onRegsiter, formError, loading };
  },
};
</script>

<style lang="scss" scoped>
.register {
  background-color: #fff;
  padding: 30px;
  box-shadow: 0px 0px 38px -5px rgba(0, 0, 0, 0.45);
  width: 400px;
  border-radius: 10px;

  h1 {
    text-align: center;
    margin-bottom: 30px;
  }

  form {
    input.error {
      border-color: #faa;
      background-color: #ffeded;
    }
  }

  p {
    text-align: center;
    margin-top: 30px;

    &:hover {
      cursor: pointer;
      opacity: 0.6;
    }
  }
}
</style>
