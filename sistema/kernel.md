## Compilação do gentoo-kernel com savedconfig (Nome e CPU)

Otimize o `sys-kernel/gentoo-kernel` para a arquitetura nativa e altere o nome da *release*, mantendo a automação do `dist-kernel` (initramfs e GRUB).

### 1. Habilitar versão instável e USE flag (savedconfig)

```bash
sudo mkdir -p /etc/portage/package.accept_keywords /etc/portage/package.use
echo "sys-kernel/gentoo-kernel ~amd64" | sudo tee -a /etc/portage/package.accept_keywords/kernel
echo "sys-kernel/gentoo-kernel savedconfig" | sudo tee -a /etc/portage/package.use/kernel

```

### 2. Preparar código-fonte

Baixe e extraia os fontes sem compilar, acessando o diretório de trabalho temporário:

```bash
sudo ebuild $(equery w sys-kernel/gentoo-kernel) prepare
cd "$(ls -dt /var/tmp/portage/sys-kernel/gentoo-kernel-*/work/linux-* | head -n 1)"

```

### 3. Configurar o Kernel

```bash
sudo make menuconfig

```

Faça as seguintes alterações:

* **Nome:** Acesse `General setup` ---> `Local version - append to kernel release` e defina um sufixo (ex: `-custom`).
* **Otimização CPU:** Acesse `Processor type and features` ---> Marque `[*] Build and optimize for local/native CPU`.
* Salve e saia.

### 4. Exportar configuração e compilar

Mova o `.config` para o diretório oficial do `savedconfig` e inicie a instalação:

```bash
sudo mkdir -p /etc/portage/savedconfig/sys-kernel
sudo cp .config /etc/portage/savedconfig/sys-kernel/gentoo-kernel
sudo emerge --ask --verbose sys-kernel/gentoo-kernel

```

### 5. Configurar link simbólico

Atualize o link do sistema para o novo código-fonte:

```bash
sudo eselect kernel list
sudo eselect kernel set <NUMERO_DA_VERSAO>

```
