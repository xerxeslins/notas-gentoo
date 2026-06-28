## Diagnóstico e solução para Band Steering (Wi-Fi)

Para investigar instabilidades e microquedas, busque erros de desconexão ou *roaming* no log do sistema:

```bash
sudo grep -iE "roaming|disconnect|failed" /var/log/messages

```

**Exemplo de problema (Band Steering):** O log indica saltos constantes entre diferentes BSSIDs (ex: finais `1f` e `20`). Isso ocorre quando o roteador unifica as redes 2.4GHz e 5GHz sob o mesmo SSID, forçando o adaptador a trocar de rádio repetidamente.

**Solução:** Fixar o BSSID na conexão ativa e desativar a randomização de MAC e scans de fundo.

Identifique a conexão ativa e fixe o BSSID (substitua o MAC `74:6f:88:f5:95:20` pelo endereço correto do rádio desejado, identificado no log):

```bash
WIFI_CON=$(nmcli -t -f NAME,TYPE connection show --active | grep 802-11-wireless | cut -d: -f1)
sudo nmcli connection modify "$WIFI_CON" 802-11-wireless.bssid 74:6f:88:f5:95:20

```

Desative a randomização de MAC criando a regra no NetworkManager:

```bash
sudo mkdir -p /etc/NetworkManager/conf.d
echo -e "[device]\nwifi.scan-rand-mac-address=no\n\n[connection]\nwifi.cloned-mac-address=permanent" | sudo tee /etc/NetworkManager/conf.d/disable-wifi-roaming.conf

```

Reinicie o serviço de rede (OpenRC):

```bash
sudo rc-service NetworkManager restart

```

