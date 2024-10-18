## 硅基Duix-SDK V1.1.0使⽤⽂档
更新时间：2024年10月17日

本文介绍如何使用SAAS DUIX iOS SDK，包括安装下载，关键接口及其代码示例。
### 物料准备
 GJDigitalSDK.framework、WebRTC.framework
 下载：<a href="https://digital-public.obs.cn-east-3.myhuaweicloud.com/duix/ios/duix-cloud-demo-ios_1.1.0.zip" download="https://digital-public.obs.cn-east-3.myhuaweicloud.com/duix/ios/duix-cloud-demo-ios_1.1.0.zip">duix_sdk_demo_ios</a>
 `GJDigitalSDK.framework`里`Resources.bundle`中`config.json`中为配置主域名配置，一般默认配置即可。
### 开发环境
开发⼯具: Xcode

### 快速开始
```
#pragma mark -开始会话
- (void)toStart {

    [[DigitalManager manager] initWithAppId:AppId appKey:AppKey conversationId:ConversationId block:^(BOOL isSuccee, NSString *errorMsg) {
        if (isSuccee) {
            [[DigitalManager manager] toStart];
        } else {
            NSLog(@"GJDigitalDemo==errorMsg==%@",errorMsg);
        }
    }];
}
```

### 调用流程
```
1.启动DUIX服务前需要准备好授权的appId,appKey,conversationId;
2.授权麦克风权限权限；
3.init初始化数字人渲染，ASR及对话交互服务,开始初始化通讯；
4.调用toStart函数开始渲染数字人；
5.根据自身需求是否调用文本驱动、音频驱动、文本问答，打断；
6. 调用toStop结束并释放数字人渲染。
```

### SDK引⽤（参照GJDigitalSDKDemo）

1. 将 GJDigitalSDK.framework和webrtc⽂件拖拽至工程的资源目录下:
工程-General-Frameworks,Libraries,and Embedded Content中的以下`framework`
```
GJDigitalSDK.framework
WebRTC.framework
```
改为```Embed & Sign```

2. Info.plist文件中添加录音权限和本地网络权限
```
<key>NSLocalNetworkUsageDescription</key>
<string>获取本地网络权限</string>
<key>NSMicrophoneUsageDescription</key>
<string>需要录音权限来语音识别</string>
```

------------------------------------------------------------
#### SDK关键接口
```
/**
 * SDK初始化
 *
 * - Parameters:
 *   - appId: appId
 *   - appKey: appkey
 *   - conversationId: 会话Id
 *   - block: 回调
 */
- (void)initWithAppId:(NSString *)appId appKey:(NSString *)appKey conversationId:(NSString *)conversationId block:(void (^)(BOOL isSuccee, NSString *errorMsg))block;

/**
 * 开始通讯
 */
- (void)toStart;

/**
 * 结束通讯
 */
- (void)toStop;

/**
 * 文本驱动数字人
 *
 * - Parameters:
 *   - text: 文本内容
 *   - interrupt: 是否打断前面的话
 */
- (void)commandEventWithText:(NSString *)text interrupt:(BOOL)interrupt;

/**
 * 音频驱动数字人
 *
 * - Parameters:
 *   - audioUrl: 音频地址
 *   - interrupt: 是否打断前面的话
 */
- (void)commandEventWithAudioUrl:(NSString *)audioUrl interrupt:(BOOL)interrupt;

/**
 * 文本问答
 *
 * - Parameters:
 *   - text: 文本内容
 *   - interrupt: 是否打断前面的话
 */
- (void)commandAskWithText:(NSString *)text interrupt:(BOOL)interrupt;

/**
 * 打断数字人播报
 */
- (void)toBreakDigital;

/**
 * 麦克风收音
 *
 * - Parameter isEnabled: YES表示收音，NO表示静音
 */
- (void)setMute:(BOOL)isEnabled;

/**
 * 数字人静音
 *
 * - Parameter enable: YES表示静音，NO表示收音
 */
- (void)toSpeakerMute:(BOOL)enable;

```

