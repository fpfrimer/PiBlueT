# Otimizando o desenvolvimento de sistemas embarcados com o Raspberry Pi 

De forma simplificada, pode-se dizer que sistemas embarcados (ou embutidos) são aqueles que possuem um computador completamente encapsulado e dedicado ao funcionamento do aparato que ele controla. Exemplos: eletrodomésticos; dispositivos de armazenamento como SSDs (_Solid State Drives_) e pen-drives; drones; sistemas de aquisição de dados; entre outros.

Com o advento de diversas ferramentas de desenvolvimento para sistemas embarcados, assim como o Arduino e o Raspberry Pi, as possibilidades de novos e criativos projetos se expandiram consideravelmente. O Raspberry Pi é uma série computadores de tamanho reduzido feitos em uma única placa de circuito (_Single Board Computer_ ou SBC). Os modelos principais possuem o tamanho de um cartão de crédito. Além disso, é compatível com diversas distribuições do sistema operacional Linux, como é o caso do Ubuntu e do Raspberry Pi OS.

No entanto, apesar do Raspberry Pi ser um computador completo, onde pode-se conectar teclado, mouse e monitor, nem sempre é o ideal trabalhar diretamente no dispositivo devido a suas limitações ou a forma como ele é utilizado no projeto. Isso fica mais evidente ao se utilizar os modelos mais antigos ou o Raspberry Pi zero, cujo o poder de processamento é inferior. Dessa forma, esse tutorial possui o intuito de auxiliar àqueles que desejam configurar o Raspberry Pi de forma a facilitar o desenvolvimento de sistemas embarcados, permitindo o acesso remoto ao terminal do sistema operacional e o compartilhamento de arquivos.

## Sumário

