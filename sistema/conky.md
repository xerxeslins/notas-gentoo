## Conky: Configuração Simples (Gentoo)

Requisito: `app-admin/conky` instalado.

### 1. Arquivo de configuração principal

Cria o diretório e o arquivo `conky.conf` com as definições de interface e monitoramento do sistema:

```bash
mkdir -p ~/.config/conky
cat << 'EOF' > ~/.config/conky/conky.conf
conky.config = {
    out_to_wayland = true,
    own_window = true,
    own_window_transparent = true,
    own_window_argb_visual = true,
    own_window_type = 'desktop',
    double_buffer = true,
    use_xft = true,
    font = 'monospace:size=14',
    update_interval = 2.0,
    alignment = 'top_right',
    gap_x = 20,
    gap_y = 50,
    default_color = 'ffffff',
    color1 = 'a020f0'
}

conky.text = [[
${color1}${exec whoami}${color}@${color1}${nodename}${color}
-------------------------
${color1}OS:${color} ${execi 86400 . /etc/os-release && echo $PRETTY_NAME} ${machine}
${color1}Kernel:${color} ${sysname} ${kernel}
${color1}Uptime:${color} ${uptime}
${color1}Packages:${color} ${execi 3600 qlist -I | wc -l} (emerge), ${execi 3600 flatpak list | wc -l} (flatpak)
${color1}CPU:${color} AMD Ryzen 7 3700X @ ${freq_g} GHz
${color1}CPU Temp:${color} ${execi 5 sensors | grep 'Tctl' | awk '{print $2}'}
${color1}CPU Usage:${color} ${cpu}% ${color1}${cpubar 8,100}${color}
${color1}Top CPU:${color}
 1. ${top name 1} ${top cpu 1}%
 2. ${top name 2} ${top cpu 2}%
 3. ${top name 3} ${top cpu 3}%
${color1}GPU:${color} AMD Radeon RX 5700 XT
${color1}GPU Temp:${color} ${execi 5 sensors | grep 'edge' | head -n 1 | awk '{print $2}'}
${color1}Memory:${color} ${mem} / ${memmax} (${memperc}%)
${color1}Swap:${color} ${swap} / ${swapmax} (${swapperc}%)
${color1}Disk (/):${color} ${fs_used /} / ${fs_size /} (${fs_used_perc /}%)
${color1}Disk (/mnt/meusjogos):${color} ${fs_used /mnt/meusjogos} / ${fs_size /mnt/meusjogos} (${fs_used_perc /mnt/meusjogos}%)
${color1}Net (wlan8):${color} ${addr wlan8}
 D: ${downspeed wlan8} | U: ${upspeed wlan8}
]]

EOF

```

### 2. Teste manual

Inicie o Conky em segundo plano:

```bash
conky -c ~/.config/conky/conky.conf &

```

### 3. Inicialização automática (KDE/Desktop)

Cria a entrada `.desktop` para iniciar o Conky automaticamente no login:

```bash
mkdir -p ~/.config/autostart
cat << 'EOF' > ~/.config/autostart/conky.desktop
[Desktop Entry]
Type=Application
Exec=conky
Hidden=false
NoDisplay=false
X-KDE-autostart-after=panel
Name=Conky
EOF

```
