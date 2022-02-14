<template>
    <div ref="place" :style="{ width: placeWidth, height: placeHeight, outline: '1px solid green' }">
        place
        <document-list :width="documentWidth" :height="documentHeight"></document-list>
    </div>
</template>
<script setup>
import { defineProps, ref, computed, onBeforeMount, onBeforeUpdate } from 'vue';

import DocumentList from './document/List.vue';

const props = defineProps({
    width: Number,
    height: Number,
});

const placeWidth = computed({
    get: () => (props.width != null ? props.width + 'px' : '100%'),
});
const placeHeight = computed({
    get: () => (props.height != null ? props.height + 'px' : '100%'),
});

const place = ref(null);
const documentWidth = ref(0);
const documentHeight = ref(0);
const setDocumentSize = function () {
    documentWidth.value = place.value == null ? 0 : place.value.offsetWidth;
    documentHeight.value = place.value == null ? 0 : place.value.offsetHeight;
};

onBeforeMount(() => {
    setDocumentSize();
});
onBeforeUpdate(() => {
    setDocumentSize();
});
</script>
