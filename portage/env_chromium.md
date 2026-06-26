
## Env para o Chromium

```bash
sudo mkdir -p /etc/portage/env

```

Limita os jobs a 8:

```bash
cat << 'EOF' | sudo tee /etc/portage/env/chromium
MAKEOPTS="-j8"
NINJAJOBS="8"
EOF

echo "www-client/chromium chromium" | sudo tee -a /etc/portage/package.env

```

Apesar disso, não foi necessário usar essa configuração para compilar o Chromium tendo 32GB de RAM, 26GB reservado para TMPFS e 16GB de swap, no AMD Ryzen 7 3700X. Levou cerca de 7h e 30min.
