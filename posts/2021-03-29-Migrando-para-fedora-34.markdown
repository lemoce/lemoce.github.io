---
title: 'Dicas para migrar do Ubuntu para o Fedora 34'
description: 'Fedora: é como se fosse o retorno a casa antiga'
tags: linux, fedora, instalacao, bluetooth, a2dp
---

Essa semana foi marcada por um video do Diolinux com o título "[Testei o Fedora Linux por uma semana, veja o que eu achei.](https://www.youtube.com/watch?v=IOEACWRrEnk)".
Basicamente, ele elogiou o Fedora 33 com os seus defeitos e suas características. Eu sou um usuário
antigo de Fedora e Red Hat, que precisei mudar quando minha empresa decidiu ir para os Debian *flavors*
como Ubuntu. A última versão Ubuntu que usei foi o [Ubuntu Studio](https://ubuntustudio.org/). É muito fácil
configurar o Ubuntu com os aplicativos da minha empresa, mas um *glitch* no login minou a minha experiencia com
o Ubuntu.

Assim, na semana passada, eu decidi instalar o Fedora 33 no GPD Pocket. A instalação aconteceu sem problemas e
usei um pouco. Certo, então dá para instalar na minha máquina principal: um **Lenovo Legion Y530**. Não houve
nenhuma supresa. A única coisa mandatória é o blacklist do módulo *ideapad_laptop*. Mas, isso é para todas as
distribuições Linux, caso contrário, temos problemas com o Wifi e Bluetooth. Aproveitei e mudei o pulseaudio
para o [pipewire](https://pipewire.org/). Funcionou. Por último, eu apontei os reposirórios para 34 Beta.
Atualizei sem nenhum problema posterior.

Nesse instante, eu clonei a partição do Ubuntu, baixei a iso do Fedora 34 Beta e instalei na partição principal
sobrescrevendo o Ubuntu Studio. A instalação ocorreu sem problema algum, nesse ponto, eu recomendo uma atualização
do Linux, a versão Beta tem a tendência para lançar muitos pacotes antes do *final release*. No aplicativo
*Software*, eu apontei os repositórios da Nvidia e da Steam e executei os seguintes comandos ([tutorial](https://docs.fedoraproject.org/en-US/quick-docs/how-to-set-nvidia-as-primary-gpu-on-optimus-based-laptops/)):

``` bash
sudo dnf update --refresh
sudo dnf install gcc kernel-headers kernel-devel akmod-nvidia xorg-x11-drv-nvidia xorg-x11-drv-nvidia-libs xorg-x11-drv-nvidia-libs.i686
```

Aqui, basicamente, é feito um restart. Tem uma configuração para habilitar a placa da Nvidia como primária.
Segui essa configuração:

``` bash
sudo cp -p /usr/share/X11/xorg.conf.d/nvidia.conf /etc/X11/xorg.conf.d/nvidia.conf
```

No final de cada *Section* do *nvidia.conf*, acrescentar a linha ```Option "PrimaryGPU" "yes"```.

Após, eu decidi criar dois script: um para habilitar e outro para desabilitar. 
Arquivo /usr/sbin/change_to_nvidia.sh
```
#!/usr/bin/env bash

sed -i -E 's/^(\s+)(# )(Option.*Primary.*)$/\1\3/' /etc/X11/xorg.conf.d/nvidia.conf
```

Arquivo /usr/sbin/change_to_intel.sh
```
#!/usr/bin/env bash

sed -i -E 's/^(\s+)(Option.*Primary.*)$/\1# \2/' /etc/X11/xorg.conf.d/nvidia.conf
```

Conceda as permissões de execução, pronto.

Agora tem um passo que é *cpu_governor*. Esse pode ficar em *powersave* ou *performance*. O modo *performace*
é bom para tarefas que demanda mais processamento. Para fazer isso, foi criado mais dois scripts para
modificar esse parâmetro:
Arquivo /usr/sbin/change_to_powersave.sh
´´´
#!/usr/bin/env bash

for cpu in `ls /sys/devices/system/cpu | egrep "cpu[[:digit:]]+"`
do
    echo powersave > /sys/devices/system/cpu/$cpu/cpufreq/scaling_governor
done
´´´

Arquivo /usr/sbin/change_to_performance.sh
´´´
#!/usr/bin/env bash

for cpu in `ls /sys/devices/system/cpu | egrep "cpu[[:digit:]]+"`
do
    echo performance > /sys/devices/system/cpu/$cpu/cpufreq/scaling_governor
done
´´´

Parte final, é a configuração do bluetooth do meu aparelho de som. Eu tenho um [Yamaha MCR-B142](https://uk.yamaha.com/files/download/other_assets/6/328816/MCR-B142_om_AB-1.pdf).
Nesse modelo, a especificação fala que é aceito 2 codecs SBC ou AAC. Apesar que default o Fedora já está
configurado para isso, eu não obtive som com uma mensagem muito estranha da console:

```
# journalctl --user -u pipewire
Mar 29 11:03:26 fedora.lemoce.org systemd[4951]: Started Multimedia Service.
Mar 29 11:03:27 fedora.lemoce.org pipewire-media-session[5639]: oFono: Registering Profile /Profile/ofono failed
Mar 29 11:03:27 fedora.lemoce.org pipewire-media-session[5639]: hsphfpd: Registering application /Profile/hsphfpd/manager failed
Mar 29 11:04:01 fedora.lemoce.org pipewire-media-session[5639]: could not find device /org/bluez/hci0/dev_00_1F_47_02_FA_15
Mar 29 11:04:01 fedora.lemoce.org pipewire-media-session[5639]: no device found for transport
Mar 29 11:04:03 fedora.lemoce.org pipewire-media-session[5639]: native: Unregistering Profile /Profile/HSPHS failed
Mar 29 11:04:03 fedora.lemoce.org pipewire-media-session[5639]: native: Unregistering Profile /Profile/HFPAG failed
Mar 29 11:05:53 fedora.lemoce.org pipewire-media-session[5639]: Failed to release transport /org/bluez/hci0/dev_00_1F_47_02_FA_15/fd1: Input/output error
Mar 29 11:05:53 fedora.lemoce.org pipewire-media-session[5639]: bluez5-device: failed to switch codec (-19), setting fallback profile
Mar 29 11:05:56 fedora.lemoce.org pipewire-media-session[5639]: unexpected call, connected_profiles:00000000 connected:1
Mar 29 11:07:19 fedora.lemoce.org pipewire-media-session[5639]: Transport Acquire() failed for transport /org/bluez/hci0/dev_00_1F_47_02_FA_15/sep4/fd2 (Input/output error)
Mar 29 11:07:19 fedora.lemoce.org pipewire-media-session[5639]: audioadapter 0x55b038b2b808: can't send command: Input/output error
Mar 29 11:08:41 fedora.lemoce.org pipewire-media-session[5639]: a2dp-sink 0x55b0389320d8: error 24
Mar 29 11:08:41 fedora.lemoce.org pipewire-media-session[5639]: Failed to release transport /org/bluez/hci0/dev_00_1F_47_02_FA_15/fd3: Method "Release" with signature "" on interface "org.bluez.MediaTransport1" doesn't exist
Mar 29 11:17:17 fedora.lemoce.org pipewire-media-session[5639]: a2dp-sink 0x55b038932148: error 24
Mar 29 11:17:17 fedora.lemoce.org pipewire-media-session[5639]: Failed to release transport /org/bluez/hci0/dev_00_1F_47_02_FA_15/fd4: Method "Release" with signature "" on interface "org.bluez.MediaTransport1" doesn't exist
Mar 29 11:39:19 fedora.lemoce.org pipewire-media-session[5639]: native: Unregistering Profile /Profile/HSPHS failed
Mar 29 11:39:19 fedora.lemoce.org pipewire-media-session[5639]: native: Unregistering Profile /Profile/HFPAG failed
Mar 29 11:39:19 fedora.lemoce.org systemd[4951]: Stopping Multimedia Service...
Mar 29 11:39:19 fedora.lemoce.org systemd[4951]: pipewire.service: Deactivated successfully.
Mar 29 11:39:19 fedora.lemoce.org systemd[4951]: Stopped Multimedia Service.
Mar 29 11:39:19 fedora.lemoce.org systemd[4951]: pipewire.service: Consumed 12.429s CPU time.
```

No arquivo */etc/pipewire/media-session.d/bluez-monitor.conf*, tem um comentário muito interessante:

´´´
    # Enabled headset roles (default: [ hsp_hs hfp_ag ]), this
    # property only applies to native backend. Currently some headsets
    # (Sony WH-1000XM3) are not working with both hsp_ag and hfp_ag
    # enabled, disable either hsp_ag or hfp_ag to work around it.
´´´

Quer dizer que tem casos onde eu devo escolher uma das duas opções, ao invés da default. O arquivo ficou
assim:

```
properties = {
    #bluez5.sbc-xq-support = true

    # Enabled headset roles (default: [ hsp_hs hfp_ag ]), this
    # property only applies to native backend. Currently some headsets
    # (Sony WH-1000XM3) are not working with both hsp_ag and hfp_ag
    # enabled, disable either hsp_ag or hfp_ag to work around it.
    #
    # Supported headset roles: hsp_hs (HSP Headset),
    #                          hsp_ag (HSP Audio Gateway),
    #                          hfp_hf (HFP Hands-Free),
    #                          hfp_ag (HFP Audio Gateway)
    #bluez5.headset-roles = [ hsp_hs hsp_ag hfp_hf hfp_ag ]
    bluez5.headset-roles = [ hfp_ag ]

    # Enabled A2DP codecs (default: all).
    bluez5.codecs = [ sbc ]
}
```

Eu decidi restringir o *codecs* e o *headset-roles*. E *voi lá*, tenho som bluetooth via pipewire. No próximo,
eu vou mostrar como configurar o Safenet eToken e como utilizar o eToken com os repositórios git.
