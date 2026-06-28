## genlop: Histórico de compilação no Gentoo

O `genlop` é uma ferramenta essencial para monitorar o histórico de instalações e verificar o tempo de compilação (`merge time`) de pacotes no Portage.

### 1. Instalação

```bash
sudo emerge --ask app-portage/genlop

```

### 2. Uso principal

Para visualizar o tempo de compilação de um pacote específico, utilize a flag `-i` (info) e `-t` (time):

```bash
genlop -it <nome-do-pacote>

```

**Exemplo de saída:**

```text
 * www-client/chromium

     Tue Jun 23 23:57:11 2026 >>> www-client/chromium-149.0.7827.155
       merge time: 7 hours, 26 minutes and 20 seconds.

     Fri Jun 26 05:24:45 2026 >>> www-client/chromium-149.0.7827.155
       merge time: 7 hours, 25 minutes and 34 seconds.

   Total builds: 2
   Global build time: 14 hours, 51 minutes and 54 seconds.
   Average merge time: 7 hours, 25 minutes and 57 seconds.

```

### Outras funcionalidades úteis

* **Ver o que está sendo compilado agora:**
```bash
genlop -c

```


* **Ver as últimas compilações:**
```bash
genlop -l

```
```
