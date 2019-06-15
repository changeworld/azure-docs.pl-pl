---
title: Usługa Azure konsoli szeregowej wywołań SysRq i NMI | Dokumentacja firmy Microsoft
description: Za pomocą konsoli szeregowej SysRq i NMI wywołuje w maszynach wirtualnych platformy Azure.
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
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: ad29bbd038c8982778f2dbca63756f6995077dce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204918"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Użyj konsoli szeregowej wywołań SysRq i NMI

## <a name="system-request-sysrq"></a>Żądanie systemu (SysRq)
SysRq jest sekwencją kluczy rozpoznawane przez jądro systemu Linux operacji, które mogą wyzwalać zestaw predefiniowanych akcji. Te polecenia są często używane w przypadku maszyny wirtualnej rozwiązywania problemów lub odzyskiwania nie można wykonać przy użyciu tradycyjnych administracyjnej (na przykład, jeśli nie odpowiada maszynie Wirtualnej). Za pomocą funkcji SysRq konsoli szeregowej Azure przypominające naciśnięcie klucza SysRq i znaki wprowadzone na klawiaturze fizycznych.

Po sekwencji SysRq zostało dostarczone, konfiguracji jądra kontrolować, jak zachowuje się system. Aby uzyskać informacji na temat włączania i wyłączania SysRq, zobacz *podręczniku administratora SysRq* [tekstu](https://aka.ms/kernelorgsysreqdoc) | [języka znaczników markdown](https://aka.ms/linuxsysrq).  

Konsoli szeregowej Azure może służyć do wysyłania SysRq na maszynie wirtualnej platformy Azure przy użyciu ikonę klawiatury na pasku poleceń, pokazano poniżej.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Polecenie "Wyślij SysRq" zostanie otwarte okno dialogowe, które zapewniają typowe opcje SysRq, lub zaakceptować sekwencji poleceń SysRq wprowadzane do okna dialogowego.  Dzięki temu dla serii SysRq do wykonywania operacji wysokiego poziomu, takie jak przy użyciu bezpiecznego rozruchu: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Nie można użyć polecenia SysRq na maszynach wirtualnych, które zostały zatrzymane lub których jądra jest w stanie przestanie odpowiadać. (na przykład jądra).

### <a name="enable-sysrq"></a>Włącz SysRq 
Zgodnie z opisem w *podręczniku administratora SysRq* powyżej SysRq można skonfigurować tak, aby wszystkie, none lub tylko niektórych poleceń, które są dostępne. Możesz włączyć wszystkie polecenia SysRq przy użyciu poniższego kroku, ale nie będą obowiązywać po ponownym uruchomieniu:
```
echo "1" >/proc/sys/kernel/sysrq
```
Aby konfiguracja SysReq uzyskania trwałego, należy wykonać następujące polecenie, aby włączyć wszystkie polecenia SysRq
1. Dodanie tego wiersza, aby */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Ponowne uruchamianie lub aktualizowanie sysctl, uruchamiając <br>
    `sysctl -p`

### <a name="command-keys"></a>Polecenia klawiszy 
W powyższym podręczniku administratora SysRq:

|Polecenie| Funkcja
| ------| ----------- |
|``b``  |   System natychmiast zostanie uruchomiony bez synchronizacji lub odinstalowywania dysków.
|``c``  |   Będzie wykonywać awarii systemu przez wskaźnik NULL wyłuskania. Zrzutu awaryjnego zostaną wykonane, jeśli skonfigurowane.
|``d``  |   Przedstawia wszystkie blokady, które są wstrzymane.
|``e``  |   Wyślij SIGTERM do wszystkich procesów, z wyjątkiem init.
|``f``  |   Wywoła identyfikatory za mało pamięci do kończenia procesu świń pamięci, ale nie awaryjnego, jeśli nic nie może zostać zakończona.
|``g``  |   Używane przez kgdb (debuger jądra)
|``h``  |   Zostanie wyświetlona Pomoc (dowolny klawisz, niż te wymienione w tym miejscu będą również wyświetlane, aby uzyskać pomoc, ale ``h`` jest łatwa do zapamiętania :-)
|``i``  |    Wyślij SIGKILL do wszystkich procesów, z wyjątkiem init.
|``j``  |    Wymuś "Po prostu odblokuj go" - zamrożony przez FIFREEZE ioctl systemy plików.
|``k``  |    Bezpieczny dostęp do klucza SAK kasuje wszystkich programów znajdujących się w bieżącej konsoli wirtualnego. UWAGA: Zobacz ważne uwagi poniżej w sekcji SAK.
|``l``  |    Przedstawia backtrace stosu dla wszystkich aktywnych procesorów CPU.
|``m``  |    Będzie zrzutu bieżące informacje o pamięci do konsoli.
|``n``  |    Używane do tworzenia zadań RT nieuprzywilejowany stanie
|``o``  |    Wyłączy systemu (jeśli jest skonfigurowane i obsługiwane).
|``p``  |    Będzie zrzutu bieżącej rejestrów i flagi do konsoli.
|``q``  |    Będzie zrzutu dla procesora CPU listę wszystkich hrtimers zbrojnych (, ale nie czasomierzy regularne timer_list) i szczegółowe informacje o wszystkich urządzeniach clockevent.
|``r``  |    Wyłącza tryb pierwotne klawiatury i ustawia ją na XLATE.
|``s``  |    Będzie podejmować próby synchronizacji wszystkich zainstalowanych systemów plików.
|``t``  |    Będzie zrzutu listę bieżących zadań i ich do konsoli.
|``u``  |    Podejmie próbę ponownego instalowania wszystkich zainstalowanych systemów plików tylko do odczytu.
|``v``  |    Wymuszone przywraca konsoli bufor ramki
|``v``  |    Powoduje, że zrzutu buforu ETM ARM na specyficznych.
|``w``  |    Zrzuca zadania, które są w stanie (zablokowane) urządzenia UPS.
|``x``  |    Używana przez interfejs xmon na platformach do ppc/powerpc. Pokaż rejestruje PMU globalnej, wybierz sparc64. Zrzuć wszystkie wpisy TLB na MIPS.
|``y``  |    Pokaż globalnego rejestry Procesora [64 procesorami SPARC określonych]
|``z``  |    Bufor ftrace zrzutu
|``0``-``9`` | Ustawia poziom dziennika konsoli, kontrolowanie, które komunikaty jądra będą wypisywane w konsoli programu. (``0``, na przykład spowodowałoby, że tak, aby tylko awaryjnego wiadomości, takich jak rozruchem lub OOPSes spowodowałoby, że do konsoli.)

### <a name="distribution-specific-documentation"></a>Dystrybucja dokumentacji ###
Dokumentację specyficzne dla dystrybucji na SysRq i kroki, aby skonfigurować Linux do utworzenia zrzutu awaryjnego, gdy odbierze polecenie "Awarii" SysRq na ten temat można znaleźć w poniższych linków:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Zrzut awaryjny jądra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Co to jest funkcji SysRq i jak go używać?](https://access.redhat.com/articles/231663)
- [Jak używać funkcji SysRq do zbierania informacji z serwera systemu RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Konfigurowanie funkcji przechwytywania zrzutu core jądra](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Zbieranie dzienników awarii](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Niemaskowalnego przerwania (NMI) 
Niemaskowalnego przerwania (NMI) jest przeznaczone do tworzenia sygnał, który nie zignoruje oprogramowania na maszynie wirtualnej. W przeszłości NMIs zostały użyte w celu monitorowania problemów ze sprzętem w systemach, które są wymagane określone czasy.  Dzisiaj, programistów i system Administratorzy często używają NMI jako mechanizm do debugowania i rozwiązywanie problemów z systemów, które nie odpowiadają.

Konsoli szeregowej może służyć do wysyłania NMI na maszynie wirtualnej platformy Azure przy użyciu ikonę klawiatury na pasku poleceń, pokazano poniżej. Po NMI zostało dostarczone, konfiguracja maszyny wirtualnej będzie kontrolować, jak zachowuje się system.  Linux systemów operacyjnych można skonfigurować do awarii i Utwórz zrzut pamięci systemu operacyjnego odbiera NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Włącz NMI
W systemach Linux, które obsługują sysctl konfigurowania jądra parametry można włączyć alarm, po odebraniu tego NMI przy użyciu następujących czynności:
1. Dodanie tego wiersza, aby */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Ponowne uruchamianie lub aktualizowanie sysctl, uruchamiając <br>
    `sysctl -p`

Aby uzyskać więcej informacji na temat konfiguracje jądra systemu Linux, w tym `unknown_nmi_panic`, `panic_on_io_nmi`, i `panic_on_unrecovered_nmi`, zobacz: [Dokumentacja/proc/sys/jądra / *](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Dokumentację specyficzne dla dystrybucji na NMI i kroki, aby skonfigurować Linux do utworzenia zrzutu awaryjnego, gdy odbierze NMI na ten temat można znaleźć w poniższych linków:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Zrzut awaryjny jądra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [Co to jest NMI i co może służyć?](https://access.redhat.com/solutions/4127)
 - [Jak można skonfigurować system awarię wypchnięcie przełącznika NMI?](https://access.redhat.com/solutions/125103)
 - [Awaria zrzutu Podręcznik administratora](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Konfigurowanie funkcji przechwytywania zrzutu core jądra](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Zbieranie dzienników awarii](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Kolejne kroki
* Znajduje się Strona główna dokumentacji Serial konsoli systemu Linux [tutaj](serial-console-linux.md).
* Rozruch za pomocą konsoli szeregowej [CHODNIKÓW i wprowadzić w trybie jednego użytkownika](serial-console-grub-single-user-mode.md)
* Jest również dostępny dla konsoli szeregowej [Windows](serial-console-windows.md) maszyn wirtualnych
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md)