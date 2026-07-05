## Diagnóstico e solução para Band Steering (Wi-Fi)

Para investigar instabilidades e microquedas, busque erros de desconexão ou roaming no log do sistema:
`sudo grep -iE "roaming|disconnect|failed" /var/log/messages`

Exemplo de problema (Band Steering / Roaming Agressivo): O log indica saltos constantes entre diferentes BSSIDs. Isso ocorre quando o roteador unifica as redes 2.4GHz e 5GHz sob o mesmo SSID ou há pontos de acesso sobrepostos, forçando o adaptador a trocar de rádio a qualquer variação mínima de sinal.

Solução: Configurar o parâmetro `bgscan` globalmente para definir um limiar de sinal (ex: -70 dBm) que justifique o roaming, e desativar a randomização de MAC. Isso mantém a capacidade de transição física sem causar quedas por oscilações triviais.

Crie o arquivo de configuração no NetworkManager:

```bash
sudo mkdir -p /etc/NetworkManager/conf.d

sudo bash -c "cat << 'EOF' > /etc/NetworkManager/conf.d/wifi-stability.conf
[connection]
wifi.bgscan=simple:30:-70:300
wifi.cloned-mac-address=permanent

[device]
wifi.scan-rand-mac-address=no
EOF"

```

Reinicie o serviço (OpenRC):

```bash
sudo rc-service NetworkManager restart

```
