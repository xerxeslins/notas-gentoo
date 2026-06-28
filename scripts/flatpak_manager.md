## Flatpak Master Manager

Este script em Bash serve como um gerenciador centralizado para manter a organização, limpeza e integridade das instalações Flatpak no seu sistema. Ele automatiza tarefas de manutenção que, de outra forma, exigiriam múltiplos comandos manuais, sendo especialmente útil para economizar espaço em disco e resolver erros comuns de execução.

### Principais funcionalidades:
* **Atualização centralizada:** Atualiza todos os aplicativos e *runtimes* simultaneamente.
* **Limpeza inteligente:** Remove *runtimes* órfãos, limpa caches e executa reparos na base de dados.
* **Gestão de Espaço:** Lista seus aplicativos instalados ordenados pelo tamanho ocupado em disco.
* **Reparo:** Automatiza os comandos `flatpak repair` (sistema e usuário) para corrigir problemas de permissão ou arquivos corrompidos.
* **Remoção Segura:** Remove aplicativos junto com seus dados de configuração (`--delete-data`).
* **Reset de Permissões:** Facilita o retorno de permissões de um aplicativo para o padrão, ideal para corrigir comportamentos inesperados.

### Instalação

1. Salve o código abaixo em um arquivo chamado `flatpak_manager.sh`:

