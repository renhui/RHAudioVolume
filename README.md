# RHAudioVolume
Android 实时获取音量
一、实时音量相关基础知识
说到获取音量，大家首先想到的应该就是分贝（dB），分贝是一个相对单位（是一个比值，是一个数值，是一个纯计数方法）。


二、Android 获取实时音量
获取音量之前，我们必须先在AndroidManifest.xml文件里面申请相应的权限：
```
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

这样我们才能正常继续后面的事情。

在Android SDK提供的API中，我们能获取到音频方式有两个：android.media.MediaRecorder 和 android.media.AudioRecord。

1. MediaRecorder
MediaRecorder 是用来录制一段完整的音视频并写入到文件系统中的API。通过它，我们能很简单的通过它的无参方法getMaxAmplitude来获取一小段时间内音频源数据中的最大振幅，此方法是很多录音软件计算音量等级所采用的办法。（注：因为是取最大值，所以存在受到极端数据的影响而导致计算的分贝波动值较大的问题）。

使用MediaRecorder.getMaxAmplitude返回的是0到32767范围的16位整型。如果设置参考振幅为1的话，那么计算出来的分贝值域的正常范围应该为 0dB 到 90.3dB。


2. AudioRecord
此API相对MediaRecorder来说更偏底层一点，我们可以使用AudioRecord获得具体的音频数据。

音源数据通过read(byte[] audioData, int offsetInBytes, int sizeInBytes)方法从缓冲区读取到我们传入的字节数组audioData后，我们便可以对其进行操作，如求平方和或绝对值的平均值。这样可以避免个别极端值的影响，使计算的结果更加稳定。求得平均值之后，如果是平方和则代入常数系数为10的公式中，如果是绝对值的则代入常数系数为20的公式中，算出分贝值。
