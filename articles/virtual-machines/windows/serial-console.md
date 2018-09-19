---
title: Konsola szeregowa maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dwukierunkowa konsoli szeregowej maszyn wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: d46c0382540f33f41c44249cf9464426de265000
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124249"
---
# <a name="virtual-machine-serial-console"></a>Konsola szeregowa maszyny wirtualnej


Konsola szeregowa maszyny wirtualnej na platformie Azure zapewnia dostęp do konsoli usługi oparte na tekście dla maszyn wirtualnych Windows. Jest to połączenie szeregowe do portu szeregowego COM1 maszyny wirtualnej, zapewniając dostęp do maszyny wirtualnej, który jest niezależny od sieci lub stan systemu operacyjnego maszyny wirtualnej. Dostęp do konsoli szeregowej dla maszyny wirtualnej można obecnie tylko można zrobić za pomocą witryny Azure portal i jest dozwolone tylko dla tych użytkowników, którzy mają Współautor maszyny Wirtualnej lub nowszej dostęp do maszyny wirtualnej. 

Konsola szeregowa dokumentację dotyczącą maszyn wirtualnych systemu Linux [tutaj](../linux/serial-console.md).

> [!Note] 
> Konsola szeregowa dla maszyn wirtualnych jest ogólnie dostępna w globalnych regionów platformy Azure. W tym momencie konsoli szeregowej nie jest jeszcze dostępna w chmurach platformy Azure Government lub Azure (Chiny).

 

## <a name="prerequisites"></a>Wymagania wstępne 

