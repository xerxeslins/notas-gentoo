## Atualizar gentoo-kernel-bin para última versão (Gentoo Estável)

1. Libere as keywords de teste para o pacote e sua dependência virtual:

```bash
sudo mkdir -p /etc/portage/package.accept_keywords
echo "sys-kernel/gentoo-kernel-bin ~amd64" | sudo tee -a /etc/portage/package.accept_keywords/gentoo-kernel-bin
echo "virtual/dist-kernel ~amd64" | sudo tee -a /etc/portage/package.accept_keywords/gentoo-kernel-bin

```

2. Sincronize a árvore do Portage:

```bash
sudo emaint sync -r gentoo

```

3. Identifique a versão mais recente disponível:

```bash
eix sys-kernel/gentoo-kernel-bin

```

4. Instale forçando a versão exata (substitua `X.Y.Z` pela versão encontrada no comando anterior):

```bash
sudo emerge --ask --oneshot =sys-kernel/gentoo-kernel-bin-X.Y.Z

```

5. Selecione o novo kernel:

```bash
sudo eselect kernel list
sudo eselect kernel set <NUMERO_DA_VERSAO_NOVA>

```

6. Force a instalação dos arquivos no `/boot` e atualize o GRUB:

```bash
sudo emerge --config =sys-kernel/gentoo-kernel-bin-X.Y.Z
sudo grub-mkconfig -o /boot/grub/grub.cfg

```


