## Sincronizando o repositório de ebuilds do Gentoo com Git

Instale a ferramenta de gerenciamento (o `git` é instalado automaticamente se a USE flag correspondente estiver ativa):

```bash
sudo emerge --ask app-eselect/eselect-repository

```

Remova o repositório rsync antigo (remova também a variável obsoleta `PORTDIR` do `make.conf`, caso exista):

```bash
sudo rm -rf /var/db/repos/gentoo

```

Adicione o espelho Git oficial e sincronize:

```bash
sudo eselect repository add gentoo git https://github.com/gentoo-mirror/gentoo
sudo emaint sync -r gentoo

```
