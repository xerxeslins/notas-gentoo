## Plugins multimídia e codecs

```bash
echo "media-video/ffmpeg X mp3 opus x264 x265 webp vpx" | sudo tee -a /etc/portage/package.use/ffmpeg
sudo emerge --ask media-video/ffmpeg
```

## Suporte a miniaturas de arquivos compactados e mídia

```bash
sudo emerge --ask media-gfx/imagemagick media-video/ffmpegthumbnailer
```