#### SDK代理
```
@protocol DigitalViewDelegate <NSObject>

/**
 * 加载数字人资源
 *
 * - Parameters:
 *   - isSuccess: 是否成功
 *   - progress: 加载进度
 */
- (void)onVideoShow:(BOOL)isSuccess progress:(float)progress;

/**
 * 异常回调
 *
 * - Parameters:
 *   - errorCode: 错误码
 *   - errorMsg: 错误信息
 */
- (void)onError:(NSInteger)errorCode errorMsg:(NSString *)errorMsg;

@optional

/**
 * ASR开始识别
 */
- (void)toWebrtcAsrStart;

/**
 * ASR识别
 *
 * - Parameters:
 *   - asrText: 识别的文本
 *   - isFinish: 是否结束
 */
- (void)toWebrtcAsrText:(NSString *)asrText isFinish:(BOOL)isFinish;

/**
 * 开始说话
 *
 * - Parameter dict: 开始说话的字典信息
 */
- (void)toSpeakStart:(NSDictionary *)dict;

/**
 * 结束说话
 *
 * - Parameter dict: 结束说话的字典信息
 */
- (void)toSpeakStop:(NSDictionary *)dict;

/**
 * 音频版开始说话
 *
 * - Parameter dict: 开始说话的字典信息
 */
- (void)toTTSSpeakStart:(NSDictionary *)dict;

/**
 * 音频版结束说话
 *
 * - Parameter dict: 结束说话的字典信息
 */
- (void)toTTSSpeakStop:(NSDictionary *)dict;

/**
 * 中控结束通话，发送bye事件
 */
- (void)onByeBye;

/**
 * 远程视频通讯完成
 */
- (void)onRTCReomteSuccess;

/**
 * 是否成功加载音频
 *
 * - Parameter isSuccess: 是否成功
 */
- (void)onAudioShow:(BOOL)isSuccess;

/**
 * webrtc连接状态回调
 *
 * - Parameter state: RTCIceConnectionState
 */
- (void)didIceConnectionChange:(RTCIceConnectionState)state;

/**
 * 中控获取到渲染端信息后通知客户端
 *
 * - Parameters:
 *   - ID: id
 *   - name: name
 */
- (void)onRender:(NSString *)ID name:(NSString *)name;

/**
 * 获取本地视频的视频流
 *
 * - Parameters:
 *   - capturer: capturer
 *   - frame: 视频帧
 */
- (void)capturer:(RTCVideoCapturer *)capturer didCaptureVideoFrame:(RTCVideoFrame *)frame;

/**
 * 获取远程音频流
 *
 * - Parameter sampleBuffer: Buffer
 */
- (void)onRemoteAudioBuffer:(CMSampleBufferRef)sampleBuffer;

@end
```
------------------------------------------------------------
#### 模块使⽤ 代码样例
详细⻅Demo终代码，:
```
/// 设置自定义View
[DigitalManager manager].remote_view = self.customView;

#pragma mark -SDK初始化
[[GJAccess manager] getCamerapermissions:^(bool isPermis) {
    if (isPermis)  {
        [[DigitalManager manager] initWithAppId:AppId appKey:AppKey conversationId:ConversationId block:^(BOOL isSuccee, NSString *errorMsg) {
            if (isSuccee) {
                [[DigitalManager manager] toStart];
            } else {
                NSLog(@"GJDigitalDemo==errorMsg==%@",errorMsg);
            }
        }];
    }
}];

#pragma mark -结束会话
- (void)toStop {

    self.digitalShow = NO;
    [[DigitalManager manager] toStop];
}

#pragma mark -SDK代理 DigitalViewDelegate
#pragma mark -显示视频是否加载完成
- (void)onVideoShow:(BOOL)isSuccess progress:(float)progress {
    if (isSuccess) {
        self.digitalShow = YES;
        NSLog(@"GJDigitalDemo==加载完成");
    } else {
        NSLog(@"GJDigitalDemo==加载中-%lf",progress);
    }
}

#pragma mark -异常场景错误信息
- (void)onError:(NSInteger)error_code errorMsg:(NSString *)errorMsg {

    if (error_code == -1 || error_code == -2) {
        //MQTT连接异常
    } else if (error_code == 50001) {
        //错误或者空的appId
    } else if (error_code == 50002) {
        //资源检查失败，请联系管理员
    } else if (error_code == 50003) {
        //资源占用中，请检查后再试~
    } else if (error_code == 50004) {
        //human请求超时~
    } else if (error_code == 50005) {
        //从资源组中获取资源异常
    } else if (error_code == 50006) {
        //签名失败
    } else if (error_code == 50007) {
        //资源总并发不足，请检查后再试~
    }  else if (error_code == 50009) {
        //资源超时或未配置~
    }
    NSLog(@"GJDigitalDemo==errorMsg==%@",errorMsg);
}

```

#### 常见错误
| 错误码 | 说明                                                      |
| ------ | --------------------------------------------------------- |
| -1     | MQTT连接异常                                             |
| -2     | 客户端接口请求http状态码异常。                                |
| -3     | 客户端获取DUIX资源异常                                      |
| -4     | 客户端MQTT连接失败                                         |
| -5     | 客户端WebRTC的SDP创建失败，一般为iceServer异常。             |
| -6     | 客户端WebRTC的SDP设置失败，一般为iceServer异常。            |
| -7     | 客户端会话交互异常，常见的有鉴权失败、ASR资源启动失败等。       |
| 1005   | 服务端token不能为空                                      |
| 1006   | 服务端toke失效                                            |
| 1007   | 服务端话术信息不存在                                      |
| 1009   | 服务端用户可用次数不足                                    |
| 2002   | 服务端会话信息不存在                                      |
| 40001  | 服务端appid或者appscrect无效                              |
| 40002  | 服务端内部错误                                            |
| 50001  | 错误或者空的appId                                         |
| 50002  | 资源检查失败，请联系管理员                                   |
| 50003  | 资源占用中，请检查后再试~                                   |
| 50004  | human请求超时~                                           |
| 50005  | 从资源组中获取资源异常                                      |
| 50006  | 签名失败                                                 |
| 50007  | 资源总并发不足，请检查后再试~                                |
| 50009  | 资源超时或未配置~                                          |
