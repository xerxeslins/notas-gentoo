## Manutenção

1. Sincronizar os repositórios (opcional):

```bash
sudo emaint sync -a

```

2. Atualizar o sistema e aplicar novas USE flags/make.conf:

```bash
sudo emerge -avuDN @world

```

3. Remover dependências órfãs:

```bash
sudo emerge -av --depclean

```

4. Recompilar pacotes vinculados a bibliotecas antigas:

```bash
sudo emerge -av @preserved-rebuild

```

5. Mesclar atualizações de arquivos de configuração:

```bash
sudo dispatch-conf

```
