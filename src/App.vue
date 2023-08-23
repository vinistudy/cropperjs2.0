<template>
  
  <div class="flex flex-col">
    <el-button type="primary" @click="openCropper">打开裁切</el-button>

    <img :src="source" class="mt-4" :width="outWidth" />

    <span class="text-sm mt-4 break-words">{{ source }}</span>
  </div>

  <ImageCropper ref="cropper" :title="'编辑头像'" :path="url" :outWidth="outWidth" :conWidth="720" :conHeight="350" :aspectRatio="1" :movable="true" :rounded="true" @onCropperComplete="onCropperComplete"></ImageCropper>
</template>

<script setup lang="ts">

import { ref } from 'vue'

const cropper = ref(null)

const outWidth = ref(240)

// const url = 'https://www.yuepaibao.com/storage/upload/image/meet/20230702/1365_20230702065630jUV1Sq_l.jpg'
const url = null

const source = ref('')

function openCropper() {
  cropper.value?.open()
}

function onCropperComplete(value:string) {
  source.value = value
}
</script>

<style scoped>

</style>
