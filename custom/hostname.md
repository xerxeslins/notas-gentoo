## Alterar nome da máquina (OpenRC)

Altera o *hostname* atual, define a configuração permanente, habilita o serviço na inicialização e recarrega o *shell*. Substitua `gentoo` pelo nome desejado.

```bash
sudo hostname gentoo

sudo tee /etc/hostname > /dev/null << 'EOF'
gentoo
EOF

sudo tee /etc/conf.d/hostname > /dev/null << 'EOF'
hostname="gentoo"
EOF

sudo sed -i '/127\.0\.0\.1/ s/localhost/gentoo localhost/' /etc/hosts
sudo sed -i '/::1/ s/localhost/gentoo localhost/' /etc/hosts

sudo rc-update add hostname boot
sudo /etc/init.d/hostname restart
exec bash
```
