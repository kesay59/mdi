<template>
    <div style="height: 24px" :style="{ backgroundColor: isTop ? 'black' : 'LightGray', color: isTop ? 'white' : 'black' }">
        <div class="toolbar-element" style="position: relative; overflow: hidden; width: calc(100% - 72px)" @mousedown="props.document.increaseOrder()">
            <slot></slot>
            <span style="padding-left: 4px">{{ props.document.title }}</span>
        </div>
        <div class="toolbar-element">
            <icon-button @click="props.document.toggleIconize()">
                <img class="icon" src="@/assets/iconize.svg" style="fill: white" />
            </icon-button>
            <icon-button @click="props.document.toggleMaximize()">
                <img class="icon" src="@/assets/minimize.svg" v-if="props.document.maximize" />
                <img class="icon" src="@/assets/maximize.svg" v-else />
            </icon-button>
            <icon-button @click="props.document.close()">
                <img class="icon" src="@/assets/close.svg" />
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
.toolbar-element {
    display: inline-block;
    height: 24px;
}
.icon {
    width: 16px;
    height: 16px;
    margin-top: 4px;
}
</style>
