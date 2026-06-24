
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
```
```bash
echo "www-client/chromium chromium" | sudo tee -a /etc/portage/package.env

```
