<template>
    <div class="icon-button" style="display: inline-block; cursor: pointer; overflow: hidden; position: relative; width: 24px; height: 24px; text-align: center">
        <slot></slot>
    </div>
</template>
<style>
.icon-button {
    display: inline-block;
    cursor: pointer;
    overflow: hidden;
    position: relative;
    width: 24px;
    height: 24px;
    text-align: center;
}
.icon-button:active,
.icon-button:hover,
.icon-button:focus {
    background: grey;
    outline: 0;
}
</style>
