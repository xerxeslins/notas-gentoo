## Compilar pacotes com LLVM/Clang

### Definições

* **GCC (GNU Compiler Collection):** Tradutor histórico e padrão do Linux. Converte código C/C++ em código executável.
* **LLVM:** Infraestrutura moderna e modular de compilação. Otimiza o código em várias etapas intermediárias.
* **Clang:** Interface (frontend) do LLVM para C/C++. Substitui o GCC lendo o código e repassando ao LLVM.

### Vantagens

Tempo de compilação reduzido, menor consumo de RAM em pacotes pesados e mensagens de erro estruturadas. O ganho de desempenho na execução diária é marginal.

### Instalação

```bash
sudo emerge --ask --verbose --quiet llvm-core/clang llvm-core/llvm llvm-core/lld

```

### Configuração do Ambiente

Crie o arquivo de ambiente para o Clang utilizando o linker `lld`:

```bash
sudo mkdir -p /etc/portage/env /etc/portage/package.env

cat << 'EOF' | sudo tee /etc/portage/env/clang.conf
CC="clang"
CXX="clang++"
LDFLAGS="${LDFLAGS} -fuse-ld=lld"
EOF

```

### Ativação por pacote

Ative o ambiente para pacotes específicos mapeando o arquivo `clang.conf` (exemplo para o Chromium):

```bash
echo "www-client/chromium clang.conf" | sudo tee -a /etc/portage/package.env/clang

```
