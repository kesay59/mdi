<template>
    <div ref="place" :style="{ width: placeWidth, height: placeHeight, outline: '1px solid green' }">
        <document-list :limitRect="limitRect"></document-list>
    </div>
</template>
<script setup>
import { defineProps, ref, computed, onMounted, onUpdated } from 'vue';

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

const limitRect = ref({ width: 0, height: 0 });
const setDocumentSize = function () {
    limitRect.value.width = place.value == null ? 0 : place.value.offsetWidth;
    limitRect.value.height = place.value == null ? 0 : place.value.offsetHeight;
};

onMounted(() => {
    setDocumentSize();
});
onUpdated(() => {
    setDocumentSize();
});
</script>
