## Como desmascarar pacotes (Exemplo: CPU-X e dependências)

O uso de `tee -a` (append) garante que novas entradas não sobrescrevam as anteriores ao configurar pacotes e suas dependências no mesmo arquivo.

Se `/etc/portage/package.accept_keywords` for diretório:

```bash
echo "sys-apps/cpu-x ~amd64" | sudo tee -a /etc/portage/package.accept_keywords/cpu-x
echo "dev-libs/libcpuid ~amd64" | sudo tee -a /etc/portage/package.accept_keywords/cpu-x

```

Se `/etc/portage/package.accept_keywords` for arquivo único:

```bash
echo "sys-apps/cpu-x ~amd64" | sudo tee -a /etc/portage/package.accept_keywords
echo "dev-libs/libcpuid ~amd64" | sudo tee -a /etc/portage/package.accept_keywords

```

Caso possua "hard mask" e `/etc/portage/package.unmask` for diretório:

```bash
echo "sys-apps/cpu-x" | sudo tee -a /etc/portage/package.unmask/cpu-x

```

Caso possua "hard mask" e `/etc/portage/package.unmask` for arquivo único:

```bash
echo "sys-apps/cpu-x" | sudo tee -a /etc/portage/package.unmask

```
