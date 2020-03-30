---
title: Konsola szeregowa platformy Azure dla grub i tryb jednego użytkownika | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania konsoli szeregowego dla grub na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883930"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Dostęp do trybu GRUB i tryb jednego użytkownika za pomocą konsoli szeregowej
GRand Unified Bootloader (GRUB) jest prawdopodobnie pierwszą rzeczą, którą widzisz podczas uruchamiania maszyny wirtualnej (VM). Ponieważ jest wyświetlany przed rozpoczęciem systemu operacyjnego, GRUB nie jest dostępny za pośrednictwem SSH. W GRUB, można zmodyfikować konfigurację rozruchu, aby uruchomić w trybie jednego użytkownika, między innymi.

Tryb pojedynczego użytkownika to minimalne środowisko z minimalną funkcjonalnością. Może to być przydatne do badania problemów z rozruchem, problemami z systemem plików lub problemami z siecią. Mniej usług można uruchomić w tle, a w zależności od poziomu uruchomienia system plików może nawet nie być automatycznie montowany.

Tryb pojedynczego użytkownika jest również przydatne w sytuacjach, gdy maszyna wirtualna może być skonfigurowany do akceptowania tylko klucze SSH do logowania. W takim przypadku można użyć trybu pojedynczego użytkownika, aby utworzyć konto z uwierzytelnianiem hasłem. 

> [!NOTE]
> Usługa Konsoli szeregowej umożliwia tylko użytkownikom z poziomem *współautora* lub wyższym uprawnieniami dostęp do konsoli szeregowej maszyny Wirtualnej.

Aby przejść do trybu pojedynczego użytkownika, wprowadź grub, gdy maszyna wirtualna jest uruchomiony i zmodyfikuj konfigurację rozruchu w GRUB. Zobacz szczegółowe instrukcje wprowadzania GRUB w następnej sekcji. Ogólnie rzecz biorąc, jeśli maszyna wirtualna została skonfigurowana do wyświetlania GRUB, można użyć przycisku ponownego uruchomienia w konsoli szeregowej maszyny Wirtualnej, aby ponownie uruchomić maszynę wirtualną i wyświetlić GRUB.

