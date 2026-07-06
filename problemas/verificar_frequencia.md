## Verificar frequência do Wi-Fi (2.4GHz / 5GHz)

Verifica a frequência (coluna `FREQ`) da conexão atual via NetworkManager. A rede ativa é indicada por um asterisco (`*`).

```bash
nmcli -f IN-USE,SSID,FREQ,CHAN device wifi list

```
