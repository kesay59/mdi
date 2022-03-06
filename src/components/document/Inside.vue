<template>
    <div style="width: 100%; height: 100%; overflow: auto">
        <component :is="innerContents"></component>
    </div>
</template>
<script>
import { defineAsyncComponent } from 'vue';
import ErrorContents from '@/components/error/ErrorContents.vue';

export default {
    props: {
        document: Object,
    },
    setup(props) {
        const innerContents = defineAsyncComponent({
            loader: () => props.document.contentsFn,
            delay: 200,
            timeout: 5000,
            error: ErrorContents,
        });
        return {
            print,
            innerContents,
        };
    },
};
</script>
