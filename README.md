# notas-gentoo
Notas técnicas, procedimento de instalação, configurações de pós-instalação e rotinas de otimização para o Gentoo Linux.

## Índice de Navegação

### Portage e Otimizações de Compilação
* [make.conf](portage/make.conf.md) — Conteúdo do make.conf.
* [Portage com Git](portage/git.md) — Como sincronizar o repositório do Gentoo com Git.
* [tmpfs](portage/tmpfs.md) — Configuração de tmpfs.
* [env low_ram](portage/low_ram.md) — Configuração para compilar com pouca RAM.

### Configuração do Sistema e Ferramentas
* [iwd no NetworkManager](sistema/rede_audio.md) — Migração do backend do NetworkManager para o iwd.
* [Usar Zram](sistema/zram.md) — Configuração de Zram.
* [Usar Swapfile](sistema/swapfile.md) — Configuração de Swapfile.
* [Flatpak](sistema/flatpak.md) — Configurar flatpak.
* [Steam (Flatpak) + NTFS](sistema/steam_ntfs.md) — Configurar Steam (Flatpak) com jogos em partição NTFS.
* [Script para encriptar a home com ext4](sistema/fscrypt.md) — Script de encriptação.
* [Eix](sistema/eix.md) — Como usar o Eix.
* [equery e flaggie](sistema/equery_flaggie.md) — Como usar o equery e flaggie.
* [Atualizar kernel-bin](sistema/kerne_bin_update.md) — Atualizando para a última versão do gentoo-kernel-bin no Gentoo Estável.

### Customização, Fontes e Codecs
* [Fontes](sistema/fontes.md) — Fontes Microsoft e outras.
* [Codecs](sistema/codecs.md) — Instalação de codecs.
* [Compactadores](sistema/compactadores.md) — Ferramentas de compactação.

### Resolução de problemas
* [Spectacle não grava vídeo](problemas/spectacle.md) — Como resolver o problema do Spectacle que não grava vídeo.
* [Correção de instabilidade no iwd (Realtek RTW88)](problemas/instabilidade_iwd.md) — Solução para sumiço de redes e microquedas (OpenRC).

### Manutenção e Limpeza
* [Rotina de Atualização](manutencao/rotina.md) — Comandos para manter o sistema limpo e atualizado.
* [Remoção segura de pacotes](manutencao/remover_pacotes.md) — Como remover pacotes de forma segura.
* [Desmascarar](sistema/unmask.md) — Como desmascarar um pacote.
