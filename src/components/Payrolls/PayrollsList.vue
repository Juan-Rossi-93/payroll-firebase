<template>
  <div class="payroll-list">
    <p class="payroll-list__not-found" v-if="payrolls?.length === 0">
      No tienes ninguna nomina, sube la primera. <br />
      Recuerda que por el momento solo esta permitido subir extensiones .PDF
    </p>
    <div
      class="payroll-list__payroll"
      v-for="payroll in payrolls"
      :key="payroll.id"
    >
      <p>{{ formatDate(payroll.dateString) }}</p>
      <div class="action">
        <a :href="payroll.payrollUrl" target="_blank" class="ui button blue"
          >Descargar</a
        >
        <button class="ui button violet" @click="deletePayroll(payroll.id)">
          Eliminar
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";
import "moment/locale/es";
import { auth, db } from "../../utils/firebase";

export default {
  name: "PayrollsList",
  props: {
    payrolls: Array,
    getPayrolls: Function,
  },
  setup(props) {
    const formatDate = (date) => {
      return moment(date).format("MMM [del ] YYYY");
    };
    const deletePayroll = async (id) => {
      try {
        await db
          .collection(auth.currentUser.uid)
          .doc(id)
          .delete();
        props.getPayrolls();
      } catch (error) {
        console.log(error);
      }
      console.log("Eliminando Nomina :" + id);
    };
    return {
      formatDate,
      deletePayroll,
    };
  },
};
</script>

<style lang="scss" scoped>
.payroll-list {
  &__not-found {
    text-aling: center;
    font-size: 20px;
  }
  &__payroll {
    display: flex;
    aling-items: center;
    justify-content: space-between;
    margin: 15px 0;
    p {
      margin: 0;
      text-transform: uppercase;
      font-weight: bold;
    }
  }
}
</style>
