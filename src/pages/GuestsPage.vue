<template>
  <q-page>
    <q-tabs v-model="tab">
      <q-tab name="register" label="Регистрация гостя" />
      <q-tab name="table" label="Таблица гостей" />
    </q-tabs>
    <q-tab-panels v-model="tab">
      <q-tab-panel name="register">
        <guest-registration />
      </q-tab-panel>
      <q-tab-panel name="table">
        <guest-table />
      </q-tab-panel>
    </q-tab-panels>
  </q-page>
</template>

<script>
import GuestRegistration from "components/GuestRegistration.vue";
import GuestTable from "components/GuestTable.vue";

export default {
  components: {
    GuestRegistration,
    GuestTable,
  },
  data() {
    return {
      tab: "register",
    };
  },
};
</script>
