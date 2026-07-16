## Otimizar velocidade do Portage (mirrorselect)

Seleciona os espelhos (*mirrors*) mais rápidos para download de código-fonte e pacotes binários, gravando as melhores opções automaticamente no `/etc/portage/make.conf`.

### 1. Instalação

```bash
sudo emerge -v app-portage/mirrorselect

```

### 2. Execução (Teste de Banda)

```bash
sudo mirrorselect -s4 -b10 -D

```

**Parâmetros:**

* **`-s4`**: Mantém os 4 servidores mais rápidos (1 principal + 3 reservas).
* **`-b10`**: Testa em blocos de 10 conexões simultâneas. Evita esgotamento da banda local e bloqueios de IP por mecanismos anti-DDoS.
* **`-D`**: Teste profundo (`--deep`). Baixa um arquivo de 100 KB de cada servidor para aferir a taxa de transferência real (banda), ignorando métricas imprecisas baseadas apenas em latência (*ping*).

```
