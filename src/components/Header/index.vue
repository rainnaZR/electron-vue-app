<template>
    <div>
        <button @click="onWindowMax">最大化窗口</button>
        <button @click="onWindowMin">最小化窗口</button>
        <button @click="onWindowRestore">恢复窗口</button>
        <button @click="onWindowClose">关闭窗口</button>
        <button @click="onModalWindowOpen">打开模态窗口</button>
        <button @click="onGetWebContents">打印页面对象</button>
        <button @click="onSetZoom(2)">页面缩放比例设置为2</button>
        <button @click="onSetZoom(0)">页面还原</button>
        <button @click="onCreateBrowserView">创建BrowserView</button>
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
        },
        onGetWebContents(){
            let webContent = remote.getCurrentWebContents();
            console.log(webContent);
        },
        onSetZoom(value){
            let webContent = remote.getCurrentWebContents();
            webContent.setZoomLevel(value);
        },
        onCreateBrowserView(){
            let view = new remote.BrowserWindow({
                webPerferences: {
                    nodeIntegration: true
                }
            });
            let win = remote.getCurrentWindow();
            win.setBrowserView(view);
            let size = win.getSize();
            view.setBounds({
                x: 80,
                y: 80,
                width: size[0],
                height: size[1]
            });
            view.webContents.loadURL('http://www.baidu.com');
        }
    }
}
</script>