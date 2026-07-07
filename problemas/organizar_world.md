## Organizar arquivo world em ordem alfabética

O arquivo `/var/lib/portage/world` registra os pacotes instalados explicitamente. Ordená-lo facilita a leitura, otimiza *diffs* de controle de versão e elimina entradas duplicadas.

**Aviso:** Nunca utilize redirecionamento simples (`sort arquivo > arquivo`). O shell trunca o arquivo de destino antes da leitura, o que apaga todo o conteúdo do seu arquivo `world`.

### Método seguro

O parâmetro `-o` instrui o `sort` a ler o arquivo para a memória antes de gravar, permitindo sobrescrever o original com segurança. O parâmetro `-u` remove as duplicatas silenciosamente.

Comandos com backup preventivo:

```bash
sudo cp /var/lib/portage/world /var/lib/portage/world.bak
sudo sort -u -o /var/lib/portage/world /var/lib/portage/world

```
