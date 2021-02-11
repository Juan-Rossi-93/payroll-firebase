<template>
  <BasicLayout>
    <div class="payrolls">
      <h1>Mis nominas</h1>
      <UploadPayroll :getPayrolls="getPayrolls" />
      <PayrollsList :payrolls="payrolls" :getPayrolls="getPayrolls" />
    </div>
  </BasicLayout>
</template>

<script>
import { ref, onMounted } from "vue";
import { auth, db } from "../utils/firebase";
import BasicLayout from "../layouts/BasicLayout";
import UploadPayroll from "../components/Payrolls/UploadPayroll";
import PayrollsList from "../components/Payrolls/PayrollsList";

export default {
  name: "Payrolls",

  components: {
    BasicLayout,
    UploadPayroll,
    PayrollsList,
  },
  setup() {
    let payrolls = ref(null);
    const getPayrolls = async () => {
      const response = await db
        .collection(auth.currentUser.uid)
        .orderBy("date", "desc")
        .get();

      const result = [];
      response.docs.forEach((doc) => {
        const data = doc.data();
        data.id = doc.id;
        result.push(data);
        console.log(doc.data());
      });
      payrolls.value = result;
    };

    onMounted(() => {
      getPayrolls();
    });
    return { payrolls, getPayrolls };
  },
};
</script>

<style lang="scss" scoped>
.payrolls {
  margin: 50px 0;
  text-align: center;
}
</style>
