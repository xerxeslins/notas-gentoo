## Executar AppImages (Erro libfuse.so.2)

A maioria dos AppImages exige a versão 2 do FUSE (slot 0). Se o sistema possuir apenas a versão 3 (slot 3, padrão atual), ocorrerá o erro `dlopen(): error loading libfuse.so.2`.

Instale o pacote especificando o slot 0:

```bash
sudo emerge --ask sys-fs/fuse:0

```

Conceda permissão de execução e inicie o arquivo:

```bash
chmod +x pacote.AppImage
./pacote.AppImage

```
