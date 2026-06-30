## Configurar CPU Governor para Performance Permanente (Gentoo + OpenRC)

O kernel Linux gerencia o clock e o consumo de energia da CPU via *governors*. Por padrão, muitos sistemas utilizam o perfil `powersave`. Alterações diretas em `/sys/devices/system/cpu/cpu*/cpufreq/scaling_governor` funcionam imediatamente, mas são apagadas ao reiniciar, pois o `/sys` (sysfs) reside na RAM.

A maneira correta e integrada ao OpenRC para tornar a mudança permanente é utilizando o serviço `cpupower`.

### 1. Instalação do utilitário

Instale a ferramenta oficial do kernel para gerenciamento de energia:

```bash
sudo emerge --ask sys-power/cpupower

```

### 2. Teste temporário (Opcional)

Para aplicar o perfil de alto desempenho imediatamente na sessão atual:

```bash
sudo cpupower frequency-set -g performance

```

### 3. Configuração do serviço no OpenRC

Defina as opções de inicialização (`START_OPTS`) para aplicar o perfil de máximo desempenho, e as opções de parada (`STOP_OPTS`) para retornar ao modo econômico durante o desligamento.

```bash
echo -e 'START_OPTS="--governor performance"\nSTOP_OPTS="--governor powersave"' | sudo tee /etc/conf.d/cpupower

```

### 4. Habilitar e iniciar o serviço

Adicione o serviço ao nível de execução (*runlevel*) `default` para persistência no boot e inicialize-o imediatamente:

```bash
sudo rc-update add cpupower default
sudo rc-service cpupower start

```
