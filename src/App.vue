<template>
    <div>
        <input type="button" @click="changeSize(900, 600)" value="small" />
        <input type="button" @click="changeSize(1200, 800)" value="big" />
    </div>
    <div style="border: 1px solid black" :style="{ width: width + 'px', height: height + 'px' }">
        <mdi ref="mdi" :width="width - 100" :height="height - 100" />
    </div>
</template>

<script>
import { ref } from 'vue';
import Mdi from './components/Place';

export default {
    name: 'App',
    components: {
        Mdi,
    },
    setup() {
        const mdi = ref(null);
        const width = ref(1000);
        const height = ref(700);
        return {
            mdi,
            width,
            height,
            changeSize: function (w, h) {
                width.value = w;
                height.value = h;
            },
        };
    },
};
</script>
