# Modificando as configurações de display na toradex IMX6
> Como mudar as especificações de exibição de display na toradex IMX6 com a BSP versão 5.
#

Durante o processo você irá adicionar um estrutura com as especificações do display na kernel, modificar uma Device tree Oveerlay(DTO) e compilar ambas.

## Pré-requisitos

### Preparar a Host para Cross-Compilação

-   Prepare o pc host com o GNU para a cross-compilação como indicado no [tutorial] da toradex. 

-   Instale o Device Tree Compiler(DTC), esta logo após o GNU no mesmo tutorial.

_Não esquecer de fazer os exports sempre que abrir um terminal novo._

#

## Configuração da Kernel
Baixe o source code da kernel usando git:

```
$ mkdir -p ~/workdir
$ cd ~/workdir
$ git clone -b (branch) git://git.toradex.com/linux-toradex.git
```

Substitua o (branch) pelo kernel Git Branch exata da placa que esta usando, no link [tutorial] é possivel encontrar a correta para sua placa.

### Setando a configuração da kernel padrão
```
$ cd ~/workdir/linux-toradex
$ make (defconfig)
```
Substitua o (defconfig) pela kernel Configuration da placa que esta usando tambem encontrada no [tutorial].

### Adicionando especificações

Adicione a estrutura com as especificações no driver de video do framebuffer. 

Abra o arquivo mxc_lcdif.c no editor de texto da sua escolha em /linux-toradex/drivers/video/fbdev/mxc/mcx_lcdif.c você deve encontrar algo assim:

![](./mxc_lcdif.png)

Na struct fb_videomode adicione um modo com as especificações do seu display seguindo esta estrutura:

```
struct fb_videomode {
const char *name;
u32 refresh; /* optional */
u32 xres;
u32 yres;
u32 pixclock;
u32 left_margin;
u32 right_margin;
u32 upper_margin;
u32 lower_margin;
u32 hsync_len;
u32 vsync_len;
u32 sync;
u32 vmode;
u32 flag;
};
```

Recomendo utilizar um dos modos como exemplo. A sua struct deve ficar parecida com esta:

![](esp_ex.png)

Utilizarei a "METROVAL-WVGA" para os exemplos seguintes.

## Compilação da Kernel

Estarei utilizando como exemplo para a compilação a placa Apalis IMX6. No [tutorial] da toradex é possivel encontrar informações mais detalhadas sobre os outros modelos.

Para compilar a kernel e o device tree:

```
$ make -j$(nproc) zImage LOADADDR=10008000 2>&1 | tee build.log
$ make DTC_FLAGS="-@" (device-tree).dtb
```

Substitua o (device-tree) pela da placa que esta usando, no link [tutorial] é possivel encontrar a correta para sua placa.

A chamada $(nproc) é para a quantidade de nucleos do processador

## Configuração para Desenvolvimento


## Histórico de lançamentos

* 0.2.1
    * MUDANÇA: Atualização de docs (código do módulo permanece inalterado)
* 0.2.0
    * MUDANÇA: Remove `setDefaultXYZ()`
    * ADD: Adiciona `init()`
* 0.1.1
    * CONSERTADO: Crash quando chama `baz()` (Obrigado @NomeDoContribuidorGeneroso!)
* 0.1.0
    * O primeiro lançamento adequado
    * MUDANÇA: Renomeia `foo()` para `bar()`
* 0.0.1
    * Trabalho em andamento



[tutorial]: https://developer.toradex.com/linux-bsp/5.0/os-development/build-u-boot-and-linux-kernel-from-source-code/#32bitarm