- [Otimizando o desenvolvimento de sistemas embarcados com o Raspberry Pi](#otimizando-o-desenvolvimento-de-sistemas-embarcados-com-o-raspberry-pi)
  - [Sumário](#sumário)
  - [Objetivo](#objetivo)
  - [Requisitos](#requisitos)
  - [Baixando a imagem do Raspberry Pi OS](#baixando-a-imagem-do-raspberry-pi-os)
  - [Gravando a imagem em um _micro SD card_](#gravando-a-imagem-em-um-micro-sd-card)
  - [Configurando _Wifi_ e SSH](#configurando-wifi-e-ssh)
    - [SSH](#ssh)
    - [_Wifi_](#wifi)
  - [Fazendo o primero _boot_ e acessando o terminal remotamente](#fazendo-o-primero-boot-e-acessando-o-terminal-remotamente)
    - [Verificando a conexão](#verificando-a-conexão)
    - [Estabelecendo conexão remota através de SSH](#estabelecendo-conexão-remota-através-de-ssh)
  - [Configurações iniciais](#configurações-iniciais)
    - [Alterando a senha do usuário pi](#alterando-a-senha-do-usuário-pi)
    - [Atualizando o sistema](#atualizando-o-sistema)
    - [O comando `raspi-config`](#o-comando-raspi-config)
  - [Opcional - Criando uma pasta compartilhada com o _Windows_](#opcional---criando-uma-pasta-compartilhada-com-o-windows)
    - [Samba/CIFS](#sambacifs)
    - [Instalando suporte CIFS/Samba no Raspberry Pi](#instalando-suporte-cifssamba-no-raspberry-pi)
    - [Acessando a pasta no _Windows 10_](#acessando-a-pasta-no-windows-10)
  - [Conclusão](#conclusão)

## Objetivo

O objetivo deste tutorial é ensinar um procedimento para programar o Raspberry Pi remotamente, facilitando o desenvolvimento de sistemas embarcados.

## Requisitos

São requisitos para este tutorial:

- Um __Raspberry pi 3__ (ou superior) ou o __zero w__;
- Um cartão _micro SD_, 8 GB ou maior;
- Fonte de alimentação do _Raspberry Pi_;
- Um computador (_laptop_ ou PC); e
- Conexão com a internet.

## Baixando a imagem do Raspberry Pi OS

Esse tutorial é baseado no _Raspberry Pi OS_. Dessa forma, o primeiro passo é baixar a imagem (arquivo com extensão __img__) do sistema operacional no site oficial. Entre em no endereço:

> [https://www.raspberrypi.org/software/operating-systems/](https://www.raspberrypi.org/software/operating-systems/)
> (acessado em 11/12/2021)

Existirão três opções de download para o _Raspberry Pi OS_:

- _Raspberry Pi OS with desktop and recommended software_ - é a versão mais completa com todos os _softwares_ recomendados já embutidos;
- _Raspberry Pi OS with desktop_ - é a versão básica com a interface de _desktop_ instalada; e
- _Raspberry Pi OS Lite_ - é a versão mais simples sem interface gráfica.

Para a maioria dos projetos a melhor opção é a última (_Raspberry Pi OS Lite_). Por ser a versão mais básica, o desenvolvedor fica livre para escolher o básico que será instalado. No entanto, a escolha da imagem depende da aplicação final do projeto em desenvolvimento. Os procedimentos apresentados aqui funcionam para qualquer uma das opções.

## Gravando a imagem em um _micro SD card_

Enquanto a imagem do _Raspberry Pi OS_ é baixada, pode-se fazer o _download_ da ferramenta para gravar a imagem no cartão SD. Existem diversos _softwares_ que podem ser utilizados para esta tarefa. 

No _Windows_, pode-se citar:

- Win32 Disk Imager (https://sourceforge.net/projects/win32diskimager/);
- balenaEtcher (https://www.balena.io/etcher/); 
- Upswift imgFlasher (https://www.upswift.io/imgflasher/)

Os três aplicativos são bem simples e intuitivos de se utilizar. Basicamente, basta selecionar o arquivo da imagem, o cartão SD e clicar no botão de gravação, conforme explicado no [site officinal do projeto Raspberry](https://www.raspberrypi.org/documentation/installation/installing-images/windows.md).

No caso do Win32 Disk Imager, baixe o instalador [clicando aqui](https://sourceforge.net/projects/win32diskimager/), e siga os passos de instalação.

Abra o aplicativo e uma janela igual ao da Figura 1 se abrirá. Primeiro selecione o arquivo da imagem do _Raspberry Pi OS_ clicando no botão com a imagem de uma pasta (1). Posteriormente, selecione a unidade onde está inserido o cartão de memória (2). Por fim, clique no botão Write (3). O processo de gravação deverá demorar alguns minutos.

|__Figura 1 - Janela do Win32 Disk Imager__|
|---|
|![Win32 Disk Imager](figs/win32.png)|

Ao finalizar a gravação ainda não remova o cartão do computador, pois alguns procedimentos deverão ser feitos, para configurar _Wifi_ e acesso remoto via SSH.

Caso o usuário utilize outro sistema operacional, como o Linux ou Mac OS, pode-se seguir os procedimentos apresentados no próprio site do Raspberry Pi (https://www.raspberrypi.org/documentation/installation/installing-images/).

## Configurando _Wifi_ e SSH

Após a gravação da imagem do sistema operacional no cartão de memória, pode-se habilitar a rede _Wifi_ e o acesso remoto ao Raspberry PI através de SSH (_Secure Shell_). 

### SSH

Crie um arquivo nomeado  `ssh` (sem extensão) na raiz do cartão de memória, conforme a Figura 2. Note que a unidade foi nomeada como `boot`. O arquivo pode ser criado diretamente através do Bloco de Notas do Windows através da opção "Salvar como" e alterando o tipo para "Todos os tipos (\*.\*)", assim como mostra a Figura 3. Atenção: o conteúdo do arquivo deve estar vazio.

|__Figura 2 - Arquivo para habilitar SHH__|
|---|
|![ssh](figs/ssh.png)|

|__Figura 3 - Salvando o arquivo SSH através do bloco de notas__|
|---|
|![ssh2](figs/notepad.png)|

### _Wifi_

Abra novamente o bloco de notas, copie e cole o seguinte código:

```
country=BR
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
	ssid="nomeWifi"
	psk="senha"
	key_mgmt=WPA-PSK
}
```

Altere: o país (`country`) se for necessário, o nome da rede _Wifi_ em `ssid` e a senha em `psk`. Atenção: mantenha as aspas.

Salve o arquivo na raiz do cartão de memória com o nome `wpa_supplicant.conf`, assim como indicado na Figura 4. 

|__Figura 4 - Salvando o arquivo `wpa_supplicant.conf` através do bloco de notas__|
|---|
|![ssh2](figs/wpa.png)|

## Fazendo o primero _boot_ e acessando o terminal remotamente

Insira o cartão de memória no Raspberry Pi e ligue-o à energia através do cabo USB. Não é necessário conectar monitores, teclado ou mouse. Aguarde alguns minutos, até as luzes dos LEDs pararem de piscar.

Certifique-se que seu computador esteja conectado na mesma rede que o Raspberry Pi. No windows abra o Prompt de comando (cmd) ou o Windows PowerShell. Ambos podem ser acessados pressionando Win+R (botão do _Windows_ + a tecla R) e digitando cmd ou PowerShell, respectivamente. 

No Linux ou Mac OS, simplesmente abra o terminal.

### Verificando a conexão

Para verificar se o Raspberry Pi se conectou corretamente na rede _Wifi_ entre com o comando `ping`:

```
ping raspberrypi
```

O resultado do comando no PowerShell do _Windows_ deve ser semelhante à Figura 5. No terminal do Linux pode ser necessário pressionar Ctrl+C para finalizar o comando.

|__Figura 5 - Resultado do comando no PowerShell do *Windows*__|
|---|
|![ping](figs/ping.png)|

Basicamente, __ping__ é um comando usado para medir o tempo de resposta da conexão do seu computador com outros dispositivos na rede local ou Internet. O nome `raspberrypi`, que aparece no comando, é o nome de rede (_hostname_) padrão do Raspberry Pi. 

Caso o comando retorne algum erro, recomenda-se procurar o endereço IP do Raspberry Pi. Isso pode ser feito diretamente na página de configurações do roteador ou através de _softwares_, como é o caso do [Adafruit Pi Finder](https://github.com/adafruit/Adafruit-Pi-Finder/releases). Obtendo o endereço IP do dispositivo, repita o comando `ping`, como no exemplo:

```
ping 192.168.1.8
```

O número IP `192.168.1.8` é apenas um exemplo e deve ser substituído pelo endereço encontrado. Caso o Raspberry Pi não seja encontrado na rede, é preciso verificar a configuração de rede ou o arquivo `wpa_supplicant.conf`.

### Estabelecendo conexão remota através de SSH

Finalmente, para estabelecer uma conexão com o Raspberry Pi, entre com o comando `ssh`, utilizando o _hostname_ ou o endereço IP:

```
ssh pi@raspberrypi
```
ou

```
ssh pi@<entre com o IP>
```

Na primeira vez, pode ser necessário aceitar a conexão, assim como mostra a Figura 6. Dessa forma, digite _yes_ e tecle Enter para aceitar.

|__Figura 6 - Primeira conexão__|
|---|
|![con](figs/ssh2.png)|

Ao aceitar, será pedido a senha do usuário `pi` que, por padrão, é `raspberry`. Entre com a senha e você terá acesso remoto ao sistema Linux do Raspberry Pi OS, assim como pode ser visto na Figura 7.

|__Figura 7 - Acesso remoto ao sistema Linux do Raspberry Pi OS__|
|---|
|![con](figs/loginSsh.png)|

## Configurações iniciais 

O Raspberry Pi OS vem com algumas configurações por padrão e pode ser interessante alterar algumas delas. Primeiramente, é recomendável alterar a senha do sistema.

### Alterando a senha do usuário pi 

É importante alterar a senha do usuário `pi`, pois esta vem por padrão como `raspberry`. Para alterar a senha entre com o seguinte comando:

```
sudo passwd pi
```

Entre com a nova senha duas vezes.

### Atualizando o sistema

É sempre importante atualizar o sistema. Dessa forma, entre com os seguintes comandos na sequência:

```
sudo apt-get dist-upgrade
sudo apt-get update
sudo apt-get upgrade
```

O processo de atualização pode demorar um pouco.

### O comando `raspi-config`

Entre com o seguinte comando para acessar diversas configurações do Raspberry Pi OS:

```
sudo raspi-config
```

O resultado do comando pode ser visto na Figura 8. Nessa ambiente, é possível ajustar diversas configurações. Neste tutorial serão citadas as mais importantes. 

|__Figura 8 - Interface do raspi-config__|
|---|
|![raspi-config](figs/raspi-config.png)|

Em _System Options_ é possível: 
- mudar a configuração de _Wifi_ alterando a rede e a senha;
- Ajustar a saída de audio;
- Mudar a senha do usuário `pi` (essa é uma segunda alternativa);
- Mudar o nome de rede, _hostname_, do Raspberry Pi;
- Entre outras opções.

Em _Interface options_, é possível habilitar o acesso remoto de diversos periféricos do Raspberry Pi, como é o caso das portas SPI (_Serial Peripheral Interface_), I2C (_Inter-Integrated Circuit_), GPIO (_General Purpose Input Output_), serial e _1-wire_. Também é uma forma de habilitar o VNC caso queira acessar a área de trabalho remotamente. Dessa forma, o usuário pode habilitar aqui os dispositivos que necessitará em seus projetos.

Seguindo estas instruções, o usuário terá acesso quase que total ao Raspberry Pi OS. É possível criar códigos, compilar, instalar aplicações, configurar o sistema e atualizar. Obviamente, todas essas ações devem ser feitas por linha de comando. Dessa forma, é importante ler, pesquisar e se aprofundar no assunto.

## Opcional - Criando uma pasta compartilhada com o _Windows_

A criação de uma pasta compartilhada pode ajudar no desenvolvimento de projetos, visto que os códigos podem ser escritos em editores mais sofisticados em um _laptop_ ou PC. Além disso, os códigos também podem ser compilados remotamente, utilizando um _hardware_ mais potente.

### Samba/CIFS

Samba é a implementação do protocolo SMB/CIFS utilizado pelo _Windows_ para possibilitar acesso remoto à arquivos, impressoras, portas seriais, entre outros.

Essa seção irá mostrar como compartilhar uma pasta do Raspberry Pi com o _Windows_. As instruções mostradas aqui são baseadas no tutorial disponibilizado no [site oficial](https://www.raspberrypi.org/documentation/remote-access/samba.md) do projeto Raspberry Pi.

### Instalando suporte CIFS/Samba no Raspberry Pi

Primeiramente, atualize os repositórios do Raspberry Pi:

```
sudo apt-get update
```

Instale os pacotes necessários:

```
sudo apt install samba samba-common-bin smbclient cifs-utils
```

Crie uma pasta, de preferencia no diretório `home`:

```
cd ~
mkdir compartilhado
```

Adicione permissão de leitura e escrita na pasta:

```
chmod 777 compartilhado
```

Posteriormente, é preciso "dizer" ao samba para compartilhar esta pasta, utilizando o arquivo de configuração:

```
sudo nano /etc/samba/smb.conf
```

Este comando abrirá o arquivo `smb.conf` no editor nano. Dessa forma, adicione o seguinte texto ao final do arquivo:

```
[compartilhado]
    path = /home/pi/compartilhado
    read only = no
    public = yes
    writable = yes
```

No mesmo arquivo, procure uma linha `workgroup` e, se necessário, mude o nome do grupo de trabalho da rede do _Windows_

Salve e feche o arquivo e reinicie o samba:

```
sudo service smbd restart
```

### Acessando a pasta no _Windows 10_

Para acessar a esta pasta no _Windows_ 10, abra o explorador de arquivos (Win+E), clique em "Este Computador" e depois em "Mapear unidade de rede" que fica na parte de cima da janela, assim como mostra a Figura 9.

|__Figura 9 - Mapeando unidade de rede__|
|---|
|![mapear](figs/mapear.png)|

Abrirá uma janela igual ao da Figura 10. Dessa forma, escolha a letra da unidade e entre com o endereço da pasta na rede. 

|__Figura 10 - Mapeando unidade de rede__|
|---|
|![mapear2](figs/mapear2.png)|

Neste exemplo, o endereço da pasta é `\\raspberrypi\compartilhado`. Alternativamente, também é possível substituir `raspberrypi` pelo endereço IP. Se tudo estiver certo, entre com o nome do usuário `pi` (não é necessário entrar com a senha). Isso dará acesso direto à pasta criada no Raspberry Pi, assim como pode ser visto na Figura 11.

|__Figura 11 - Pasta compartilhada__|
|---|
|![pasta](figs/pasta.png)|

Agora que a pasta está criada é possível editar os códigos diretamente de um PC convencional, assim como transferir arquivos.

## Conclusão

Este tutorial mostrou como otimizar o Raspberry Pi para o desenvolvimento de projetos de sistemas embarcados. Com os procedimentos apresentados, é possível acessar o terminal remotamente e realizar a troca de arquivos através de uma pasta compartilhada.
