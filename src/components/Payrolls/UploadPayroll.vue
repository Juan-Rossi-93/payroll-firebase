<template>
  <div class="upload-payroll">
    <button class="ui button violet" @click="showCloseForm">
      Nueva nomina
    </button>
    <form
      class="ui form upload-payroll__form "
      :class="{ open: showForm }"
      @submit.prevent="handleSubmit"
    >
      <div class="field">
        <label for="file" class="ui icon button">
          <i class="file icon" />
          seleccionar nomina
          <span v-if="file">({{ file.name }})</span>
        </label>
        <input
          type="file"
          id="file"
          style="display:none"
          @change="uploadFile"
        />
      </div>
      <div class="field">
        <div class="ui calendar">
          <div class="ui input left icon">
            <input type="date" @change="changeDate" :value="date" />
          </div>
        </div>
      </div>
      <button class="ui button blue" :class="{ loading }">
        Subir nomina
      </button>
      <p v-if="error">{{ error }}</p>
    </form>
  </div>
</template>

<script>
import { ref } from "vue";
import { v4 as uuidv4 } from "uuid";
import { auth, storage, db } from "../../utils/firebase";
export default {
  name: "UploadPayroll",
  props: {
    getPayrolls: Function,
  },
  setup(props) {
    let showForm = ref(false);
    let file = ref(null);
    let date = ref(null);
    let loading = ref(false);
    let error = ref(null);

    const showCloseForm = () => (showForm.value = !showForm.value);
    const uploadFile = (e) => {
      error.value = null;
      console.log(e.target.files[0]);
      console.log(e.target.files[0].type);
      const fileTemp = e.target.files[0];
      console.log(fileTemp.type);
      if (fileTemp.type === "application/pdf") {
        file.value = fileTemp;
        console.log("trueeee");
        console.log(file);
      } else {
        error.value = "Fichero no valido";
      }
    };
    const changeDate = (e) => {
      date.value = e.target.value;
    };

    const handleSubmit = async () => {
      if (date.value && file.value) {
        console.log("Enviando Nomina");
        loading.value = true;
        try {
          const nameFile = uuidv4();
          await storage
            .ref(auth.currentUser.uid)
            .child(`${nameFile}.pdf`)
            .put(file.value);
          console.log("TodoChill");
          const payrollUrl = await storage
            .ref(`${auth.currentUser.uid}/${nameFile}.pdf`)
            .getDownloadURL();
          await db.collection(auth.currentUser.uid).add({
            payrollUrl: payrollUrl,
            date: new Date(date.value),
            dateString: date.value,
          });
          props.getPayrolls();
          console.log(payrollUrl);
        } catch (error) {
          console.log(error);
        }

        console.log("subiendo nomina");
        console.log("FILE->", file);
        console.log("DATE->", date);
        loading.value = false;
        file.value = null;
        file.date = null;
        showForm.value = false;
        error.value = null;
        document.getElementById("file").value = "";
      } else {
        error.value = "Sube una nomina y selecciona una fecha";
      }
    };
    return {
      showForm,
      showCloseForm,
      file,
      date,
      loading,
      error,
      uploadFile,
      handleSubmit,
      changeDate,
    };
  },
};
</script>

<style lang="scss" scoped>
.upload-payroll {
  > .ui.button.primary {
    margin-bottom: 30px;
  }
  &__form {
    display: flex;
    align-items: center;
    flex-direction: column;
    height: 0;
    overflow: hidden;
    transition: height 0.3s ease;
    &.open {
      height: 200px;
    }
  }
}
</style>
