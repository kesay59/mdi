<template>
    <div style="width: 1000px; height: 700px">
        <place @ready="test"></place>
    </div>
</template>
<script setup>
import Place from './components/Place.vue';
const test = function (e) {
    console.log('test ok');
    console.log(e);
    // e.method.open({ title: 'title', contentsFn: import('@/page/Test.vue'), data: {}, size: { current: { width: 200, height: 100 } }, fixedContents: true });
};
</script>
