<template>
     <el-dialog v-model="dialog.visible" :width="dialog.width" :title="title" :show-close="false">
        
        <img id="img" :src="props.path" />
    
        <div class="cropper-tools">
          <div>
            <div class="input-btn">
                <label class="el-button el-button--info el-button--small" for="file">选择图片</label>
                <input class="hidden" id="file" name="file" type="file" @change="chageImage" />
            </div>

            <el-button size="small" type="info" @click="actionHandler('rl')">向左旋转</el-button>
            <el-button size="small" type="info" @click="actionHandler('rr')">向右旋转</el-button>
            <el-button size="small" type="info" @click="actionHandler('fx')">左右镜像</el-button>
            <el-button size="small" type="info" @click="actionHandler('fy')">上下镜像</el-button>
          </div>
          <el-button type="primary" @click="cropperComplete">保存</el-button>
        </div>
    </el-dialog>
</template>

<script setup lang="ts">
import { ref, reactive, nextTick, watch } from 'vue'
import Cropper, { CropperImage, CropperSelection } from 'cropperjs';

interface Emits {
    (e: 'onCropperComplete', value: string):void
}

interface Props {
    title: string,
    path: string,
    outWidth?: number,
    conWidth?: number,
    conHeight?: number,
    aspectRatio?: number,
    coverage?: number,
    movable?: boolean
    rounded?: boolean
}

const emits = defineEmits<Emits>()

const props = withDefaults(defineProps<Props>(), {
    title: '',
    path: '',
    conWidth: 620,
    conHeight: 300,
    outWidth: 240,
    aspectRatio: 1,
    coverage: 0.72,
    movable: false,
    rounded: false
})

const dialog = reactive({
    visible: false,
    width: '50%'
})

watch(() => props, () => {
    dialog.width = (props.conWidth + 208) + 'px'
}, {
    immediate: true
})

const cropper = ref<Cropper>()

const open = function() {
    dialog.visible = true

    initCropper()
}

const close = function() {
    dialog.visible = false
}

function initCropper() {
    const coverage =  Math.round(100 * props.outWidth / Math.min(props.conWidth, props.conHeight)) * 0.01
    const viewBorderRadius = props.rounded ? '50%' : '8px';
    nextTick(() => {
        const template:string = `
            <div style="display:flex;width:${props.conWidth + 16}px">
                <div>
                    <cropper-canvas background style="display:flex;border-radius:8px;width:${props.conWidth}px;height:${props.conHeight}px">
                        <cropper-image></cropper-image>
                        <cropper-shade hidden></cropper-shade>
                        <cropper-handle action="move" plain></cropper-handle>
                        <cropper-selection id="cropperSelection" initial-coverage="${ coverage }" aspect-ratio="${props.aspectRatio}" movable="${props.movable}">
                            <cropper-grid role="grid" bordered covered></cropper-grid>
                            <cropper-crosshair theme-color="rgba(238, 238, 238, 0.5)" centered></cropper-crosshair>
                            <cropper-handle action="move" theme-color="rgba(255, 255, 255, 0.35)"></cropper-handle>
                            <cropper-handle action="n-resize"></cropper-handle>
                            <cropper-handle action="e-resize"></cropper-handle>
                            <cropper-handle action="s-resize"></cropper-handle>
                            <cropper-handle action="w-resize"></cropper-handle>
                            <cropper-handle action="ne-resize"></cropper-handle>
                            <cropper-handle action="nw-resize"></cropper-handle>
                            <cropper-handle action="se-resize"></cropper-handle>
                            <cropper-handle action="sw-resize"></cropper-handle>
                        </cropper-selection>
                    </cropper-canvas>
                </div>
                <div style="width:160px;margin-left:16px">
                    <div class="cropper-viewers">
                        <cropper-viewer selection="#cropperSelection" style="width:160px;border-radius:${viewBorderRadius};"></cropper-viewer>
                    </div>
                </div>
            </div>
        `
        
        if (!cropper.value) {
            try {
                document.getElementById('img')?.setAttribute('crossOrigin', 'anonymous')
            } catch(error) {
                console.log('img set crossOrigin fail')
            }

            cropper.value = new Cropper(document.getElementById('img') as HTMLImageElement, {template})
        }
    })
}

function chageImage() {
    const fileInput: HTMLInputElement = document.getElementById('file') as HTMLInputElement
    const files:FileList  = fileInput?.files as FileList
    if (files.length > 0 ) {

        if (cropper.value) {
            const cropperImage:CropperImage | null = cropper.value.getCropperImage()
            if (cropperImage) {
                cropperImage.setAttribute('src', window.URL.createObjectURL(files[0]))
            }
        }
    }
}

function actionHandler(value:string) {
    if (!cropper.value) {
        return
    }

    switch (value) {
        case 'rl':
            cropper.value?.getCropperImage()?.$rotate('-30deg')
        break;
        case 'rr':
            cropper.value?.getCropperImage()?.$rotate('30deg')
        break;
        case 'fx':
            cropper.value?.getCropperImage()?.$scale(-1, 1)
        break;
        case 'fy':
            cropper.value?.getCropperImage()?.$scale(1, -1)
        break;
        default:
            break;
    }
}

function cropperComplete() {
    const cropperSelection:CropperSelection | undefined | null = cropper.value?.getCropperSelection()
    if (cropperSelection) {
        const data:Promise<HTMLCanvasElement> = cropperSelection.$toCanvas()
        data.then((value) => {
            const base:string = value.toDataURL('image/jpeg')

            emits('onCropperComplete', base)

            close()
        })
    }
}

defineExpose({
    open,
    close
})
</script>

<style>
.cropper-tools {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 12px;
}

.cropper-tools .input-btn {
    display: inline-block;
    margin-right: 12px;
}

.hidden {
    display: none;
}

.el-dialog {
    border-radius: 10px;
}

.el-dialog__header {
    padding-top: 12px !important;
    padding-bottom: 12px !important;
    padding-left: 16px !important;
    padding-right: 16px !important;
    margin: 0 !important;
}

.el-dialog__body { 
    padding-left: 16px !important;
    padding-right: 16px !important;
    padding-top: 12px !important;
    padding-bottom: 12px !important;
}
</style>