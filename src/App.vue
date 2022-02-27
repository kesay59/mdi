<template>
    <div>
        <input type="button" @click="changeSize(900, 600)" value="small" />
        <input type="button" @click="changeSize(1200, 800)" value="big" />
    </div>
    <div style="border: 1px solid black" :style="{ width: width + 'px', height: height + 'px' }">
        <mdi @ready="test" />
    </div>
</template>

<script>
import { ref } from 'vue';
import Mdi from '@/components/Place.vue';
// import Mdi from '@/components/document/List.vue';

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
            test: function (e) {
                console.log(e);
                e.method.open({ title: 'title', contentsFn: import('@/page/Test.vue'), data: {}, size: { current: { width: 200, height: 100 } }, fixedContents: true });
                console.log(e.list);
            },
        };
    },
};
</script>
