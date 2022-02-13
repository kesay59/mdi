<template>
    <div ref="place" :style="{ width: width, height: height, outline: '1px solid red' }">asdf</div>
</template>
<script setup>
import { defineProps, ref, computed, onMounted } from 'vue';

const props = defineProps({
    width: Number,
    height: Number,
});
console.log(props);

const width = computed({
    get: () => {
        console.log(props.width);
        return props.width != null ? props.width + 'px' : '100%';
    },
});
console.log(width.value);
const height = computed({
    get: () => (props.height != null ? props.height + 'px' : '100%'),
});
const place = ref(null);
onMounted(() => {
    console.log(place.value);
});
</script>
