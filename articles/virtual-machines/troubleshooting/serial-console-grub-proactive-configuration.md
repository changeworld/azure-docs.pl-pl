---
title: Proaktywna konfiguracja GRUB w usłudze Azure Serial Console| Dokumenty firmy Microsoft
description: Skonfiguruj GRUB w różnych dystrybucjach umożliwiając dostęp do trybu pojedynczego użytkownika i trybu odzyskiwania na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: vilibert
ms.openlocfilehash: a154ab4742f0d0d7acae0376bcf894bc2b62b4cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186931"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Proaktywne zapewnienie dostępu do GRUB i sysrq może zaoszczędzić dużo czasu przestoju

Dostęp do konsoli szeregowej i GRUB w większości przypadków skracają czas odzyskiwania maszyny wirtualnej IaaS Linux. GRUB oferuje opcje odzyskiwania, które w przeciwnym razie zajęłoby więcej czasu, aby odzyskać maszynę wirtualną. 


Powody, dla których można wykonać odzyskiwanie maszyny Wirtualnej, jest wiele i można je przypisać do scenariuszy, takich jak:

   - Uszkodzone systemy plików/jądro/MBR (główny rekord rozruchowy)
   - Nieudane uaktualnienia jądra
   - Nieprawidłowe parametry jądra GRUB
   - Nieprawidłowe konfiguracje fstab
   - Konfiguracje zapory
   - Utracone hasło
   - Pliki konfiguracji sshd zniekształcone
   - Konfiguracje sieci

 Wiele innych scenariuszy, jak opisano [tutaj](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Sprawdź, czy można uzyskać dostęp do grub i konsoli szeregowej na maszynach wirtualnych wdrożonych na platformie Azure. 

Jeśli jesteś nowym użytkownikem konsoli szeregowego, zapoznaj się z [tym linkiem](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Przed wprowadzeniem zmian upewnij się, że tworzysz kopie zapasowe plików

Obejrzyj ten film poniżej, aby zobaczyć, jak można szybko odzyskać maszynę wirtualną z systemem Linux po uzyskaniu dostępu do GRUB

[Odzyskiwanie wideo maszyny Wirtualnej z systemem Linux](https://youtu.be/KevOc3d_SG4)

Istnieje wiele metod ułatwiające odzyskiwanie maszyn wirtualnych z systemem Linux. W środowisku chmury ten proces był wyzwaniem.
Stale postępuje się w zakresie narzędzi i funkcji zapewniających szybkie odzyskanie usług.

Za pomocą konsoli szeregowej platformy Azure możesz wchodzić w interakcje z maszyną wirtualną z systemem Linux tak, jakbyś był na konsoli systemu.

Można manipulować wieloma plikami konfiguracyjnymi, w tym w jaki sposób jądro zostanie uruchomiony. 

Bardziej doświadczeni administratorzy systemu Linux/Unix sys docenią **tryby pojedynczego użytkownika** i **awaryjne,** które są dostępne za pośrednictwem konsoli szeregowej platformy Azure, dzięki czemu wymiana dysku i usuwanie maszyn wirtualnych dla wielu scenariuszy odzyskiwania są zbędne.

Metoda odzyskiwania zależy od występującego problemu, na przykład zgubione lub zagubione hasło można zresetować za pomocą opcji portalu Azure - > **Resetowanie hasła**. Funkcja **Resetowanie hasła** jest znana jako rozszerzenie i komunikuje się z agentem gościa systemu Linux.

Inne rozszerzenia, takie jak Skrypt niestandardowy są dostępne jednak te opcje wymagają, aby **waagent** Linuksa był w stanie i w dobrym stanie, co nie zawsze ma miejsce.

![stan agenta](./media/virtual-machines-serial-console/agent-status.png)


Zapewnienie dostępu do usługi Azure Serial Console i GRUB oznacza, że zmiana hasła lub nieprawidłowa konfiguracja może zostać naprawiona w ciągu kilku minut, a nie godzin. Można nawet wymusić na maszynie Wirtualnej uruchomienie z alternatywnego jądra, jeśli masz wiele jąder na dysku w scenariuszu, w którym podstawowe jądro zostanie uszkodzone.

![multi jądro](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Sugerowana kolejność metod odzyskiwania:

- Konsola szeregowa platformy Azure

- Wymiana dysku – może być zautomatyzowana za pomocą:

   - [Skrypty odzyskiwania powłoki zasilania](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [skrypty odzyskiwania bash](https://github.com/sribs/azure-support-scripts)

- Starsza metoda

## <a name="disk-swap-video"></a>Wideo wymiany dysku:

Jeśli nie masz dostępu do GRUB obejrzeć [ten](https://youtu.be/m5t0GZ5oGAc) film i zobaczyć, jak można łatwo zautomatyzować procedurę wymiany dysku, aby odzyskać maszynę wirtualną

## <a name="challenges"></a>Wyzwania:

Nie wszystkie maszyny wirtualne platformy Azure z systemem Linux są domyślnie skonfigurowane dla dostępu grub i nie są one wszystkie skonfigurowane do przerwania z polecenia sysrq. Niektóre starsze dystrybucje, takie jak SLES 11, nie są skonfigurowane do wyświetlania monitu logowania w konsoli szeregowej platformy Azure

W tym artykule przejrzymy różne dystrybucje Linuksa i konfiguracje dokumentów, jak udostępnić GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Jak skonfigurować maszynę wirtualną systemu Linux do akceptowania kluczy SysRq
Klucz sysrq jest domyślnie włączony w niektórych nowszych dystrybucjach Linuksa, chociaż w innych może być skonfigurowany do akceptowania wartości tylko dla niektórych funkcji SysRq.
W starszych dystrybucjach może być całkowicie wyłączona.

Funkcja SysRq jest przydatna do ponownego uruchamiania rozbitej lub zawieszonej maszyny Wirtualnej bezpośrednio z konsoli szeregowej platformy Azure, również pomocna w uzyskaniu dostępu do menu GRUB, alternatywnie ponowne uruchomienie maszyny wirtualnej z innego okna portalu lub sesja ssh może spowodować upuszczenie bieżącego połączenia konsoli upływający w ten sposób limity czasu GRUB, do których są używane do wyświetlania menu GRUB.
Maszyna wirtualna musi być skonfigurowana tak, aby akceptowała wartość 1 dla parametru jądra, który umożliwia wszystkie funkcje sysrq lub 128, co umożliwia ponowne uruchomienie/wyłączanie


[Włączanie wideo sysrq](https://youtu.be/0doqFRrHz_Mc)


Aby skonfigurować maszynę wirtualną do akceptowania ponownego uruchomienia za pomocą poleceń SysRq w witrynie Azure portal, należy ustawić wartość 1 dla parametru jądra kernel.sysrq

Aby ta konfiguracja utrwaliła ponowne uruchomienie, dodaj wpis do pliku **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Aby dynamicznie skonfigurować parametr jądra

`sysctl -w kernel.sysrq=1`

Jeśli nie masz dostępu **do roota** lub sudo jest uszkodzony, nie będzie możliwe skonfigurowanie sysrq z monitu powłoki.

Można włączyć sysrq w tym scenariuszu przy użyciu witryny Azure portal. Ta metoda może być korzystna, jeśli plik **sudoers.d/waagent** został uszkodzony lub został usunięty.

Za pomocą usługi Azure portal Operations -> Uruchom polecenie -> Funkcja RunShellScript, wymaga, aby proces waagent był zdrowy, można następnie wstrzyknąć to polecenie, aby włączyć sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Jak pokazano ![poniżej: włącz sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Po zakończeniu możesz spróbować uzyskać dostęp do **sysrq** i zobaczyć, że możliwy jest ponowny rozruch.

![włącz sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Wybierz **polecenie Uruchom ponownie** i Wyślij polecenie **SysRq**

![włącz sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

System powinien rejestrować komunikat resetowania, taki jak ten

![włącz sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Konfiguracja Ubuntu GRUB

Domyślnie powinieneś mieć dostęp do GRUB, przytrzymując klawisz **Esc** podczas rozruchu maszyny Wirtualnej, jeśli menu GRUB nie jest prezentowane, można wymusić i zachować menu GRUB na ekranie w konsoli szeregowej platformy Azure przy użyciu jednej z tych opcji.

**Opcja 1** - Wymusza wyświetlanie GRUB na ekranie 

Zaktualizuj plik /etc/default/grub.d/50-cloudimg-settings.cfg, aby utrzymać menu GRUB na ekranie dla określonego limitu czasu.
Nie musisz trafić **Esc,** ponieważ GRUB zostanie natychmiast wyświetlony

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opcja 2** - Umożliwia **naciśnięcie esc** przed uruchomieniem

Podobne zachowanie może wystąpić poprzez wprowadzenie zmian w pliku /etc/default/grub i obserwowanie 3-sekundowego limitu czasu, aby trafić **Esc**


Skomentuj te dwa wiersze:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
i dodaj ten wiersz:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 pozwoli na dostęp do konsoli szeregowej, ale nie oferuje możliwości interakcji. **Logowanie:** monit nie jest widoczny


W 12.04, aby uzyskać **login:** monit:
1. Utwórz plik o nazwie /etc/init/ttyS0.conf zawierający następujący tekst:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Poproś upstart, aby rozpocząć getty     
    ```
    sudo start ttyS0
    ```
 
Ustawienia wymagane do skonfigurowania konsoli szeregowej dla wersji Ubuntu można znaleźć [tutaj](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Tryb odzyskiwania Ubuntu

Dodatkowe opcje odzyskiwania i oczyszczania są dostępne dla Ubuntu za pośrednictwem GRUB, jednak te ustawienia są dostępne tylko wtedy, gdy odpowiednio skonfigurujesz parametry jądra.
Niepowodzenie skonfigurowania tego parametru rozruchu jądra wymusiłoby wysłanie menu odzyskiwania do diagnostyki platformy Azure, a nie do konsoli szeregowej platformy Azure.
Dostęp do menu odzyskiwania Ubuntu można uzyskać, wykonując następujące kroki:

Przerwanie procesu rozruchu i dostęp do menu GRUB

Wybierz opcje zaawansowane dla Ubuntu i naciśnij enter

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Wybierz wyświetlanie linii *(tryb odzyskiwania)* nie naciskaj enter, ale naciśnij "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Znajdź linię, która załaduje jądro i zastąp ostatni **parametr nomodeset** jako **console=ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Naciśnij **klawisze Ctrl-x,** aby uruchomić i załadować jądro.
Jeśli wszystko pójdzie dobrze, zobaczysz te dodatkowe opcje, które mogą pomóc w wykonaniu innych opcji odzyskiwania

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Konfiguracja RED Hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Red Hat\.7\+ 4 Konfiguracja GRUB
Domyślna konfiguracja /etc/default/grub w tych wersjach jest odpowiednio skonfigurowana

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Włączanie klawisza SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Konfiguracja Red\.Hat 7\.2 i 7 3 GRUB
Plik do zmodyfikowania to /etc/default/grub – domyślny config wygląda jak w tym przykładzie:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Zmień następujące wiersze w /etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Dodaj również ten wiersz:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub powinien teraz wyglądać podobnie do tego przykładu:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Kompletne i aktualizuje konfigurację grub za pomocą

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Ustaw parametr jądra SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Można też skonfigurować GRUB i SysRq za pomocą pojedynczej linii w powłoce lub za pomocą polecenia Uruchom. Przed uruchomieniem tego polecenia utwórz kopię zapasową plików:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat\.6 x konfiguracja GRUB
Plik do zmodyfikowania to /boot/grub/grub.conf. Wartość `timeout` określi, jak długo GRUB jest wyświetlany.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


Ostatnia *linia terminalu --timeout=5 konsoli szeregowej* dodatkowo zwiększy limit czasu **GRUB,** dodając monit o 5 sekund wyświetlania **Naciśnij dowolny klawisz, aby kontynuować.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

Menu GRUB powinno pojawić się na ekranie dla skonfigurowanego limitu czasu=15 bez konieczności naciskania klawisza Esc. Pamiętaj, aby kliknąć w konsoli w przeglądarce, aby uaktywnić menu i wybrać wymagane jądro

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
Albo użyj yast bootloader jak na oficjalne [dokumenty](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

Lub dodaj/zmień na /etc/default/grub następujące parametry:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Sprawdź, czy ttys0 jest używany w GRUB_CMDLINE_LINUX lub GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Odtwórz grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 Z DODATKIEM SP4 
Konsola szeregowa pojawia się i wyświetla komunikaty rozruchowe, ale nie wyświetla **logowania:** monit

Otwórz sesję ssh do maszyny Wirtualnej i zaktualizuj plik **/etc/inittab,** odrzucając ten wiersz:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Następnie uruchom polecenie 

`telinit q`

Aby włączyć GRUB, należy wyw. 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Ta konfiguracja włączy komunikat **Naciśnij dowolny klawisz, aby nadal** pojawiać się na konsoli przez 5 sekund 

Następnie wyświetli menu GRUB przez dodatkowe 5 sekund - naciskając strzałkę w dół, przerywasz licznik i wybiera jądro, które chcesz uruchomić albo dołączyć słowo kluczowe **pojedyncze** dla trybu pojedynczego użytkownika, który wymaga hasła głównego do ustawienia.

Dołączenie polecenia **init=/bin/bash** spowoduje załadowanie jądra, ale zapewnia, że program init zostanie zastąpiony przez powłokę bash.

Uzyskasz dostęp do powłoki bez konieczności wprowadzania hasła. Następnie można przystąpić do aktualizacji hasła dla kont Linuksa lub wprowadzić inne zmiany konfiguracji.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Wymuś jądro monitem bash
Mając dostęp do GRUB pozwala przerwać proces inicjowania tej interakcji jest przydatne dla wielu procedur odzyskiwania.
Jeśli nie masz hasła głównego i pojedynczy użytkownik wymaga, aby mieć hasło root, można uruchomić jądro zastępując init program z monitem bash - to przerwanie można osiągnąć przez dołączenie init = / bin / bash do linii startowej jądra

![bash1 (bash)](./media/virtual-machines-serial-console/bash1.png)

Ponownie zamontuj swój / (główny) system plików RW za pomocą polecenia

`mount -o remount,rw /`

![bash2 (bash2)](./media/virtual-machines-serial-console/bash2.png)


Teraz możesz dokonać zmiany hasła roota lub wielu innych zmian konfiguracji Linuksa

![bash3 (bash3)](./media/virtual-machines-serial-console/bash3.png)

Uruchom ponownie maszynę wirtualną za pomocą 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Tryb pojedynczego użytkownika

Alternatywnie może być konieczne uzyskiwać dostęp do maszyny Wirtualnej w trybie pojedynczego użytkownika lub awaryjnego. Wybierz jądro, które chcesz uruchomić lub przerwać za pomocą klawiszy strzałek.
Wprowadź żądany tryb, dołączając słowo kluczowe **pojedyncze** lub **1** do linii rozruchowej jądra. W systemach RHEL można również dołączyć **plik rd.break**.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do trybu pojedynczego użytkownika, zobacz [ten dokument](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [usłudze Azure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)