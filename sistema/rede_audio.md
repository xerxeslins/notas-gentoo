## Trocar backend do NetworkManager pelo iwd

```bash
echo "net-misc/networkmanager iwd -wext" | sudo tee /etc/portage/package.use/networkmanager
sudo emerge --ask --changed-use net-misc/networkmanager

sudo emerge --ask net-wireless/iwd

cat << 'EOF' | sudo tee /etc/NetworkManager/conf.d/iwd.conf
[device]
wifi.backend=iwd
EOF

sudo rc-service wpa_supplicant stop
sudo rc-update del wpa_supplicant default

sudo rc-update add iwd default
sudo rc-service iwd start
sudo rc-service NetworkManager restart
```
