<template>
  <div className="row">
    <div className="col-lg-6 mb-20">
      <ClientesList :clientes="clientes" />
    </div>
    <div className="col-lg-6">
      <LaudosList :laudos="laudos" />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from "vue";
import ClientesList from "@/views/pages/widgets/ClientesList.vue";
import LaudosList from "@/views/pages/widgets/LaudosList.vue";

import { saveToken } from "@/core/services/JwtService";
import ApiService from "@/core/services/ApiService";

export default defineComponent({
  name: "Dashboard",
  components: { ClientesList, LaudosList },
  setup() {
    const clientes = ref([]);
    const laudos = ref([]);

    onMounted(() => {
      saveToken(
        "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IlBlZHJvIiwicm9sZSI6IkFkbWluaXN0cmFkb3IiLCJuYmYiOjE2MjUwOTk2MzQsImV4cCI6MTY1NjYzNTYzNCwiaWF0IjoxNjI1MDk5NjM0fQ.SFpPm7a5AIKjIkb0rwXIi5DxqI_pjAaNG4XtPw-_VJk"
      );
      ApiService.setHeader();
      ApiService.get("clientes/listar").then(({ data }) => {
        clientes.value = data;
      });
      ApiService.get("/analise/listar").then(({ data }) => {
        laudos.value = data;
      });
    });

    return {
      ClientesList,
      LaudosList,
      clientes,
      laudos
    };
  }
});
</script>
