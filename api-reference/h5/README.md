# 硅基DUIX H5 SDK使用文档

## 安装

```shell
npm i duix-guiji-light -S
```

## 快速开始

```js

import DUIX from 'duix-guiji-light'

const duix = new DUIX()

duix.on('intialSucccess', () => {
  // 开始会话
  duix.start({
    conversationId: '',
    openAsr: true
  }).then(res => {
    console.info(res)
  })
})

// 初始化
duix.init({
  sign: 'xxxx',
  containerLable: '.remote-container'
})

```
**注意：** 不要将duix实例放在Vue的data中，也不要放在react的state中（无需使其响应式）。

## 调用流程
<img src="./h5sdk.png" alt="image.png"/>

## 方法

#### init(option: object): Promise
> ```js
> duix.init({
>   sign: '',
>   containerLable: '.remote-container'   
> })
> ```

***参数***

| 名称| 类型 |必填 | 描述 | 
| :-----------------| :-------------- | :----------------------------------------------------------- | :------    | 
| containerLable   | string   |  是 |  数字人容器。数字人将渲染到这个Dom中。 | 
| sign | string   |   是    |  鉴权字符串。[如何获取sign?](https://duix.guiji.ai/document/server-api/sign.html) |

#### start(option:object): Promise

调用 `start` 方法将开始渲染数字人并进行交互。
> **注意：** 此方法需要在`intialSucccess`事件触发后调用，`intialSucccess`事件表示所有资源准备完成。如下：
>
> ```js
> duix.on('intialSucccess', () => {
>   duix.start({
>      conversationId: '',    
>      muted: true,
>      wipeGreen: false,
>  }).then(res => {
>     console.log('res', res)
>  })
> })
> ```

***参数***

| key         | 类型    | 必填 | 默认值 | 说明                                                         |
| ----------- | ------- | ---- | ------ | ------------------------------------------------------------ |
| conversationId| number | 是 | | 平台会话id|
| muted       | boolean | 否   | false  | 是否以静音的模式开启数字人视频。<br/>**重要提示:** 由于[自动播放政策](https://developer.mozilla.org/zh-CN/docs/Web/Media/Autoplay_guide)限制，如果您的网页还没有与用户有任何点击交互，请把这个参数置为`true`，**否则将导致数字人无法加载**。如果您有这样的需求，建议您先用静音模式启动，在产品中设计一个交互，比如一个“开始”的按钮来调用`duix.setVideoMuted(false)` |
| openAsr     | boolean | 否   | false  | 是否直接开启实时识别，此项传true，相当于在调start后立即调用 `openAsr` 方法 |
| wipeGreen   | boolean | 否   | false  | 是否对视频做扣绿幕操作。*在平台上创建会话时需上传一个纯绿色的背景图片* |
| userId   | number | 否   | | 用户唯一标识 |

#### setVideoMuted(flag:boolean)

设置数字人视频是否静音, true是静音，false为非静音。

#### break()

打断数字人说话

#### speak(option: Object): Promise

驱动数字人说话，支持文本驱动和音频文件驱动。

```javascript
duix.speak({content: '', audio: 'https://your.website.com/xxx/x.wav'})
```

***参数***

| 名称| 类型 |必填 | 描述 | 
| :-----------------| :-------------- | :----------------------------------------------------------- | ------    | 
| content   | string   |  是 |  数字人回答的文本 |
| audio | string   |   否    |  数字人回答音频地址，可以通过`getAnswer`获取平台配置的答案|
| interrupt | boolean   |   否    |  是否打断之前说的话 |

#### answer(option: Object): Promise

数字人回答问题

```javascript
duix.answer({question: 'xxx'})
```

***参数***

| 名称| 类型 |必填 | 描述 | 
| :-----------------| :-------------- | :----------------------------------------------------------- | ------    | 
| question   | string   |  是 |  问题文本 |
| interrupt | boolean   |   否    |  是否打断之前说的话 |

#### getAnswer(option: Object): Promise

平台获取问题的答案

```javascript
duix.getAnswer({ question: '今天的天气怎么样？' })
```

***参数***

| 名称| 类型 |必填 | 描述 | 
| :-----------------| :-------------- | :----------------------------------------------------------- | ------    | 
| question   | string   |  是 |  问题文本 | 
| userId   | number   |  否 |  业务侧用户唯一id，指定后获得答案是开启记忆功能 | 


***返回data***

| 名称| 类型 | 描述 | 
| :-----------------| :-------------- | :----------------------------------------------------------- | 
| answer   | string   | 数字人回答的文本 |  
| audio | string   |   数字人回答音频地址 |

#### startRecord():Promise

开始录音。

#### stopRecord():Promise

结束录音，录音成功后将返加语音识别结果（文本），返回Promise。

#### openAsr():Promise

开启语音实时识别(注意此方法需在show触发时候调用)。开启语音实时识别后，可通过监听 `asrResult` 事件，接收识别（文本）结果。

#### closeAsr():Promise

关闭语音实时识别。

#### stop()

停止当前会话。建议在页卸载事件中调用此方法，以防止刷新或关闭网页时当前会话资源未及时释放。

```js
window.addEventListener('beforeunload', function(event) {
  if (duix) {
    duix.stop()
  }
});
````

#### getLocalStream()

获取本地语音流，方便外部做音频可视化等功能

#### getRemoteStream()

获取远端音视频流，方便外部做自定义

#### resume()

恢复播放，目前仅针对部分移动端浏览器即便由用户操作触发自动播放，仍无效的情况。在抛出4009的error中由用户操作触发resume方法可解决。

#### on(eventname, callback)

监听事件。

##### 参数

###### eventname

事件名称，详见下方表格。

###### callback

回调函数

## 返回格式说明
对于返回`Promise`的方法，参数格式为` { err, data } `,如果`err`不为空，则代表调用失败。

## 事件

| 名称           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| error       | 有未捕获错误时触发。 |
| bye           | 会话结束时触发。                                |
| intialSucccess          | 数字人初始化成功。可以在这个事件的回调中调用`start`方法 |
| show         | 出数字人已显示。 |
| progress         | 数字人加载进度。 |
| speakSection         | 数字人说话当前的音频和文本片段（answer方法会采用流式获取结果，如果单独调用speak,该事件与speakStart一致） |
| speakStart         | 驱动数字人说话，到数字人实际说话之间有一点延迟，此事件表示数字人实际开始说话了 |
| speakEnd         | 数字人说话结束 |
| asrStart | 单句语音识别开启 |
| asrData | 单句语音实时识别结果 |
| asrStop | 单句语音识别结束 |
| report | 每秒报告RTC网络/画面质量等信息 |

### error

```javascript
{
  code: '', // 错误码  
  message: '', // 错误信息
  data: {} // 错误内容
}

```

***error code***


| 名称           | 描述   | data |
| :------------- | :----------| :---------- |
| 3001 | RIC连接失败| |
| 4001 | 开始会话失败 | |
| 4005 | 鉴权失败 | |
| 4007 | 服务端会话异常结束 | code: 100305 模型文件未找到|
| 4008 | 获取麦克风流失败 | |
| 4009 | 浏览器基于播放策略无法自动播放 | 请先考虑静音播放或用户操作调用start方法|

### progress 

number类型的进度，0-100

### speakSection 

```javascript

{
  audio: '', // 音频地址
  content: '', // 文本内容
}

```

### speakStart 

```javascript

{
  audio: '', // 音频地址
  content: '', // 文本内容
}

```

### speakEnd 

```javascript

{
  audio: '', // 音频地址
  content: '', // 文本内容
}

```

### asrData
每段文本的识别，会以一个asrStart开始，一个asrStop结束，中间有一个或多个asrData（递增式的推送），可以在asrData事件中，获取语音识别结果，用于展示。
```javascript

{
  content: '', // 文本内容
}

```

### report 

```javascript
{
    "video": { // 视频相关信息
        "download": {
            "frameWidth": 1920, // 分辨率-宽
            "frameHeight": 1080,// 分辨率-高
            "framesPerSecond": 24,// 分辨率-帧率
            "packetsLost": 0, // 总丢包数
            "packetsLostPerSecond": 0 // 总丢率（每秒丢包数）
        }
    },
    "connection": { // 连接信息
        "bytesSent": 206482, // 发送总字节数
        "bytesReceived": 79179770, // 接收总字节数
        "currentRoundTripTime": 3, // 包往返时间（单位毫秒），这个时间越大画面越延迟
        "timestamp": 1688043940523,
        "receivedBitsPerSecond": 2978472, // 接收码率（每秒接收到的bit数，1Mb = 1024^2 bit）
        "sentBitsPerSecond": 7920 // 发送码率（每秒发送的bit数，1Mb = 1024^2 bit）
    }
}
```
**注意：** 未在上述详细列出的事件参数均为空

## 常见问题
1. 部分浏览器，特别是移动端浏览器，播放数字人需要由用户操作触发，推荐start事件由用户点击调用，哪怕muted是静音状态，否则部分浏览器可能不显示数字人
2. 部分ios系统下的微信浏览器需要调用navigator.mediaDevices.getUserMedia授权后才能播放远端流
3. 如果是开启asr识别的情况下，会需要getUserMedia的用户授权，由于浏览器限制，此时本地调试需要https的环境。
4. 微信小程序对接的方式，可以通过web-view组件对接。
5. 部分移动端浏览器即便由用户点击调用start开启，仍无法适应浏览器的自动播放策略，可在收到4009的error时，再由用户事件触发resume方法

## 版本记录

**0.0.1**
1.初始化sdk

