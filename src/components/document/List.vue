<template>
    <div :style="{ width: props.limitRect.width, height: props.limitRect.height }">
        <template v-for="(document, idx) in documentList" :key="idx"></template>
        <input type="button" text="open" value="open" @click="openDocument()" />
        <input type="button" text="close" value="close" @click="closeDocument(0)" />
        <input type="button" text="closeAll" value="closeAll" @click="closeDocumentAll()" />
        <input type="button" text="test" value="test" @click="test()" />
    </div>
</template>
<script setup>
import { defineProps, defineEmits, onMounted } from 'vue';

const props = defineProps({
    limitRect: Object,
});
const emit = defineEmits(['ready']);

function* generateOrder(start, end) {
    for (let i = start; i <= end; i++) yield i;
}
const orderGen = generateOrder(0, 2147483647);
class Document {
    #index;
    #order;
    #contentFn;
    constructor(index, title, contentFn, param, size, maxSize, minSize) {
        this.#index = index;
        this.#order = orderGen.next().value;
        this.title = title != null ? title : '';
        if (contentFn != null && (typeof contentFn === 'object' || typeof contentFn === 'function') && typeof contentFn.then === 'function') this.#contentFn = contentFn;
        else throw 'The type of contentFn must be Promise.';
        this.content = null;
        if (param == null) this.param = {};
        else if (typeof param != 'object') throw 'The type of param must be object.';
        else this.param = param;
        this.iconize = false;
        this.maximize = false;

        if (maxSize == null) this.maxSize = { width: props.limitRect.width, height: props.limitRect.height };
        else if (typeof maxSize != 'object') throw 'The type of maxSize must be object.';
        else this.maxSize = maxSize;
        this.maxSize = {
            width: maxSize.width != null ? maxSize.width : props.limitRect.width,
            height: maxSize.height != null ? maxSize.height : props.limitRect.height,
        };

        if (minSize == null) this.minSize = { width: 0, height: 0 };
        else if (typeof minSize != 'object') throw 'The type of minSize must be object.';
        else this.minSize = minSize;
        this.minSize = {
            width: minSize.width != null ? minSize.width : 0,
            height: minSize.height != null ? minSize.height : 0,
        };

        if (size == null) this.size = { width: maxSize.width, height: maxSize.height };
        else if (typeof size != 'object') throw 'The type of size must be object.';
        else this.size = size;
        this.size = {
            width: size.width != null ? size.width : maxSize.width,
            height: size.height != null ? size.height : maxSize.height,
        };
        if (this.minSize.width > this.maxSize.width || this.minSize.height > this.maxSize.height) throw 'The maximum size must be larger than the minimum size.';
    }

    get index() {
        return this.#index;
    }
    get order() {
        return this.#order;
    }
    increaseOrder() {
        this.#order = orderGen.next().value;
    }
}

const DOCUMENT_MAXIMUM_NUMBER = 12;
const documentList = new Array(DOCUMENT_MAXIMUM_NUMBER);
documentList.fill(null);

const openDocument = function (title, param, size, maxSize, minSize) {
    var targetIndex = 0;
    for (var idx = 0; idx < documentList.length; idx++) {
        if (documentList[idx] == null) {
            targetIndex = idx;
            break;
        } else {
            if (documentList[targetIndex].order > documentList[idx].order) targetIndex = idx;
        }
    }
    const document = new Document(targetIndex, title, param, size, maxSize, minSize);
    documentList[targetIndex] = document;
    return document;
};
const closeDocument = function (index) {
    documentList[index] = null;
};
const closeDocumentAll = function () {
    documentList.fill(null);
};
const test = function () {
    console.log(documentList);
};

const documentApi = {
    method: {
        open: openDocument,
        close: closeDocument,
        closeAll: closeDocumentAll,
    },
    list: documentList,
};
onMounted(() => {
    // console.log(import('./Frame.vue'));
    emit('ready', documentApi);
});
</script>
