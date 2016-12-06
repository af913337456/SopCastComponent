## SopCastComponentSDK
Welcome to SopCastComponentSDK, a good component for Android SopCast.
![sopcast](sopcast.png)

### Features
>* Support Hardware Encoding
>* Drop frames on bad network
>* Dynamic switching rate
>* Camera configuration
>* Audio configuration
>* Video Configuration
>* Support multiple window size
>* Support video effect With GPU
>* Support audio muting
>* Support rtmp transporting
>* Support watermark
>* Support screen recording
>* Support camera switching
>* Support flv package
>* Support camera auto focus and touch focus mode
>* Support torch operation
>* Support audio aec

### Requirements

>* Camera preview and operate: Android sdk version 14+
>* Camera living: Android sdk version 18+
>* Device must have a camera

### How to Use
#### 1. Camera Configuration

```
CameraConfiguration.Builder cameraBuilder = new CameraConfiguration.Builder();
cameraBuilder.setOrientation(CameraConfiguration.Orientation.LANDSCAPE)
        .setFacing(CameraConfiguration.Facing.BACK).setPreview(720, 1280)
        .setFps(24).setFocusMode(CameraConfiguration.FocusMode.TOUCH);
CameraConfiguration cameraConfiguration = cameraBuilder.build();
mLFLiveView.setCameraConfiguration(cameraConfiguration);
```
You can add these code in activity onCreate method, and the camera will be open as you set.
If you use this view in a landscape activity, you must set the orientation to landscape.
You can set a preview size to this view, the camera will find the nearest size and use it.
If the preview ratio doesn't fit the view ratio, it will choose the center part of the preview and display it.
If you use the default setting, you needn't to set it again.

The default settings:
```
public static final int DEFAULT_HEIGHT = 1280;
public static final int DEFAULT_WIDTH = 720;
public static final int DEFAULT_FPS = 24;
public static final Facing DEFAULT_FACING = Facing.FRONT;
public static final Orientation DEFAULT_ORIENTATION = Orientation.PORTRAIT;
public static final FocusMode DEFAULT_FOCUSMODE = FocusMode.AUTO;
```

#### 2. Video Configuration
```
VideoConfiguration.Builder videoBuilder = new VideoConfiguration.Builder();
videoBuilder.setSize(640, 360).setMime(DEFAULT_MIME)
        .setFps(24).setBps(300, 800).setIfi(2);
mVideoConfiguration = videoBuilder.build();
mLFLiveView.setVideoConfiguration(mVideoConfiguration);
```
You can add these code before living. You can set the video output size by 'setSize()'
method, if the ratio doesn't fit the view ratio, it will choose the center part of the view
and output it. You can set the min and max bps before living, at first it will use the
max bps as the current bps. The mime will be set to the video mediacodec, so you need to set
a correct mime. The ifi will be set to the video mediacodec too. If you use the default setting,
you needn't to set it again.

The default settings:
```
public static final int DEFAULT_HEIGHT = 640;
public static final int DEFAULT_WIDTH = 360;
public static final int DEFAULT_FPS = 24;
public static final int DEFAULT_MAX_BPS = 1300;
public static final int DEFAULT_MIN_BPS = 400;
public static final int DEFAULT_IFI = 2;
public static final String DEFAULT_MIME = "video/avc";
```

#### 3. Audio Configuration
```
AudioConfiguration.Builder audioBuilder = new AudioConfiguration.Builder();
audioBuilder.setAec(true).setBps(32, 64).setFrequency(48000).setMime(DEFAULT_MIME).
        setAacProfile(DEFAULT_AAC_PROFILE).setAdts(DEFAULT_ADTS).
        setChannelCount(1).setEncoding(DEFAULT_AUDIO_ENCODING);
AudioConfiguration audioConfiguration = audioBuilder.build();
mLFLiveView.setAudioConfiguration(audioConfiguration);
```
You can open the echo cancellation module by the method 'setAec(true)',  echo cancellation module
just work in (8000, 16000, 48000) frequency and one channel count. The mime will be set to the
video mediacodec, so you need to set a correct one. If you use the default setting, you needn't
to set it again.

