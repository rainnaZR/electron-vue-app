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
        <button @click="onDatabaseModify('add')">数据库增加数据</button>
        <button @click="onDatabaseModify('query')">数据库查询数据</button>
        <button @click="onDatabaseModify('modify')">数据库修改数据</button>
        <button @click="onDatabaseModify('delete')">数据库删除数据</button>
        <button @click="onOpenFile">打开文件</button>
        <button @click="onSettingMenu">自定义设置菜单</button>
        <button @click="onCopyContent('text', '你好文本')">复制文字</button>
        <button @click="onReadContent('text')">读取复制的文字</button>
        <button @click="onCopyContent('html', '<strong>你好HTML</strong>')">复制HTML</button>
        <button @click="onReadContent('html')">读取复制的HTML</button>
        <button @click="onCopyContent('img')">复制图片</button>
        <button @click="onCopyContent('clear')">清除复制的内容</button>
        <button @click="onSendNotice">发送系统通知</button>
        <button @click="onGetRequest('http', 'http://192.168.31.8:9084/api/business/linctex/video/view/21?id=21')">与web服务通信(http)</button>
        <button @click="onGetRequest('https', 'https://www.sukuan3d.com/api_v2/business/linctex/video/view/26?id=26')">与web服务通信(https)</button>
        <button @click="onGetWebRequest('https://www.sukuan3d.com/api_v2/business/linctex/video/view/26?id=26')">接口调用</button>
        <button @click="onGetSocketRequest('ws://www.sukuan3d.com/api_v2/business/linctex/video/view/26?id=26')">使用websocket调用服务</button>
        <button @click="onGetCapture">录屏</button>
        <button @click="onViewBattery">查看电源信息</button>
        <button @click="onPrint">打印</button>
        <button @click="onSavePdf">保存为PDF</button>

        <video style="position:fixed;bottom:0;left:0;background:#000;" width="400" height="300"></video>
    </div>
</template>

<script>
import Dexie from 'dexie';
const { remote } = window.require('electron');

