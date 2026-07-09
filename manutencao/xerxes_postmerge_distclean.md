## Limpeza Automática de Distfiles (Pós-instalação)

Apaga automaticamente o arquivo de código-fonte (`distfile`) logo após a instalação bem-sucedida do pacote, economizando espaço em disco. Utiliza os *hooks* do Portage.

### 1. Configurar o bashrc do Portage

Adicione a função e o *hook* de pós-instalação executando o bloco abaixo:

```bash
cat << 'EOF' | sudo tee -a /etc/portage/bashrc
xerxes_postmerge_distclean() {
  if [ -n "$DISTDIR" ] && [ -n "$A" ]; then
    for f in $A; do
      rm -f "$DISTDIR/$f"
    done
  fi
}

post_pkg_postinst() {
  if grep -q "postmerge_distclean" <<< "$XERXES_FEATURES"; then
    xerxes_postmerge_distclean
  fi
}
EOF

```

### 2. Ativar funcionalidade no make.conf

Declare a variável personalizada para habilitar a execução:

```bash
echo 'XERXES_FEATURES="postmerge_distclean"' | sudo tee -a /etc/portage/make.conf

```
