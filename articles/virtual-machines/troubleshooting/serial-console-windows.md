---
title: Konsola szeregowa platformy Azure dla systemu Windows | Microsoft Docs
description: Dwukierunkowa konsola szeregowa dla Virtual Machines i Virtual Machine Scale Sets platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: a6e303c26278eff290a2d4efb6f96e9962cf2f87
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775364"
---
# <a name="azure-serial-console-for-windows"></a>Konsola szeregowa platformy Azure dla systemu Windows

Konsola szeregowa w Azure Portal zapewnia dostęp do konsoli opartej na tekście dla maszyn wirtualnych z systemem Windows i wystąpień zestawów skalowania maszyn wirtualnych. To połączenie szeregowe łączy się z portem seryjnym COM1 maszyny wirtualnej lub wystąpienia zestawu skalowania maszyn wirtualnych, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko przy użyciu Azure Portal i jest to dozwolone tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

Konsola szeregowa działa w taki sam sposób w przypadku maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. W tym dokumencie wszystkie wzmianki dotyczące maszyn wirtualnych będą niejawnie obejmować wystąpienia zestawu skalowania maszyn wirtualnych, chyba że określono inaczej.

Aby uzyskać dokumentację konsoli szeregowej dla maszyn wirtualnych z systemem Linux i zestawu skalowania maszyn wirtualnych, zobacz [Azure serial Console for Linux](serial-console-linux.md).

> [!NOTE]
> Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure. Nie jest jeszcze dostępne w Azure dla instytucji rządowych lub chmury chińskiej wersji platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne

* W maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych musi być używany model wdrażania zarządzania zasobami. W przypadku wdrożeń klasycznych nie są obsługiwane.

- Twoje konto używające konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny wirtualnej i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md) .

- Maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych muszą mieć użytkownika opartego na hasłach. Możesz je utworzyć za pomocą [Resetuj hasło](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkcji rozszerzenia dostępu do maszyny Wirtualnej. Wybierz **Resetuj hasło** z **pomoc techniczna i rozwiązywanie problemów z** sekcji.

* Maszyna wirtualna, w którym uzyskujesz dostęp do konsoli szeregowej muszą mieć [diagnostykę rozruchu](boot-diagnostics.md) włączone.

    ![Ustawienia diagnostyki rozruchu](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="get-started-with-the-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsola szeregowa dla maszyn wirtualnych i zestawu skalowania maszyn wirtualnych jest dostępna tylko za pośrednictwem Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Konsola szeregowa dla Virtual Machines
Konsola szeregowa dla maszyn wirtualnych jest tak prosta jak kliknięcie **konsola szeregowa** w sekcji **Pomoc techniczna i rozwiązywanie problemów** w Azure Portal.
  1. Otwórz [portal Azure](https://portal.azure.com).

  1. Przejdź do opcji **wszystkie zasoby** i wybierz maszynę wirtualną. Zostanie otwarta strona przegląd dla maszyny wirtualnej.

  1. Przewiń w dół do **pomoc techniczna i rozwiązywanie problemów z** i wybierz pozycję **konsoli szeregowej**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Konsola szeregowa dla Virtual Machine Scale Sets
Konsola szeregowa jest dostępna dla poszczególnych wystąpień dla zestawów skalowania maszyn wirtualnych. Przed wyświetleniem przycisku **konsola szeregowa** należy przejść do poszczególnych wystąpień zestawu skalowania maszyn wirtualnych. Jeśli zestaw skalowania maszyn wirtualnych nie ma włączonej diagnostyki rozruchu, należy zaktualizować model zestawu skalowania maszyn wirtualnych, aby umożliwić diagnostykę rozruchu, a następnie uaktualnić wszystkie wystąpienia do nowego modelu w celu uzyskania dostępu do konsoli szeregowej.
  1. Otwórz [portal Azure](https://portal.azure.com).

  1. Przejdź do opcji **wszystkie zasoby** i wybierz zestaw skalowania maszyn wirtualnych. Zostanie otwarta strona przegląd zestawu skalowania maszyn wirtualnych.

  1. Przejdź do **wystąpień**

  1. Wybierz wystąpienie zestawu skalowania maszyn wirtualnych

  1. W sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **konsola szeregowa**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

## <a name="enable-serial-console-functionality"></a>Włącz funkcje konsoli szeregowej

> [!NOTE]
> Jeśli nie widzisz niczego w konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona na maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Włącz konsoli szeregowej na obrazach niestandardowych lub starszy
Nowsze obrazy systemu Windows Server na platformie Azure mają [specjalnej konsoli administracyjnej](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) domyślnie włączone (SAC). Konsola SAC jest obsługiwane w wersji Windows server, ale nie jest dostępna w wersji klienta (na przykład systemu Windows 10, Windows 8 lub Windows 7).

Starsze obrazów systemu Windows Server (utworzone przed lutym 2018 r.) można automatycznie włączyć konsoli szeregowej, za pomocą funkcji polecenia uruchomienia witryny Azure portal. W Azure Portal wybierz pozycję **Uruchom polecenie**, a następnie wybierz polecenie o nazwie **EnableEMS** z listy.

![Uruchom listy poleceń](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Alternatywnie, aby ręcznie włączyć konsolę szeregową dla maszyn wirtualnych z systemem Windows/zestawu skalowania maszyn wirtualnych utworzonych przed luty 2018, wykonaj następujące kroki:

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

1. Połącz się z maszyną wirtualną z systemem Windows lub wystąpieniem zestawu skalowania maszyn wirtualnych za pomocą Pulpit zdalny.

1. W administracyjnym wierszu polecenia Uruchom następujące polecenia:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Ponowne uruchomienie systemu pod kątem menu rozruchu jest włączona

> [!NOTE]
> Limit czasu, ustawionego dla menu Menedżera rozruchu, aby wyświetlić będzie miało wpływ na czas rozruchu systemu operacyjnego. Jeśli uważasz, że wartość 10-sekundowy limit jest za krótka lub za długa, ustaw ją na inną wartość.

## <a name="use-serial-console"></a>Korzystanie z konsoli szeregowej

### <a name="use-cmd-or-powershell-in-serial-console"></a>Używanie CMD lub PowerShell w konsoli szeregowej

1. Łączenie z konsolą szeregową. Jeśli udało się połączyć, monit jest **SAC >** :

    ![Nawiązać połączenie z SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Wprowadź `cmd` do utworzenia kanału, który znajduje się wystąpienie CMD.

1.  Wprowadź `ch -si 1` lub naciśnij `<esc>+<tab>` klawisze skrótów, aby przełączyć się na kanał, na którym działa wystąpienie cmd.

1.  Naciśnij klawisz **Enter**, a następnie wprowadź poświadczenia logowania z uprawnieniami administracyjnymi.

1.  Po wprowadzeniu prawidłowych poświadczeń wystąpienia CMD otwiera.

1.  Aby uruchomić wystąpienie programu PowerShell, należy wprowadzić `PowerShell` wystąpienia CMD, a następnie naciśnij klawisz **Enter**.

    ![Otwórz wystąpienie programu PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Użyj konsoli szeregowej NMI wywołań
Niemaskowalnego przerwania (NMI) jest przeznaczone do tworzenia sygnał, który oprogramowania na maszynie wirtualnej nie są ignorowane. W przeszłości NMIs zostały użyte w celu monitorowania problemów ze sprzętem w systemach, które są wymagane określone czasy. Obecnie programiści i Administratorzy systemu często używają NMI jako mechanizmu debugowania lub rozwiązywania problemów z nieodpowiadającymi systemami.

Konsoli szeregowej może służyć do wysyłania NMI na maszynie wirtualnej platformy Azure przy użyciu ikonę klawiatury na pasku poleceń. Po NMI zostało dostarczone, konfiguracja maszyny wirtualnej będzie kontrolować, jak zachowuje się system. Windows można skonfigurować do awarii i tworzenie pliku zrzutu pamięci podczas odbierania NMI.

![Wyślij NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Aby uzyskać informacje na temat konfigurowania Windows, aby utworzyć plik zrzutu awaryjnego, po odebraniu NMI, zobacz [sposób generowania pliku zrzutu awaryjnego przy użyciu NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Użyj klawiszy funkcyjnych w konsoli szeregowej
Klawiszy funkcyjnych są włączone dla użycia konsoli szeregowej na maszynach wirtualnych Windows. F8 na liście rozwijanej konsoli szeregowej udostępnia wygodne łatwe wprowadzanie menu Zaawansowane ustawienia rozruchu, ale konsoli szeregowej jest zgodny z innymi klawiszy funkcyjnych. W zależności od komputera, z którego korzystasz z konsoli szeregowej, może być konieczne naciśnięcie klawisza **Fn** + **F1** (lub F2, F3 itp.).

### <a name="use-wsl-in-serial-console"></a>Użyj WSL w konsoli szeregowej
Podsystem Windows dla systemu Linux (WSL) została włączona dla systemu Windows Server 2019 lub nowszego, więc istnieje również możliwość włączenia WSL do użycia w konsoli szeregowej, jeśli korzystasz z systemu Windows Server 2019 lub nowszej. Może to być przydatne w przypadku użytkowników, którzy mają również znajomość polecenia systemu Linux. Aby uzyskać instrukcje, aby umożliwić WSL dla systemu Windows Server, zobacz [Przewodnik instalacji](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Ponowne uruchamianie wystąpienia maszyny wirtualnej z systemem Windows/zestawu skalowania maszyn wirtualnych w konsoli szeregowej
Możesz zainicjować ponowne uruchomienie w konsoli szeregowej, przechodząc do przycisku energia, a następnie klikając pozycję "Uruchom ponownie maszynę wirtualną". Spowoduje to zainicjowanie ponownego uruchomienia maszyny wirtualnej, a w Azure Portal zostanie wyświetlone powiadomienie dotyczące ponownego uruchomienia.

Jest to przydatne w sytuacjach, w których możesz chcieć uzyskać dostęp do menu rozruchu bez opuszczania środowiska konsoli szeregowej.

![Ponowne uruchomienie konsoli szeregowej systemu Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>Wyłącz konsoli szeregowej
Domyślnie wszystkie subskrypcje mają dostęp do konsoli szeregowej włączone dla wszystkich maszyn wirtualnych. Można wyłączyć konsoli szeregowej na poziomie subskrypcji lub na poziomie maszyny Wirtualnej.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Wyłączanie maszyny wirtualnej/zestawu skalowania maszyn wirtualnych
Konsolę szeregową można wyłączyć dla określonej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych, wyłączając ustawienie diagnostyki rozruchu. Wyłącz diagnostykę rozruchu z Azure Portal, aby wyłączyć konsolę seryjną dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. Jeśli używasz konsoli szeregowej w zestawie skalowania maszyn wirtualnych, upewnij się, że Twoje wystąpienia zestawu skalowania maszyn wirtualnych zostały uaktualnione do najnowszego modelu.

> [!NOTE]
> Aby włączyć lub wyłączyć konsoli szeregowej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Uprawnienia te obejmują, ale nie są ograniczone do ról administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

### <a name="subscription-level-disable"></a>Wyłącz poziom subskrypcji
Można wyłączyć dla całej subskrypcji, za pośrednictwem konsoli szeregowej [Wyłącz konsoli wywołania interfejsu API REST](/rest/api/serialconsole/console/disableconsole). Ta akcja wymaga dostępu na poziomie współautora lub wyższego do subskrypcji. Możesz użyć **wypróbuj** funkcji dostępnych na tej stronie dokumentacji interfejsu API, wyłączyć lub włączyć konsoli szeregowej dla subskrypcji. Wprowadź swój identyfikator subskrypcji dla **subscriptionId**, wprowadź "domyślna" dla **domyślne**, a następnie wybierz pozycję **Uruchom**. Poleceń interfejsu wiersza polecenia platformy Azure nie są jeszcze dostępne.

Aby ponownie włączyć konsolę szeregową dla subskrypcji, użyj [wywołania interfejsu API REST konsoli](/rest/api/serialconsole/console/enableconsole).

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
Blokowanie sieci w systemie | W witrynie Azure portal do zarządzania systemem, należy uzyskać dostęp do konsoli szeregowej. Niektóre polecenia sieciowe są wymienione w [poleceniach systemu Windows: CMD i PowerShell](serial-console-cmd-ps-commands.md).
Interakcja z programu inicjującego | Dostęp do danych konfiguracji rozruchu za pośrednictwem konsoli szeregowej. Aby uzyskać informacje, zobacz [Włącz menu Windows rozruchowego w konsoli szeregowej](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Błędy
Ponieważ większość błędów przejściowych, ponawianie próby połączenia można często je naprawić. W poniższej tabeli przedstawiono listę błędów i środków zaradczych dla maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych.

Błąd                            |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu dla  *&lt;VMNAME&gt;* . Aby korzystać z konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostykę rozruchu](boot-diagnostics.md) włączone.
Maszyna wirtualna jest w stanie zatrzymania, przydział zostanie cofnięty. Uruchom maszynę Wirtualną i spróbuj ponownie nawiązać połączenie konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym uzyskać dostęp do konsoli szeregowej
Nie masz wymaganych uprawnień do używania konsoli szeregowej maszyny Wirtualnej. Upewnij się, że co najmniej uprawnienia roli Współautor maszyny wirtualnej.| Dostęp do konsoli szeregowej wymaga pewnych uprawnień. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu  *&lt;STORAGEACCOUNTNAME&gt;* . Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga pewnych uprawnień. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).
Odpowiedź "Dostęp zabroniony" napotkano podczas uzyskiwania dostępu do konta magazynu diagnostyki rozruchu dla tej maszyny Wirtualnej. | Upewnij się, że tej diagnostyki rozruchu zapory konta. Konto magazynu diagnostyki rozruchu dostępny jest niezbędne do konsoli szeregowej funkcjonowania.
Gniazda sieci Web został zamknięty lub nie można otworzyć. | Może być konieczne do listy dozwolonych `*.console.azure.com`. Bardziej szczegółowe, ale dłużej podejście jest do listy dozwolonych adresów [zakresów adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są stosunkowo regularnie zmieniane.
Tylko informacje o kondycji jest wyświetlany podczas nawiązywania połączenia maszyny Wirtualnej z systemem Windows| Ten błąd występuje, jeśli nie włączono specjalnej konsoli administracyjnej dla obrazu systemu Windows. Zobacz [Włącz konsoli szeregowej na obrazach niestandardowych lub starsze](#enable-the-serial-console-in-custom-or-older-images) instrukcje na temat sposobu ręcznego włączenia SAC na maszynie Wirtualnej Windows. Aby uzyskać więcej informacji, zobacz [sygnałów kondycji Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Znane problemy
Mamy świadomość problemy z konsoli szeregowej. Poniżej przedstawiono listę tych problemów oraz kroki dotyczące ograniczania ryzyka. Te problemy i środki zaradcze dotyczą zarówno maszyn wirtualnych, jak i wystąpień zestawów skalowania maszyn wirtualnych.

Problem                             |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Naciśnięcie klawisza **Enter** po transparent połączenia nie powoduje, że monit logowania do wyświetlenia. | Aby uzyskać więcej informacji, zobacz [Hitting wprowadź, nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten błąd może wystąpić, jeśli używasz niestandardowej maszyny Wirtualnej, urządzenia ze wzmocnionymi zabezpieczeniami lub konfiguracji rozruchu, który powoduje, że Windows niepowodzenie prawidłowo połączenia do portu szeregowego. To również wystąpi błąd Jeśli korzystasz z klienta systemu Windows 10 maszyny Wirtualnej, ponieważ tylko systemu Windows Server dla maszyn wirtualnych są skonfigurowane do mieć włączony pakiet EMS.
Nie można wpisać w SAC stanie się monit, jeśli włączone jest debugowanie jądra. | Nawiązać połączenia RDP z maszyną Wirtualną i uruchom `bcdedit /debug {current} off` z wiersza polecenia z podwyższonym poziomem uprawnień. Jeśli z jakiegoś powodu protokołu RDP, możesz zamiast tego Dołącz dysk systemu operacyjnego do innej maszyny Wirtualnej platformy Azure i zmodyfikuj go, gdy dołączony jako dysk danych, uruchamiając `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, następnie zamienianie dysku w ponownie.
Wklejając w programie PowerShell powoduje SAC trzeciego znaku gdyby oryginalną zawartość powtarzających się znaków. | Obejście tego problemu, uruchom `Remove-Module PSReadLine` wyładować modułu PSReadLine z bieżącej sesji. Ta akcja nie zostanie skasowana czy odinstalowana modułu.
Niektóre dane wejściowe z klawiatury generuje dziwne SAC dane wyjściowe (na przykład **[A**, **[3 ~** ). | [VT100](https://aka.ms/vtsequences) sekwencje ucieczki nie są obsługiwane przez wiersz SAC.
Wklejanie ciągów długich nie działa. | Konsoli szeregowej ogranicza długość ciągów w terminalu, aby 2048 znaków, aby zapobiec przeciążeniu przepustowość portu szeregowego.
Konsola szeregowa nie działa z zaporą konta magazynu. | Konsola szeregowa według projektu nie może współpracować z zaporami konta magazynu włączonymi na koncie magazynu diagnostyki rozruchu.
Konsola szeregowa nie działa z kontem magazynu przy użyciu Azure Data Lake Storage Gen2 z hierarchicznymi przestrzeniami nazw. | Jest to znany problem z hierarchicznymi przestrzeniami nazw. Aby rozwiązać problem, upewnij się, że konto magazynu diagnostyki rozruchu maszyny wirtualnej nie zostało utworzone przy użyciu Azure Data Lake Storage Gen2. Tę opcję można ustawić tylko podczas tworzenia konta magazynu. Może być konieczne utworzenie oddzielnego konta magazynu diagnostyki rozruchu bez Azure Data Lake Storage Gen2 włączenia tego problemu.


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

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowy przewodnik dotyczący poleceń cmd i PowerShell, których można użyć w konsoli SAC systemu Windows, [Zobacz polecenia systemu Windows: CMD i PowerShell](serial-console-cmd-ps-commands.md).
* Jest również dostępny dla konsoli szeregowej [Linux](serial-console-linux.md) maszyn wirtualnych.
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md).
