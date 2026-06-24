## Trocar backend do NetworkManager pelo iwd

```bash
su
```
```bash
echo "net-misc/networkmanager iwd -wext" | tee /etc/portage/package.use/networkmanager
```
```bash
emerge --ask --changed-use net-misc/networkmanager
```
```bash
emerge --ask net-wireless/iwd
```
```bash
cat << 'EOF' > /etc/NetworkManager/conf.d/iwd.conf
[device]
wifi.backend=iwd
EOF
```
```bash
rc-service wpa_supplicant stop
```
```bash
rc-update del wpa_supplicant default
```
```bash
rc-update add iwd default
```
```bash
rc-service iwd start
```
```bash
rc-service NetworkManager restart

```
