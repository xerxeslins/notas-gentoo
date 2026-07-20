## Instalação do Gentoo (amd64, OpenRC, KDE Plasma, UEFI, Ext4)

Guia de instalação direta a partir da Live GUI USB do Gentoo (ambiente Plasma).

### 1. Preparação Inicial no Live USB

Conecte-se à rede, defina a senha de root e ajuste o relógio:

```bash
sudo su
passwd
chronyd -q

```

### 2. Particionamento e Formatação (Exemplo M.2 NVMe)

Atenção: Os comandos abaixo destroem os dados do disco `/dev/nvme0n1`.

```bash
parted -a optimal /dev/nvme0n1
```
```text
mklabel gpt
unit mib
mkpart ESP 1 1024
set 1 boot on
mkpart Swap 1024 5120
mkpart Root 5120 -1
q
```
```bash
mkfs.vfat -F 32 /dev/nvme0n1p1
mkswap /dev/nvme0n1p2 && swapon /dev/nvme0n1p2
mkfs.ext4 /dev/nvme0n1p3

```

*(Para discos SATA, substitua `/dev/nvme0n1` por `/dev/sda` e as partições por `sda1`, `sda2`, `sda3`).*

### 3. Montagem e Stage3 (OpenRC)

```bash
mkdir -p /mnt/gentoo/efi
mount /dev/nvme0n1p3 /mnt/gentoo
mkdir -p /mnt/gentoo/efi
mount /dev/nvme0n1p1 /mnt/gentoo/efi

cd /mnt/gentoo
# Copie o link do stage3 (desktop profile | openrc) do site oficial e baixe:
wget -c https://distfiles.gentoo.org/releases/amd64/autobuilds/current-stage3-amd64-openrc/stage3-amd64-openrc-<DATA>.tar.xz
tar xpvf stage3* --xattrs-include='*.*' --numeric-owner

```

### 4. Chroot

```bash
cp -L /etc/resolv.conf etc/
arch-chroot .

```

### 5. Configuração do Portage (`make.conf`)

Verifique o número de threads lógicas com `nproc`. Edite o arquivo:

```bash
nano /etc/portage/make.conf

```

Garanta pelo menos estas configurações (ajuste `-j`, `-l` e `VIDEO_CARDS` para o seu hardware):

```text
COMMON_FLAGS="-O2 -pipe -march=native"
ACCEPT_LICENSE="*"
MAKEOPTS="-j16 -l16"

# Drivers de vídeo (descomente/ajuste o seu):
# AMD: "amdgpu radeonsi"
# Intel (Broadwell/Gen8+): "intel"
# Intel (Legado/Antigas): "intel i965"
# NVIDIA: "nvidia" (proprietário) ou "nouveau" (open-source)
VIDEO_CARDS="amdgpu radeonsi"

```

Em seguida, execute:

```bash
emerge-webrsync
emerge --ask app-portage/cpuid2cpuflags
echo "*/* $(cpuid2cpuflags)" | tee /etc/portage/package.use/00cpu-flags

```

### 6. Contas e Sistema Base

```bash
passwd
useradd -g users -G wheel,portage,audio,video,usb,cdrom -m xerxes
passwd xerxes

ln -sf /usr/share/zoneinfo/America/Recife /etc/localtime

```

Edite o arquivo de localidades e descomente (remova o `#`) das linhas `en_US` e `pt_BR`:

```bash
nano /etc/locale.gen

```

Gere e selecione a localidade:

```bash
locale-gen
eselect locale set $(eselect locale list | grep "pt_BR" | awk '{print $1}' | tr -d '[]')
env-update && source /etc/profile

echo gentoo > /etc/hostname

```

Edite o arquivo de mapeamento de hosts e adicione o nome do host escolhido:

```bash
nano /etc/hosts

```

Adicione as linhas correspondentes ao seu host:

```text
127.0.0.1 gentoo.home gentoo localhost
::1       gentoo.home gentoo localhost

```

Abra o arquivo de configuração de teclado e defina o layout do seu console manualmente na variável `keymap`:

```bash
nano /etc/conf.d/keymaps

```

Defina o campo correspondente:

```text
keymap="br-abnt2"

```

Gere o ID da máquina:

```bash
dbus-uuidgen --ensure=/etc/machine-id

```

### 7. Tabela de Partições (`fstab`)

```bash
echo -e "PARTLABEL=ESP /efi vfat noatime,umask=0077,tz=UTC 0 2\nPARTLABEL=Root / ext4 noatime 0 1\nPARTLABEL=Swap none swap sw 0 0\n/dev/cdrom /mnt/cdrom auto noauto,user,ro 0 0" | tee /etc/fstab

```

### 8. Firmware, Kernel e Bootloader (GRUB)

```bash
mkdir -p /etc/portage/package.license /etc/portage/package.use

echo "sys-kernel/linux-firmware linux-fw-redistributable" | tee -a /etc/portage/package.license/linux-firmware
emerge --ask sys-kernel/linux-firmware

# Se usar CPU Intel:
# echo "sys-firmware/intel-microcode intel-ucode" | tee -a /etc/portage/package.license/intel-microcode
# emerge --ask sys-firmware/intel-microcode

echo "sys-kernel/installkernel ugrd" | tee -a /etc/portage/package.use/installkernel
echo 'USE="${USE} dist-kernel"' | tee -a /etc/portage/make.conf
emerge --ask sys-kernel/installkernel sys-kernel/gentoo-kernel-bin

echo 'GRUB_PLATFORMS="efi-64"' | tee -a /etc/portage/make.conf
emerge --ask sys-boot/grub
grub-install --efi-directory=/efi
grub-mkconfig -o /boot/grub/grub.cfg

```

### 9. Serviços de Rede, Logs e Ferramentas

```bash
emerge --ask net-misc/networkmanager
rc-update add NetworkManager default

echo "app-admin/sysklogd logrotate" | tee -a /etc/portage/package.use/sysklogd
emerge --ask app-admin/sysklogd net-misc/chrony
rc-update add sysklogd default
rc-update add chronyd default
rc-update add cronie default

emerge --ask app-portage/gentoolkit app-portage/eix media-sound/alsa-utils sys-boot/efibootmgr app-text/wgetpaste sys-apps/cpuid sys-apps/ethtool sys-apps/usbutils sys-process/lsof sys-process/btop

```

### 10. Interface Gráfica (KDE Plasma)

```bash
eselect profile list
# Defina X para o número do perfil correspondente a: default/linux/amd64/<versao>/desktop/plasma/openrc
eselect profile set X

emerge -uUDv @world
emerge -c

emerge -vD xorg-server plasma-meta kdecore-meta
# Para instalação completa, substitua kdecore-meta por: kde-apps-meta

```

Edite o arquivo abaixo e altere a variável `DISPLAYMANAGER` colocando `"sddm"`:

```bash
nano /etc/conf.d/display-manager

```

Em seguida:

```bash
rc-update add elogind boot
rc-update add display-manager default

```

### 11. Finalização

```bash
exit
cd /
umount -Rl /mnt/gentoo
reboot

```
