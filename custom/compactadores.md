## Compactação e descompactação

Instale os pacotes mais comuns:

```bash
sudo emerge --ask \
    app-arch/zip \
    app-arch/unzip \
    app-arch/7zip \
    app-arch/unrar \
    app-arch/rar \
    app-arch/lz4 \
    app-arch/zstd \
    app-arch/xz-utils \
    app-arch/lzip \
    app-arch/lha \
    app-arch/arj

```

### Ferramenta universal

Uma opção conveniente é instalar:

```bash
sudo emerge --ask app-arch/atool

```

Depois você pode usar:

```bash
aunpack arquivo.rar
aunpack arquivo.7z
aunpack arquivo.zip

```

Para listar conteúdo:

```bash
als arquivo.zip

```

## Suporte gráfico em gerenciadores de arquivos

Se usa KDE Plasma:

```bash
sudo emerge --ask kde-apps/ark

```

Se usa GNOME:

```bash
sudo emerge --ask app-arch/file-roller

```

Se usa XFCE:

```bash
sudo emerge --ask app-arch/xarchiver

```