* Należy używać modelu wdrażania usługi resource management. W przypadku wdrożeń klasycznych nie są obsługiwane. 
* Maszyna wirtualna musi mieć [diagnostykę rozruchu](boot-diagnostics.md) włączone 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Konto, za pomocą konsoli szeregowej musi mieć [rola "Współautor"](../../role-based-access-control/built-in-roles.md) dla maszyny Wirtualnej i [diagnostykę rozruchu](boot-diagnostics.md) konta magazynu. 
* Maszyny wirtualnej, dla której jesteś konsoli szeregowej uzyskiwanie dostępu musi również mieć konto opartego na hasłach. Możesz je utworzyć za pomocą [Resetuj hasło](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkcjonalność maszyny Wirtualnej rozszerzenia dostępu — Zobacz poniższy zrzut ekranu.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsola szeregowa dla maszyn wirtualnych jest dostępny za pośrednictwem tylko [witryny Azure portal](https://portal.azure.com). Poniżej przedstawiono kroki umożliwiające dostęp do konsoli szeregowej dla maszyn wirtualnych za pośrednictwem portalu.

  1. Otwórz witrynę Azure portal
  2. W menu po lewej stronie wybierz maszyny wirtualne.
  3. Kliknij maszynę Wirtualną na liście. Zostanie otwarta strona Przegląd dla maszyny Wirtualnej.
  4. Przewiń w dół, pomoc techniczna i rozwiązywanie problemów z sekcji, a następnie kliknij opcję "Konsoli szeregowej". Nowe okienko z konsolą szeregową otworzy się i rozpocząć połączenie.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

## <a name="enable-serial-console-in-custom-or-older-images"></a>Włącz konsoli szeregowej na obrazach niestandardowych lub starszy
Nowsze obrazy systemu Windows Server na platformie Azure będzie mieć [specjalnej konsoli administracyjnej](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) domyślnie włączone (SAC). Konsola SAC jest obsługiwana w wersji Windows server, ale nie jest dostępna w wersji klienta (na przykład systemu Windows 10, Windows 8 lub Windows 7). Aby włączyć konsoli szeregowej dla maszyn wirtualnych Windows utworzone przed lutym 2018 roku, użyj następujących kroków: 

1. Połącz się z maszyną wirtualną Windows za pomocą pulpitu zdalnego
2. W administracyjnym wierszu polecenia Uruchom następujące polecenia 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Ponowne uruchomienie systemu pod kątem Konsola SAC włączenia


Jeśli to konieczne, konsola SAC można włączyć w trybie offline oraz:

1. Dołącz dysk systemu windows, który ma SAC skonfigurowany dla jako dysk danych do istniejącej maszyny Wirtualnej. 
2. W administracyjnym wierszu polecenia Uruchom następujące polecenia 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>Jak sprawdzić, czy włączono SAC?

Jeśli [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nie włączono konsoli szeregowej nie wyświetli monit SAC. W niektórych przypadkach pojawią się informacje o kondycji maszyny Wirtualnej, a w innych przypadkach będzie pusta. Jeśli używasz obrazu systemu Windows Server, utworzone przed lutym 2018 roku SAC prawdopodobnie nie zostaną włączone.

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>Włącz Menu Windows rozruchowego w konsoli szeregowej 

Jeśli musisz włączyć moduł ładujący rozruchu Windows monituje o do wyświetlenia w konsoli szeregowej, można dodać następujące dodatkowe opcje do danych konfiguracji rozruchu. Zobacz [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) Aby uzyskać więcej informacji

1. Połącz się z maszyną wirtualną Windows za pomocą pulpitu zdalnego
2. W administracyjnym wierszu polecenia Uruchom następujące polecenia 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Ponowne uruchomienie systemu pod kątem menu rozruchu jest włączona

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Użyj konsoli szeregowej NMI wywołań na maszynach wirtualnych Windows
Niemaskowalnego przerwania (NMI) jest przeznaczone do tworzenia sygnał, który nie zignoruje oprogramowania na maszynie wirtualnej. W przeszłości NMIs zostały użyte w celu monitorowania problemów ze sprzętem w systemach, które są wymagane określone czasy.  Dzisiaj, programistów i system Administratorzy często używają NMI jako mechanizm do debugowania i rozwiązywanie problemów z systemów, które są zawieszone.

Konsoli szeregowej może służyć do wysyłania NMI na maszynie wirtualnej platformy Azure przy użyciu ikonę klawiatury na pasku poleceń, pokazano poniżej. Po NMI zostało dostarczone, konfiguracja maszyny wirtualnej będzie kontrolować, jak zachowuje się system. Windows można skonfigurować do awarii i utworzyć zrzut pamięci podczas odbierania NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Aby uzyskać informacje na temat konfigurowania Windows do utworzenia zrzutu awaryjnego, gdy odbierze NMI, zobacz: [sposób generowania pliku zrzutu awaryjnego pełną lub pliku zrzutu awaryjnego jądra za pomocą NMI na komputerze z systemem Windows](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="disable-serial-console"></a>Wyłącz konsoli szeregowej
Domyślnie wszystkie subskrypcje mają dostęp do konsoli szeregowej włączone dla wszystkich maszyn wirtualnych. Można wyłączyć konsoli szeregowej na poziomie subskrypcji lub na poziomie maszyny Wirtualnej. 

> [!Note] 
> Aby włączyć lub wyłączyć konsoli szeregowej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Obejmuje, ale nie jest ograniczona do ról administratora lub właściciela. Role niestandardowe mogą również mieć uprawnienia do zapisu.

### <a name="subscription-level-disable"></a>Wyłącz poziom subskrypcji
Można wyłączyć dla całej subskrypcji, za pośrednictwem konsoli szeregowej [Wyłącz konsoli wywołania interfejsu API REST](https://aka.ms/disableserialconsoleapi). Może używać "Try It" Funkcje dostępne na stronie dokumentacji interfejsu API, wyłączyć lub włączyć konsoli szeregowej dla subskrypcji. Wprowadź swoje `subscriptionId`, "default" w `default` pola, a następnie kliknij przycisk Uruchom. Poleceń interfejsu wiersza polecenia platformy Azure nie są jeszcze dostępne i zostaną dostarczone w późniejszym terminie. [Spróbuj wywołania interfejsu API REST w tym miejscu](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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
Po zalogowaniu się żadne hasła dostępu do konsoli, czy polecenia uruchamiane w ramach konsoli programu zawiera dane wyjściowe haseł, kluczy tajnych, nazwy użytkowników lub inne formy z osobiście identyfikowalne dane osobowe, te zostaną zapisane Diagnostyka rozruchu maszyny wirtualnej dzienniki, oraz wszystkie inne teksty widoczne, jako część wdrożenia konsoli szeregowej przewijania kopii funkcji. Te dzienniki są cykliczne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mają do nich dostępu, jednak zalecamy następujące najlepsze rozwiązanie polegające na przy użyciu pulpitu zdalnego dla wszystkich elementów, które mogą obejmować wpisów tajnych i/lub dane osobowe. 

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsoli szeregowej i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, zostaną rozłączone pierwszego użytkownika, a drugi użytkownik nawiązał połączenie w sposób, podobnie pierwszy użytkownik, stały i pozostawienie niezmienionej konsoli fizycznej i nowy Użytkownik, znajdują się w dół.

>[!CAUTION] 
Oznacza to, że użytkownik, który pobiera odłączony nie będą rejestrowane! Możliwość wymuszenia wylogowania po rozłączenia (za pośrednictwem SIGHUP lub podobny mechanizm) nadal działa w planach. Windows jest automatyczne limitu czasu włączone w SAC, jednak dla systemu Linux można skonfigurować ustawienia limitu czasu w terminalu. 

## <a name="common-scenarios-for-accessing-serial-console"></a>Typowe scenariusze dotyczące uzyskiwania dostępu do konsoli szeregowej 
Scenariusz          | Akcje w konsoli szeregowej                
:------------------|:-----------------------------------------
Reguły zapory niepoprawne | Dostęp do serial konsoli i napraw zasady Windows w zapory. 
System plików uszkodzenie/wyboru | Dostęp do konsoli szeregowej i odzyskiwanie systemu plików. 
Problemy z konfiguracją protokołu RDP | Dostęp do konsoli szeregowej i zmienić ustawienia. Przejdź do [dokumentacji protokołu RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access) na rozpoczęcie pracy.
Blokowanie sieci w systemie| Dostęp do konsoli szeregowej za pośrednictwem portalu zarządzania systemem. Niektóre polecenia sieci są wymienione w [dokumentacji Serial konsoli CMD i programu PowerShell](./serial-console-cmd-ps-commands.md). 
Interakcja z programu inicjującego | Dostęp do danych konfiguracji rozruchu za pośrednictwem konsoli szeregowej. Przejdź do [włączanie menu rozruchu do wyświetlenia w konsoli szeregowej](#enabling-boot-menu-to-show-in-the-serial-console) na rozpoczęcie pracy. 

## <a name="accessibility"></a>Ułatwienia dostępu
Dostępność jest kluczowym dla konsoli szeregowej platformy Azure. W tym celu firma Microsoft ma zapewnić, że konsoli szeregowej jest dostępny dla osób z wizualizacji i osoby niedosłyszące oraz osoby, które mogą okazać się niemożliwe przy użyciu myszy.

### <a name="keyboard-navigation"></a>Nawigowanie przy użyciu klawiatury
Użyj `tab` kluczowe na klawiaturze, aby nawigować interfejsu konsoli szeregowej, w portalu Azure. Twoja lokalizacja zostanie wyróżniony na ekranie. Aby opuścić fokus bloku konsoli szeregowej, naciśnij klawisz `Ctrl + F6` na klawiaturze.

### <a name="use-serial-console-with-a-screen-reader"></a>Użyj konsoli szeregowej przy użyciu czytnika zawartości ekranu
Konsola szeregowa ma wbudowaną obsługę czytników zawartości ekranu. Przemieszczać się przy użyciu czytnika zawartości ekranu włączone umożliwi tekst alternatywny dla aktualnie wybranego przycisku zostanie odczytany na głos przez czytnik zawartości ekranu.

## <a name="errors"></a>Błędy
Większość błędów są przejściowe w rodzaju i ponawianie próby adres połączenia tych. W poniższej tabeli przedstawiono listę błędy i środki zaradcze

Błąd                            |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu "<VMNAME>". Aby korzystać z konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostykę rozruchu](boot-diagnostics.md) włączone. 
Maszyna wirtualna jest w stanie zatrzymania, przydział zostanie cofnięty. Uruchom maszynę Wirtualną i spróbuj ponownie nawiązać połączenie konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym uzyskać dostęp do konsoli szeregowej
Nie masz wymaganych uprawnień do używania konsoli szeregowej maszyny Wirtualnej. Upewnij się, że co najmniej uprawnienia roli Współautor maszyny Wirtualnej.| Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymaganiami dotyczącymi dostępu](#prerequisites) Aby uzyskać szczegółowe informacje
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu "<STORAGEACCOUNTNAME>". Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymaganiami dotyczącymi dostępu](#prerequisites) Aby uzyskać szczegółowe informacje
Odpowiedź "Dostęp zabroniony" napotkano podczas uzyskiwania dostępu do konta magazynu diagnostyki rozruchu dla tej maszyny Wirtualnej. | Upewnij się, że tej diagnostyki rozruchu zapory konta. Konto magazynu diagnostyki rozruchu dostępny jest niezbędne do konsoli szeregowej funkcjonowania.
Gniazda sieci Web został zamknięty lub nie można otworzyć. | Może być konieczne do listy dozwolonych `*.console.azure.com`. Bardziej szczegółowe, ale dłużej podejście jest do listy dozwolonych adresów [zakresów adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), które są stosunkowo regularnie zmieniane.
Tylko informacje o kondycji jest wyświetlany podczas nawiązywania połączenia maszyny Wirtualnej z systemem Windows| Spowoduje to wyświetlenie Jeśli specjalnej konsoli administracyjnej nie został włączony dla obrazu systemu Windows. Zobacz [dostęp szeregowy konsoli dla Windows](#access-serial-console-for-windows) instrukcje na temat sposobu ręcznego włączenia SAC na maszynie Wirtualnej Windows. Więcej informacji znajduje się w temacie [sygnałów kondycji Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Znane problemy 
Mamy świadomość, problemy z konsolą szeregową. Poniżej przedstawiono listę tych problemów oraz kroki dotyczące ograniczania ryzyka.

Problem                             |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Naciśnięcie wprowadź po transparent połączenia nie są wyświetlane dziennika w wierszu polecenia | Zobacz tę stronę: [Hitting wprowadź, nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). To może się zdarzyć, jeśli używasz niestandardowej maszyny Wirtualnej wzmocnione urządzenia lub CHODNIKÓW konfiguracji tego Windows causers niepowodzenie prawidłowo połączenia do portu szeregowego.
Nie można wpisać w SAC stanie się monit, jeśli włączone jest debugowanie jądra | Nawiązać połączenia RDP z maszyną Wirtualną i uruchom `bcdedit /debug {current} off` z wiersza polecenia z podwyższonym poziomem uprawnień. Jeśli z jakiegoś powodu RDP można zamiast tego Dołącz dysk systemu operacyjnego do innej maszyny Wirtualnej platformy Azure i zmodyfikuj go podczas podłączyć jako dysku danych przy użyciu `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, następnie zamienić ponownie dysk.
Wklejając w programie PowerShell powoduje SAC trzeciego znaku gdyby pierwotną wersją zawartości powtarzające się znaki | Obejście tego problemu jest zwolnienie modułu PSReadLine z bieżącej sesji. Uruchom `Remove-Module PSReadLine` wyładować modułu PSReadLine z bieżącej sesji — to będzie nie usuwać ani odinstalowania modułu.
Niektóre dane wejściowe z klawiatury generuje dziwne SAC danych wyjściowych (np. `[A`, `[3~`) | [VT100](https://aka.ms/vtsequences) sekwencje ucieczki nie są obsługiwane przez wiersz SAC.
Wklejanie bardzo długich ciągów nie działa | Konsola szeregowa ogranicza długość ciągów w terminalu, aby 2048 znaków. To, aby uniknąć przeciążenia przepustowość portu szeregowego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania 
**PYTANIA I ODPOWIEDZI. Jak wysłać opinię?**

A. Przekazać opinię jako problem, przechodząc do https://aka.ms/serialconsolefeedback. Alternatywnie mniej (preferowane) wysyłanie opinii za pośrednictwem azserialhelp@microsoft.com lub w kategorii maszyny wirtualnej http://feedback.azure.com

**PYTANIA I ODPOWIEDZI. Konsola szeregowa obsługuje kopiowania/wklejania?**

A. Tak samo. Użyj klawiszy Ctrl + Shift + C i Ctrl + Shift + V do kopiowania i wklejania w terminalu.

**PYTANIA I ODPOWIEDZI. Kto może włączyć lub wyłączyć konsoli szeregowej dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsoli szeregowej na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują, ale nie są ograniczone do ról administratora lub właściciela. Role niestandardowe mogą również mieć uprawnienia do zapisu.

**PYTANIA I ODPOWIEDZI. Kto ma dostęp do konsoli szeregowej dla mojej maszyny Wirtualnej?**

A. Konieczne jest posiadanie dostępu na poziomie współautora lub nowszej, aby maszyna wirtualna w celu uzyskania dostępu do konsoli szeregowej maszyny Wirtualnej. 

**PYTANIA I ODPOWIEDZI. Moje konsoli szeregowej nie widać niczego, co należy zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowane, aby uzyskać dostęp do konsoli szeregowej. Zobacz [Włącz konsoli szeregowej na obrazach niestandardowych lub starsze](#Enable-Serial-Console-in-custom-or-older-images) Aby uzyskać szczegółowe informacje na temat konfigurowania swój obraz, aby umożliwić konsoli szeregowej.

**PYTANIA I ODPOWIEDZI. Konsola szeregowa jest dostępna dla zestawów skalowania maszyn wirtualnych?**

A. Dostęp do konsoli szeregowej dla wystąpień zestawu skalowania maszyn wirtualnych w tej chwili nie jest obsługiwana.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać szczegółowy przewodnik poleceń programu PowerShell i CMD, można użyć w Windows SAC, kliknij [tutaj](serial-console-cmd-ps-commands.md).
* Jest również dostępny dla konsoli szeregowej [Linux](../linux/serial-console.md) maszyn wirtualnych.
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md).
