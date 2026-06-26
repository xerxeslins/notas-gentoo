
## Env para programas pesados tendo pouca RAM

```bash
sudo mkdir -p /etc/portage/env

```

Limita os jobs a 2, por exemplo, para o Chromium:

```bash
cat << 'EOF' | sudo tee /etc/portage/env/low-ram.conf > /dev/null
MAKEOPTS="-j2"
NINJAOPTS="-j2"
EOF

sudo mkdir -p /etc/portage/package.env
echo "www-client/chromium low-ram.conf" | sudo tee -a /etc/portage/package.env/jobs > /dev/null

```

Apesar disso, não foi necessário usar essa configuração para compilar o Chromium tendo 32GB de RAM, 26GB reservado para TMPFS e 16GB de swap, no AMD Ryzen 7 3700X. Levou cerca de 7h e 30min.
