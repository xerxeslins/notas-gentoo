## Zram

Exemplo com 16GB:

```bash
sudo emerge --ask sys-block/zram-init

cat << 'EOF' | sudo tee /etc/conf.d/zram-init
num_devices=1
type0=swap
size0=16384
algo0=zstd
labl0=zram_swap
EOF

sudo rc-service zram-init stop
sudo modprobe -r zram
sudo rc-service zram-init start

```

Verificar:

```bash
sudo zramctl
sudo swapon --show

```

