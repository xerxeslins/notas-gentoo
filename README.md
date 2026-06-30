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
* [CPU Governor](sistema/governor.md) — Configurar CPU Governor para Performance Permanente (OpenRC)
* [Flatpak](sistema/flatpak.md) — Configurar flatpak.
* [Steam (Flatpak) + NTFS](sistema/steam_ntfs.md) — Configurar Steam (Flatpak) com jogos em partição NTFS.
* [Eix](sistema/eix.md) — Como usar o Eix.
* [equery e flaggie](sistema/equery_flaggie.md) — Como usar o equery e flaggie.
* [Atualizar kernel-bin](sistema/kernel_bin_update.md) — Atualizando para a última versão do gentoo-kernel-bin no Gentoo Estável.
* [genlop - tempo e histórico de compilação](sistema/genlop.md) — Ferramenta para saber quanto tempo levou para compilar um pacote.

### Scripts, aliases e funções
* [Aliases e funções](scripts/aliases.md) — Alguns aliases e funções para inserir no ~/.bashrc.
* [Encriptar a home com ext4](scripts/fscrypt.md) — Script de encriptação da home em ext4.
* [Gerenciar Flatpaks](scripts/flatpak_manager.md) — Script de gerenciamento dos pacotes flatpaks instalados.
* [Plugin Warsaw](scripts/warsaw_manager.md) — Script que instala/remove o plugin warsaw para acesso aos bancos.
* [Adicionar impressoras](scripts/printer_manager.md) — Script que instala impressoras de rede automaticamente ou inserindo o IP.

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
* [Desmascarar](manutencao/unmask.md) — Como desmascarar um pacote.
* [Limpeza de distfiles](manutencao/distfiles.md) — Limpeza de arquivos de código-fonte.
