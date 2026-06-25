## Compactação e descompactação

Instale os pacotes mais comuns:

```bash
sudo emerge --ask \
    app-arch/zip \
    app-arch/unzip \
    app-arch/p7zip \
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
