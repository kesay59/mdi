<template>
    <div style="height: 24px" :style="{ backgroundColor: isTop ? 'black' : 'LightGray', color: isTop ? 'white' : 'black' }">
        <div class="toolbar-element" style="position: relative; overflow: hidden; width: calc(100% - 72px); display: inline-block; height: 24px" @mousedown="props.document.increaseOrder()">
            <slot></slot>
            <span style="padding-left: 4px">{{ props.document.title }}</span>
        </div>
        <div class="toolbar-element" style="display: inline-block; height: 24px; width: 72px">
            <icon-button @click="props.document.toggleIconize()">
                <!-- <img class="icon" src="@/assets/iconize.svg" style="fill: white" /> -->
                <div>_</div>
            </icon-button>
            <icon-button @click="props.document.toggleMaximize()">
                <!-- <img class="icon" src="@/assets/minimize.svg" v-if="props.document.maximize" />
                <img class="icon" src="@/assets/maximize.svg" v-else /> -->
                <div>O</div>
            </icon-button>
            <icon-button @click="props.document.close()">
                <!-- <img class="icon" src="@/assets/close.svg" /> -->
                <div>X</div>
            </icon-button>
        </div>
    </div>
</template>
<script setup>
import { defineProps, ref } from 'vue';

import IconButton from '@/components/button/IconButton.vue';

const props = defineProps({
    document: Object,
    isTop: Boolean,
});
const dragField = ref(null);
defineExpose({
    dragField,
});
</script>
<style scoped>
.icon {
    width: 16px;
    height: 16px;
    margin-top: 4px;
}
</style>
