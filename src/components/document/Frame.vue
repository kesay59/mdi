<template>
    <div
        ref="documentFrame"
        class="document-frame"
        :style="{
            width: (document.maximize ? document.size.max.width : document.size.current.width) + 'px',
            height: (document.maximize ? document.size.max.height : document.size.current.height) + 'px',
            top: position.top + 'px',
            left: position.left + 'px',
            zIndex: document.order,
        }"
    >
        <toolbar :document="document" :isTop="isTop">
            <div ref="dragField" style="position: absolute; top: 4px; left: 4px; height: calc(100% - 4px); width: calc(100% - 4px)" @dblclick="document.toggleMaximize()"></div>
        </toolbar>
        <div style="width: 100%; height: calc(100% - 24px)" @mousedown="document.increaseOrder()">
            <Inside :document="document"></Inside>
        </div>
    </div>
</template>
<script>
import { ref, onMounted } from 'vue';

import interact from 'interactjs';

import Toolbar from './frame/Toolbar.vue';
import Inside from './Inside.vue';

export default {
    components: {
        Toolbar,
        Inside,
    },
    props: {
        document: Object,
        isTop: Boolean,
    },
    setup(props) {
        const position = ref({ top: 0, left: 0 });

        const dragField = ref(null);
        const documentFrame = ref(null);

        onMounted(() => {
            const dragController = interact(dragField);
            dragController.draggable({
                listeners: {
                    move(event) {
                        var dy = Math.round(event.dy);
                        var dx = Math.round(event.dx);
                        if (Math.abs(dy) > 0 || Math.abs(dx) > 0) {
                            position.value.top = position.value.top + dy;
                            position.value.left = position.value.left + dx;
                        }
                    },
                },
                modifiers: [
                    interact.modifiers.restrictRect({
                        restriction: '.limit-rect',
                        endOnly: true,
                    }),
                ],
                inertia: true,
            });
            const resizeController = interact(documentFrame.value);
            resizeController.resizable({
                margin: 4,
                edges: {
                    left: true,
                    right: true,
                    bottom: true,
                    top: true,
                },
                listeners: {
                    move(event) {
                        if (!props.document.maximize) {
                            var currentSize = props.document.size.current;
                            currentSize.height = Math.round(event.rect.height);
                            currentSize.width = Math.round(event.rect.width);
                            position.value.top += Math.round(event.deltaRect.top);
                            position.value.left += Math.round(event.deltaRect.left);
                        }
                    },
                },
                modifiers: [
                    interact.modifiers.restrictEdges({
                        outer: '.limit-rect',
                    }),
                    interact.modifiers.restrictSize({
                        min: props.document.size.min,
                        max: props.document.size.max,
                    }),
                ],
            });
        });
        return {
            position,
            dragField,
            documentFrame,
        };
    },
};
</script>
<style lang="css" scoped>
.document-frame {
    background-color: white;
    position: absolute;
    border: 1px solid black;
}
</style>
