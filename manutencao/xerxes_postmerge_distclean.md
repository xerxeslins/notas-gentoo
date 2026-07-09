## Limpeza Automática de Distfiles (Pós-instalação)

Apaga automaticamente o arquivo de código-fonte (`distfile`) logo após a instalação bem-sucedida do pacote, economizando espaço em disco. Utiliza os *hooks* do Portage.

### 1. Configurar o bashrc do Portage

Adicione a função e o *hook* de pós-instalação executando o bloco abaixo:

```bash
cat << 'EOF' | sudo tee -a /etc/portage/bashrc
# darkelf-features-0.1::darkelf
#
# This enables cleaning the distfiles after every emerge,
# to enable this feature set
# DARKELF_FEATURES="postmerge_distclean"
# in /etc/portage/make.conf or per command basis e.g.:
# DARKELF_FEATURES="postmerge_distclean" emerge ...

darkelf_postmerge_distclean(){
 echo cleaning distfiles for ${CATEGORY}/${PF}...
 if [ -z $DISTDIR ] ; then
  echo ERROR: DISTDIR is empty!
  exit 1
 fi
 for f in $DISTDIR/* ; do
  echo deleting "`readlink -f $f`"...
  rm -r "`readlink -f $f`"
 done
}

post_pkg_postinst(){
 if grep -q "postmerge_distclean" <<< $DARKELF_FEATURES ; then
  darkelf_postmerge_distclean
 fi
}
EOF

```

### 2. Ativar funcionalidade no make.conf

Declare a variável personalizada para habilitar a execução:

```bash
echo 'DARKELF_FEATURES="postmerge_distclean"' | sudo tee -a /etc/portage/make.conf

```
Fonte: [Remove obsoleted distfiles](https://wiki.gentoo.org/wiki/Knowledge_Base:Remove_obsoleted_distfiles) - Gentoo WIKI
