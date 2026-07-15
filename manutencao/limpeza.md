## Limpeza profunda e liberação de espaço no Gentoo

Procedimentos para remover códigos-fonte, pacotes binários obsoletos, resíduos de compilação do kernel e *runtimes* abandonados.

### 1. Limpeza do cache do Portage (eclean)

Requer o pacote `app-portage/gentoolkit`.

Remove códigos-fonte (*tarballs*) obsoletos não referenciados por *ebuilds* instalados:

```bash
sudo eclean-dist --deep

```

> **Nota:** Comando inócuo caso a flag `DARKELF_FEATURES="postmerge_distclean"` esteja configurada no `make.conf`, pois o sistema já realiza a deleção automática.

Remove pacotes binários obsoletos, mantendo apenas os equivalentes às versões ativas (útil para quem gera binários com `FEATURES="buildpkg"`):

```bash
sudo eclean-pkg --deep

```

### 2. Limpeza de resíduos de compilação do kernel

Após compilar, instalar e inicializar o novo kernel, limpe os arquivos intermediários (objetos `.o`) e o binário não compactado (`vmlinux`). O comando preserva o código-fonte e o arquivo `.config`.

Acesse o diretório do kernel correspondente e execute a limpeza:

```bash
cd /usr/src/linux
sudo make clean

```

### 3. Remoção de runtimes órfãos do Flatpak

Varrer o sistema e purgar dependências (bibliotecas de ambiente) que não são mais exigidas por nenhuma aplicação em contêiner ativa:

```bash
flatpak uninstall --unused
