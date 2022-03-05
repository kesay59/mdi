<script>
import { h } from 'vue';
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
        return () => h('div', { style: { width: '100%', height: '100%' } }, [h(DocumentList, { onReady: handleReady })]);
    },
};
</script>