export default {
    data(){
        return{
            cssKey: '',
            dataJson: {
                name: 'test',
                age: 10
            },
            db: ''
        }
    },
    mounted(){
        this.onCreateDatabase();
        this.onKeyboardClick();
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

            fs.writeFileSync(dataPath, JSON.stringify(this.dataJson), {
                encoding: 'utf8'
            })
            alert(`文件写入成功，地址为${dataPath}`);
        },
        onReadFile(){
            let fs = window.require('fs-extra');
            let path = require('path');
            let dataPath = remote.app.getPath('userData');
            dataPath = path.join(dataPath, 'test.data.json');

            let content = fs.readFileSync(dataPath, {
                encoding: 'utf8'
            })
            alert(`文件读取成功，内容为${content}`);
        },
        onCreateDatabase(){
            this.db = new Dexie('testDatabase');
            this.db.version(1).stores({
                friends: '++id,name,age'
            })
        },
        async onDatabaseModify(type){
            switch(type){
                case 'add':
                    await this.db.friends.add({
                        name: 'testA',
                        age: 20
                    })
                    break;
                case 'query':
                    await this.db.friends.filter(i => i.id == 1);
                    break;
                case 'modify':
                    await this.db.friends.put({
                        id: 1,
                        name: 'modifiedName'
                    });
                    break;
                case 'delete':
                    await this.db.friends.delete(2);
                    break;
            }
        },
        async onOpenFile(){
            let filePath = await remote.dialog.showOpenDialog({
                title: '我要选择一个文件',
                buttonLabel: '选择按钮',
                defaultPath: remote.app.getPath('downloads'),
                files: [{
                    name: '图片',
                    extensions: ['jpg', 'png']
                }]
            });
            console.log('onOpenFile', filePath);
        },
        onSettingMenu(){
            let Menu = remote.Menu;
            let templateArr = [{
                    label: ''
                },{
                label: '菜单A',
                submenu: [{
                    label: '菜单A-1'
                },{
                    label: '菜单A-2',
                    click(){
                        alert('菜单A-2点击');
                    }
                },{
                    label: '菜单A-3'
                }]
            }];
            let menu = Menu.buildFromTemplate(templateArr);
            Menu.setApplicationMenu(menu);
        },
        onKeyboardClick(){
            window.onkeydown = event => {
                if((event.ctrlKey || event.metaKey) && event.keyCode == 83){
                    alert('Ctrl+S 按键监听');
                }
            }
        },
        onCopyContent(type, value){
            let { clipboard, nativeImage } = window.require('electron');
            switch(type){
                case 'text':
                    clipboard.writeText(value);
                    break;
                case 'html':
                    clipboard.writeHTML(value);
                    break;
                case 'img':
                    {
                    let imgPath = window.require('path').join(__dirname, 'favicon.ico');
                    let img = nativeImage.createFromPath(imgPath);
                    clipboard.writeImage(img);
                    break;
                    }
                case 'clear':
                    clipboard.clear();
                    break;
            }
            value && alert(`复制成功，复制内容是"${value}"`);
        },
        onReadContent(type){
            let { clipboard } = window.require('electron');
            switch(type){
                case 'text':
                    alert(clipboard.readText());
                    break;
                case 'html':
                    alert(clipboard.readHTML());
                    break;
            }
        },
        onSendNotice(){
            let { Notification } = remote;
            let notification = new Notification({
                title: '消息的标题',
                body: '消息的正文，消息的正文，消息的正文，消息的正文，消息的正文，消息的正文'
            })
            notification.show();
            notification.on('click', () => {
                alert('用户点击了消息')
            })
        },
        onGetRequest(type, url){
            let https = type == 'http' ? require('http') : require('https');
            https.get(url, res => {
                let html = '';
                res.on('data', data => (html += data));
                res.on('end', () => {
                    alert(`接口响应内容为：${html}`)
                })
            })
        },
        async onGetWebRequest(url){
            let res = await fetch(url);
            let json = await res.json();
            alert(`接口响应内容为：${JSON.stringify(json)}`)
        },
        onGetSocketRequest(url){
            let websocket = new WebSocket(url);
            websocket.onopen = evt => {
                alert('open', evt);
            }
            websocket.onclose = evt => {
                alert('close', evt)
            }
            websocket.onmessage = evt => {
                alert(evt.data);
            }
            // 向服务端发送数据
            websocket.sent('我要发送数据');
            // 关闭链接
            websocket.close();
        },
        async onGetCapture(){
            let { desktopCapturer } = window.require('electron');
            let sources = await desktopCapturer.getSources({
                types: ['window', 'screen']
            });
            let target = sources.find(v => v.name == 'vue-electron');
            let mediaStream = await navigator.mediaDevices.getUserMedia({
                audio: false,
                video: {
                    mandatory: {
                        chromeMediaSource: 'desktop',
                        chromeMediaSourceId: target.id
                    }
                }
            });
            var video = document.querySelector('video');
            video.srcObject = mediaStream;
            video.onloadedmetadata = () => video.play();
        },
        async onViewBattery(){
            let batteryManager = await navigator.getBattery();
            let batteryInfo = `是否在充电：${batteryManager.charging}，还剩余电量：${batteryManager.dischargingTime}，当前充电水平：${batteryManager.level}`;
            alert(batteryInfo);
        },
        onPrint(){
            let webContents = remote.getCurrentWebContents();
            let printers = webContents.getPrinters(); // 获取所有的打印机
            let printer = printers.filter(i => i.name == 'myprint')[0];  // 选择需要打印的打印机
            webContents.print({
                slient: false,
                printBackground: true,
                deviceName: printer?.name
            }, (success, errorType) => {
                !success && alert(errorType);
            })
        },
        async onSavePdf(){
            let fs = window.require('fs');
            let webContents = remote.getCurrentWebContents();
            let data = await webContents.printToPDF({});
            let pathObj = await remote.dialog.showSaveDialog({
                title: '当前页面保存为pdf文件',
                filters: [{
                    name: 'pdf',
                    extensions: ['pdf']
                }]
            });
            if(pathObj.canceled) return; // 如果用户取消，则文件不保存
            fs.writeFile(pathObj.filePath, data, error => {
                error && console.log(error);
                alert('保存成功');
            });
        }
    }
}
</script>