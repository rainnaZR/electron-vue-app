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
        <button @click="onInsertCss">插入样式</button>
        <button @click="onRemoveCss">移除样式</button>
        <button @click="onGetUserDataPath">获取用户个性化数据存储目录</button>
        <button @click="onWriteFile">写文件</button>
        <button @click="onReadFile">读文件</button>
    </div>
</template>

<script>
const { remote } = window.require('electron');

export default {
    data(){
        return{
            cssKey: '',
            dataJson: {
                name: 'test',
                age: 10
            }
        }
    },
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
        },
        async onInsertCss(){
            let webContent = remote.getCurrentWebContents();
            this.cssKey = await webContent.insertCSS('body,html{background:red!important}');
        },
        async onRemoveCss(){
            let webContent = remote.getCurrentWebContents();
            await webContent.removeInsertedCSS(this.cssKey);
        },
        onGetUserDataPath(){
            let path = remote.app.getPath('userData');
            alert(path);
        },
        onWriteFile(){
            let fs = window.require('fs-extra');
            let path = require('path');
            let dataPath = remote.app.getPath('userData');
            dataPath = path.join(dataPath, 'test.data.json');
            console.log('文件写入地址', dataPath);

            fs.writeFileSync(dataPath, JSON.stringify(this.dataJson), {
                encoding: 'utf8'
            })
        },
        onReadFile(){
            let fs = window.require('fs-extra');
            let path = require('path');
            let dataPath = remote.app.getPath('userData');
            dataPath = path.join(dataPath, 'test.data.json');

            let content = fs.readFileSync(dataPath, {
                encoding: 'utf8'
            })
            console.log('文件内容读取', content);
        }
    }
}
</script>