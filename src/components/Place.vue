<template>
    <div style="width: 100%; height: 100%">
        <document-list @readyApi="handleReady" :maximumNumber="maximumNumber"></document-list>
    </div>
</template>
<script>
import DocumentList from './document/List.vue';

export default {
    props: {
        maximumNumber: Number,
    },
    components: {
        DocumentList,
    },
    emits: ['readyApi'],
    setup(props, { emit }) {
        const handleReady = function (documentApi) {
            emit('readyApi', documentApi);
        };
        return {
            handleReady,
        };
    },
};
</script>
