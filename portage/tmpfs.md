## tmpfs na RAM

```bash
echo "tmpfs /var/tmp/portage tmpfs size=26G,uid=portage,gid=portage,mode=775,nosuid,noatime,nodev 0 0" >> /etc/fstab
```
Isso cria um tmpfs de 26 GB.

```bash
rm -rf /var/tmp/portage/*
```
```bash
mount /var/tmp/portage

```
Verifique:

```bash
df -h /var/tmp/portage

```
