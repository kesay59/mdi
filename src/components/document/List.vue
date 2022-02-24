<template>
    <div :style="{ width: props.limitRect.width, height: props.limitRect.height }">
        <template v-for="(document, idx) in documentList" :key="idx">
            <frame :document="document" v-if="document != null"></frame>
        </template>
        <input type="button" text="open" value="open" @click="openDocument('title', import('@/CompTest.vue'), {}, { width: 100, height: 100 })" />
        <input type="button" text="close" value="close" @click="closeDocument(0)" />
        <input type="button" text="closeAll" value="closeAll" @click="closeDocumentAll()" />
        <input type="button" text="test" value="test" @click="test()" />
    </div>
</template>
<script setup>
import { defineProps, defineEmits, ref, onMounted } from 'vue';

import Frame from './Frame.vue';

const props = defineProps({
    limitRect: Object,
});
const emit = defineEmits(['ready']);

function* generateOrder(start, end) {
    for (let i = start; i <= end; i++) yield i;
}
const orderGen = generateOrder(0, 2147483647);
const Document = (() => {
    let indexSymbol = Symbol('INDEX');
    let orderSymbol = Symbol('ORDER');
    let contentsFnSymobl = Symbol('CONTENT_FN');
    return class {
        constructor(index, title, contentsFn, param, size) {
            this[indexSymbol] = index;
            this[orderSymbol] = orderGen.next().value;
            this.title = title;
            this[contentsFnSymobl] = contentsFn;
            this.param = param;
            this.size = size;
            this.contents = null;
            this.iconize = false;
            this.maximize = false;
        }

        get index() {
            return this[indexSymbol];
        }
        get order() {
            return this[orderSymbol];
        }
        increaseOrder() {
            this[order] = orderGen.next().value;
        }
        get contentsFn() {
            return this[contentsFnSymobl];
        }
    };
})();

const DOCUMENT_MAXIMUM_NUMBER = 12;
const documentList = ref(new Array(DOCUMENT_MAXIMUM_NUMBER));
documentList.value.fill(null);

const validateTitle = function (title) {
    return title != null ? title : '';
};
const validateContentsFn = function (contentsFn) {
    if (contentsFn == null || (typeof contentsFn != 'object' && typeof contentsFn != 'function') || typeof contentsFn.then != 'function') {
        throw 'The type of contentFn must be Promise.';
    } else {
        return contentFn;
    }
};
const validateParam = function (param) {
    if (param == null) return {};
    else if (typeof param != 'object') throw 'The type of param must be object.';
    else return param;
};
const validateSize = function (size) {
    if (size.max == null) size.max = {};
    else if (typeof size.max != 'object') throw 'The type of size.max must be object.';
    size.max = {
        width: size.max.width != null ? size.max.width : props.limitRect.width,
        height: size.max.height != null ? size.max.height : props.limitRect.height,
    };

    if (size.min == null) size.min = {};
    else if (typeof size.min != 'object') throw 'The type of size.min must be object.';
    size.min = {
        width: size.min.width != null ? size.min.width : 100,
        height: size.min.height != null ? size.min.height : 100,
    };

    if (size.current == null) size.current = {};
    else if (typeof size.current != 'object') throw 'The type of size.current must be object.';
    size.current = {
        width: size.current.width != null ? size.current.width : size.min.width,
        height: size.current.height != null ? size.current.height : size.min.height,
    };

    if (size.max.height < size.min.height || size.max.width < size.max.width) throw 'height/width of size.max must be bigger then that of size.min';
    // if (size.max.height < size.current.height || size.max.width < size.current.width) throw 'height/width of size.max must be bigger then that of size.current';

    return size;
};

/**
 * @param {string} title
 * @param {function(promise)} contentsFn
 * @param {object} param
 * @param {object} size
 * @return {class(Document)}
 * @code openDocument("title", import("src/Example.vue"), {}, {current: {width: 300, height: 300}, min: {width: 100, height: 100}, max: {width: 500, height: 500}})
 */
const openDocument = function (title, contentsFn, param, size) {
    var targetIndex = 0;
    for (var idx = 0; idx < documentList.value.length; idx++) {
        if (documentList.value[idx] == null) {
            targetIndex = idx;
            break;
        } else {
            if (documentList.value[targetIndex].order > documentList.value[idx].order) targetIndex = idx;
        }
    }
    const document = new Document(targetIndex, validateTitle(title), validateContentsFn(contentsFn), validateParam(param), validateSize(size));
    documentList.value[targetIndex] = document;
    return document;
};
const closeDocument = function (index) {
    documentList.value[index] = null;
};
const closeDocumentAll = function () {
    documentList.value.fill(null);
};
const test = function () {
    console.log(documentList.value);
};

const documentApi = {
    method: {
        open: openDocument,
        close: closeDocument,
        closeAll: closeDocumentAll,
    },
    list: documentList.value,
};
onMounted(() => {
    // console.log(import('./Frame.vue'));
    emit('ready', documentApi);
});
</script>
