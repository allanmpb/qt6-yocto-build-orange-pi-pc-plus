# Gerar Imagem Linux para Orange Pi PC Plus com Qt6 usando Yocto

## Passo a Passo

### 1. Configuração do Ambiente

1. **Instale as dependências:**

Abra o terminal Linux e faça: 
   ```bash
   sudo apt-get update
   sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev xterm lz4
   
2. **Clone o Poky (Yocto Project):**

   git clone -b kirkstone git://git.yoctoproject.org/poky.git
   
   cd poky

3. **Clone o meta-openembedded:**

   git clone -b kirkstone git://git.openembedded.org/meta-openembedded.git

4. **Clone o meta-sunxi:**

   git clone -b kirkstone https://github.com/linux-sunxi/meta-sunxi.git

5. **Clone o meta-qt6:**

   git clone -b 6.4.2 git://code.qt.io/yocto/meta-qt6.git


Para este build estou usando a branch Kirkstone.

### 2. Configuração do Ambiente de Build

1. **Inicialize o ambiente de build:**
   
   source oe-init-build-env
   
2. **Configure os layers:**

Edite o arquivo conf/bblayers.conf para incluir os novos layers:

BBLAYERS ?= " \
  /path/to/poky/meta \
  /path/to/poky/meta-poky \
  /path/to/poky/meta-yocto-bsp \
  /path/to/poky/meta-openembedded/meta-oe \
  /path/to/poky/meta-openembedded/meta-networking \
  /path/to/poky/meta-openembedded/meta-python \
  /path/to/poky/meta-sunxi \
  /path/to/poky/meta-qt6 \
"

Em /path/to -> adicione o caminho para chegar na pasta 'poky'.

Exemplo: 

BBLAYERS ?= " \
  /home/user/poky/meta \
  /home/user/poky/meta-poky \
  /home/user/poky/meta-yocto-bsp \
  /home/user/poky/meta-openembedded/meta-oe \
  /home/user/poky/meta-openembedded/meta-networking \
  /home/user/poky/meta-openembedded/meta-python \
  /home/user/poky/meta-sunxi \
  /home/user/poky/meta-qt6 \
"

3. **Configure a máquina:**

Edite o arquivo conf/local.conf para definir a máquina como orange-pi-pc-plus:

   MACHINE = "orange-pi-pc-plus"
   
Você poderá gerar a mesma imagem para outras placas Orange Pi. Consulte no site da Orange Pi as possibilidades.

4. **Adicione suporte ao Qt6:**

Em local.conf, adicione também: 

   IMAGE_INSTALL:append = "\
    qtbase \
    qtdeclarative \
    qtdeclarative-tools \
    qttools \
    qttranslations-qtbase \
    qttranslations-qtdeclarative \
    \
    ${@bb.utils.contains('DISTRO_FEATURES', 'opengl', 'qt3d', '', d)} \
    qt5compat \
    qtapplicationmanager \
    qtcharts \
    qtconnectivity \
    ${@bb.utils.contains('DISTRO_FEATURES', 'bluetooth', 'qtconnectivity-tools', '', d)} \
    ${@bb.utils.contains('DISTRO_FEATURES', 'opengl', 'qtdatavis3d', '', d)} \
    qtdeviceutilities \
    qthttpserver \
    qtimageformats \
    ${@bb.utils.contains('BBFILE_COLLECTIONS', 'meta-python', 'qtinterfaceframework', '', d)} \
    qtmultimedia \
    qtnetworkauth \
    qtquick3d \
    qtquickdesigner-components \
    qtquicktimeline \
    qtserialbus \
    qtserialbus-tools \
    qtserialport \
    qtshadertools \
    qtsvg \
    qttranslations \
    ${@bb.utils.contains('DISTRO_FEATURES', 'wayland', 'qtwayland', '', d)} \
    qtwebchannel \
    qtwebsockets \
    "

### 3. Construção da Imagem

1. **Inicie a construção da imagem:**

   bitbake core-image-minimal 
   
Você poderá escolher qualquer outro "image recipes".  Como exemplo, optei por usar core-image-minimal.

### 4. Gravação da Imagem no Cartão SD

1. **Localize o arquivo de imagem:**

Após a conclusão da construção, o arquivo de imagem estará localizado no diretório tmp/deploy/images/orange-pi-pc-plus/.

2. **Grave a imagem no cartão SD:**

   sudo dd if=path/to/image.wic of=/dev/sdX bs=4M
   sync
   
Substitua path/to/image.wic pelo caminho do arquivo de imagem gerado e /dev/sdX pelo dispositivo correspondente ao seu cartão SD.

### 5. Configuração Pós-instalação

1. Insira o cartão SD no Orange Pi PC Plus e ligue o dispositivo.

### 6. Dicas Adicionais

1. Documentação e suporte: Consulte a documentação do Yocto Project e os repositórios específicos dos layers para obter informações detalhadas e suporte adicional.
2. Customizações: Dependendo das suas necessidades, você pode querer personalizar receitas ou adicionar pacotes adicionais.
    
Seguindo esses passos, você deve conseguir gerar e instalar uma imagem Linux com Qt6 para o Orange Pi PC Plus usando o Yocto Project.


