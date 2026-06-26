## Como desmascarar um pacote (Exemplo: CPU-X)

Se `/etc/portage/package.accept_keywords` for diretório:

```bash
cat << 'EOF' | sudo tee /etc/portage/package.accept_keywords/cpu-x
sys-apps/cpu-x ~amd64
EOF

```

Se `/etc/portage/package.accept_keywords` for arquivo:

```bash
cat << 'EOF' | sudo tee -a /etc/portage/package.accept_keywords
sys-apps/cpu-x ~amd64
EOF

```

Caso possua "hard mask" e `/etc/portage/package.unmask` for diretório:

```bash
cat << 'EOF' | sudo tee /etc/portage/package.unmask/cpu-x
sys-apps/cpu-x
EOF

```

Caso possua "hard mask" e `/etc/portage/package.unmask` for arquivo:

```bash
cat << 'EOF' | sudo tee -a /etc/portage/package.unmask
sys-apps/cpu-x
EOF

```



