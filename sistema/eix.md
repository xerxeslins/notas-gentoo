## eix: Busca rápida no Portage

O `eix` cria um banco de dados local do Portage, permitindo consultas imediatas sem a latência do `emerge -s`.

### 1. Instalação

```bash
sudo emerge --ask app-portage/eix

```

### 2. Configuração Inicial

Gere o banco de dados local pela primeira vez:

```bash
sudo eix-update

```

### 3. Comandos Principais

Sincronizar repositórios e atualizar o banco do `eix`:

```bash
sudo eix-sync

```

Buscar pacotes pelo nome:

```bash
eix nome-do-pacote

```

Buscar por termos na descrição:

```bash
eix -S "termo"

```

Listar pacotes instalados:

```bash
eix -I

```
