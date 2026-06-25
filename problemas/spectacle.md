## Gravação em vídeo do Spectacle

Caso o Spectacle não grave vídeo, isto pode resolver.

```bash
cat << 'EOF' | sudo tee /etc/portage/package.use/ffmpeg_vpx
media-video/ffmpeg vpx
EOF

sudo emerge -1v media-video/ffmpeg

```
