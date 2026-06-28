## Configurador de Impressoras Universal (Driverless IPP)

Este script em Bash automatiza a detecção, instalação e configuração de impressoras em rede sem a necessidade de drivers proprietários (*Driverless Printing*), utilizando o protocolo **IPP Everywhere** ou conexões diretas via socket TCP.

### Funcionalidades
* **Suporte Multi-distribuição:** Detecta o gerenciador de pacotes ativo (`apt-get`, `dnf`, `pacman` ou `emerge`) e instala automaticamente o ecossistema de impressão (`CUPS`, `Avahi`, `Ghostscript`).
* **Auto-detecção via mDNS:** Varre a rede local usando o Avahi para identificar impressoras compatíveis com IPP de forma automática.
* **Fallback Manual:** Permite a inserção manual do endereço IP caso a impressora não envie anúncios mDNS ou esteja em uma sub-rede diferente.
* **Compatibilidade Init:** Gerencia a ativação de serviços tanto em sistemas com `systemd` quanto com `OpenRC` / `sysvinit`.
* **Validação de Redundância:** Evita erros de loop impedindo a configuração do próprio IP da máquina como alvo da impressora.

### Instalação e Uso

1. Salve o código abaixo em um arquivo chamado `printer_manager.sh`:

```bash
#!/bin/bash

RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[1;34m'
NC='\033[0m'

[[ $EUID -ne 0 ]] && echo -e "${RED}[!] Execute como root.${NC}" && exit 1

if command -v apt-get &>/dev/null; then
    PKG_MGR="apt-get install -y"
    PKGS="cups avahi-daemon avahi-utils cups-filters iproute2 ghostscript"
elif command -v dnf &>/dev/null; then
    PKG_MGR="dnf install -y"
    PKGS="cups avahi avahi-tools cups-filters iproute ghostscript"
elif command -v pacman &>/dev/null; then
    PKG_MGR="pacman -S --needed --noconfirm"
    PKGS="cups avahi cups-filters iproute2 ghostscript"
elif command -v emerge &>/dev/null; then
    PKG_MGR="emerge -av"
    PKGS="net-print/cups net-dns/avahi net-print/cups-filters sys-apps/iproute2 app-text/ghostscript-gpl"
else
    echo -e "${RED}[!] Distribuição não suportada.${NC}"; exit 1
fi

for cmd in lpadmin avahi-daemon avahi-browse ip gs; do
    if ! command -v "$cmd" &> /dev/null; then
        echo -e "${YELLOW}[!] Dependência '$cmd' ausente. Instalando pacotes base...${NC}"
        $PKG_MGR $PKGS
        break
    fi
done

iniciar_servicos() {
    echo -e "${YELLOW}Verificando serviços essenciais...${NC}"
    if command -v systemctl &>/dev/null; then
        systemctl enable --now avahi-daemon cups &>/dev/null
    else
        /etc/init.d/avahi-daemon start &>/dev/null
        /etc/init.d/cups start &>/dev/null
    fi
    sleep 2
}

configurar_manual() {
    echo -e "\n${YELLOW}>>> Modo Manual (IP Fixo)${NC}"
    read -rp "Nome da Impressora (sem espaços): " NAME
    read -rp "IP da Impressora: " IP

    MEUS_IPS=$(ip -4 addr show | awk '/inet/ {print $2}' | cut -d/ -f1)
    if grep -q "\b$IP\b" <<< "$MEUS_IPS"; then
        echo -e "${RED}[!] Erro: IP local informado.${NC}"; return 1
    fi

    echo -e "${YELLOW}Aplicando configurações...${NC}"
    if lpadmin -p "$NAME" -E -v "ipp://$IP/ipp/print" -m everywhere 2>/dev/null; then
        lpadmin -d "$NAME"
        echo -e "${GREEN}[OK] Impressora configurada via IPP Everywhere.${NC}"
    elif lpadmin -p "$NAME" -E -v "socket://$IP" -m raw 2>/dev/null; then
        lpadmin -d "$NAME"
        echo -e "${GREEN}[OK] Impressora configurada via genérico/raw.${NC}"
    else
        echo -e "${RED}[!] Falha ao configurar. Verifique o IP e se o serviço CUPS está rodando.${NC}"
    fi
}

clear
echo -e "${BLUE}=== Configurador de Impressoras Universal ===${NC}"
echo "1) Auto-detecção e Instalação (Avahi)"
echo "2) Configurar manualmente por IP"
echo "3) Sair"
read -rp "Escolha: " OP

if [[ "$OP" == "1" || "$OP" == "2" ]]; then
    iniciar_servicos
fi

if [[ "$OP" == "1" ]]; then
    echo -e "${YELLOW}Buscando impressoras via mDNS...${NC}"
    
    INFO=$(avahi-browse -rtp _ipp._tcp 2>/dev/null | awk -F';' '/^=/ && /IPv4/ {print $0}' | head -n 1)
    
    if [[ -n "$INFO" ]]; then
        NOME_RAW=$(echo "$INFO" | cut -d';' -f4)
        IP=$(echo "$INFO" | cut -d';' -f8)
        
        NOME=$(echo "$NOME_RAW" | tr -cd '[:alnum:]_')
        [[ -z "$NOME" ]] && NOME="Impressora_Detectada"

        echo -e "${GREEN}Impressora localizada: $NOME_RAW (IP: $IP)${NC}"
        echo -e "${YELLOW}Criando fila nativa...${NC}"
        
        if lpadmin -p "$NOME" -E -v "ipp://$IP/ipp/print" -m everywhere 2>/dev/null; then
            lpadmin -d "$NOME"
            echo -e "${GREEN}[OK] Sucesso! Impressora configurada no sistema.${NC}"
        else
            echo -e "${RED}[!] Falha ao criar fila. O serviço CUPS está rodando?${NC}"
        fi
    else
        echo -e "${RED}[!] Nenhuma impressora detectada.${NC}"
        read -rp "Configurar manualmente pelo IP? (s/n): " AUTO_FAIL
        [[ "$AUTO_FAIL" == "s" ]] && configurar_manual
    fi
elif [[ "$OP" == "2" ]]; then
    configurar_manual
fi

```

2. Atribua permissão de execução:

```bash
chmod +x printer_manager.sh

```

3. Execute o script como `root` ou via `sudo`:

```bash
sudo ./printer_manager.sh

```
