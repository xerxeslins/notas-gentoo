## Compilação do gentoo-kernel (Otimização e Prevenção de Erros)

Obs.: se estiver usando TMPFS, talvez seja o caso de desativá-la temporariamente para não sofrer com falta de memória:

```bash
sudo fuser -k -m /var/tmp/portage
sudo umount /var/tmp/portage
sudo rm -rf /var/tmp/portage/*

```

Procedimento para compilar `sys-kernel/gentoo-kernel` otimizado para CPU nativa, com nome personalizado e correção de erro de compilação BTF (comum no amdgpu), mantendo a automação do dist-kernel (initramfs e GRUB).

### 1. Desmascarar, habilitar versão instável e USE flag (savedconfig)

Verifique a versão com `eix gentoo-kernel`. Se a versão desejada estiver precedida por `[m]` (conforme exibido na image_518cf7.png), ela está mascarada (`hard-masked`). É necessário adicionar uma entrada no diretório `package.unmask` para liberá-la.

Execute o bloco de comandos para criar os diretórios necessários, desmascarar o pacote, aceitar a ramificação instável e definir a flag `savedconfig`:

```bash
sudo mkdir -p /etc/portage/package.accept_keywords /etc/portage/package.use /etc/portage/package.unmask

echo "sys-kernel/gentoo-kernel" | sudo tee /etc/portage/package.unmask/kernel
echo "sys-kernel/gentoo-kernel ~amd64" | sudo tee /etc/portage/package.accept_keywords/kernel

cat << 'EOF' | sudo tee /etc/portage/package.use/kernel
sys-kernel/gentoo-kernel savedconfig
EOF

```

### 2. Preparar código-fonte

```bash
sudo ebuild $(equery w sys-kernel/gentoo-kernel) prepare
sudo su
cd /var/tmp/portage/sys-kernel/gentoo-kernel-[VERSAO.RELEASE]/work/linux-[VERSAO]

```

### 3. Configurar o Kernel

```bash
make menuconfig

```

Realize estritamente as três alterações abaixo:

* **Nome customizado:** Vá em `General setup` ---> `Local version - append to kernel release` e insira um sufixo (ex: `-custom`).
* **Otimização de CPU:** Vá em `Processor type and features` ---> Marque `[*] Build and optimize for local/native CPU`.
* **Desabilitar BTF (Prevenção de erro em módulos grandes):** Vá em `Kernel hacking` ---> `Compile-time checks and compiler options` ---> Desmarque `[ ] Generate BTF typeinfo`.
* Salve e saia.

### 4. Exportar configuração

```bash
mkdir -p /etc/portage/savedconfig/sys-kernel
cp .config /etc/portage/savedconfig/sys-kernel/gentoo-kernel

```

### 5. Compilação e Instalação

```bash
cd /
emerge --ask --verbose sys-kernel/gentoo-kernel

```

### 6. Configurar link simbólico

```bash
eselect kernel list
eselect kernel set <NUMERO_DA_VERSAO>

```
