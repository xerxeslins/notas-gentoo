## Como desmascarar um pacote (Exemplo: CPU-X)

Se `/etc/portage/package.accept_keywords` for diretório:

```bash
echo "sys-apps/cpu-x ~amd64" | sudo tee /etc/portage/package.accept_keywords/cpu-x

```

Se `/etc/portage/package.accept_keywords` for arquivo:

```bash
echo "sys-apps/cpu-x ~amd64" | sudo tee -a /etc/portage/package.accept_keywords

```

Caso possua "hard mask" e `/etc/portage/package.unmask` for diretório:

```bash
echo "sys-apps/cpu-x" | sudo tee /etc/portage/package.unmask/cpu-x

```

Caso possua "hard mask" e `/etc/portage/package.unmask` for arquivo:

```bash
echo "sys-apps/cpu-x" | sudo tee -a /etc/portage/package.unmask

```

