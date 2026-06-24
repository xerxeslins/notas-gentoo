# Configuração do make.conf

Arquivo principal do Portage (`/etc/portage/make.conf`).

## Arquivo Completo

```make
COMMON_FLAGS="-O2 -pipe -march=native"
CFLAGS="${COMMON_FLAGS}"
CXXFLAGS="${COMMON_FLAGS}"
FCFLAGS="${COMMON_FLAGS}"
FFLAGS="${COMMON_FLAGS}"
LC_MESSAGES=C.UTF-8
USE="dist-kernel -webengine jumbo-build"
GRUB_PLATFORMS="efi-64"
FEATURES="getbinpkg binpkg-request-signature"
EMERGE_DEFAULT_OPTS="--usepkg --binpkg-respect-use=y --binpkg-changed-deps=y"
VIDEO_CARDS="amdgpu radeonsi"
ACCEPT_LICENSE="*"
MAKEOPTS="-j16 -l14"

```

## Explicação das Variáveis

* **COMMON_FLAGS (`-march=native`)**: Otimiza compilação para o processador local.
* **LC_MESSAGES**: Força saída de mensagens em inglês (UTF-8).
* **USE**:
* `dist-kernel`: Usa kernel distribuído/gerenciado pelo Portage.
* `-webengine`: Evita compilar o qtwebengine.
* `jumbo-build`: Agrupa arquivos-fonte para acelerar compilações pesadas.


* **GRUB_PLATFORMS**: Habilita suporte a UEFI 64 bits.
* **FEATURES / EMERGE_DEFAULT_OPTS**: Habilita e força o uso de pacotes binários.
* **VIDEO_CARDS**: Define suporte a drivers de vídeo AMD.
* **ACCEPT_LICENSE**: Aceita todas as licenças.
* **MAKEOPTS**: Permite 16 processos paralelos de compilação, limitados a uma carga de sistema (load average) de 14.
