<template>
  <div class="form-group">
    <label for="my-file">Select Image</label>
    <input
      type="file"
      multiple="multiple"
      @change="onFileAdd"
      class="form-control-file"
      id="my-file"
      ref="uploadInput"
    />

    <div class="border p-2 mt-3">
      <p>Preview Here:</p>
      <template v-if="previewList.length">
        <div v-for="(item, index) in previewList" :key="index">
          <div class="symbol symbol-50px me-5">
            <img :src="item" class="img-fluid" />
          </div>
          <p class="mb-0">file name: {{ imageList[index].name }}</p>
          <p>size: {{ imageList[index].size / 1024 }}KB</p>
        </div>
      </template>
    </div>
    <button @click="enviar" class="btn btn-primary">Enviar</button>
  </div>
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
    const previewList = ref<any>([]);
    const imageList = ref<any>([]);
    const uploadInput = ref<any>();

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

    const onFileAdd = (event) => {
      const input = event.target;
      let count = input.files.length;
      let index = 0;
      if (input.files) {
        while (count--) {
          const reader = new FileReader();
          reader.onload = (e: any) => {
            previewList.value.push(e.target.result);
          };
          imageList.value.push(input.files[index]);
          reader.readAsDataURL(input.files[index]);
          index++;
        }
      }
    };

    const onUpload = (status) => {
      console.log('status', status);
    };

    const enviar = () => {
      console.log("uploadInput", uploadInput.value.files);
      const formData = new FormData();

      Array.prototype.forEach.call(uploadInput.value.files, file => {
        formData.append("imagens", file);
      });
      // uploadInput.value.files.forEach((file) => {
      //   formData.append("imagens", file);
      // });
      // form.append("file", this.file);

      ApiService.post("analise/upload", formData, {
        onUploadProgress: onUpload,
        headers: {
          "Content-Type": "multipart/form-data"
        }
      }).then((resp) => {
        console.log(resp);
      });
    };

    return {
      ClientesList,
      LaudosList,
      clientes,
      laudos,
      previewList,
      imageList,
      onFileAdd,
      enviar,
      uploadInput
    };
  },
});
</script>
