<template>
    <div>
        <button @click="onWindowMax">最大化窗口</button>
        <button @click="onWindowMin">最小化窗口</button>
        <button @click="onWindowRestore">恢复窗口</button>
        <button @click="onWindowClose">关闭窗口</button>
    </div>
</template>

<script>
const { remote } = window.require('electron');

export default {
    methods: {
        onWindowMax(){
            remote.getCurrentWindow().maximize();
        },
        onWindowMin(){
            remote.getCurrentWindow().minimize();
        },
        onWindowRestore(){
            remote.getCurrentWindow().restore();
        },
        onWindowClose(){
            remote.getCurrentWindow().close();
        }
    }
}
</script>