```bash
#!/bin/bash
# -----------------------------------------------------------------------------
# Nome: Flatpak Manager
# Descrição: Gerenciador para manutenção, limpeza e updates do Flatpak
# Autor: Xerxes Lins
# V 2.0 (24/04/2026)
# -----------------------------------------------------------------------------

GREEN='\033[1;32m'
BLUE='\033[1;34m'
RED='\033[1;31m'
YELLOW='\033[1;33m'
CYAN='\033[1;36m'
BOLD='\033[1m'
NC='\033[0m'

if ! command -v flatpak &> /dev/null; then
    echo -e "${RED}[Erro] Flatpak não encontrado.${NC}"
    exit 1
fi

show_menu() {
    clear
    echo -e "${BLUE}================================================================${NC}"
    echo -e "${BOLD}                FLATPAK MASTER MANAGER                 ${NC}"
    echo -e "${BLUE}================================================================${NC}"
    echo -e "  ${GREEN}[ 1 ]${NC} Atualizar Tudo (Apps + Runtimes)"
    echo -e "  ${GREEN}[ 2 ]${NC} Limpeza Leve (Runtimes órfãos)"
    echo -e "  ${GREEN}[ 3 ]${NC} Limpeza Profunda (Cache + Reparo + Órfãos)"
    echo -e "  ${GREEN}[ 4 ]${NC} Reparar Instalação (Sistema e Usuário)"
    echo -e "  ${GREEN}[ 5 ]${NC} Listar Apps (Tabela Formatada por Tamanho)"
    echo -e "  ${GREEN}[ 6 ]${NC} Ver Histórico de Operações"
    echo -e "  ${GREEN}[ 7 ]${NC} Resetar Permissões de um App"
    echo -e "  ${GREEN}[ 8 ]${NC} Remover App e Dados (Busca Interativa)"
    echo -e "${RED}[ 0 ] Sair${NC}"
    echo -e "${BLUE}================================================================${NC}"
    echo -e "${YELLOW}  Dica: A opção 4 resolve a maioria dos erros de execução.${NC}"
    echo -e "${BLUE}================================================================${NC}\n"
}

wait_user() {
    echo -e "\n${CYAN}Pressione ENTER para voltar ao menu...${NC}"
    read -r
}

print_header() {
    echo -e "\n${BLUE}----------------------------------------------------------------${NC}"
    echo -e "${BOLD} [*] $1${NC}"
    echo -e "${BLUE}----------------------------------------------------------------${NC}\n"
}

while true; do
    show_menu
    read -p "  Escolha uma opção: " option

    case $option in
        1)
            print_header "Buscando e aplicando atualizações"
            flatpak update
            wait_user
            ;;
        2)
            print_header "Removendo runtimes não usados"
            flatpak uninstall --unused
            wait_user
            ;;
        3)
            print_header "Executando limpeza profunda do sistema"
            echo -e "${CYAN}Passo 1:${NC} Removendo órfãos..."
            flatpak uninstall --unused -y
            echo -e "${CYAN}Passo 2:${NC} Limpando cache de downloads temporários..."
            sudo rm -rf /var/tmp/flatpak-cache-* 2>/dev/null || true
            echo -e "${CYAN}Passo 3:${NC} Reparando objetos soltos (pruning)..."
            sudo flatpak repair
            echo -e "\n${GREEN}[OK] Limpeza completa finalizada.${NC}"
            wait_user
            ;;
        4)
            print_header "Reparando base de dados do Flatpak"
            sudo flatpak repair
            flatpak repair --user
            echo -e "\n${GREEN}[OK] Reparo concluído.${NC}"
            wait_user
            ;;
        5)
            print_header "Aplicativos Instalados (Ordenados por Tamanho)"
            
            flatpak list --app --columns=name,application,size | \
            LC_ALL=C sort -t $'\t' -k3 -h | \
            awk -F'\t' 'BEGIN {
                printf "\033[1;36m\033[1m %-3s | %-30s | %-38s | %s\033[0m\n", "Nº", "NOME DO APLICATIVO", "ID DO PACOTE", "TAMANHO"
                printf "\033[1;34m%s\033[0m\n", "-----+--------------------------------+----------------------------------------+---------"
            }
            {
                printf " %-3s | %-30s | %-38s | %s\n", NR")", substr($1,1,30), substr($2,1,38), $3
            }'
            wait_user
            ;;
        6)
            print_header "Histórico de Operações (Últimas 20)"
            flatpak history | head -n 20
            wait_user
            ;;
        7)
            print_header "Resetar Permissões (Flatseal Default)"
            read -p "  Digite o nome ou ID (ex: telegram): " search_term
            if [ -n "$search_term" ]; then
                app_info=$(flatpak list --app --columns=name,application | grep -i "$search_term" | head -n 1)
                if [ -n "$app_info" ]; then
                    app_name=$(echo "$app_info" | cut -f1)
                    app_id=$(echo "$app_info" | cut -f2)
                    echo -e "\n${CYAN}Alvo encontrado:${NC} $app_name ($app_id)"
                    flatpak permission-reset "$app_id"
                    echo -e "${GREEN}[OK] Permissões resetadas.${NC}"
                else
                    echo -e "${RED}[Erro] Aplicativo não encontrado.${NC}"
                fi
            fi
            wait_user
            ;;
        8)
            print_header "Remoção Completa (Aplicativo + Dados Locais)"
            
            echo -e "${CYAN}Aplicativos Instalados:${NC}"
            flatpak list --app --columns=name,application | \
            awk -F'\t' '{printf "  - %-30s \033[1;30m(%s)\033[0m\n", substr($1,1,30), $2}'
            echo ""

            read -p "  Digite o nome/ID para remover (ou '0' para cancelar): " search_term
            
            if [[ "$search_term" == "0" ]]; then
                echo -e "\n${CYAN}[!] Operação cancelada.${NC}"
            elif [ -n "$search_term" ]; then
                app_info=$(flatpak list --app --columns=name,application | grep -i "$search_term" | head -n 1)
                
                if [ -n "$app_info" ]; then
                    app_name=$(echo "$app_info" | cut -f1)
                    app_id=$(echo "$app_info" | cut -f2)
                    
                    echo -e "\n${YELLOW}Atenção: Você está prestes a remover o seguinte aplicativo:${NC}"
                    echo -e "  ${BOLD}Nome:${NC} $app_name"
                    echo -e "  ${BOLD}ID:  ${NC} $app_id\n"
                    
                    read -p "  Confirmar remoção total (app + config)? [s/N]: " confirm
                    if [[ "$confirm" =~ ^[sS]$ ]]; then
                        flatpak uninstall --delete-data "$app_id"
                        echo -e "\n${GREEN}[OK] '$app_name' removido com sucesso.${NC}"
                    else
                        echo -e "\n${CYAN}[!] Operação cancelada.${NC}"
                    fi
                else
                    echo -e "\n${RED}[Erro] Nenhum aplicativo corresponde a '$search_term'.${NC}"
                fi
            else
                echo -e "\n${CYAN}[!] Operação cancelada (entrada vazia).${NC}"
            fi
            wait_user
            ;;
        0) 
            clear
            exit 0 
            ;;
        *) 
            sleep 0.5 
            ;;
    esac
done

```

2. Torne o script executável:

```bash
chmod +x flatpak_manager.sh

```

3. Execute-o sempre que precisar realizar a manutenção dos seus apps:

```bash
./flatpak_manager.sh

```
