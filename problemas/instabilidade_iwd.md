## Correção de instabilidade no iwd (Realtek RTW88)

Solução para sumiço de redes e microquedas (OpenRC). Limita funções automatizadas do daemon (gerenciamento de energia agressivo e *roaming*) que derrubam o driver `rtw88`.

### 1. Configuração do iwd (Anti-roaming e Protocolos)

```bash
sudo mkdir -p /etc/iwd
cat << 'EOF' | sudo tee /etc/iwd/main.conf
[General]
EnableNetworkConfiguration=false
RoamThreshold=-85
RoamThreshold5G=-80
RoamRetryInterval=0
BSSBlacklistThreshold=5

[Network]
EnableIPv6=false

[Scan]
DisableHE=true
DisableVHT=true
EOF

```

### 2. Persistência do Power Save (udev)

Garante que o *Power Save* permaneça em `off` independentemente de mudanças no nome da interface.

```bash
echo 'ACTION=="add", SUBSYSTEM=="net", KERNEL=="wlan*", RUN+="/usr/sbin/iw dev %k set power_save off"' | sudo tee /etc/udev/rules.d/70-wifi-powertweak.rules

```

### 3. Ajuste no NetworkManager

Nas configurações de rede da interface gráfica (KDE/GNOME), edite a conexão atual e altere o método **IPv6** para **Ignorado**.

### 4. Aplicando as configurações (OpenRC)

```bash
sudo rc-service NetworkManager stop
sudo rc-service iwd restart
sleep 5
sudo rc-service NetworkManager start

```

### 5. Comandos de diagnóstico

Verifique o status do *Power Save* e qualidade do link (substitua `wlan1` pela sua interface):

```bash
echo -e "--- POWER SAVE ---\n$(iw dev wlan1 get power_save)\n\n--- LINK QUALITY ---\n$(iw dev wlan1 link | grep -E 'signal|bitrate|freq')"

```

Para confirmar se a conexão estabilizou (buscando erros no log do sistema):

```bash
sudo grep -iE "roaming|disconnect|failed" /var/log/messages

```
