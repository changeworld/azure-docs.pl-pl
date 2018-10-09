---
title: Konsola szeregowa maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dwukierunkowa konsoli szeregowej maszyn wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/11/2018
ms.author: harijay
ms.openlocfilehash: ce799f4201a560077c5bb1b943a9e587a71806f2
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856347"
---
# <a name="virtual-machine-serial-console"></a>Konsola szeregowa maszyny wirtualnej


Konsola szeregowa maszyny wirtualnej na platformie Azure zapewnia dostęp do konsoli usługi oparte na tekście dla maszyn wirtualnych systemu Linux. Jest to połączenie szeregowe do portu szeregowego COM1 maszyny wirtualnej, zapewniając dostęp do maszyny wirtualnej, który jest niezależny od sieci lub stan systemu operacyjnego maszyny wirtualnej. Dostęp do konsoli szeregowej dla maszyny wirtualnej można obecnie tylko można zrobić za pomocą witryny Azure portal i jest dozwolone tylko dla tych użytkowników, którzy mają Współautor maszyny Wirtualnej lub nowszej dostęp do maszyny wirtualnej. 

Dokumentacja konsoli szeregowej, w przypadku maszyn wirtualnych Windows [tutaj](../windows/serial-console.md).

> [!Note] 
> Konsola szeregowa dla maszyn wirtualnych jest ogólnie dostępna w globalnych regionów platformy Azure. W tym momencie konsoli szeregowej nie jest jeszcze dostępna w chmurach platformy Azure Government lub Azure (Chiny).


## <a name="prerequisites"></a>Wymagania wstępne 

