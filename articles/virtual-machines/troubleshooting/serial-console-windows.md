---
title: Serial konsoli platformy Azure dla Windows | Dokumentacja firmy Microsoft
description: Dwukierunkowa konsoli szeregowej maszyny wirtualne platformy Azure i zestawów skalowania maszyn wirtualnych.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 6fd7f36510bdc7ed56ede6a5743a5f131149472e
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834753"
---
# <a name="azure-serial-console-for-windows"></a>Serial konsoli platformy Azure dla Windows

Konsoli szeregowej w witrynie Azure portal zapewnia dostęp do konsoli usługi oparte na tekście dla Windows maszyn wirtualnych (VM) i wystąpienia zestawu skalowania maszyn wirtualnych. To połączenie szeregowe łączy do portu szeregowego COM1 maszyny Wirtualnej lub maszyny wirtualnej wystąpienia w zestawie skalowania, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego. Konsoli szeregowej może zostać oceniony jedynie przy użyciu witryny Azure portal i jest dozwolone tylko dla tych użytkowników, którzy mają dostęp do roli współautora lub nowszej, aby zestaw skalowania maszyny Wirtualnej lub maszyny wirtualnej.

Konsola szeregowa działa w taki sam sposób w przypadku maszyn wirtualnych i wystąpień zestawu skalowania maszyn wirtualnych. W tym dokumencie wszystkie wystąpienia maszyn wirtualnych niejawnie obejmie wystąpień zestawu skalowania maszyny wirtualnej, chyba że określono inaczej.

Konsola szeregowa dokumentację dotyczącą maszyn wirtualnych systemu Linux i zestawu skalowania maszyn wirtualnych, zobacz [konsoli szeregowej platformy Azure dla systemu Linux](serial-console-linux.md).

> [!NOTE]
> Konsoli szeregowej jest ogólnie dostępna w regionach platformy Azure na świecie. Nie jest jeszcze dostępne w Azure dla instytucji rządowych lub chmury chińskiej wersji platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne

* Wystąpienia zestawu skalowania maszyny Wirtualnej lub maszyny wirtualnej, należy użyć modelu wdrażania usługi resource management. W przypadku wdrożeń klasycznych nie są obsługiwane.

