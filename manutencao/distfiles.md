## Limpeza de Distfiles no Gentoo

O Portage armazena todos os arquivos de código-fonte baixados em `/var/cache/distfiles`. Como o sistema não limpa esses arquivos automaticamente, o diretório pode acumular gigabytes de dados obsoletos ao longo do tempo.

### 1. Instalação do eclean

A maneira mais segura e recomendada de gerenciar esses arquivos é através do utilitário `eclean`, presente no pacote `gentoolkit`.

```bash
sudo emerge --ask app-portage/gentoolkit

```

### 2. Comandos de Limpeza

O `eclean-dist` analisa seus arquivos e remove apenas o que é seguro, mantendo o necessário para o seu sistema atual.

* **Simular a limpeza (verificar espaço livre):**
Sempre execute com `--pretend` para visualizar o que será removido antes de confirmar.
```bash
sudo eclean-dist --pretend

```


* **Limpeza padrão:**
Remove arquivos de pacotes que não estão mais instalados ou disponíveis nas árvores do Portage.
```bash
sudo eclean-dist

```


* **Limpeza profunda (`--deep`):**
Remove fontes de pacotes que estão instalados, mas cujos *ebuilds* foram removidos do repositório, garantindo uma limpeza ainda mais rigorosa.
```bash
sudo eclean-dist --deep

```



> **Nota:** Embora seja possível esvaziar a pasta `/var/cache/distfiles` manualmente, o uso do `eclean` é indispensável para evitar a remoção acidental de arquivos necessários, evitando downloads desnecessários caso você precise reinstalar ou recompilar um software posteriormente.