* Należy używać modelu wdrażania usługi resource management. W przypadku wdrożeń klasycznych nie są obsługiwane. 
* Maszyna wirtualna musi mieć [diagnostykę rozruchu](boot-diagnostics.md) włączone — zobacz poniższy zrzut ekranu.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Musi mieć konto platformy Azure przy użyciu konsoli szeregowej [rola "Współautor"](../../role-based-access-control/built-in-roles.md) dla maszyny Wirtualnej i [diagnostykę rozruchu](boot-diagnostics.md) konta magazynu. 
* Maszyny wirtualnej, dla której jesteś konsoli szeregowej uzyskiwanie dostępu musi również mieć konto opartego na hasłach. Możesz je utworzyć za pomocą [Resetuj hasło](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkcjonalność maszyny Wirtualnej rozszerzenia dostępu — Zobacz poniższy zrzut ekranu.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Aby uzyskać ustawienia właściwe dla dystrybucje systemu Linux, zobacz [dostęp do konsoli szeregowej dla systemu Linux](#Serial-Console-Linux-distro-availability)



## <a name="get-started-with-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsola szeregowa dla maszyn wirtualnych jest dostępny za pośrednictwem tylko [witryny Azure portal](https://portal.azure.com). Upewnij się, że zostały spełnione [wymagania wstępne](#prerequisites) powyżej. Poniżej przedstawiono kroki umożliwiające dostęp do konsoli szeregowej dla maszyn wirtualnych za pośrednictwem portalu:

  1. Otwórz witrynę Azure portal
  1. (Pomiń to Jeśli maszyna wirtualna ma użytkownika, który korzysta z uwierzytelniania za pomocą hasła) Dodawanie użytkownika przy użyciu uwierzytelniania nazwy użytkownika/hasła, klikając blok "Resetuj hasło"
  1. W menu po lewej stronie wybierz maszyny wirtualne.
  1. Kliknij maszynę Wirtualną na liście. Zostanie otwarta strona Przegląd dla maszyny Wirtualnej.
  1. Przewiń w dół, pomoc techniczna i rozwiązywanie problemów z sekcji, a następnie kliknij opcję "Konsoli szeregowej". Nowe okienko z konsolą szeregową otworzy się i rozpocząć połączenie.

![](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### 

> [!NOTE] 
> Konsoli szeregowej wymaga użytkownika lokalnego przy użyciu hasła skonfigurowane. W tej chwili maszyny wirtualne tylko przy użyciu klucza publicznego SSH nie będzie można zalogować się do konsoli szeregowej. Aby utworzyć użytkownika lokalnego przy użyciu hasła, użyj [rozszerzenia dostępu do maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), która jest dostępna w witrynie portal, klikając pozycję "Resetuj hasło" w portalu i utworzenie użytkownika lokalnego przy użyciu hasła.
> Może także zresetować hasło administratora na swoim koncie przez [umieszczeniu w trybie jednego użytkownika za pomocą programu GRUB](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distro-availability"></a>Serial dostępności dystrybucja systemu Linux z konsoli
Aby konsoli szeregowej działała poprawnie system operacyjny gościa, należy określić do odczytywania i zapisywania komunikatów konsoli do portu szeregowego. Większość [dystrybucji systemu Linux zalecanych dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) zostały skonfigurowane domyślnie konsoli szeregowej. Wystarczy kliknąć sekcji konsoli szeregowej w witrynie Azure portal zapewni dostęp do konsoli. 

Dystrybucja      | Dostęp do konsoli szeregowej
:-----------|:---------------------
Red Hat Enterprise Linux    | Red Hat Enterprise Linux obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli. 
CentOS      | CentOS obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli. 
Ubuntu      | Ubuntu obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli.
CoreOS      | CoreOS obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli.
SUSE        | Nowsze obrazów SLES dostępnych na platformie Azure mają domyślnie dostęp do konsoli. Jeśli używasz starszych wersji w systemie SLES (10 lub starszej) na platformie Azure, postępuj zgodnie z [artykuł bazy wiedzy](https://www.novell.com/support/kb/doc.php?id=3456486) umożliwiające konsoli szeregowej. 
Oracle Linux        | Obrazy oprogramowania Oracle Linux dostępnych na platformie Azure mają domyślnie dostęp do konsoli.
Niestandardowych obrazów systemu Linux     | Aby włączyć konsoli szeregowej niestandardowego obrazu maszyny Wirtualnej systemu Linux, Włącz dostęp do konsoli w `/etc/inittab` systemem terminalu `ttyS0`. Oto przykład, aby dodać to w pliku inittab: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Aby uzyskać więcej informacji na temat prawidłowo tworzenia obrazów niestandardowych, zobacz [tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure](https://aka.ms/createuploadvhd). Jeśli tworzysz niestandardowy jądra są niektóre flagi jądra, należy rozważyć włączenie `CONFIG_SERIAL_8250=y` i `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Plik konfiguracji, który często znajduje się w /boot/ dalszych badań.

## <a name="common-scenarios-for-accessing-serial-console"></a>Typowe scenariusze dotyczące uzyskiwania dostępu do konsoli szeregowej 
Scenariusz          | Akcje w konsoli szeregowej                
:------------------|:-----------------------------------------
Uszkodzony plik FSTAB | `Enter` klucz, aby kontynuować, a następnie usuń plik fstab za pomocą edytora tekstów. Konieczne może być w trybie jednego użytkownika dla tego. Zobacz [Rozwiązywanie problemów z fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) i [przy użyciu konsoli szeregowej, dostęp do programu GRUB i tryb jednego użytkownika](serial-console-grub-single-user-mode.md) na rozpoczęcie pracy.
Reguły zapory niepoprawne | Dostęp do konsoli szeregowej i naprawić iptables. 
System plików uszkodzenie/wyboru | Dostęp do konsoli szeregowej i odzyskiwanie systemu plików. 
Problemy z konfiguracją protokołu RDP/SSH | Dostęp do konsoli szeregowej i zmienić ustawienia. 
Blokowanie sieci w systemie| Dostęp do konsoli szeregowej za pośrednictwem portalu zarządzania systemem. 
Interakcja z programu inicjującego | Program GRUB dostęp za pośrednictwem konsoli szeregowej. Przejdź do [przy użyciu konsoli szeregowej, dostęp do programu GRUB i tryb jednego użytkownika](serial-console-grub-single-user-mode.md) na rozpoczęcie pracy. 

## <a name="disable-serial-console"></a>Wyłącz konsoli szeregowej
Domyślnie wszystkie subskrypcje mają dostęp do konsoli szeregowej włączone dla wszystkich maszyn wirtualnych. Można wyłączyć konsoli szeregowej na poziomie subskrypcji lub na poziomie maszyny Wirtualnej.

> [!Note] 
> Aby włączyć lub wyłączyć konsoli szeregowej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Obejmuje, ale nie jest ograniczona do ról administratora lub właściciela. Role niestandardowe mogą również mieć uprawnienia do zapisu.

### <a name="subscription-level-disable"></a>Wyłącz poziom subskrypcji
Można wyłączyć dla całej subskrypcji, za pośrednictwem konsoli szeregowej [Wyłącz konsoli wywołania interfejsu API REST](https://aka.ms/disableserialconsoleapi). Może używać "Try It" Funkcje dostępne na stronie dokumentacji interfejsu API, wyłączyć lub włączyć konsoli szeregowej dla subskrypcji. Wprowadź swoje `subscriptionId`, "default" w `default` pola, a następnie kliknij przycisk Uruchom. Poleceń interfejsu wiersza polecenia platformy Azure nie są jeszcze dostępne i zostaną dostarczone w późniejszym terminie. [Spróbuj wywołania interfejsu API REST w tym miejscu](https://aka.ms/disableserialconsoleapi).

![](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Można również mogą użyć zestawu poniższe polecenia w usłudze Cloud Shell (przedstawionych poleceń powłoki bash) można wyłączyć, włączyć i wyświetlić stan wyłączonymi konsoli szeregowej dla subskrypcji. 

* Aby wyświetlić stan wyłączenia konsoli szeregowej dla subskrypcji:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Aby wyłączyć konsoli szeregowej subskrypcji:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Aby włączyć konsoli szeregowej subskrypcji:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Wyłącz poziomie maszyny Wirtualnej
Konsola szeregowa można wyłączyć dla określonych maszyn wirtualnych przez wyłączenie ustawienia diagnostyki rozruchu dla tej maszyny Wirtualnej. Po prostu wyłączyć diagnostykę rozruchu w witrynie Azure portal i konsoli szeregowej zostanie wyłączony dla maszyny Wirtualnej.

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowej 

### <a name="access-security"></a>Zabezpieczenia dostępu 
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają [współautorzy maszyny Wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub powyżej dostęp do maszyny wirtualnej. Jeśli dzierżawa usługi AAD wymaga uwierzytelniania wieloskładnikowego, a następnie dostęp do konsoli szeregowej muszą także MFA się jego dostęp za pośrednictwem [witryny Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane, które są wysyłane w obie strony są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Dostęp do konsoli szeregowej jest aktualnie zalogowany [diagnostykę rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) dzienniki maszyny wirtualnej. Dostęp do tych dzienników są własnością i kontrolowane przez administratora maszyny wirtualnej platformy Azure.  

>[!CAUTION] 
Po zalogowaniu się żadne hasła dostępu do konsoli, czy polecenia uruchamiane w ramach konsoli programu zawiera dane wyjściowe haseł, kluczy tajnych, nazwy użytkowników lub inne formy z osobiście identyfikowalne dane osobowe, te zostaną zapisane Diagnostyka rozruchu maszyny wirtualnej rejestruje oraz wszystkie inne teksty widoczne, jako część wykonania funkcji scrollback konsoli szeregowej. Te dzienniki są cykliczne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mają do nich dostępu, jednak zalecamy następujące najlepsze rozwiązanie polegające na przy użyciu konsoli SSH dla wszystkich elementów, które mogą obejmować wpisów tajnych i/lub dane osobowe. 

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsoli szeregowej i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, zostaną rozłączone pierwszego użytkownika, a drugi użytkownik nawiązał połączenie w sposób, podobnie pierwszy użytkownik, stały i pozostawienie niezmienionej konsoli fizycznej i nowy Użytkownik, znajdują się w dół.

>[!CAUTION] 
Oznacza to, że użytkownik, który pobiera odłączony nie będą rejestrowane! Możliwość wymuszenia wylogowania po rozłączenia (za pośrednictwem SIGHUP lub podobny mechanizm) nadal działa w planach. Dla Windows ma automatycznego limitu czasu w SAC włączone jednak dla systemu Linux można skonfigurować ustawienia limitu czasu w terminalu. Aby zrobić to po prostu Dodaj `export TMOUT=600` .bash_profile lub podstawową dla użytkownika logowania w konsoli, przekroczenie limitu czasu sesji, po upływie 10 minut.

## <a name="accessibility"></a>Ułatwienia dostępu
Dostępność jest kluczowym dla konsoli szeregowej platformy Azure. W tym celu firma Microsoft ma zapewnić, że konsoli szeregowej jest dostępny dla osób z wizualizacji i osoby niedosłyszące oraz osoby, które mogą okazać się niemożliwe przy użyciu myszy.

### <a name="keyboard-navigation"></a>Nawigowanie przy użyciu klawiatury
Użyj `tab` kluczowe na klawiaturze, aby nawigować interfejsu konsoli szeregowej, w portalu Azure. Twoja lokalizacja zostanie wyróżniony na ekranie. Aby opuścić fokus bloku konsoli szeregowej, naciśnij klawisz `Ctrl + F6` na klawiaturze.

### <a name="use-serial-console-with-a-screen-reader"></a>Użyj konsoli szeregowej przy użyciu czytnika zawartości ekranu
Konsola szeregowa ma wbudowaną obsługę czytników zawartości ekranu. Przemieszczać się przy użyciu czytnika zawartości ekranu włączone umożliwi tekst alternatywny dla aktualnie wybranego przycisku zostanie odczytany na głos przez czytnik zawartości ekranu.

## <a name="errors"></a>Błędy
Większość błędów jest przejściowy z natury i ponawianie próby połączenia konsoli szeregowej często tych adresów. W poniższej tabeli przedstawiono listę błędy i środki zaradcze

Błąd                            |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu "<VMNAME>". Aby korzystać z konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostykę rozruchu](boot-diagnostics.md) włączone. 
Maszyna wirtualna jest w stanie zatrzymania, przydział zostanie cofnięty. Uruchom maszynę Wirtualną i spróbuj ponownie nawiązać połączenie konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym uzyskać dostęp do konsoli szeregowej
Nie masz wymaganych uprawnień, aby użyć tej konsoli szeregowej maszyny Wirtualnej. Upewnij się, że co najmniej uprawnienia roli Współautor maszyny Wirtualnej.| Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymaganiami dotyczącymi dostępu](#prerequisites) Aby uzyskać szczegółowe informacje
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu "<STORAGEACCOUNTNAME>". Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymaganiami dotyczącymi dostępu](#prerequisites) Aby uzyskać szczegółowe informacje
Gniazda sieci Web został zamknięty lub nie można otworzyć. | Może być konieczne do listy dozwolonych `*.console.azure.com`. Bardziej szczegółowe, ale dłużej podejście jest do listy dozwolonych adresów [zakresów adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), które są stosunkowo regularnie zmieniane.
Odpowiedź "Dostęp zabroniony" napotkano podczas uzyskiwania dostępu do konta magazynu diagnostyki rozruchu dla tej maszyny Wirtualnej. | Upewnij się, że tej diagnostyki rozruchu zapory konta. Konto magazynu diagnostyki rozruchu dostępny jest niezbędne do konsoli szeregowej funkcjonowania.

## <a name="known-issues"></a>Znane problemy 
Mamy świadomość, problemy z konsolą szeregową. Poniżej przedstawiono listę tych problemów oraz kroki dotyczące ograniczania ryzyka.

Problem                           |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Naciśnięcie wprowadź po transparent połączenia nie są wyświetlane dziennika w wierszu polecenia | Zobacz tę stronę: [Hitting wprowadź, nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Może to nastąpić, jeśli używasz niestandardowej maszyny Wirtualnej, urządzenia ze wzmocnionymi zabezpieczeniami lub konfiguracji programu GRUB, powodujący systemu Linux nie można prawidłowo nawiązać portu szeregowego.
Tekst konsoli szeregowej trwa tylko część rozmiaru ekranu (często po nim za pomocą edytora tekstów) | Negocjowanie o rozmiar okna nie obsługują konsoli szeregowej ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), co oznacza, że nie będzie brak sygnału SIGWINCH wysłanych do zaktualizowania rozmiaru ekranu i maszyna wirtualna będzie miała żadnej znajomości rozmiar usługi terminalowe. Firma Microsoft zaleca instaling xterm lub niektóre podobnej użyteczności, zapewniająca polecenia "Zmień rozmiar". Uruchamianie "rozmiar" Aby rozwiązać ten problem.
Wklejanie bardzo długich ciągów nie działa | Konsola szeregowa ogranicza długość ciągów w terminalu, aby 2048 znaków. To, aby uniknąć przeciążenia przepustowość portu szeregowego.


## <a name="frequently-asked-questions"></a>Często zadawane pytania 
**PYTANIA I ODPOWIEDZI. Jak wysłać opinię?**

A. Przekazać opinię jako problem, przechodząc do https://aka.ms/serialconsolefeedback. Alternatywnie (mniej preferowany), wysyłanie opinii za pośrednictwem azserialhelp@microsoft.com lub w kategorii maszyny wirtualnej http://feedback.azure.com

**PYTANIA I ODPOWIEDZI. Konsola szeregowa obsługuje kopiowania/wklejania?**

A. Tak samo. Użyj klawiszy Ctrl + Shift + C i Ctrl + Shift + V do kopiowania i wklejania w terminalu.

**PYTANIA I ODPOWIEDZI. Czy można użyć konsoli szeregowej, zamiast połączenia SSH?**

A. A to może wydawać się to technicznie możliwe, konsoli szeregowej ma służyć przede wszystkim podczas rozwiązywania problemów w sytuacjach, gdy łączność za pośrednictwem protokołu SSH nie jest możliwe. Zaleca się przy użyciu konsoli szeregowej jako zamiennika SSH dwóch powodów:

1. Konsola szeregowa nie ma przepustowości, takiej jak SSH - jest połączenie tylko do tekstu, dlatego więcej interakcje ciężkich graficznego interfejsu użytkownika będzie trudne w konsoli szeregowej.
1. Dostęp do konsoli szeregowej jest obecnie tylko przez użytkownika i hasło. Klucze SSH są znacznie bezpieczniejsze niż kombinacji nazwy użytkownika/hasła, więc z punktu widzenia zabezpieczeń logowania SSH firma Microsoft zaleca za pośrednictwem konsoli szeregowej.

**PYTANIA I ODPOWIEDZI. Kto może włączyć lub wyłączyć konsoli szeregowej dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsoli szeregowej na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują, ale nie są ograniczone do ról administratora lub właściciela. Role niestandardowe mogą również mieć uprawnienia do zapisu.

**PYTANIA I ODPOWIEDZI. Kto ma dostęp do konsoli szeregowej dla mojej maszyny Wirtualnej?**

A. Konieczne jest posiadanie dostępu na poziomie współautora lub nowszej, aby maszyna wirtualna w celu uzyskania dostępu do konsoli szeregowej maszyny Wirtualnej. 

**PYTANIA I ODPOWIEDZI. Moje konsoli szeregowej nie widać niczego, co należy zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowane, aby uzyskać dostęp do konsoli szeregowej. Zobacz [dostępu do konsoli szeregowej dla systemu Linux](#Access-Serial-Console-for-Linux) Aby uzyskać szczegółowe informacje na temat konfigurowania swój obraz, aby umożliwić konsoli szeregowej.

**PYTANIA I ODPOWIEDZI. Konsola szeregowa jest dostępna dla zestawów skalowania maszyn wirtualnych?**

A. Dostęp do konsoli szeregowej dla wystąpień zestawu skalowania maszyn wirtualnych w tej chwili nie jest obsługiwana.

**PYTANIA I ODPOWIEDZI. Skonfigurować moją maszynę Wirtualną przy użyciu tylko uwierzytelnianie klucza SSH, czy nadal mogę korzystać konsoli szeregowej nawiązywania połączenia z maszyną Wirtualną?** A. tak. Konsola szeregowa nie wymaga kluczy SSH, wszystko, co należy zrobić jest więc skonfigurowane kombinacja nazwy użytkownika i hasła. Można to zrobić za pomocą bloku "Resetuj hasło" w portalu, a następnie użyciu tych poświadczeń do zalogowania się do konsoli szeregowej.

## <a name="next-steps"></a>Kolejne kroki
* Użyj konsoli szeregowej [rozruch CHODNIKÓW, a następnie wprowadź w trybie jednego użytkownika](serial-console-grub-single-user-mode.md)
* Użyj konsoli szeregowej dla [NMI i SysRq wywołania](serial-console-nmi-sysrq.md)
* Jest również dostępny dla konsoli szeregowej [Windows](../windows/serial-console.md) maszyn wirtualnych
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md)