The default settings:
```
public static final int DEFAULT_FREQUENCY = 44100;
public static final int DEFAULT_MAX_BPS = 64;
public static final int DEFAULT_MIN_BPS = 32;
public static final int DEFAULT_ADTS = 0;
public static final String DEFAULT_MIME = "audio/mp4a-latm";
public static final int DEFAULT_AUDIO_ENCODING = AudioFormat.ENCODING_PCM_16BIT;
public static final int DEFAULT_AAC_PROFILE = MediaCodecInfo.CodecProfileLevel.AACObjectLC;
public static final int DEFAULT_CHANNEL_COUNT = 1;
public static final boolean DEFAULT_AEC = false;
```

#### 4. Packer
```
RtmpPacker packer = new RtmpPacker();
packer.initAudioParams(AudioConfiguration.DEFAULT_FREQUENCY, 16, false);
mLFLiveView.setPacker(packer);
```
We provide flv packer and rtmp packer in this component, you also can define your packer too.
The packer must implement the 'Packer' interface. The packer can pack up the video and audio data,
the packed data will be send to the sender.

#### 5. Sender
```
String url = "rtmp://[host]:1935/[app]/[stream]";
mRtmpSender = new RtmpSender(url);
mRtmpSender.setVideoParams(640, 360);
mRtmpSender.setAudioParams(AudioConfiguration.DEFAULT_FREQUENCY, 16, false);
mRtmpSender.setSenderListener(mSenderListener);
mLFLiveView.setSender(mRtmpSender);
```
We provide local and rtmp sender in this component, you also can define your packer too.
The sender must implement the 'Sender' interface.

#### 6. Effect
We provide Null and Gray video effect, you also can define your video effect.
```
mLFLiveView.setEffect(mGrayEffect);
```
#### 7. Watermark
It's easy to set a watermark to the video, and the preview and the output video will
display the watermark. Please set the watermark length-width ratio equals the
bitmap length-width ratio.
```
Bitmap watermarkImg = BitmapFactory.decodeResource(getResources(), R.mipmap.watermark);
Watermark watermark = new Watermark(watermarkImg, 50, 25, WatermarkPosition.WATERMARK_ORIENTATION_BOTTOM_RIGHT, 8, 8);
mLFLiveView.setWatermark(watermark);
```

#### 8. CameraListener
You can set a camera listener to this view, then you can get the camera feedback.
```
//设置预览监听
mLFLiveView.setCameraOpenListener(new CameraListener() {
    @Override
    public void onOpenSuccess() {
        Toast.makeText(LandscapeActivity.this, "Camera open success", Toast.LENGTH_LONG).show();
    }

    @Override
    public void onOpenFail(int error) {
        Toast.makeText(LandscapeActivity.this, "Camera open fail", Toast.LENGTH_LONG).show();
    }

    @Override
    public void onCameraChange() {
        Toast.makeText(LandscapeActivity.this, "Camera switch", Toast.LENGTH_LONG).show();
    }
});
```

#### 9. LivingStartListener
You can set a living start listener to this view, then you can get the starting feedback.
```
mLFLiveView.setLivingStartListener(new CameraLivingView.LivingStartListener() {
    @Override
    public void startError(int error) {
        Toast.makeText(LandscapeActivity.this, "Start fail", Toast.LENGTH_SHORT).show();
        mLFLiveView.stop();
    }

    @Override
    public void startSuccess() {
        Toast.makeText(LandscapeActivity.this, "Start success", Toast.LENGTH_SHORT).show();
    }
});
```
The errors:
```
public static final int NO_ERROR = 0;
public static final int VIDEO_TYPE_ERROR = 1;
public static final int AUDIO_TYPE_ERROR = 2;
public static final int VIDEO_CONFIGURATION_ERROR = 3;
public static final int AUDIO_CONFIGURATION_ERROR = 4;
public static final int CAMERA_ERROR = 5;
public static final int AUDIO_ERROR = 6;
public static final int AUDIO_AEC_ERROR = 7;
public static final int SDK_VERSION_ERROR = 8;
```









