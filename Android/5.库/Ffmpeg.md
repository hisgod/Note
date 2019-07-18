# 命令

## 音频

> pcm转wav

```
格式：ffmpeg -f 数据格式 -ar 采样率 -ac 通道数 -i input.pcm output.wav

案例：ffmpeg -f s16le -ar 16000 -ac 1 -i input.pcm output.wav
```

> wav转pcm

```
ffmpeg -i input.wav -f s16le ouput.pcm
```

> 设置音频采样率

```

```

