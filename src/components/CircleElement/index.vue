<template>
    <div :style="{width: `150px`, height: `150px`, background: `#f00`, borderRadius: `150px`}" @click="onCircleClick">我是不规则窗口</div>
</template>

<script>
    export default {
        methods: {
            onCircleClick(){
                alert('我是不规则窗口');
            }
        }
    }
</script>