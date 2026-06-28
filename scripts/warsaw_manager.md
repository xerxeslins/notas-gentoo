## Warsaw Manager: Instalação e Gerenciamento

Este script automatiza a instalação, configuração e remoção do módulo de segurança **Warsaw** (usado por instituições bancárias) no Gentoo Linux. Ele oferece suporte nativo a sistemas baseados em **Systemd** ou **OpenRC**, configurando o serviço, permissões e imutabilidade dos arquivos.

### Funcionalidades
* **Detecção Automática:** Identifica seu sistema de inicialização (`systemd` ou `openrc`).
* **Instalação Automatizada:** Realiza o download, extração, instalação de binários e bibliotecas.
* **Segurança:** Aplica a imutabilidade (`chattr`) aos binários core e ajusta o *stack* executável via `patchelf`.
* **Gerenciamento de Ciclo de Vida:** Inclui opção para remoção completa do serviço e de todos os arquivos associados.
* **Diagnóstico:** Verifica automaticamente se o serviço está respondendo na porta `30900`.

### Instalação

1. Salve o código abaixo em um arquivo chamado `warsaw_manager.sh`:

```bash
#!/usr/bin/env bash
# Autor: Xerxes Lins
# Versao: 1.2
set -e

if [ "$EUID" -ne 0 ]; then
    exec sudo "$0" "$@"
fi

C_NONE='\033[0m'
C_RED='\033[1;31m'
C_GREEN='\033[1;32m'
C_YELLOW='\033[1;33m'
C_BLUE='\033[1;34m'

spinner() {
    local pid=$1
    local delay=0.1
    local spinstr='|/-\'
    while kill -0 $pid 2>/dev/null; do
        local temp=${spinstr#?}
        printf " [%c]  " "$spinstr"
        local spinstr=$temp${spinstr%"$temp"}
        sleep $delay
        printf "\b\b\b\b\b\b"
    done
    printf "    \b\b\b\b"
}

echo -e "${C_BLUE}[*] Verificando dependências...${C_NONE}"
for cmd in wget bsdtar tar patchelf ss sudo; do
    if ! command -v "$cmd" &> /dev/null; then
        echo -e "${C_RED}[ERRO] Comando não encontrado: $cmd${C_NONE}"
        exit 1
    fi
done

INIT_SYS=$(pidof systemd &>/dev/null && echo "systemd" || echo "openrc")

if [ -f "/usr/local/bin/warsaw/core" ]; then
    read -p "Warsaw já instalado. Remover? (s/n): " resp
    if [[ "$resp" == [sS] ]]; then
        echo -e "${C_BLUE}[*] Removendo Warsaw...${C_NONE}"
        if [[ "$INIT_SYS" == "systemd" ]]; then
            systemctl stop warsaw 2>/dev/null || true
            systemctl disable warsaw 2>/dev/null || true
            rm -f /etc/systemd/system/warsaw.service
            systemctl daemon-reload
        else
            rc-service warsaw stop 2>/dev/null || true
            rc-update del warsaw default 2>/dev/null || true
            rm -f /etc/init.d/warsaw
        fi
        
        chattr -i /usr/local/bin/warsaw/core 2>/dev/null || true
        chattr -a /usr/local/bin/warsaw/ 2>/dev/null || true
        rm -rf /usr/local/bin/warsaw /usr/local/lib/warsaw /usr/local/etc/warsaw /usr/bin/warsaw /usr/share/fonts/truetype/dbldwrsw.ttf /usr/share/locale/pt_BR/LC_MESSAGES/warsaw.mo
        echo -e "${C_GREEN}[OK] Remoção concluída.${C_NONE}"
        exit 0
    else
        echo "Operação cancelada."
        exit 0
    fi
fi

URL="[https://cloud.gastecnologia.com.br/bb/downloads/ws/debian/warsaw_setup64.run](https://cloud.gastecnologia.com.br/bb/downloads/ws/debian/warsaw_setup64.run)"
TMPDIR=$(mktemp -d)
cd "$TMPDIR"

echo -en "${C_BLUE}[*] Baixando Warsaw...${C_NONE}"
wget -q -O warsaw.run "$URL" &
spinner $!
echo -e ""

echo -en "${C_BLUE}[*] Extraindo arquivos...${C_NONE}"
(bsdtar -xpf warsaw.run && bsdtar -xf warsaw_setup/warsaw_*.deb data.tar.xz && tar xJf data.tar.xz) &
spinner $!
echo -e ""

echo -e "${C_BLUE}[*] Copiando arquivos...${C_NONE}"
mkdir -p /usr/local/bin/warsaw /usr/local/lib/warsaw /usr/local/etc/warsaw /usr/share/fonts/truetype /usr/share/locale/pt_BR/LC_MESSAGES

cp usr/local/bin/warsaw/* /usr/local/bin/warsaw/
chmod +x /usr/local/bin/warsaw/*
cp usr/local/lib/warsaw/*.so /usr/local/lib/warsaw/
cp -r usr/local/etc/warsaw/* /usr/local/etc/warsaw/
cp usr/bin/warsaw /usr/bin/
chmod +x /usr/bin/warsaw
cp usr/local/share/fonts/truetype/dbldwrsw.ttf /usr/share/fonts/truetype/
cp usr/share/locale/pt_BR/LC_MESSAGES/warsaw.mo /usr/share/locale/pt_BR/LC_MESSAGES/

echo -e "${C_BLUE}[*] Configurando inicialização ($INIT_SYS)...${C_NONE}"
if [[ "$INIT_SYS" == "systemd" ]]; then
    cp lib/systemd/system/warsaw.service /etc/systemd/system/
    sed -i 's|/var/run/core.pid|/run/core.pid|' /etc/systemd/system/warsaw.service
    systemctl daemon-reload
else
    cp etc/init.d/warsaw /etc/init.d/
    chmod +x /etc/init.d/warsaw
fi

echo -e "${C_BLUE}[*] Aplicando patch e imutabilidade...${C_NONE}"
patchelf --set-execstack /usr/local/bin/warsaw/core
chattr +i /usr/local/bin/warsaw/core 2>/dev/null || true
chattr +a /usr/local/bin/warsaw/ 2>/dev/null || true

cd /
rm -rf "$TMPDIR"

echo -e "${C_BLUE}[*] Iniciando serviço...${C_NONE}"
if [[ "$INIT_SYS" == "systemd" ]]; then
    systemctl enable --now warsaw
else
    rc-update add warsaw default
    rc-service warsaw start
fi

echo -e "${C_BLUE}[*] Verificando status na porta 30900...${C_NONE}"
sleep 3
ss -tlpn | grep core || true

if ss -tlpn | grep -q ":30900"; then
    echo -e "\n${C_GREEN}[OK] Sucesso: Warsaw em execucao na porta 30900.${C_NONE}"
    echo -e "${C_GREEN}[OK] Voce ja pode usar o site do banco.${C_NONE}\n"
    
    echo -e "${C_YELLOW}========================================================================${C_NONE}"
    echo -e "${C_YELLOW}[AVISO] SE O WARSAW FALHAR NO NAVEGADOR MESMO ESTANDO ATIVO, SIGA OS PASSOS:${C_NONE}"
    echo -e "  1. Abra uma nova aba no navegador e acesse diretamente:"
    echo -e "     ${C_BLUE}[https://127.0.0.1:30900](https://127.0.0.1:30900)${C_NONE}"
    echo -e "  2. O navegador exibira um alerta de seguranca ('A conexao nao e privada')."
    echo -e "  3. Clique em Avancado e selecione 'Aceitar o risco e continuar'."
    echo -e "  4. Uma pagina em branco ou erro 404 aparecera. Isso e o esperado."
    echo -e "  5. Volte a aba do banco e atualize a pagina (F5)."
    echo -e "${C_YELLOW}========================================================================${C_NONE}"
else
    echo -e "\n${C_RED}[ERRO] Falha: O servico nao esta escutando na porta 30900.${C_NONE}"
fi

```

2. Torne o script executável e execute:

```bash
chmod +x warsaw_manager.sh
sudo ./warsaw_manager.sh

```
