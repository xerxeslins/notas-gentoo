## equery e flaggie: Investigação e manipulação de USE flags

A combinação destas ferramentas permite investigar e aplicar mudanças nas USE flags com agilidade.

### 1. Instalação

Instale o `gentoolkit` (que inclui o `equery`) e o `flaggie`:

```bash
sudo emerge -avU app-portage/gentoolkit app-portage/flaggie

```

### 2. Investigando com o equery

Visualize as flags disponíveis para um pacote e a descrição de suas respectivas funções:

```bash
equery uses <nome-do-pacote>

```

* `[+]`: Flag ativada.
* `[-]`: Flag desativada.

### 3. Aplicando com o flaggie

Após identificar a flag desejada, aplique-a ao sistema (o `flaggie` edita os arquivos do Portage automaticamente):

```bash
sudo flaggie <nome-do-pacote> +<flag-escolhida>

```
