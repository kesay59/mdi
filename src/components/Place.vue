<template>
    <div style="width: 100%; height: 100%">
        <!-- <component :is="DocumentList" @ready="handleReady"></component> -->
        <document-list @ready="handleReady"></document-list>
    </div>
</template>
<script>
import DocumentList from './document/List.vue';

export default {
    components: {
        DocumentList,
    },
    emits: ['ready'],
    setup(props, { emit }) {
        console.log('place : start');
        const handleReady = function (documentApi) {
            console.log('place : ready');
            emit('ready', documentApi);
        };
        return {
            handleReady,
            DocumentList,
        };
    },
};
</script>
