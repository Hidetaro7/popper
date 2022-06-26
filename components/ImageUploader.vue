<template>
  <div class="ui">
    <input type="file" multiple @change="fileChanged" />

    <ImagePreviewer
      v-for="imageData in useUploadedImages().value"
      :src="imageData.path"
    />
  </div>
</template>

<script setup>
const fileChanged = (e) => {
  useUploadedImages().value.length = 0;
  Array.from(e.target.files).forEach(async (filePath) => {
    useUploadedImages().value.push({
      path: window.URL.createObjectURL(filePath),
      name: filePath.name,
    });
  });
};
</script>

<style scoped>
.ui {
  position: fixed;
  background-color: rgba(255, 255, 255, 0.5);
}
</style>