![Przycisk ponownego uruchamiania konsoli szeregowej Systemu Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Ogólny dostęp do GRUB
Aby uzyskać dostęp do grub, uruchom ponownie maszynę wirtualną, gdy jest otwarte okienko Konsola szeregowa. Niektóre dystrybucje wymagają wprowadzania danych za pomocą klawiatury, aby pokazać GRUB, a inne automatycznie pokazują GRUB przez kilka sekund, aby umożliwić wprowadzanie danych za pomocą klawiatury użytkownika, aby anulować limit czasu.

Aby mieć dostęp do trybu pojedynczego użytkownika, należy upewnić się, że GRUB jest włączona na maszynie Wirtualnej. W zależności od dystrybucji niektóre prace konfiguracyjne mogą być konieczne, aby upewnić się, że GRUB jest włączona. Aby uzyskać informacje dotyczące dystrybucji, zobacz następną sekcję i naszą pomoc techniczną dla systemu Linux na stronie [Azure.](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Uruchom ponownie maszynę wirtualną, aby uzyskać dostęp do grub w konsoli szeregi
Maszynę wirtualną można ponownie uruchomić w konsoli szeregowej, najeżdżając kursor na przycisk **Uruchom ponownie,** a następnie wybierając **pozycję Uruchom ponownie maszynę wirtualną**. Powiadomienie o ponownym uruchomieniu jest wyświetlane w dolnej części okienka.

Można również ponownie uruchomić maszynę wirtualną, uruchamiając polecenie SysRq "b", jeśli [funkcja SysRq](./serial-console-nmi-sysrq.md) jest włączona. Aby dowiedzieć się, czego można oczekiwać od GRUB po ponownym uruchomieniu komputera, zapoznaj się z instrukcjami dotyczącymi dystrybucji w następnych sekcjach.

![Ponowne uruchomienie konsoli szeregowego Linuksa](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Ogólny dostęp w trybie dla jednego użytkownika
Ręczny dostęp do trybu pojedynczego użytkownika może być potrzebny, gdy konto z uwierzytelnianiem hasłem nie zostało skonfigurowane. Zmodyfikuj konfigurację GRUB, aby ręcznie przejść do trybu pojedynczego użytkownika. Po wykonaniu tej tej pracy zobacz sekcję "Resetowanie lub dodawanie hasła" w trybie "Użyj trybu pojedynczego użytkownika", aby uzyskać dalsze instrukcje.

Jeśli maszyna wirtualna nie może uruchomić, dystrybucje często automatycznie upuszczają Cię w trybie jednego użytkownika lub w trybie awaryjnym. Inne dystrybucje wymagają jednak dodatkowej konfiguracji, takiej jak konfigurowanie hasła głównego, zanim będą mogły automatycznie wpaść w tryb dla jednego użytkownika lub awaryjnego.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Resetowanie lub dodawanie hasła za pomocą trybu pojedynczego użytkownika
Po przejściu do trybu jednego użytkownika dodaj nowego użytkownika z uprawnieniami sudo, wykonując następujące czynności:
1. Uruchom, `useradd <username>` aby dodać użytkownika.
1. Uruchom, `sudo usermod -a -G sudo <username>` aby udzielić nowych uprawnień administratora głównego użytkownika.
1. Służy `passwd <username>` do ustawiania hasła dla nowego użytkownika. Następnie możesz zalogować się jako nowy użytkownik.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Dostęp dla red hat enterprise linux (RHEL)
Jeśli RHEL nie może uruchomić się normalnie, automatycznie przechodzi do trybu jednego użytkownika. Jeśli jednak nie skonfigurowałeś dostępu administratora w trybie jednego użytkownika, nie masz hasła głównego i nie możesz się zalogować. Istnieje obejście (patrz sekcja "Ręczne wprowadzanie trybu pojedynczego użytkownika w RHEL"), ale zalecamy, aby początkowo skonfigurować dostęp administratora.

### <a name="grub-access-in-rhel"></a>Dostęp do GRUB w RHEL
RHEL jest wyposażony w grub włączone po wyjęciu z pudełka. Aby wprowadzić GRUB, uruchom ponownie `sudo reboot`maszynę wirtualną, uruchamiając , a następnie naciśnij dowolny klawisz. Powinno zostać wyświetlone okienko GRUB. Jeśli tak nie jest, upewnij się, że w`/etc/default/grub`pliku GRUB znajdują się następujące wiersze ( ):

**Dla RHEL 8**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Dla RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat udostępnia również dokumentację uruchamiania w trybie ratunkowym, trybie awaryjnym lub trybie debugowania oraz resetowania hasła głównego. Aby uzyskać instrukcje, zobacz [Edytowanie menu terminala podczas rozruchu](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurowanie dostępu głównego dla trybu pojedynczego użytkownika w RHEL
Użytkownik root jest domyślnie wyłączony. Tryb pojedynczego użytkownika w RHEL wymaga włączenia użytkownika głównego. Jeśli chcesz włączyć tryb jednego użytkownika, należy użyć następujących instrukcji:

1. Zaloguj się do systemu Red Hat za pośrednictwem SSH.
1. Przełącz się do katalogu głównego.
1. Włącz hasło dla użytkownika root, wykonując następujące czynności:
    * Uruchom `passwd root` (ustaw silne hasło główne).
1. Upewnij się, że użytkownik root może zalogować się tylko za pośrednictwem ttyS0, wykonując następujące czynności:  
    a. Uruchom `edit /etc/ssh/sshd_config`i upewnij się, że PermitRootLogIn jest ustawiona na `no`.  
    b. Uruchom, `edit /etc/securetty file` aby umożliwić logowanie tylko za pośrednictwem ttyS0.

Teraz, jeśli system uruchamia się w trybie jednego użytkownika, możesz zalogować się przy za pomocą hasła głównego.

Alternatywnie, dla RHEL 7.4+ lub 6.9 +, aby włączyć tryb jednego użytkownika w monitach GRUB, zobacz [Uruchamianie w trybie jednego użytkownika](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ręczne wejście w tryb pojedynczego użytkownika w RHEL
Jeśli masz skonfigurowany grub i dostęp do roota przy użyciu powyższych instrukcji, możesz wejść w tryb jednego użytkownika, wykonując następujące czynności:

1. Aby wprowadzić GRUB, naciśnij klawisz Esc podczas ponownego uruchamiania maszyny wirtualnej.
1. W grub, naciśnij klawisz E, aby edytować system operacyjny, który chcesz uruchomić. System operacyjny jest zwykle wyświetlany w pierwszym wierszu.
1. Znajdź linię jądra. Na platformie Azure zaczyna się od *linux16*.
1. Naciśnij klawisze Ctrl+E, aby przejść do końca wiersza.
1. Na końcu wiersza dodaj *systemd.unit=rescue.target*.
    
    Ta akcja uruchamia cię w trybie dla jednego użytkownika. Jeśli chcesz użyć trybu awaryjnego, dodaj *systemd.unit=emergency.target* na końcu linii (zamiast *systemd.unit=rescue.target).*

1. Naciśnij klawisze Ctrl+X, aby zakończyć i ponownie uruchomić komputer z zastosowanymi ustawieniami.

   Przed wprowadzeniem hasła administratora zostanie wyświetlony monit o podanie hasła administratora. To hasło jest tym, które zostało utworzone w poprzednich instrukcjach.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Wejdź w tryb jednego użytkownika bez włączonego konta głównego w RHEL
Jeśli użytkownik root nie został wcielił się w użytkownika root, postępując zgodnie z wcześniejszymi instrukcjami, nadal możesz zresetować hasło główne, wykonując następujące czynności:

> [!NOTE]
> Jeśli używasz SELinux, podczas resetowania hasła głównego, należy wykonać dodatkowe kroki, które są opisane w [dokumentacji Red Hat](https://aka.ms/rhel7grubterminal).

1. Aby wprowadzić GRUB, naciśnij klawisz Esc podczas ponownego uruchamiania maszyny wirtualnej.

1. W grub, naciśnij klawisz E, aby edytować system operacyjny, który chcesz uruchomić. System operacyjny jest zwykle wyświetlany w pierwszym wierszu.
1. Znajdź linię jądra. Na platformie Azure zaczyna się od *linux16*.
1. Na końcu wiersza dodaj *rd.break* na końcu wiersza. Pozostaw odstęp między linią jądra a *rd.break*.

    Ta akcja przerywa proces rozruchu przed `initramfs` `systemd`przekazaniem formantu z do , zgodnie z opisem w [dokumentacji Red Hat](https://aka.ms/rhel7rootpassword).
1. Naciśnij klawisze Ctrl+X, aby zakończyć i ponownie uruchomić komputer z zastosowanymi ustawieniami.

   Po ponownym uruchomieniu komputera zostaniesz przesunięty w tryb awaryjny z systemem plików tylko do odczytu. 
   
1. W powłoce `mount -o remount,rw /sysroot` wprowadź, aby ponownie zamontować główny system plików z uprawnieniami do odczytu/zapisu.
1. Po uruchomieniu w trybie jednego `chroot /sysroot` użytkownika, `sysroot` wprowadź, aby przełączyć się do więzienia.
1. Jesteś teraz u podstaw. Hasło główne można zresetować, `passwd` wprowadzając, a następnie korzystając z powyższych instrukcji, aby przejść do trybu jednego użytkownika. 
1. Po zakończeniu wprowadź, `reboot -f` aby ponownie uruchomić komputer.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Przebieganie przez poprzednie instrukcje wrzuca cię do powłoki awaryjnej, dzięki czemu można również wykonywać zadania, takie jak edycja `fstab`. Jednak zwykle sugerujemy zresetowanie hasła głównego i użycie go do wejścia w tryb jednego użytkownika.

## <a name="access-for-centos"></a>Dostęp dla CentOS
Podobnie jak Red Hat Enterprise Linux, tryb jednego użytkownika w CentOS wymaga GRUB i użytkownika root, aby być włączone.

### <a name="grub-access-in-centos"></a>Dostęp do GRUB w CentOS
CentOS jest wyposażony w GRUB włączone po wyjęciu z pudełka. Aby wprowadzić GRUB, uruchom ponownie `sudo reboot`maszynę wirtualną, wprowadzając przycisk , a następnie naciśnij dowolny klawisz. Ta akcja powoduje wyświetlenie okienka GRUB.

### <a name="single-user-mode-in-centos"></a>Tryb dla jednego użytkownika w centos
Aby włączyć tryb jednego użytkownika w CentOS, postępuj zgodnie z wcześniejszymi instrukcjami dotyczącymi RHEL.

## <a name="access-for-ubuntu"></a>Dostęp dla Ubuntu
Obrazy Ubuntu nie wymagają hasła głównego. Jeśli system uruchamia się w trybie jednego użytkownika, można go używać bez dodatkowych poświadczeń.

### <a name="grub-access-in-ubuntu"></a>Dostęp DO GRUB w Ubuntu
Aby uzyskać dostęp do GRUB, naciśnij i przytrzymaj klawisz Esc podczas uruchamiania maszyny Wirtualnej.

Domyślnie obrazy Ubuntu mogą nie wyświetlać automatycznie okienka GRUB. Ustawienie można zmienić, wykonując następujące czynności:
1. W edytorze tekstu otwórz plik */etc/default/grub.d/50-cloudimg-settings.cfg.*

1. Zmień `GRUB_TIMEOUT` wartość na wartość niezerową.
1. W edytorze tekstu otwórz */etc/default/grub*.
1. Skomentuj `GRUB_HIDDEN_TIMEOUT=1` wiersz.
1. Upewnij się, że `GRUB_TIMEOUT_STYLE=menu` istnieje linia.
1. Uruchom polecenie `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Tryb dla jednego użytkownika w Ubuntu
Jeśli Ubuntu nie może uruchomić się normalnie, automatycznie wprowadza cię w tryb jednego użytkownika. Aby ręcznie przejść do trybu pojedynczego użytkownika, wykonaj następujące czynności:

1. W GRUB naciśnij klawisz E, aby edytować wpis startowy (wpis Ubuntu).
1. Poszukaj linii, która zaczyna się od *linuksa*, a następnie poszukaj *ro*.
1. Dodaj *pojedynczy* po *ro*, upewniając się, że jest miejsce przed i po *singlu.*
1. Naciśnij klawisze Ctrl+X, aby ponownie uruchomić komputer z tymi ustawieniami i przejść do trybu jednego użytkownika.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Użyj GRUB wywołać bash w Ubuntu
Po wypróbowaniu powyższych instrukcji może zajść sytuacja (np. zapomniane hasło główne), w której nadal nie możesz uzyskać dostępu do trybu pojedynczego użytkownika w maszynie wirtualnej Ubuntu. Można również powiedzieć jądrze, aby działało `/bin/bash` jako init, a nie init systemu. Ta akcja daje powłoki bash i pozwala na konserwację systemu. Skorzystaj z następujących instrukcji:

1. W GRUB naciśnij klawisz E, aby edytować wpis startowy (wpis Ubuntu).

1. Poszukaj linii, która zaczyna się od *linuksa*, a następnie poszukaj *ro*.
1. Wymień *ro* *na rw init=/bin/bash*.

    Ta akcja instaluje system plików jako `/bin/bash` odczyt i zapis i używa jako init procesu.
1. Naciśnij klawisze Ctrl+X, aby ponownie uruchomić komputer z tymi ustawieniami.

## <a name="access-for-coreos"></a>Dostęp dla CoreOS
Tryb jednego użytkownika w CoreOS wymaga włączonej funkcji GRUB.

### <a name="grub-access-in-coreos"></a>Dostęp do GRUB w CoreOS
Aby uzyskać dostęp do GRUB, naciśnij dowolny klawisz podczas uruchamiania maszyny Wirtualnej.

### <a name="single-user-mode-in-coreos"></a>Tryb dla jednego użytkownika w CoreOS
Jeśli CoreOS nie może normalnie uruchomić, automatycznie przechodzi do trybu jednego użytkownika. Aby ręcznie przejść do trybu pojedynczego użytkownika, wykonaj następujące czynności:

1. W grub, naciśnij klawisz E, aby edytować wpis rozruchu.

1. Poszukaj linii, która zaczyna się od *linux$*. Powinny istnieć dwa wystąpienia wiersza, każdy zamknięty w *innym, jeśli... innej* klauzuli.
1. Dołącz *coreos.autologin=ttyS0* na końcu każdej linii *linux$.*
1. Naciśnij klawisze Ctrl+X, aby ponownie uruchomić komputer z tymi ustawieniami i przejść do trybu jednego użytkownika.

## <a name="access-for-suse-sles"></a>Dostęp dla SUSE SLES
Nowsze obrazy SLES 12 SP3+ umożliwiają dostęp za pośrednictwem konsoli szeregowej, jeśli system uruchomi się w tryb awaryjny.

### <a name="grub-access-in-suse-sles"></a>Dostęp do GRUB w SUSE SLES
Dostęp GRUB w SLES wymaga konfiguracji bootloadera za pośrednictwem YaST. Aby utworzyć konfigurację, wykonaj następujące czynności:

1. Użyj SSH, aby zalogować się do maszyny `sudo yast bootloader`Wirtualnej SLES, a następnie uruchom . Naciśnij klawisz Tab, naciśnij klawisz Enter, a następnie przejdź przez menu za pomocą klawiszy strzałek.

1. Przejdź do opcji **Parametry jądra**, a następnie zaznacz pole wyboru **Użyj konsoli szeregowej.**
1. Dodaj `serial --unit=0 --speed=9600 --parity=no` do argumentów **konsoli.**
1. Naciśnij klawisz F10, aby zapisać ustawienia i wyjść.
1. Aby wprowadzić grub, uruchom ponownie maszynę wirtualną i naciśnij dowolny klawisz podczas sekwencji rozruchowej, aby wyświetlić okienko GRUB.

    Domyślny limit czasu dla GRUB to **1s**. To ustawienie można zmodyfikować, zmieniając zmienną `GRUB_TIMEOUT` w pliku */etc/default/grub.*

![Inicjowanie konfiguracji bootloadera](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Tryb dla jednego użytkownika w SUSE SLES
Jeśli SLES nie może uruchomić się normalnie, automatycznie spada do powłoki awaryjnej. Aby ręcznie wprowadzić powłokę awaryjną, wykonaj następujące czynności:

1. W GRUB naciśnij klawisz E, aby edytować wpis rozruchowy (wpis SLES).

1. Poszukaj linii jądra, która zaczyna się od *linuksa*.
1. Dołącz *systemd.unit=emergency.target* na końcu linii jądra.
1. Naciśnij klawisze Ctrl+X, aby ponownie uruchomić komputer z tymi ustawieniami i wprowadzić powłokę awaryjną.

   > [!NOTE]
   > Ta akcja przenosi cię do powłoki awaryjnej z systemem plików tylko do odczytu. Aby edytować wszystkie pliki, należy ponownie zamontować system plików z uprawnieniami do odczytu i zapisu. Aby to zrobić, wprowadź `mount -o remount,rw /` w powłoce.

## <a name="access-for-oracle-linux"></a>Dostęp dla Oracle Linux
Podobnie jak Red Hat Enterprise Linux, tryb jednego użytkownika w systemie Oracle Linux wymaga włączenia GRUB i głównego użytkownika.

### <a name="grub-access-in-oracle-linux"></a>Dostęp do GRUB w systemie Oracle Linux
Oracle Linux jest wyposażony w GRUB włączone po wyjęciu z pudełka. Aby wprowadzić GRUB, uruchom ponownie `sudo reboot`maszynę wirtualną, uruchamiając , a następnie naciśnij klawisz Esc. Ta akcja powoduje wyświetlenie okienka GRUB. Jeśli okienko GRUB nie jest wyświetlane, upewnij się, że wartość `GRUB_TERMINAL` wiersza `GRUB_TERMINAL="serial console"`zawiera *konsolę szeregową* (czyli ). Odbuduj GRUB z `grub2-mkconfig -o /boot/grub/grub.cfg`.

### <a name="single-user-mode-in-oracle-linux"></a>Tryb dla jednego użytkownika w systemie Oracle Linux
Aby włączyć tryb jednego użytkownika w systemie Oracle Linux, postępuj zgodnie z wcześniejszymi instrukcjami dotyczącymi RHEL.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o konsoli szeregi, zobacz:
* [Dokumentacja konsoli szeregowej Linuksa](serial-console-linux.md)
* [Użyj konsoli szeregowych, aby włączyć GRUB w różnych dystrybucjach](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Używanie konsoli szeregowej dla połączeń NMI i SysRq](serial-console-nmi-sysrq.md)
* [Konsola szeregowa dla maszyn wirtualnych z systemem Windows](serial-console-windows.md)
* [Diagnostyka rozruchu](boot-diagnostics.md)
