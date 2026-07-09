## Compilação do gentoo-kernel (Otimização e Prevenção de Erros)

Procedimento para compilar `sys-kernel/gentoo-kernel` otimizado para CPU nativa, com nome personalizado e correção de erro de compilação BTF (comum no `amdgpu`), mantendo a automação do `dist-kernel` (initramfs e GRUB).

### 1. Habilitar versão instável e USE flag (savedconfig)

```bash
sudo mkdir -p /etc/portage/package.accept_keywords /etc/portage/package.use

echo "sys-kernel/gentoo-kernel ~amd64" | sudo tee /etc/portage/package.accept_keywords/kernel

cat << 'EOF' | sudo tee /etc/portage/package.use/kernel
sys-kernel/gentoo-kernel savedconfig
EOF

```

### 2. Preparar código-fonte

```bash
sudo ebuild $(equery w sys-kernel/gentoo-kernel) prepare
cd "$(ls -dt /var/tmp/portage/sys-kernel/gentoo-kernel-*/work/linux-* | head -n 1)"

```

### 3. Configurar o Kernel

```bash
sudo make menuconfig

```

Realize estritamente as três alterações abaixo:

* **Nome customizado:** Vá em `General setup` ---> `Local version - append to kernel release` e insira um sufixo (ex: `-custom`).
* **Otimização de CPU:** Vá em `Processor type and features` ---> Marque `[*] Build and optimize for local/native CPU`.
* **Desabilitar BTF (Prevenção de erro em módulos grandes):** Vá em `Kernel hacking` ---> `Compile-time checks and compiler options` ---> Desmarque `[ ] Generate BTF typeinfo`.
* Salve e saia.

### 4. Exportar configuração

```bash
sudo mkdir -p /etc/portage/savedconfig/sys-kernel
sudo cp .config /etc/portage/savedconfig/sys-kernel/gentoo-kernel

```

### 5. Compilação e Instalação

```bash
cd /
sudo emerge --ask --verbose sys-kernel/gentoo-kernel

```

### 6. Configurar link simbólico

```bash
sudo eselect kernel list
sudo eselect kernel set <NUMERO_DA_VERSAO>

```
