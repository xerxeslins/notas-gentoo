## Zram

Exemplo com 16GB:

```bash
emerge --ask sys-block/zram-init

cat << 'EOF' > /etc/conf.d/zram-init
num_devices=1
type0=swap
size0=16384
algo0=zstd
labl0=zram_swap
EOF

rc-service zram-init stop
modprobe -r zram
rc-service zram-init start

```

Verificar:

```bash
zramctl
swapon --show

```


## Swapfile (se quiser)

```bash
fallocate -l 16G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo "/swapfile none swap sw 0 0" >> /etc/fstab

```

