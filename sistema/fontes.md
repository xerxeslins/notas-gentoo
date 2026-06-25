## Fontes Microsoft

Instale o pacote das fontes Core Fonts:

```bash
sudo emerge --ask media-fonts/corefonts
```

Ele instala fontes clássicas como:

* Arial
* Times New Roman
* Verdana
* Tahoma
* Courier New
* Comic Sans MS
* Georgia
* Trebuchet MS

Atualize o cache e verifique:

```bash
sudo fc-cache -fv
fc-list | grep Arial
```

## Fontes Microsoft modernas (alternativas para Calibri, Cambria, etc.)

```bash
sudo emerge --ask media-fonts/crosextrafonts-caladea media-fonts/crosextrafonts-carlito
```

## Fontes extras recomendadas

```bash
sudo emerge --ask \
    media-fonts/dejavu \
    media-fonts/noto \
    media-fonts/liberation-fonts \
    media-fonts/ubuntu-font-family
```

Para emojis:

```bash
sudo emerge --ask media-fonts/noto-emoji
```
