<template>
    <div>
        <button @click="onWindowMax">最大化窗口</button>
        <button @click="onWindowMin">最小化窗口</button>
        <button @click="onWindowRestore">恢复窗口</button>
        <button @click="onWindowClose">关闭窗口</button>
        <button @click="onModalWindowOpen">打开模态窗口</button>
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
        },
        onModalWindowOpen(){
            this.win = new remote.BrowserWindow({
                parent: remote.getCurrentWindow(),
                modal: true,  // true打开模态窗口，禁用父窗口所有操作；false为子窗口，不禁用父窗口操作
                webPerferences: {
                    nodeIntegration: true
                }
            })
        }
    }
}
</script>