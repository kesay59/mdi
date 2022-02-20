<template>
    <contents :document="props.document"></contents>
</template>
<script setup>
import { defineProps, defineAsyncComponent } from 'vue';
import ErrorContents from '@/components/error/ErrorContents.vue';

const props = defineProps({
    document: Object,
});

const Contents = defineAsyncComponent({
    loader: () => props.document.contentsFn,
    delay: 200,
    timeout: 5000,
    error: ErrorContents,
});
</script>
