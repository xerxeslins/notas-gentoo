## Steam (Flatpak) com jogos em partição NTFS

Solução para problemas de permissão, "Disk Write Error" e jogos do Proton que não iniciam em partições NTFS devido a caracteres incompatíveis.

### 1. Identificar UUID e UID

Descubra o UUID da partição NTFS e o seu UID/GID (geralmente 1000):

```bash
lsblk -f
id

```

### 2. Montar disco automaticamente (fstab)

Crie o ponto de montagem:

```bash
sudo mkdir -p /mnt/meusjogos

```

Adicione a entrada no `/etc/fstab` (substitua `SEU_UUID` pelo UUID obtido no passo 1):

```bash
echo "UUID=SEU_UUID  /mnt/meusjogos  ntfs-3g  defaults,uid=1000,gid=1000,umask=000,exec  0  0" | sudo tee -a /etc/fstab

```

Teste a montagem (obrigatório antes de reiniciar):

```bash
sudo mount -a

```

### 3. Permissão no Flatpak

Crie o diretório da biblioteca e conceda acesso ao sandbox do Flatpak:

```bash
mkdir -p /mnt/meusjogos/SteamLibrary
sudo flatpak override com.valvesoftware.Steam --filesystem=/mnt/meusjogos/SteamLibrary

```

### 4. Correção para o Proton (compatdata)

Redirecione o diretório `compatdata` (onde os prefixos do Windows são criados) para a sua partição Linux nativa via link simbólico, contornando as limitações do NTFS:

```bash
mkdir -p ~/.var/app/com.valvesoftware.Steam/.local/share/Steam/steamapps/compatdata_ntfs
mkdir -p /mnt/meusjogos/SteamLibrary/steamapps
ln -s ~/.var/app/com.valvesoftware.Steam/.local/share/Steam/steamapps/compatdata_ntfs /mnt/meusjogos/SteamLibrary/steamapps/compatdata

```

### 5. Configurar na Steam

1. Reinicie a Steam completamente.
2. Vá em **Configurações > Armazenamento**.
3. Adicione a pasta `/mnt/meusjogos/SteamLibrary`.

```

