<template>
    <div ref="place" :style="{ width: placeWidth, height: placeHeight, outline: '1px solid green' }">
        <document-list :limitRect="limitRect" @ready="handleReady"></document-list>
    </div>
</template>
<script setup>
import { defineProps, defineEmits, ref, computed, onMounted, onUpdated } from 'vue';

import DocumentList from './document/List.vue';

const props = defineProps({
    width: Number,
    height: Number,
});
const emit = defineEmits(['ready']);

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

const handleReady = function (documentApi) {
    emit('ready', documentApi);
};

onMounted(() => {
    setDocumentSize();
});
onUpdated(() => {
    setDocumentSize();
});
</script>
