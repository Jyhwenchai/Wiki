# 视频捕获基础

## 关键类

* 捕获会话：AVCaptureSession
* 捕获设备：AVCaptureDevice
* 捕获设备输入：AVCaptureDeviceInput
* 捕获设备输出：AVCaptureOutput 基类
   *  AVCapturePhotoOutput
   *  AVCaptureMovieFileOutput
   *  AVCaptureVideoDataOutput
   *  AVCaptureAudioFileOutput
   *  AVCaptureAudioDataOutput
   *  AVCaptureFileOutput
* 捕获连接：AVCaptureConnectionb
* 捕获预览：AVCaptureVideoPreviewLayer


## 权限检查

在开始配置会话之前我们需要对其相关的权限进行检查，例如打开相册的权限、调用相机的权限、调用麦克风的权限、定位权限（做为捕获媒体d的元数据）等等。

```swift
AVCaptureDevice.authorizationStatus(for: .video)
```
    

## 配置 AVCaptureSession
* 在配置会话时必须以 `beginConfiguration()` 指示配置的开始，在配置完成后调用 `commitConfiguration()` 方法指示配置完成。

* 添加输入设备到会话 - 我们可以使用 `AVCaptureDevice` 获取有效的输入设备, 但是无法直接使用 `AVCaptureDevice` 为 `AVCaptureSession` 提供有效内容，在这个过程中还需要使用 `AVCaptureDeviceInput` 将 `AVCaptureDevice` 提供的内容转换为有效的媒体数据并将其输入到 `AVCaptureSession` 中。同时我们还需要用 `canAddInput()` 方法验证设备是否可用，因为设备可能被其它应用占用。

    ```swift
    let videoDevice = AVCaptureDevice.default(for: .video)
    let videoDeviceInput = try AVCaptureDeviceInput(device: videoDevice)
                
    // 检查是否可以添加选择的输入设备, 如果可以才添加
    if session.canAddInput(videoDeviceInput) {
        session.addInput(videoDeviceInput)
    }

    let audioDevice = AVCaptureDevice.default(for: .audio)
    let audioDeviceInput = try AVCaptureDeviceInput(device: audioDevice)
    if session.canAddInput(audioDeviceInput) {
        session.addInput(audioDeviceInput)
    }
    ```
* 添加输出设备到会话 - 除了需要配置输入设备，我们还需要配置期望的输出设备。输出设备的类型有很多，如照片文件、音频文件、视频文件等待。添加的代码与添加输入设备类似。
    
    ```swift
    let photoOutput = AVCapturePhotoOutput()
    if session.canAddOutput(photoOutput) {
        session.addOutput(photoOutput)
    }
    ```

## 切换前后置摄像头

## 聚焦和曝光功能

## 闪光灯和手电筒功能