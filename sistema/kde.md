## Instalar última versão do KDE Plasma (Gentoo Estável)

Requisito: KDE já instalado. Procedimento para desmascarar categorias e obter a versão *testing* na base estável.

### 1. Desmascarar categorias principais

```bash
sudo mkdir -p /etc/portage/package.accept_keywords
echo -e "kde-plasma/* ~amd64\nkde-apps/* ~amd64\nkde-frameworks/* ~amd64" | sudo tee /etc/portage/package.accept_keywords/kde

```

### 2. Gerar e gravar árvore de dependências

```bash
sudo emerge -vaquDN --autounmask-write @world

```

### 3. Aplicar regras geradas

```bash
sudo etc-update

```

### 4. Compilar e instalar

```bash
sudo emerge -vaquDN @world

```
