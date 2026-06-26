## Encriptar home com fscrypt (ext4)

Ideal para usuários recém-criados. Execute como `root` no TTY (ex: Alt+F2) com o usuário-alvo **deslogado**.

Crie o script (edite as variáveis `ALVO_USER`, `ALVO_GROUP` e `PARTICAO` antes de executar, se necessário):

```bash
cat << 'EOF' > encriptar_home.sh
#!/bin/bash

# Edite estas variáveis:
ALVO_USER="convidado"
ALVO_GROUP="users"
PARTICAO="/dev/nvme0n1p3"

if [ "$EUID" -ne 0 ]; then
  exit 1
fi

if ! command -v fscrypt >/dev/null 2>&1; then
  emerge sys-fs/fscrypt || exit 1
fi

tune2fs -O encrypt "$PARTICAO"

fscrypt setup --quiet
fscrypt setup /

mv "/home/$ALVO_USER" "/home/${ALVO_USER}-backup"
mkdir "/home/$ALVO_USER"
chown "$ALVO_USER:$ALVO_GROUP" "/home/$ALVO_USER"

echo "ATENÇÃO: Etapa crítica. Ctrl+C desativado temporariamente. NÃO feche o terminal."
trap '' SIGINT

if fscrypt encrypt "/home/$ALVO_USER" --user="$ALVO_USER"; then
  echo "Copiando arquivos de volta. Este processo é demorado. Aguarde..."
  cp -a "/home/${ALVO_USER}-backup/." "/home/$ALVO_USER/"
  rm -fr "/home/${ALVO_USER}-backup"

  sed -i '/pam_fscrypt\.so/d' /etc/pam.d/system-auth
  sed -i '/auth.*pam_faillock\.so authfail/a auth\t\toptional\tpam_fscrypt.so' /etc/pam.d/system-auth
  sed -i '/^password.*pam_unix\.so/a password\toptional\tpam_fscrypt.so' /etc/pam.d/system-auth
  sed -i '/^session.*pam_unix\.so/a session\t\toptional\tpam_fscrypt.so' /etc/pam.d/system-auth

  trap - SIGINT
  echo "Concluído. Terminal liberado."
else
  rm -fr "/home/$ALVO_USER"
  mv "/home/${ALVO_USER}-backup" "/home/$ALVO_USER"
  trap - SIGINT
  exit 1
fi
EOF

```

Torne executável e execute:

```bash
chmod +x encriptar_home.sh
./encriptar_home.sh

```

* Quando perguntado, escolha a opção de usar a **mesma senha de login** do usuário.
* **Teste:** Reinicie o sistema. Logado como `root` no TTY, liste os arquivos do usuário-alvo. Eles aparecerão encriptados. A desencriptação ocorre automaticamente no login do usuário.

```
