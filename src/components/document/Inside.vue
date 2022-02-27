<template>
    <div style="width: 100%; height: 100%; overflow: auto">
        <inner-contents :document="document"></inner-contents>
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
        console.log('Inside : start');
        console.log(props);
        const innerContents = defineAsyncComponent({
            loader: () => props.document.contentsFn,
            delay: 200,
            timeout: 5000,
            error: ErrorContents,
        });
        return {
            components: [{ name: 'InnerContents', instance: innerContents }],
        };
    },
};
</script>
