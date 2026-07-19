## Remover Kernels antigos no Gentoo

Identifique os kernels instalados e o kernel em execução. Nunca remova o kernel ativo.

```bash
eselect kernel list
uname -r

```

Remova as versões antigas (exemplo: família `7.1.3`) do banco de dados do Portage:

```bash
sudo emerge -C =sys-kernel/gentoo-kernel-bin-7.1.3*
sudo emerge -C =sys-kernel/gentoo-kernel-7.1.3*

```

Remova manualmente os resíduos de código-fonte, módulos compilados e arquivos de inicialização (`vmlinuz`, `initramfs`, `System.map`, `config`):

```bash
sudo rm -rf /usr/src/linux-7.1.3-custom
sudo rm -rf /usr/src/linux-7.1.3-gentoo-dist-bin

sudo rm -rf /lib/modules/7.1.3-custom
sudo rm -rf /lib/modules/7.1.3-gentoo-dist-bin

sudo rm -f /boot/*7.1.3-custom*
sudo rm -f /boot/*7.1.3-gentoo-dist-bin*

```

Atualize o GRUB para remover as entradas órfãs do menu:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg

```
