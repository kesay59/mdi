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
import { defineProps } from 'vue';

const props = defineProps({
    limitRect: Object,
});

function* generateOrder(start, end) {
    for (let i = start; i <= end; i++) yield i;
}
const orderGen = generateOrder(0, 2147483647);
class Document {
    #index;
    #order;
    constructor(index, info, maxWidth, maxHeight, minWidth, minHeight) {
        this.#index = index;
        this.#order = orderGen.next().value;
        if (info == null) this.info = {};
        else if (typeof info != 'object') throw 'The type of info must be object.';
        else this.info = info;
        this.iconize = false;
        this.maximize = false;
        this.maxSize = {
            width: maxWidth != null ? maxWidth : props.limitRect.width,
            height: maxHeight != null ? maxHeight : props.limitRect.height,
        };
        this.minSize = {
            width: minWidth != null ? minWidth : 0,
            height: minHeight != null ? minHeight : 0,
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

const openDocument = function (info, maxWidth, maxHeight, minWidth, minHeight) {
    var targetIndex = 0;
    for (var idx = 0; idx < documentList.length; idx++) {
        if (documentList[idx] == null) {
            targetIndex = idx;
            break;
        } else {
            if (documentList[targetIndex].order > documentList[idx].order) targetIndex = idx;
        }
    }
    const doc = new Document(targetIndex, info, maxWidth, maxHeight, minWidth, minHeight);
    documentList[targetIndex] = doc;
    return doc;
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
</script>
