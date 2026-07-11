## Congelar versões de pacotes pesados (package.mask)

Evita compilações longas (kernel, navegadores) durante atualizações de rotina (`emerge -uavDN @world`). Bloquear via `/etc/portage/package.mask/` mantém o pacote protegido no *world set*, evitando sua remoção acidental pelo `emerge -c`.

### 1. Bloquear atualizações (Exemplos)

O operador `>` bloqueia qualquer versão estritamente maior que a especificada.

```bash
sudo mkdir -p /etc/portage/package.mask

# Congelar o kernel na versão 7.1.3 (bloqueia 7.1.4 e superiores)
echo ">sys-kernel/gentoo-kernel-7.1.3" | sudo tee /etc/portage/package.mask/kernel

# Congelar o Chromium na versão 149.0.7827.200
echo ">www-client/chromium-149.0.7827.200" | sudo tee /etc/portage/package.mask/chromium

```

### 2. Atualizar manualmente para nova versão (Sobrescrever regra)

Para compilar uma nova versão específica quando desejar (ex: atualizar Chromium para `150.0.0.0`), sobrescreva o arquivo da máscara e execute a instalação:

```bash
echo ">www-client/chromium-150.0.0.0" | sudo tee /etc/portage/package.mask/chromium
sudo emerge --ask --verbose www-client/chromium

```

### 3. Remover bloqueio (Descongelar)

Para que o pacote volte a atualizar automaticamente com o resto do sistema, apague o arquivo de máscara correspondente:

```bash
sudo rm /etc/portage/package.mask/kernel
sudo rm /etc/portage/package.mask/chromium

```
