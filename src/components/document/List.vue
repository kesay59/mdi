<script>
import { ref, computed } from 'vue';

import Frame from './Frame.vue';

export default {
    components: {
        Frame,
    },
    emits: ['readyApi'],
    setup(props, { emit }) {
        function* generateOrder(start, end) {
            for (let i = start; i <= end; i++) yield i;
        }
        const orderGen = generateOrder(0, 2147483647);
        const Document = (() => {
            let indexSymbol = Symbol('INDEX');
            let orderSymbol = Symbol('ORDER');
            let contentsFnSymobl = Symbol('CONTENT_FN');
            let fixedContentsSymbol = Symbol('FIXED_CONTENTS');
            return class {
                constructor(index, title, contentsFn, data, size, fixedContents) {
                    this[indexSymbol] = index;
                    this[orderSymbol] = orderGen.next().value;
                    this.title = title;
                    this[contentsFnSymobl] = contentsFn;
                    this.data = data;
                    this.size = size;
                    this[fixedContents] = true;
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
                    if (topIndex.value != this[indexSymbol]) this[orderSymbol] = orderGen.next().value;
                }
                get contentsFn() {
                    return this[contentsFnSymobl];
                }
                get fixedContents() {
                    return this[fixedContentsSymbol];
                }
                toggleMaximize() {
                    this.increaseOrder();
                    this.maximize = !this.maximize;
                }
                toggleIconize() {
                    this.increaseOrder();
                    this.iconize = !this.iconize;
                }
                close() {
                    closeDocument(this[indexSymbol]);
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
                throw 'The type of contentsFn must be Promise.';
            } else {
                return contentsFn;
            }
        };
        const validateData = function (param) {
            if (param == null) return {};
            else if (typeof param != 'object') throw "The type of 'data' must be object.";
            else return param;
        };
        const validateSize = function (size) {
            if (size.max == null) size.max = {};
            else if (typeof size.max != 'object') throw "The type of 'size.max' must be object.";
            size.max = {
                width: size.max.width != null ? size.max.width : 500,
                height: size.max.height != null ? size.max.height : 500,
            };

            if (size.min == null) size.min = {};
            else if (typeof size.min != 'object') throw "The type of 'size.min' must be object.";
            size.min = {
                width: size.min.width != null ? size.min.width : 100,
                height: size.min.height != null ? size.min.height : 100,
            };

            if (size.current == null) size.current = {};
            else if (typeof size.current != 'object') throw "The type of 'size.current' must be object.";
            size.current = {
                width: size.current.width != null ? size.current.width : 300,
                height: size.current.height != null ? size.current.height : 300,
            };

            if (size.max.height < size.min.height || size.max.width < size.max.width) throw "'height/width' of 'size.max' must be bigger then that of 'size.min'";
            // if (size.max.height < size.current.height || size.max.width < size.current.width) throw 'height/width of size.max must be bigger then that of size.current';

            return size;
        };
        const validateFixedContents = function (fixedContents) {
            if (fixedContents) fixedContents = false;
            if (typeof fixedContents != 'boolean') throw "The type of 'fixedContents' must be boolean";
            else return fixedContents;
        };

        /**
         * @param {string} title
         * @param {function(promise)} contentsFn
         * @param {object} data
         * @param {object} size
         * @param {boolean} fixedContents
         * @return {class(Document)}
         * @code openDocument({
         *           title : "title",
         *           contentsFn : import("src/Example.vue"),
         *           data : {},
         *           size : {current: {width: 300, height: 300}, min: {width: 100, height: 100}, max: {width: 500, height: 500}},
         *           fixedContents : true
         *       })
         */
        const openDocument = function (params) {
            var targetIndex = 0;
            for (var idx = 0; idx < documentList.value.length; idx++) {
                if (documentList.value[idx] == null) {
                    targetIndex = idx;
                    break;
                } else {
                    if (documentList.value[targetIndex].order > documentList.value[idx].order) targetIndex = idx;
                }
            }
            const document = new Document(targetIndex, validateTitle(params.title), validateContentsFn(params.contentsFn), validateData(params.data), validateSize(params.size), validateFixedContents(params.fixedContents));
            documentList.value[targetIndex] = document;
            return document;
        };
        const closeDocument = function (index) {
            documentList.value[index] = null;
        };
        const closeDocumentAll = function () {
            documentList.value.fill(null);
        };
        const topIndex = computed({
            get: () => {
                var top = -1;
                documentList.value.forEach((el, idx) => {
                    if (el != null) {
                        if (top == -1 || documentList.value[top].order < el.order) top = idx;
                    }
                });
                return top;
            },
        });

        const documentApi = {
            method: {
                open: openDocument,
                close: closeDocument,
                closeAll: closeDocumentAll,
            },
            list: documentList.value,
        };
        emit('readyApi', documentApi);
        return {
            documentList,
            topIndex,
        };
    },
};
</script>

<template>
    <div class="limit-rect" style="width: 100%; height: 100%; position: relative">
        <template v-for="(document, idx) in documentList" :key="idx">
            <frame :document="document" :isTop="topIndex == idx" v-if="document != null" v-show="!document.iconize"></frame>
        </template>
    </div>
</template>
