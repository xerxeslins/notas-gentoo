## IA Local no Terminal: Ollama e Shell-GPT no Gentoo (OpenRC)

Integração de LLMs locais (offline) utilizando Ollama e Shell-GPT para geração e execução de comandos no terminal. Modelo base: `qwen2.5:14b`.

### 1. Desmascarar pacotes e compilar (Portage)

O `ollama` e seu motor de inferência `ggml` requerem a ramificação de testes (`~amd64`).

```bash
sudo mkdir -p /etc/portage/package.accept_keywords /etc/portage/package.use
echo -e "sci-ml/ollama ~amd64\nsci-ml/ggml ~amd64" | sudo tee /etc/portage/package.accept_keywords/ollama

```

> **Aceleração de Hardware (Radeon):** Para usar a GPU AMD, o `ggml` deve ser compilado com a flag `rocm`. Isso exigirá o desmascaramento (`~amd64`) de diversos pacotes do ecossistema ROCm para a resolução de dependências.
> ```bash
> echo "sci-ml/ggml rocm" | sudo tee /etc/portage/package.use/ggml
> 
> ```
> 
> 

Instale o pacote:

```bash
sudo emerge -av sci-ml/ollama

```

### 2. Iniciar serviço OpenRC

O pacote instala o script de inicialização automaticamente. Adicione-o ao *runlevel* padrão e inicie o serviço:

```bash
sudo rc-update add ollama default
sudo rc-service ollama start

```

### 3. Baixar o Modelo LLM

Faça o download e inicie um modelo. Por exemplo, o `qwen2.5:14b`. Para sair do chat interativo, digite `/bye`.

```bash
ollama run qwen2.5:14b

```

> **Dica de Diagnóstico:** Para verificar a alocação do modelo e confirmar se ele está operando via CPU ou GPU, execute em outro terminal enquanto o modelo estiver carregado:
> ```bash
> ollama ps
> 
> ```
> 
> 

### 4. Instalar o Shell-GPT (sgpt)

Para evitar quebras no sistema Python global, instale via ambiente virtual (`venv`).

```bash
mkdir -p ~/.local/venv ~/.local/bin
python3 -m venv ~/.local/venv/shell-gpt
~/.local/venv/shell-gpt/bin/pip install shell-gpt click
ln -sf ~/.local/venv/shell-gpt/bin/sgpt ~/.local/bin/sgpt

```

*Certifique-se de que `~/.local/bin` esteja no seu `PATH` (adicione `export PATH="$PATH:$HOME/.local/bin"` ao `~/.bashrc` se necessário).*

### 5. Redirecionar Shell-GPT para o Ollama

Configure o arquivo do `sgpt` fornecendo uma chave falsa para contornar a validação da OpenAI e redirecione a API para o *localhost*.

```bash
mkdir -p ~/.config/shell_gpt
cat << 'EOF' > ~/.config/shell_gpt/.sgptrc
OPENAI_API_KEY=dummy
API_BASE_URL=http://localhost:11434/v1
OPENAI_BASE_URL=http://localhost:11434/v1
DEFAULT_MODEL=qwen2.5:14b
EOF

```

### 6. Uso Prático

Utilize `sgpt --help` para ver os comandos. Exemplo: a flag `--shell` (ou `-s`) gera comandos de sistema. O `sgpt` não os executa automaticamente; ele exibe o comando e solicita confirmação (Executar `[E]`, Modificar `[M]`, Descrever `[D]` ou Abortar `[A]`).

Exemplos:

```bash
sgpt -s "liste apenas os arquivos ocultos deste diretório organizados por tamanho"
sgpt -s "mostre a quantidade de memória ram livre no sistema de forma bem legível"
sgpt -s "busque pelo arquivo world em todo o sistema ignorando erros de permissão"

```
