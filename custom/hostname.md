## Alterar nome da máquina (OpenRC)

Altera o *hostname* atual, define a configuração permanente, habilita o serviço na inicialização e recarrega o *shell*. Substitua `gentoo` pelo nome desejado.

```bash
sudo hostname gentoo

sudo tee /etc/conf.d/hostname > /dev/null << 'EOF'
hostname="gentoo"
EOF

sudo rc-update add hostname boot
sudo /etc/init.d/hostname restart
exec bash

```
