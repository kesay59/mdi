<template>
    <div class="document-frame" :style="{ width: props.document.size.width + 'px', height: props.document.size.height + 'px', top: position.top + 'px', left: position.left + 'px' }">
        <contents :document="props.document"></contents>
    </div>
</template>
<script setup>
import { defineProps, ref } from 'vue';

import Contents from './Contents.vue';

const props = defineProps({
    document: Object,
});
console.log(props.document);
const position = ref({ top: 0, left: 0 });
</script>
<style lang="css" scoped>
.document-frame {
    background-color: white;
    position: absolute;
}
</style>
