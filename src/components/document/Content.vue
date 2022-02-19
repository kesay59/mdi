<template>
    <content></content>
</template>
<script setup>
import { defineProps, defineAsyncComponent } from 'vue';
import Error404 from 'src/components/error/Error404.vue';

const props = defineProps({
    document: Object,
});

const content = defineAsyncComponent({
    loader: () => props.document.contentFn,
    delay: 200,
    timeout: 5000,
    error: Error404,
});
</script>
