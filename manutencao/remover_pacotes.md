## Remoção segura de pacotes (`--deselect`)

Remove o pacote do arquivo `world` sem forçar a desinstalação imediata. O pacote será removido apenas durante a limpeza (`--depclean`), garantindo que ele não seja desinstalado se ainda for dependência de outro software no sistema.

```bash
sudo emerge --deselect <nome-do-pacote>
sudo emerge --ask --depclean

```
