## tmpfs na RAM

Isto cria um tmpfs de 26GB:

```bash
echo "tmpfs /var/tmp/portage tmpfs size=26G,uid=portage,gid=portage,mode=775,nosuid,noatime,nodev 0 0" | sudo tee -a /etc/fstab
sudo rm -rf /var/tmp/portage/*
sudo mount /var/tmp/portage

```

Verifique:

```bash
df -h /var/tmp/portage

```
