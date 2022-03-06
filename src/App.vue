<template>
    <input type="button" text="test" value="test" @click="test" />
    <input type="button" text="open" value="open" @click="documentApi.method.open({ title: 'title', contentsFn: import('@/page/Test.vue'), data: {}, size: { current: { width: 200, height: 100 } }, fixedContents: true })" />
    <input type="button" text="closeAll" value="closeAll" @click="documentApi.method.closeAll()" />
    <div style="width: 1000px; height: 700px; border: 1px solid black">
        <place @readyApi="ready"></place>
    </div>
</template>
<script setup>
import { ref } from 'vue';
import Place from './components/Place.vue';
const documentApi = ref(null);
const ready = function (api) {
    console.log(api);
    documentApi.value = api;
};
const test = function (e) {
    console.log('test ok');
    console.log(documentApi.value);
    // e.method.open({ title: 'title', contentsFn: import('@/page/Test.vue'), data: {}, size: { current: { width: 200, height: 100 } }, fixedContents: true });
};
</script>
