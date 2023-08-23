### cropperjs 2.0 裁切图片使用

官网： [https://fengyuanchen.github.io/cropperjs/v2/zh/](https://fengyuanchen.github.io/cropperjs/v2/zh/)

演练场：[https://fengyuanchen.github.io/cropperjs/v2/zh/playground.html](https://fengyuanchen.github.io/cropperjs/v2/zh/playground.html)


![image](demo.gif)

### 安装运行

```sh
pnpm install

pnpm dev
```

### 组件说明

- 组件是基于网页版，更准确的说是基于电脑版浏览器来使用的。这个由组件的布局和使用场景决定的。
- 组件是基于 vite + vue3 + ts 封装的。
- 组件包含一些 element-plus 的一些组件。更换更改也是比较方便的。
- 组件适合后台等网页版的头像裁切场景
  
### 组件使用

#### 示例代码
```
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

const url = 'https://www.yuepaibao.com/storage/upload/image/meet/20230702/1365_20230702065630jUV1Sq_l.jpg'

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
```

#### props 说明

`title`，裁切弹窗的标题
`path`, 默认裁切图片的 url。没有可以空着。
`outWidth`, 裁切完成输出的照片的宽度
`conWidth`,裁切部分的整体宽度
`conHeight`,裁切部分的整体高度
`aspectRatio`,裁切比列
`movable`, selection 是否可以滑动（个人喜欢不滑动）
`rounded` 预览图片是否是圆形的（就是 border-radius: 50%）
``
仅 title 是必须的。
#### 方法说

就一个方法 `open`, 留了一个 `close` 方法，没啥用。

#### emits 说明

只有一个 `onCropperComplete` 回调。点击完成裁切回调。会返回裁切图片的 base64 。不一定真能返回。如果遇到图片源跨域是返回不来的。

### 我的博客和网站

[https://blog.vini123.com](https://blog.vini123.com)

[https://xiangrong.pro](https://xiangrong.pro)

[https://www.zeipan.com/admin][https://www.zeipan.com/admin]

[https://yuepaibao.com](https://yuepaibao.com)