- Twoje konto, którego używa konsoli szeregowej muszą mieć [rola Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny Wirtualnej i [diagnostykę rozruchu](boot-diagnostics.md) konta magazynu

- Wystąpienia zestawu skalowania maszyny Wirtualnej lub maszyny wirtualnej musi mieć na podstawie hasła użytkownika. Możesz je utworzyć za pomocą [Resetuj hasło](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkcji rozszerzenia dostępu do maszyny Wirtualnej. Wybierz **Resetuj hasło** z **pomoc techniczna i rozwiązywanie problemów z** sekcji.

* Maszyna wirtualna, w którym uzyskujesz dostęp do konsoli szeregowej muszą mieć [diagnostykę rozruchu](boot-diagnostics.md) włączone.

    ![Ustawienia diagnostyki rozruchu](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="get-started-with-the-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsoli szeregowej dla maszyn wirtualnych i zestawu skalowania maszyn wirtualnych jest dostępna tylko za pośrednictwem witryny Azure portal:

### <a name="serial-console-for-virtual-machines"></a>Konsola szeregowa dla maszyn wirtualnych
Konsola szeregowa dla maszyn wirtualnych jest tak proste jak kliknięcie **konsoli szeregowej** w ramach **pomoc techniczna i rozwiązywanie problemów z** sekcji w witrynie Azure portal.
  1. Otwórz [portal Azure](https://portal.azure.com).

  1. Przejdź do **wszystkie zasoby** i wybierz maszynę wirtualną. Zostanie otwarta strona Przegląd dla maszyny Wirtualnej.

  1. Przewiń w dół do **pomoc techniczna i rozwiązywanie problemów z** i wybierz pozycję **konsoli szeregowej**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Konsola szeregowa dla zestawów skalowania maszyn wirtualnych
Konsola szeregowa jest dostępna na podstawie poszczególnych wystąpień dla zestawów skalowania maszyn wirtualnych. Trzeba będzie przejść do poszczególnych wystąpień zestawu skalowania maszyn wirtualnych zanim **konsoli szeregowej** przycisku. Jeśli zestaw skalowania maszyn wirtualnych nie ma włączoną diagnostyką rozruchu, upewnij się, że aktualizacja modelu zestawu skalowania maszyn wirtualnych, tak aby włączyć diagnostykę rozruchu, a następnie Uaktualnij wszystkie wystąpienia do nowego modelu, aby uzyskać dostęp do konsoli szeregowej.
  1. Otwórz [portal Azure](https://portal.azure.com).

  1. Przejdź do **wszystkie zasoby** i wybierz zestaw skalowania maszyn wirtualnych. Strona przeglądu skalowania maszyn wirtualnych Ustaw zostanie otwarta.

  1. Przejdź do **wystąpień**

  1. Wybierz wystąpienie zestawu skalowania maszyny wirtualnej

  1. Z **pomoc techniczna i rozwiązywanie problemów z** zaznacz **konsoli szeregowej**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

## <a name="enable-serial-console-functionality"></a>Włącz funkcjonalność konsoli szeregowej

> [!NOTE]
> Jeśli nie widzisz żadnych czynności w konsoli szeregowej, upewnij się, że ten Diagnostyka rozruchu jest włączona w zestawie skalowania maszyny Wirtualnej lub maszyny wirtualnej.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Włącz konsoli szeregowej na obrazach niestandardowych lub starszy
Nowsze obrazy systemu Windows Server na platformie Azure mają [specjalnej konsoli administracyjnej](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) domyślnie włączone (SAC). Konsola SAC jest obsługiwane w wersji Windows server, ale nie jest dostępna w wersji klienta (na przykład systemu Windows 10, Windows 8 lub Windows 7).

Starsze obrazów systemu Windows Server (utworzone przed lutym 2018 r.) można automatycznie włączyć konsoli szeregowej, za pomocą funkcji polecenia uruchomienia witryny Azure portal. W witrynie Azure portal wybierz **Uruchom polecenie**, następnie wybierz polecenie o nazwie **EnableEMS** z listy.

![Uruchom listy poleceń](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Alternatywnie aby ręcznie włączyć konsoli szeregowej dla Windows maszyn wirtualnych/wirtualnych zestawu skalowania maszyn utworzone przed lutym 2018 roku, wykonaj następujące kroki:

1. Połącz się z maszyną wirtualną Windows przy użyciu pulpitu zdalnego
1. W administracyjnym wierszu polecenia Uruchom następujące polecenia:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Ponowny rozruch systemu pod kątem Konsola SAC włączenia.

    ![Konsola SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Jeśli to konieczne, konsola SAC można włączyć w trybie offline oraz:

1. Dołącz dysk systemu windows, dla którego chcesz SAC skonfigurowany jako dysk danych do istniejącej maszyny Wirtualnej.

1. W administracyjnym wierszu polecenia Uruchom następujące polecenia:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Jak sprawdzić, czy włączono SAC?

Jeśli [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nie jest włączona, konsoli szeregowej nie będzie wyświetlać monitu SAC. W niektórych przypadkach wyświetlane są informacje o kondycji maszyny Wirtualnej, a w innych przypadkach jest on pusty. Jeśli używasz obrazu systemu Windows Server, utworzone przed lutym 2018 roku SAC prawdopodobnie nie jest włączony.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Włącz menu Windows rozruchowego w konsoli szeregowej

Jeśli musisz włączyć Windows rozruchowego modułu ładującego monity do wyświetlenia w konsoli szeregowej, można dodać następujące dodatkowe opcje do danych konfiguracji rozruchu. Aby uzyskać więcej informacji, zobacz [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Łączenie z maszyną wirtualną Windows lub wystąpienia zestawu skalowania maszyn wirtualnych przy użyciu pulpitu zdalnego.

1. W administracyjnym wierszu polecenia Uruchom następujące polecenia:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Ponowne uruchomienie systemu pod kątem menu rozruchu jest włączona

> [!NOTE]
> Limit czasu, ustawionego dla menu Menedżera rozruchu, aby wyświetlić będzie miało wpływ na czas rozruchu systemu operacyjnego. Jeśli uważasz, że wartość 10-sekundowy limit jest za krótka lub za długa, ustaw ją na inną wartość.

## <a name="use-serial-console"></a>Użyj konsoli szeregowej

### <a name="use-cmd-or-powershell-in-serial-console"></a>Użyj polecenia lub programu PowerShell w konsoli szeregowej

1. Łączenie z konsolą szeregową. Jeśli udało się połączyć, monit jest **SAC >**:

    ![Nawiązać połączenie z SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Wprowadź `cmd` do utworzenia kanału, który znajduje się wystąpienie CMD.

1.  Wprowadź `ch -si 1` Aby przełączyć się do kanału, który jest uruchomione wystąpienie polecenia.

1.  Naciśnij klawisz **Enter**, a następnie wprowadź poświadczenia logowania z uprawnieniami administracyjnymi.

1.  Po wprowadzeniu prawidłowych poświadczeń wystąpienia CMD otwiera.

1.  Aby uruchomić wystąpienie programu PowerShell, należy wprowadzić `PowerShell` wystąpienia CMD, a następnie naciśnij klawisz **Enter**.

    ![Otwórz wystąpienie programu PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Użyj konsoli szeregowej NMI wywołań
Niemaskowalnego przerwania (NMI) jest przeznaczone do tworzenia sygnał, który oprogramowania na maszynie wirtualnej nie są ignorowane. W przeszłości NMIs zostały użyte w celu monitorowania problemów ze sprzętem w systemach, które są wymagane określone czasy. Dzisiaj, programistów i system Administratorzy często używają NMI jako mechanizm do debugowania i rozwiązywanie problemów z systemów, które nie odpowiadają.

Konsoli szeregowej może służyć do wysyłania NMI na maszynie wirtualnej platformy Azure przy użyciu ikonę klawiatury na pasku poleceń. Po NMI zostało dostarczone, konfiguracja maszyny wirtualnej będzie kontrolować, jak zachowuje się system. Windows można skonfigurować do awarii i tworzenie pliku zrzutu pamięci podczas odbierania NMI.

![Wyślij NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Aby uzyskać informacje na temat konfigurowania Windows, aby utworzyć plik zrzutu awaryjnego, po odebraniu NMI, zobacz [sposób generowania pliku zrzutu awaryjnego przy użyciu NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Użyj klawiszy funkcyjnych w konsoli szeregowej
Klawiszy funkcyjnych są włączone dla użycia konsoli szeregowej na maszynach wirtualnych Windows. F8 na liście rozwijanej konsoli szeregowej udostępnia wygodne łatwe wprowadzanie menu Zaawansowane ustawienia rozruchu, ale konsoli szeregowej jest zgodny z innymi klawiszy funkcyjnych. Może być konieczne naciśnięcie **Fn** + **F1** (lub F2 i F3, itp.) na klawiaturze, w zależności od komputera są przy użyciu konsoli szeregowej.

### <a name="use-wsl-in-serial-console"></a>Użyj WSL w konsoli szeregowej
Podsystem Windows dla systemu Linux (WSL) została włączona dla systemu Windows Server 2019 lub nowszego, więc istnieje również możliwość włączenia WSL do użycia w konsoli szeregowej, jeśli korzystasz z systemu Windows Server 2019 lub nowszej. Może to być przydatne w przypadku użytkowników, którzy mają również znajomość polecenia systemu Linux. Aby uzyskać instrukcje, aby umożliwić WSL dla systemu Windows Server, zobacz [Przewodnik instalacji](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Ponowne uruchomienie usługi Windows VM/wirtualnego maszyny wystąpienia w zestawie skalowania w ramach konsoli szeregowej
Należy zainicjować ponownego uruchomienia w ramach konsoli szeregowej, przechodzenia do przycisku zasilania, a następnie klikając polecenie "Uruchom ponownie maszynę Wirtualną". Spowoduje to zainicjowanie ponownego uruchomienia maszyny Wirtualnej, a następnie zostanie wyświetlone powiadomienie w witrynie Azure portal dotyczących ponownego uruchomienia.

Jest to przydatne w sytuacjach, w których możesz chcieć uzyskać dostęp do menu rozruchu bez opuszczania środowiska konsoli szeregowej.

![Ponowne uruchomienie konsoli szeregowej Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>Wyłącz konsoli szeregowej
Domyślnie wszystkie subskrypcje mają dostęp do konsoli szeregowej włączone dla wszystkich maszyn wirtualnych. Można wyłączyć konsoli szeregowej na poziomie subskrypcji lub na poziomie maszyny Wirtualnej.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Wyłącz poziomu zestawu skalowania maszyn wirtualnych/maszyna wirtualna
Konsoli szeregowej można wyłączyć dla określonej maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania przez wyłączenie ustawienia diagnostyki rozruchu. Wyłącz funkcję diagnostyki rozruchu z witryny Azure portal, aby wyłączyć konsoli szeregowej maszyny Wirtualnej lub zestawu skalowania maszyn wirtualnych. Jeśli używasz konsoli szeregowej w zestawie skalowania maszyn wirtualnych, upewnij się, że uaktualnienie wystąpieniami danego zestawu skalowania maszyn wirtualnych do najnowszego modelu.

> [!NOTE]
> Aby włączyć lub wyłączyć konsoli szeregowej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Uprawnienia te obejmują, ale nie są ograniczone do ról administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

### <a name="subscription-level-disable"></a>Wyłącz poziom subskrypcji
Można wyłączyć dla całej subskrypcji, za pośrednictwem konsoli szeregowej [Wyłącz konsoli wywołania interfejsu API REST](/rest/api/serialconsole/console/disableconsole). Możesz użyć **wypróbuj** funkcji dostępnych na tej stronie dokumentacji interfejsu API, wyłączyć lub włączyć konsoli szeregowej dla subskrypcji. Wprowadź swój identyfikator subskrypcji dla **subscriptionId**, wprowadź "domyślna" dla **domyślne**, a następnie wybierz pozycję **Uruchom**. Poleceń interfejsu wiersza polecenia platformy Azure nie są jeszcze dostępne.

![Wypróbuj interfejs API REST](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Alternatywnie można następujący zestaw poleceń powłoki bash w usłudze Cloud Shell wyłączanie, włączanie i wyświetlanie wyłączone stanu konsoli szeregowej subskrypcji:

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

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowej

### <a name="access-security"></a>Zabezpieczenia dostępu
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają dostęp do roli z [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub nowszej, aby maszyna wirtualna. Jeśli Twoja dzierżawa usługi Azure Active Directory wymaga uwierzytelniania wieloskładnikowego (MFA), a następnie dostęp do konsoli szeregowej będą także potrzebować MFA, ponieważ dostęp do konsoli szeregowej za pośrednictwem [witryny Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane, które są wysyłane w obie strony są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Dostęp do konsoli szeregowej jest aktualnie zalogowany [diagnostykę rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) dzienniki maszyny wirtualnej. Dostęp do tych dzienników są własnością i kontrolowane przez administratora maszyny wirtualnej platformy Azure.

> [!CAUTION]
> Żadne hasła dostępu do konsoli są rejestrowane. Jednak jeśli polecenia uruchamiane w ramach konsoli zawierają lub danych wyjściowych haseł, kluczy tajnych, nazwy użytkowników lub jakąkolwiek inną formę identyfikowalne dane osobowe (PII), te będą zapisywane do dzienników diagnostyki rozruchu maszyny Wirtualnej. One będą zapisywane wraz z wszystkich innych widocznych tekstu, jako część wykonania wstecz przewijania konsoli szeregowej funkcji. Te dzienniki są cykliczne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mieli do nich dostęp. Jednak zaleca się następujące najlepsze rozwiązanie polegające na przy użyciu pulpitu zdalnego dla wszystkich elementów, które mogą obejmować wpisów tajnych i/lub dane osobowe.

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsoli szeregowej i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie odłączony, a drugi użytkownik nawiązał połączenie z tą samą sesją.

> [!CAUTION]
> Oznacza to, że użytkownik, który jest odłączony nie będzie można wylogować. Możliwość wymuszenia wylogowania po rozłączenia (przy użyciu SIGHUP lub podobny mechanizm) nadal działa w planach. Dla Windows to automatyczne limitu czasu w SAC; włączone w przypadku systemu Linux można skonfigurować ustawienie limitu czasu w terminalu.

## <a name="accessibility"></a>Ułatwienia dostępu
Dostępność jest kluczowym dla konsoli szeregowej platformy Azure. W tym celu upewniliśmy się, że konsoli szeregowej jest dostępny dla wizualizacji i wysłuchaniu osłabiona, a także osoby, które mogą okazać się niemożliwe przy użyciu myszy.

### <a name="keyboard-navigation"></a>Nawigowanie przy użyciu klawiatury
Użyj **kartę** kluczowe na klawiaturze, aby przejść w interfejsie konsoli szeregowej w witrynie Azure portal. Twoja lokalizacja zostanie wyróżniony na ekranie. Aby opuścić fokusu okna konsoli szeregowej, naciśnij klawisz **Ctrl**+**F6** na klawiaturze.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Użyj konsoli szeregowej przy użyciu czytnika zawartości ekranu
Konsoli szeregowej ma wbudowaną obsługę czytników zawartości ekranu. Przemieszczać się przy użyciu czytnika zawartości ekranu włączone umożliwi tekst alternatywny dla aktualnie wybranego przycisku zostanie odczytany na głos przez czytnik zawartości ekranu.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Typowe scenariusze dotyczące uzyskiwania dostępu do konsoli szeregowej

Scenariusz          | Akcje w konsoli szeregowej
:------------------|:-----------------------------------------
Reguły zapory niepoprawne | Dostęp do serial konsoli i napraw zasady Windows w zapory.
System plików uszkodzenie/wyboru | Dostęp do konsoli szeregowej i odzyskiwanie systemu plików.
Problemy z konfiguracją protokołu RDP | Dostęp do konsoli szeregowej i zmienić ustawienia. Aby uzyskać więcej informacji, zobacz [dokumentacji protokołu RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Blokowanie sieci w systemie | W witrynie Azure portal do zarządzania systemem, należy uzyskać dostęp do konsoli szeregowej. Niektóre polecenia sieci są wymienione w [Windows polecenia: Programu PowerShell i CMD](serial-console-cmd-ps-commands.md).
Interakcja z programu inicjującego | Dostęp do danych konfiguracji rozruchu za pośrednictwem konsoli szeregowej. Aby uzyskać informacje, zobacz [Włącz menu Windows rozruchowego w konsoli szeregowej](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Błędy
Ponieważ większość błędów przejściowych, ponawianie próby połączenia można często je naprawić. W poniższej tabeli przedstawiono listę błędy i środki zaradcze dla obu maszyn wirtualnych i wystąpień zestawu skalowania maszyn wirtualnych.

Błąd                            |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu dla  *&lt;VMNAME&gt;*. Aby korzystać z konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostykę rozruchu](boot-diagnostics.md) włączone.
Maszyna wirtualna jest w stanie zatrzymania, przydział zostanie cofnięty. Uruchom maszynę Wirtualną i spróbuj ponownie nawiązać połączenie konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym uzyskać dostęp do konsoli szeregowej
Nie masz wymaganych uprawnień do używania konsoli szeregowej maszyny Wirtualnej. Upewnij się, że co najmniej uprawnienia roli Współautor maszyny wirtualnej.| Dostęp do konsoli szeregowej wymaga pewnych uprawnień. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu  *&lt;STORAGEACCOUNTNAME&gt;*. Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga pewnych uprawnień. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).
Odpowiedź "Dostęp zabroniony" napotkano podczas uzyskiwania dostępu do konta magazynu diagnostyki rozruchu dla tej maszyny Wirtualnej. | Upewnij się, że tej diagnostyki rozruchu zapory konta. Konto magazynu diagnostyki rozruchu dostępny jest niezbędne do konsoli szeregowej funkcjonowania.
Gniazda sieci Web został zamknięty lub nie można otworzyć. | Może być konieczne do listy dozwolonych `*.console.azure.com`. Bardziej szczegółowe, ale dłużej podejście jest do listy dozwolonych adresów [zakresów adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są stosunkowo regularnie zmieniane.
Tylko informacje o kondycji jest wyświetlany podczas nawiązywania połączenia maszyny Wirtualnej z systemem Windows| Ten błąd występuje, jeśli nie włączono specjalnej konsoli administracyjnej dla obrazu systemu Windows. Zobacz [Włącz konsoli szeregowej na obrazach niestandardowych lub starsze](#enable-the-serial-console-in-custom-or-older-images) instrukcje na temat sposobu ręcznego włączenia SAC na maszynie Wirtualnej Windows. Aby uzyskać więcej informacji, zobacz [sygnałów kondycji Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Znane problemy
Mamy świadomość problemy z konsoli szeregowej. Poniżej przedstawiono listę tych problemów oraz kroki dotyczące ograniczania ryzyka. Te problemy i środki zaradcze są stosowane dla obu maszyn wirtualnych i wystąpień zestawu skalowania maszyn wirtualnych.

Problem                             |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Naciśnięcie klawisza **Enter** po transparent połączenia nie powoduje, że monit logowania do wyświetlenia. | Aby uzyskać więcej informacji, zobacz [Hitting wprowadź, nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten błąd może wystąpić, jeśli używasz niestandardowej maszyny Wirtualnej, urządzenia ze wzmocnionymi zabezpieczeniami lub konfiguracji rozruchu, który powoduje, że Windows niepowodzenie prawidłowo połączenia do portu szeregowego. To również wystąpi błąd Jeśli korzystasz z klienta systemu Windows 10 maszyny Wirtualnej, ponieważ tylko systemu Windows Server dla maszyn wirtualnych są skonfigurowane do mieć włączony pakiet EMS.
Nie można wpisać w SAC stanie się monit, jeśli włączone jest debugowanie jądra. | Nawiązać połączenia RDP z maszyną Wirtualną i uruchom `bcdedit /debug {current} off` z wiersza polecenia z podwyższonym poziomem uprawnień. Jeśli z jakiegoś powodu protokołu RDP, możesz zamiast tego Dołącz dysk systemu operacyjnego do innej maszyny Wirtualnej platformy Azure i zmodyfikuj go, gdy dołączony jako dysk danych, uruchamiając `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, następnie zamienianie dysku w ponownie.
Wklejając w programie PowerShell powoduje SAC trzeciego znaku gdyby oryginalną zawartość powtarzających się znaków. | Obejście tego problemu, uruchom `Remove-Module PSReadLine` wyładować modułu PSReadLine z bieżącej sesji. Ta akcja nie zostanie skasowana czy odinstalowana modułu.
Niektóre dane wejściowe z klawiatury generuje dziwne SAC dane wyjściowe (na przykład **[A**, **[3 ~**). | [VT100](https://aka.ms/vtsequences) sekwencje ucieczki nie są obsługiwane przez wiersz SAC.
Wklejanie ciągów długich nie działa. | Konsoli szeregowej ogranicza długość ciągów w terminalu, aby 2048 znaków, aby zapobiec przeciążeniu przepustowość portu szeregowego.
Konsola szeregowa nie działa z zaporą konta magazynu. | Konsola szeregowa zgodnie z projektem nie może działać z zapór konta usługi storage na konto magazynu diagnostyki rozruchu włączona.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**PYTANIA I ODPOWIEDZI. Jak wysłać opinię?**

A. Przekazać opinię, tworząc problem w usłudze GitHub w https://aka.ms/serialconsolefeedback. Można również (mniej preferowany), możesz wysłać opinię, za pośrednictwem azserialhelp@microsoft.com lub w maszynie wirtualnej kategorii https://feedback.azure.com.

**PYTANIA I ODPOWIEDZI. Konsoli szeregowej obsługuje kopiowania/wklejania?**

A. Tak. Użyj **Ctrl**+**Shift**+**C** i **Ctrl**+**Shift** + **V** do kopiowania i wklejania w terminalu.

**PYTANIA I ODPOWIEDZI. Kto może włączyć lub wyłączyć konsoli szeregowej dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsoli szeregowej na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują role administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

**PYTANIA I ODPOWIEDZI. Kto ma dostęp do konsoli szeregowej dla mojej maszyny Wirtualnej?**

A. Konieczne jest posiadanie rola Współautor maszyny wirtualnej lub nowszej, aby uzyskać dostęp do konsoli szeregowej maszyny Wirtualnej maszyny Wirtualnej.

**PYTANIA I ODPOWIEDZI. Moje konsoli szeregowej nie są wyświetlane wszystko, co należy zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowane, aby uzyskać dostęp do konsoli szeregowej. Aby uzyskać informacji o konfigurowaniu swój obraz, aby umożliwić konsoli szeregowej, zobacz [Włącz konsoli szeregowej na obrazach niestandardowych lub starsze](#enable-the-serial-console-in-custom-or-older-images).

**PYTANIA I ODPOWIEDZI. Konsoli szeregowej jest dostępna dla zestawów skalowania maszyn wirtualnych?**

A. W tej chwili dostęp do konsoli szeregowej dla wystąpień zestawu skalowania maszyn wirtualnych nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać szczegółowy przewodnik poleceń programu PowerShell i CMD, można użyć w Windows SAC, zobacz [Windows polecenia: Programu PowerShell i CMD](serial-console-cmd-ps-commands.md).
* Jest również dostępny dla konsoli szeregowej [Linux](serial-console-linux.md) maszyn wirtualnych.
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md).
