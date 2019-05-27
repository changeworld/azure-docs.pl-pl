---
title: Usługa Azure konsoli szeregowej CHODNIKÓW i trybie jednego użytkownika | Dokumentacja firmy Microsoft
description: Za pomocą konsoli szeregowej dla chodników na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2019
ms.author: alsin
ms.openlocfilehash: 89cbf220c9ae32c7f63da4941ced1bdbfa1e5293
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835038"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Umożliwia dostęp do programu GRUB i tryb jednego użytkownika konsoli szeregowej
Program GRUB jest sumy Unified program inicjujący, który prawdopodobnie jest pierwszą rzeczą, jaką będzie wyświetlany po rozruchem maszyny Wirtualnej. Ponieważ wyświetla zanim został uruchomiony system operacyjny, nie jest dostępny za pośrednictwem protokołu SSH. Z programu GRUB są możliwość modyfikowania konfiguracji rozruchu do rozruchu w trybie jednego użytkownika, między innymi.

Tryb jednego użytkownika jest minimalne środowisko z minimalną liczbę funkcji. Może być przydatne do badania problemów rozruchu, problemy z systemu plików lub problemy z siecią. Mniej usług może działać w tle i w zależności od uruchamiania przełącznika/RL, system plików może nie nawet automatycznie zainstalowany.

Tryb jednego użytkownika jest również przydatne w sytuacjach, w której maszyna wirtualna tylko może być skonfigurowany do akceptowania kluczy SSH do logowania się w. W takim przypadku można utworzyć konto przy użyciu uwierzytelniania hasła w trybie jednego użytkownika. Należy pamiętać, że usługa konsoli szeregowej zezwala tylko użytkowników z poziomu dostępu współautora lub nowszego dostępu do konsoli szeregowej maszyny wirtualnej.

Aby przejść do trybu jednego użytkownika, należy wprowadzić CHODNIKÓW, kiedy maszyna wirtualna jest uruchamiana i modyfikowanie konfiguracji rozruchu w CHODNIKÓW. Szczegółowe instrukcje dotyczące wprowadzania CHODNIKÓW są poniżej. Ogólnie rzecz biorąc może używać przycisku ponownego uruchomienia w ramach konsoli szeregowej maszyny Wirtualnej, aby ponownie uruchomić maszynę Wirtualną i Pokaż CHODNIKÓW, jeśli maszyna wirtualna została skonfigurowana do Pokaż CHODNIKÓW.

