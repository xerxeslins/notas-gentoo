## Instalar DOOM Retro no Gentoo (xerxes-overlay)

### 1. Pré-requisitos (Keywords e USE flags)

```bash
sudo mkdir -p /etc/portage/package.accept_keywords /etc/portage/package.use

echo "games-fps/doomretro ~amd64" | sudo tee -a /etc/portage/package.accept_keywords/doomretro

echo "media-libs/libsdl2 opengl" | sudo tee -a /etc/portage/package.use/doomretro
echo "media-libs/sdl2-image png jpeg" | sudo tee -a /etc/portage/package.use/doomretro
echo "media-libs/sdl2-mixer stb mod vorbis flac mp3 modplug midi timidity" | sudo tee -a /etc/portage/package.use/doomretro
echo "media-libs/libglvnd X" | sudo tee -a /etc/portage/package.use/doomretro

```

### 2. Instalar última versão (Release)

```bash
sudo eselect repository enable xerxes-overlay
sudo emaint sync -r xerxes-overlay
sudo emerge --ask --verbose games-fps/doomretro

```

### 3. Instalar versão Live (Git commit mais recente)

```bash
echo "=games-fps/doomretro-9999 **" | sudo tee -a /etc/portage/package.accept_keywords/doomretro
sudo emerge --ask --verbose =games-fps/doomretro-9999

```
