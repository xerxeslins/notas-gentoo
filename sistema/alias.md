## Personalização do .bashrc: Aliases e Funções Úteis

Para otimizar o fluxo de trabalho no Gentoo, adicione o bloco abaixo ao seu arquivo `~/.bashrc`, logo após a seção `"Put your fun stuff here."`.

```bash
# Check distfiles size on login
check_distfiles_space() {
    local DISTDIR="/var/cache/distfiles"
    local LIMIT_GB=5
    
    if [ -d "$DISTDIR" ]; then
        local size_kb=$(du -sk "$DISTDIR" | cut -f1)
        local limit_kb=$((LIMIT_GB * 1024 * 1024))
        
        if [ "$size_kb" -gt "$limit_kb" ]; then
            echo -e "\e[1;33m[Aviso]\e[0m distfiles ocupando mais de ${LIMIT_GB}GB."
            echo -e "Use: \e[1;32msudo eclean-dist --pretend\e[0m para verificar ou \e[1;32msudo eclean-dist\e[0m para limpar."
        fi
    fi
}
check_distfiles_space

# --- INICIO MEUS ALIASES ---

alias up='sudo emerge --sync && sudo emerge -avuDN @world'
alias limpar='sudo emerge --depclean'
alias ll='ls -lah --color=auto'
alias grep='grep --color=auto'
alias portas='sudo ss -tulpn'
alias df='df -h'
alias free='free -m'

# Função auxiliar para verificar/instalar dependências
_req() {
    if ! command -v "$1" >/dev/null 2>&1; then
        echo "Comando '$1' não encontrado. Instalando '$2'..."
        sudo emerge -v "$2"
    fi
}

# Exibe arquivo sem comentários e linhas vazias
catlimpo() {
    if [ -f "$1" ]; then
        sed -e '/^[[:space:]]*#/d' -e '/^[[:space:]]*$/d' "$1"
    else
        echo "Erro: '$1' não é um arquivo válido."
    fi
}

# Extrator universal
extrair() {
    if [ -f "$1" ] ; then
        case "$1" in
            *.tar.bz2|*.tbz2) tar xjf "$1"    ;;
            *.tar.gz|*.tgz)   tar xzf "$1"    ;;
            *.bz2)            bunzip2 "$1"    ;;
            *.rar)            _req unrar app-arch/unrar; unrar x "$1"   ;;
            *.gz)             gunzip "$1"     ;;
            *.tar)            tar xf "$1"     ;;
            *.zip)            _req unzip app-arch/unzip; unzip "$1"     ;;
            *.Z)              uncompress "$1" ;;
            *.7z)             _req 7z app-arch/p7zip; 7z x "$1"      ;;
            *.xz)             tar xJf "$1"    ;;
            *)                echo "Erro: formato de '$1' não suportado." ;;
        esac
    else
        echo "Erro: '$1' não é um arquivo válido."
    fi
}

# Vídeo para MP4
v2mp4() {
    _req ffmpeg media-video/ffmpeg
    if [ -f "$1" ]; then
        ffmpeg -i "$1" -vf "scale=trunc(iw/2)*2:trunc(ih/2)*2" -c:v libx264 -preset fast -crf 23 -c:a aac -b:a 128k "${1%.*}.mp4"
    else
        echo "Uso: v2mp4 <arquivo_de_video>"
    fi
}

# MP4 para GIF (p:320, m:480, g:720)
mp4togif() {
    _req ffmpeg media-video/ffmpeg
    if [ "$#" -ne 2 ] || [ ! -f "$2" ]; then
        echo "Uso: mp4togif <p|m|g> <video.mp4>"
        return 1
    fi
    local w
    case "$1" in
        p) w=320 ;;
        m) w=480 ;;
        g) w=720 ;;
        *) echo "Tamanho inválido. Use p, m ou g."; return 1 ;;
    esac
    ffmpeg -i "$2" -vf "fps=10,scale=$w:-1:flags=lanczos,split[s0][s1];[s0]palettegen=max_colors=64[p];[s1][p]paletteuse=dither=bayer:bayer_scale=5" -loop 0 "${2%.*}.gif"
}

# Manipulação de imagens (join)
imgjoin() {
    _req magick media-gfx/imagemagick
    if [ "$#" -ne 4 ]; then
        echo "Uso: imgjoin <h|v> <img1> <img2> <saida>"
        return 1
    fi
    case "$1" in
        h) magick "$2" "$3" +append "$4" ;;
        v) magick "$2" "$3" -append "$4" ;;
        *) echo "Opção inválida. Use 'h' (horizontal) ou 'v' (vertical)." ;;
    esac
}

# Redimensionar imagem
imgresize() {
    _req magick media-gfx/imagemagick
    if [ "$#" -ne 2 ]; then
        echo "Uso: imgresize <porcentagem> <imagem>"
        return 1
    fi
    magick "$2" -resize "$1%" "resized_$2"
}

# Remover imagens de PDF
pdfnoimg() {
    _req gs app-text/ghostscript-gpl
    if [ -f "$1" ]; then
        gs -o "noimg_$1" -sDEVICE=pdfwrite -dFILTERIMAGE "$1"
    else
        echo "Uso: pdfnoimg <arquivo.pdf>"
    fi
}
# --- FIM MEUS ALIASES ---