![Przycisk Linux Serial ponownego uruchomienia konsoli](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Ogólny program GRUB dostępu
Dostępu CHODNIKÓW, będzie konieczne ponowne uruchomienie maszyny Wirtualnej przy jednoczesnym zachowaniu Otwórz blok konsoli szeregowej. Niektóre dystrybucje wymaga danych wprowadzonych z klawiatury do wyświetlenia CHODNIKÓW, podczas gdy inne automatycznie Pokaż CHODNIKÓW na kilka sekund i Zezwalaj na klawiatury danych wprowadzonych przez użytkownika limit czasu anulowania.

Należy upewnić się, że CHODNIKÓW jest włączona na maszynie Wirtualnej, aby można było w trybie jednego użytkownika dostępu. W zależności od Twojej dystrybucji może być jakąś pracę Instalatora, aby upewnić się, że włączono programu GRUB. Informacje specyficzne dla dystrybucji znajduje się poniżej oraz w [ten link](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Ponowne uruchomienie maszyny Wirtualnej, aby uzyskać dostęp CHODNIKÓW w konsoli szeregowej
Możesz ponownie uruchomić maszynę Wirtualną w ramach konsoli szeregowej, przechodzenia do przycisku zasilania, a następnie klikając polecenie "Uruchom ponownie maszynę Wirtualną". Spowoduje to zainicjowanie ponownego uruchomienia maszyny Wirtualnej, a następnie zostanie wyświetlone powiadomienie w witrynie Azure portal dotyczących ponownego uruchomienia.
Ponowne uruchamianie maszyny Wirtualnej można również wykonać przy użyciu SysRq `'b'` polecenia, jeśli [SysRq](./serial-console-nmi-sysrq.md) jest włączona. Postępuj zgodnie z poniższymi instrukcjami specyficzne dla dystrybucji, aby dowiedzieć się, czego można oczekiwać od CHODNIKÓW, po ponownym uruchomieniu.

![Ponowne uruchomienie konsoli seryjne systemu Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Ogólnego dostępu tryb jednego użytkownika
Ręczne dostęp do trybu jednego użytkownika mogą być potrzebne w sytuacjach, w których nie skonfigurowano konta przy użyciu uwierzytelniania hasła. Należy zmodyfikować konfigurację programu GRUB, aby ręcznie wprowadzić w trybie jednego użytkownika. Gdy to zrobisz, zobacz tryb jednego użytkownika użycia zresetować lub Dodaj hasło, aby uzyskać dalsze instrukcje.

W przypadku gdy maszyna wirtualna nie uruchamia dystrybucje będzie często automatycznie pomijać możesz w trybie jednego użytkownika lub w trybie awaryjnym. Innych osób, jednak wymagają dodatkowej konfiguracji, zanim mogą porzucić możesz w trybie jednego użytkownika lub awaryjnego automatycznie (takich jak konfigurowanie hasła głównego).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>W trybie jednego użytkownika zresetować lub Dodaj hasło
Gdy jesteś w trybie jednego użytkownika, wykonaj następujące polecenie, aby dodać nowego użytkownika z uprawnieniami sudo:
1. Uruchom `useradd <username>` dodawania użytkownika
1. Uruchom `sudo usermod -a -G sudo <username>` udzielenia uprawnień użytkownika root nowego użytkownika
1. Użyj `passwd <username>` ustawić hasło dla nowego użytkownika. Następnie będzie można logować się jako nowy użytkownik


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Dostęp do programu Red Hat Enterprise Linux (RHEL)
RHEL spowoduje porzucenie możesz w trybie jednego użytkownika automatycznie, jeśli nie normalny rozruch. Jeśli jednak nie zdefiniowano głównego dostęp w trybie jednego użytkownika, użytkownik nie będzie hasło główne i będzie mógł zalogować się. Nie jest dostępne obejście (patrz "Ręcznego wprowadzania trybie jednego użytkownika" poniżej), ale sugestia jest wstępnie skonfigurować dostęp na poziomie administratora.

### <a name="grub-access-in-rhel"></a>Program GRUB dostępu w RHEL
RHEL jest powiązana z CHODNIKÓW włączone poza pole. Aby wprowadzić CHODNIKÓW, ponowne uruchomienie maszyny Wirtualnej za pomocą `sudo reboot` i naciśnij dowolny klawisz. Zostanie wyświetlony ekran CHODNIKÓW, wyświetlane.

> Uwaga: Red Hat również zawiera dokumentację dotyczącą rozruch w trybie ratownicze, trybu awaryjnego, tryb debugowania i resetowania hasła użytkownika root. [Kliknij tutaj, aby uzyskać do niego dostęp](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurowanie głównego dostępu w trybie jednego użytkownika w RHEL
Tryb jednego użytkownika w RHEL wymaga zalogowany jako użytkownik root do włączenia, która jest domyślnie wyłączona. Jeśli trzeba włączyć tryb jednego użytkownika, użyj poniższych instrukcji:

1. Zaloguj się w systemie Red Hat za pośrednictwem protokołu SSH
1. Przejdź do katalogu głównego
1. Włącz hasło dla użytkownika głównego
    * `passwd root` (Ustaw hasło główne silne)
1. Upewnij się, że użytkownik root tylko zalogować się za pośrednictwem ttyS0
    * `edit /etc/ssh/sshd_config` i upewnij się, że PermitRootLogIn jest ustawiona na nie
    * `edit /etc/securetty file` Aby zezwolić tylko na logowanie za pomocą ttyS0

Teraz, gdy system jest uruchamiany w trybie jednego użytkownika możesz zalogować się przy użyciu hasła użytkownika root.

Alternatywnie dla RHEL 7.4 + lub 6,9 + można włączyć tryb jednego użytkownika w konfigurację programu GRUB monity, zobacz instrukcje [tutaj](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ręcznie wprowadź trybie jednego użytkownika w RHEL
Jeśli po skonfigurowaniu CHODNIKÓW i głównego dostępu przy użyciu powyższych instrukcji, a następnie wprowadzić tryb jednego użytkownika z poniższych instrukcji:

1. Jeśli instalujesz "" podczas ponownego uruchamiania maszyny Wirtualnej, aby wprowadzić CHODNIKÓW
1. Program GRUB naciśnij klawisz "e", aby edytować wybranego systemu operacyjnego, aby rozruch (zazwyczaj pierwszy wiersz)
1. Znajdź wiersz jądra — na platformie Azure, rozpoczyna się od `linux16`
1. Naciśnij klawisze Ctrl + E, aby przejść do końca wiersza
1. Dodaj następujący element do końca wiersza: `systemd.unit=rescue.target`
    * Spowoduje to rozruch w trybie jednego użytkownika. Jeśli chcesz użyć trybu awaryjnego, należy dodać `systemd.unit=emergency.target` do końca wiersza, zamiast `systemd.unit=rescue.target`
1. Naciśnij klawisze Ctrl + X, aby zakończyć proces i ponowne uruchomienie przy użyciu ustawień zastosowanych
1. Zostanie wyświetlony monit o hasło administratora zanim będzie mógł przejść do trybu jednego użytkownika — jest to samo hasło utworzone w powyższych instrukcji

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Wejdź w tryb jednego użytkownika bez konta głównego włączone w RHEL
Jeśli użytkownik nie powiodło się powyższe kroki, aby włączyć jako użytkownik root, możesz zresetować hasła użytkownika root. Skorzystaj z poniższych instrukcji:

> Uwaga: Jeśli używasz SELinux, sprawdź, czy jakieś dodatkowe czynności opisane w dokumentacji firmy Red Hat [tutaj](https://aka.ms/rhel7grubterminal) podczas resetowania hasła użytkownika root.

1. Jeśli instalujesz "" podczas ponownego uruchamiania maszyny Wirtualnej, aby wprowadzić CHODNIKÓW
1. Program GRUB naciśnij klawisz "e", aby edytować wybranego systemu operacyjnego, aby rozruch (zazwyczaj pierwszy wiersz)
1. Znajdź wiersz jądra — na platformie Azure, rozpoczyna się od `linux16`
1. Dodaj `rd.break` do końca wiersza, zapewniona jest odstęp przed `rd.break` (zobacz poniższy przykład)
    - Spowoduje to przerwanie procesu rozruchu przed sterowanie jest przekazywane z `initramfs` do `systemd`, zgodnie z opisem zamieszczonym w dokumentacji firmy Red Hat [tutaj](https://aka.ms/rhel7rootpassword).
1. Naciśnij klawisze Ctrl + X, aby zakończyć proces i ponowne uruchomienie przy użyciu ustawień zastosowanych
1. Po uruchomieniu, użytkownik zostanie usunięty w trybie awaryjnym, przy użyciu systemu plików tylko do odczytu. Wprowadź `mount -o remount,rw /sysroot` w powłoce, aby ponownie zainstalować główny system plików z uprawnieniami do odczytu/zapisu
1. Po uruchomieniu w trybie jednego użytkownika, wpisz w `chroot /sysroot` przełączyć się do `sysroot` zdjęto zabezpieczeń systemu
1. Jesteś teraz głównego. Możesz zresetować hasła głównego przy użyciu `passwd` , a następnie użyć z powyższymi instrukcjami, aby przejść do trybu jednego użytkownika. Typ `reboot -f` ponowne uruchomienie, gdy wszystko będzie gotowe.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Uwaga: Uruchamianie przy użyciu powyższych instrukcji spowoduje porzucenie do awaryjnego shell można również wykonywać zadania, takie jak edytowanie `fstab`. Jednak powszechnie akceptowane sugestia jest do zresetowania hasła głównego i używać go do trybu jednego użytkownika.


## <a name="access-for-centos"></a>Dostęp do CentOS
Znacznie takich jak Red Hat Enterprise Linux, tryb jednego użytkownika w CentOS wymaga CHODNIKÓW i jako użytkownik root włączyć.

### <a name="grub-access-in-centos"></a>Program GRUB dostępu w CentOS
CentOS jest powiązana z CHODNIKÓW włączone poza pole. Aby wprowadzić CHODNIKÓW, ponowne uruchomienie maszyny Wirtualnej za pomocą `sudo reboot` i naciśnij dowolny klawisz. Zostanie wyświetlony ekran CHODNIKÓW, wyświetlane.

### <a name="single-user-mode-in-centos"></a>Tryb jednego użytkownika w CentOS
Postępuj zgodnie z instrukcjami dotyczącymi RHEL powyżej, aby włączyć tryb jednego użytkownika w CentOS.

## <a name="access-for-ubuntu"></a>Dostęp do systemu Ubuntu
Nie wymagają hasła głównego obrazów systemu Ubuntu. Jeśli system jest uruchamiany w trybie jednego użytkownika, możesz użyć go bez dodatkowych poświadczeń.

### <a name="grub-access-in-ubuntu"></a>Program GRUB dostępu w systemie Ubuntu
Dostępu CHODNIKÓW, naciśnij i przytrzymaj "Esc" w przypadku, gdy maszyna wirtualna jest uruchamiana.

Domyślnie obrazów systemu Ubuntu nie może automatycznie pokazywane na ekranie programu GRUB. Można to zmienić za pomocą następujących instrukcji:
1. Otwórz `/etc/default/grub.d/50-cloudimg-settings.cfg` w edytorze tekstów wybranych przez użytkownika
1. Zmiana `GRUB_TIMEOUT` wartość na wartość inną niż zero
1. Otwórz `/etc/default/grub` w edytorze tekstów wybranych przez użytkownika
1. Komentarz `GRUB_HIDDEN_TIMEOUT=1` wiersza
1. Uruchom polecenie `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Tryb jednego użytkownika w systemie Ubuntu
Ubuntu spowoduje porzucenie możesz w trybie jednego użytkownika automatycznie, jeśli nie normalny rozruch. Aby ręcznie wprowadzić w trybie jednego użytkownika, należy użyć poniższych instrukcji:

1. Program GRUB naciśnij klawisze "e", aby edytować wpis rozruchu (wpis w systemie Ubuntu)
1. Znajdź wiersz zaczynający się od `linux`, poszukaj `ro`
1. Dodaj `single` po `ro`, zapewnianie występuje znak spacji przed i po nim `single`
1. Naciśnij klawisze Ctrl + X ponowny rozruch przy użyciu tych ustawień, a następnie wprowadź w trybie jednego użytkownika

### <a name="using-grub-to-invoke-bash-in-ubuntu"></a>Za pomocą programu GRUB do wywołania funkcji bash w systemie Ubuntu
Może to być sytuacjach (np. głównym zapomniane hasła) mogą nadal być nie może uzyskać dostępu trybie jednego użytkownika na maszynie wirtualnej systemu Ubuntu po wykonaniu powyższych instrukcji. Możesz również poinformować jądra uruchamianie /bin/bash init zamiast init systemu, umożliwiają powłoki bash i umożliwić konserwacji systemu. Skorzystaj z poniższych instrukcji:

1. Program GRUB naciśnij klawisze "e", aby edytować wpis rozruchu (wpis w systemie Ubuntu)
1. Znajdź wiersz zaczynający się od `linux`, poszukaj `ro`
1. Zastąp `ro` z `rw init=/bin/bash`
    - Spowoduje to instalacji usługi systemu plików w trybie odczytu, zapisu i użyj /bin/bash jako proces inicjowania
1. Naciśnij klawisze Ctrl + X, ponowne uruchomienie przy użyciu tych ustawień

## <a name="access-for-coreos"></a>Access for CoreOS
Tryb jednego użytkownika w CoreOS wymaga CHODNIKÓW włączyć.

### <a name="grub-access-in-coreos"></a>Program GRUB dostępu w CoreOS
Aby uzyskać dostęp do programu GRUB, naciśnij dowolny klawisz, gdy maszyna wirtualna jest uruchamiana.

### <a name="single-user-mode-in-coreos"></a>Tryb jednego użytkownika w CoreOS
CoreOS spowoduje porzucenie możesz w trybie jednego użytkownika automatycznie, jeśli nie normalny rozruch. Aby ręcznie wprowadzić w trybie jednego użytkownika, należy użyć poniższych instrukcji:
1. Program GRUB naciśnij klawisze "e", aby edytować wpis rozruchu
1. Znajdź wiersz zaczynający się od `linux$`. Powinna wynosić 2, zhermetyzowanych w ramach różnych if/else klauzule
1. Dołącz `coreos.autologin=ttyS0` na końcu `linux$` wierszy
1. Naciśnij klawisze Ctrl + X ponowny rozruch przy użyciu tych ustawień, a następnie wprowadź w trybie jednego użytkownika

## <a name="access-for-suse-sles"></a>Dostęp dla systemu SLES SUSE
Zezwalaj na dostęp za pośrednictwem konsoli szeregowej nowszej obrazy z systemem SLES 12 z dodatkiem SP3 +, w przypadku, gdy system jest uruchamiany w trybie awaryjnym.

### <a name="grub-access-in-suse-sles"></a>Program GRUB dostępu w systemie SLES SUSE
Program GRUB dostępu w systemie SLES wymaga konfiguracji programu rozruchowego za pośrednictwem YaST. Aby to zrobić, wykonaj następujące instrukcje:

1. SSH do maszyny Wirtualnej z systemem SLES i uruchom `sudo yast bootloader`. Użyj `tab` klucza `enter` klucz i klawiszy strzałek, aby poruszać się po menu.
1. Przejdź do `Kernel Parameters`i sprawdź `Use serial console`.
1. Dodaj `serial --unit=0 --speed=9600 --parity=no` argumentów konsoli

1. Naciśnij klawisz F10, aby zapisać ustawienia i zamknąć okno
1. Aby wprowadzić CHODNIKÓW, ponowne uruchomienie maszyny Wirtualnej, a następnie naciśnij dowolny klawisz, podczas sekwencji rozruchu się CHODNIKÓW pozostają na ekranie
    - Domyślny limit czasu CHODNIKÓW jest wartości 1. Można to modyfikować, zmieniając `GRUB_TIMEOUT` zmienną `/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Tryb jednego użytkownika w systemie SLES SUSE
Użytkownik zostanie automatycznie usunięty do awaryjnego shell Jeśli SLES nie normalny rozruch. Aby ręcznie wprowadzić awaryjnego powłoki, użyj poniższych instrukcji:

1. Program GRUB naciśnij klawisze "e", aby edytować wpis rozruchu (wpis w systemie SLES)
1. Zwróć uwagę na wiersz jądra, który rozpoczyna się od `linux`
1. Dołącz `systemd.unit=emergency.target` do końca wiersza
1. Naciśnij klawisze Ctrl + X ponowny rozruch przy użyciu tych ustawień, a następnie wprowadź awaryjnego powłoki
   > Należy pamiętać, że użytkownik zostanie porzucony w do awaryjnego shell przy użyciu _tylko do odczytu_ systemu plików. Jeśli chcesz wprowadzić zmiany do wszystkich plików, należy ponownie zainstalować system plików z uprawnieniami do odczytu i zapisu. Aby to zrobić, należy wprowadzić `mount -o remount,rw /` powłokę

## <a name="access-for-oracle-linux"></a>Dostęp do oprogramowania Oracle w systemie Linux
Znacznie takich jak Red Hat Enterprise Linux, tryb jednego użytkownika w systemie Oracle Linux wymaga CHODNIKÓW i jako użytkownik root włączyć.

### <a name="grub-access-in-oracle-linux"></a>Program GRUB dostępu w systemie Oracle Linux
Oracle Linux jest powiązana z CHODNIKÓW włączone poza pole. Aby wprowadzić CHODNIKÓW, ponowne uruchomienie maszyny Wirtualnej za pomocą `sudo reboot` i naciśnij klawisz "Esc". Zostanie wyświetlony ekran CHODNIKÓW, wyświetlane.

### <a name="single-user-mode-in-oracle-linux"></a>Tryb jednego użytkownika w systemie Oracle Linux
Postępuj zgodnie z instrukcjami dotyczącymi RHEL powyżej, aby włączyć tryb jednego użytkownika w systemie Oracle Linux.

## <a name="next-steps"></a>Kolejne kroki
* Znajduje się na stronie dokumentacji Linux głównych konsoli szeregowej [tutaj](serial-console-linux.md).
* Dowiedz się, jak używać konsoli szeregowej [Włącz CHODNIKÓW w różnych dystrybucjach](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* Użyj konsoli szeregowej dla [NMI i SysRq wywołania](serial-console-nmi-sysrq.md)
* Jest również dostępny dla konsoli szeregowej [Windows](serial-console-windows.md) maszyn wirtualnych
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md)
