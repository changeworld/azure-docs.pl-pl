---
title: Konsola szeregowa platformy Azure dla systemu Linux | Microsoft Docs
description: Dwukierunkowa konsola szeregowa dla Virtual Machines i Virtual Machine Scale Sets platformy Azure.
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
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 4e0c91096d5efdcc9639a7127126d8e4b89ef068
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090161"
---
# <a name="azure-serial-console-for-linux"></a>Konsola szeregowa platformy Azure dla systemu Linux

Konsola szeregowa w Azure Portal zapewnia dostęp do konsoli opartej na tekście dla maszyn wirtualnych z systemem Linux i wystąpień zestawów skalowania maszyn wirtualnych. To połączenie szeregowe łączy się z portem szeregowym ttyS0 maszyny wirtualnej lub wystąpienia zestawu skalowania maszyn wirtualnych, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko przy użyciu Azure Portal i jest to dozwolone tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

Konsola szeregowa działa w taki sam sposób w przypadku maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. W tym dokumencie wszystkie wzmianki dotyczące maszyn wirtualnych będą niejawnie obejmować wystąpienia zestawu skalowania maszyn wirtualnych, chyba że określono inaczej.

Aby uzyskać dokumentację konsoli szeregowej dla systemu Windows, zobacz [konsola szeregowa dla systemu Windows](../windows/serial-console.md).

> [!NOTE]
> Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure. Nie jest jeszcze dostępne w Azure dla instytucji rządowych lub chmury chińskiej wersji platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne

- W maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych musi być używany model wdrażania zarządzania zasobami. W przypadku wdrożeń klasycznych nie są obsługiwane.

- Twoje konto używające konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny wirtualnej i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md) .

- Maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych muszą mieć użytkownika opartego na hasłach. Możesz je utworzyć za pomocą [Resetuj hasło](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkcji rozszerzenia dostępu do maszyny Wirtualnej. Wybierz **Resetuj hasło** z **pomoc techniczna i rozwiązywanie problemów z** sekcji.

- Maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych muszą mieć włączoną [diagnostykę rozruchu](boot-diagnostics.md) .

    ![Ustawienia diagnostyki rozruchu](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Aby poznać ustawienia dotyczące dystrybucji systemu Linux, zobacz [konsola szeregowa dostępność dystrybucji systemu Linux](#serial-console-linux-distribution-availability).

- Dla maszyny wirtualnej lub wystąpienia zestawu skalowania maszyn wirtualnych musi być skonfigurowane na potrzeby `ttys0`danych wyjściowych seryjnych. Jest to wartość domyślna dla obrazów platformy Azure, ale zawarto to dokładnie sprawdzić na obrazach niestandardowych. Szczegóły [poniżej](#custom-linux-images).


## <a name="get-started-with-the-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsola szeregowa dla maszyn wirtualnych i zestawu skalowania maszyn wirtualnych jest dostępna tylko za pośrednictwem Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Konsola szeregowa dla Virtual Machines
Konsola szeregowa dla maszyn wirtualnych jest tak prosta jak kliknięcie **konsola szeregowa** w sekcji **Pomoc techniczna i rozwiązywanie problemów** w Azure Portal.
  1. Otwórz [portal Azure](https://portal.azure.com).

  1. Przejdź do opcji **wszystkie zasoby** i wybierz maszynę wirtualną. Zostanie otwarta strona przegląd dla maszyny wirtualnej.

  1. Przewiń w dół do **pomoc techniczna i rozwiązywanie problemów z** i wybierz pozycję **konsoli szeregowej**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

     ![Okno konsoli szeregowej systemu Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Konsola szeregowa dla Virtual Machine Scale Sets
Konsola szeregowa jest dostępna dla poszczególnych wystąpień dla zestawów skalowania maszyn wirtualnych. Przed wyświetleniem przycisku **konsola szeregowa** należy przejść do poszczególnych wystąpień zestawu skalowania maszyn wirtualnych. Jeśli zestaw skalowania maszyn wirtualnych nie ma włączonej diagnostyki rozruchu, należy zaktualizować model zestawu skalowania maszyn wirtualnych, aby umożliwić diagnostykę rozruchu, a następnie uaktualnić wszystkie wystąpienia do nowego modelu w celu uzyskania dostępu do konsoli szeregowej.
  1. Otwórz [portal Azure](https://portal.azure.com).

  1. Przejdź do opcji **wszystkie zasoby** i wybierz zestaw skalowania maszyn wirtualnych. Zostanie otwarta strona przegląd zestawu skalowania maszyn wirtualnych.

  1. Przejdź do **wystąpień**

  1. Wybierz wystąpienie zestawu skalowania maszyn wirtualnych

  1. W sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **konsola szeregowa**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

     ![Konsola szeregowa zestawu skalowania maszyn wirtualnych z systemem Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> Konsola szeregowa wymaga użytkownika lokalnego ze skonfigurowanym hasłem. Maszyny wirtualne lub zestawy skalowania maszyn wirtualnych skonfigurowane tylko przy użyciu klucza publicznego SSH nie będą mogły zalogować się do konsoli szeregowej. Aby utworzyć użytkownika lokalnego przy użyciu hasła, użyj [rozszerzenia VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), które jest dostępne w portalu, wybierając pozycję **zresetuj hasło** w Azure Portal i Utwórz użytkownika lokalnego z hasłem.
> Możesz również zresetować hasło administratora na koncie przy [użyciu grub, aby przeprowadzić rozruch w trybie jednego użytkownika](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Dostępność dystrybucji w konsoli szeregowej systemu Linux
Aby konsola szeregowa działała poprawnie, system operacyjny gościa musi być skonfigurowany do odczytu i zapisu komunikatów konsoli na porcie szeregowym. Większość [poświadczonej dystrybucji systemu Linux platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ma domyślnie skonfigurowaną konsolę szeregowaną. Wybranie **konsola szeregowa** w sekcji **Pomoc techniczna i rozwiązywanie problemów** w Azure Portal zapewnia dostęp do konsoli szeregowej.

> [!NOTE]
> Jeśli nie widzisz niczego w konsoli szeregowej, upewnij się, że na maszynie wirtualnej jest włączona Diagnostyka rozruchu. Naciśnięcie **klawisza ENTER** często rozwiązuje problemy, w przypadku których nic nie pojawia się w konsoli szeregowej.

Dystrybucja      | Dostęp do konsoli szeregowej
:-----------|:---------------------
Red Hat Enterprise Linux    | Konsola szeregowa dostęp domyślnie włączony.
CentOS      | Konsola szeregowa dostęp domyślnie włączony.
Ubuntu      | Konsola szeregowa dostęp domyślnie włączony.
CoreOS      | Konsola szeregowa dostęp domyślnie włączony.
SUSE        | Nowsze obrazy SLES dostępne na platformie Azure mają domyślnie włączony dostęp do konsoli szeregowej. Jeśli używasz starszych wersji (10 lub wcześniejszych) SLES na platformie Azure, zapoznaj się z [artykułem KB](https://www.novell.com/support/kb/doc.php?id=3456486) , aby włączyć konsolę szeregową.
Oracle Linux        | Konsola szeregowa dostęp domyślnie włączony.

### <a name="custom-linux-images"></a>Niestandardowe obrazy systemu Linux
Aby włączyć konsolę szeregową dla niestandardowego obrazu maszyny wirtualnej z systemem Linux, Włącz dostęp do konsoli w pliku */etc/inittab* , aby uruchomić `ttyS0`terminal w. Na przykład: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`.

Warto również dodać ttyS0 jako lokalizację docelową dla danych wyjściowych seryjnych. Aby uzyskać więcej informacji na temat konfigurowania niestandardowego obrazu do pracy z konsolą szeregową, zobacz Ogólne wymagania systemowe na stronie [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Jeśli tworzysz jądro niestandardowe, rozważ włączenie tych flag jądra: `CONFIG_SERIAL_8250=y` i. `CONFIG_MAGIC_SYSRQ_SERIAL=y` Plik konfiguracji zazwyczaj znajduje się w ścieżce */Boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Typowe scenariusze uzyskiwania dostępu do konsoli szeregowej

Scenariusz          | Akcje w konsoli szeregowej
:------------------|:-----------------------------------------
Uszkodzony plik *fstab* | Naciśnij klawisz **Enter** , aby kontynuować, a następnie użyj edytora tekstów, aby naprawić plik *fstab* . Być może trzeba będzie działać w trybie jednego użytkownika. Aby uzyskać więcej informacji, zobacz sekcję konsola szeregowa w sprawie [rozwiązywania problemów z fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) i [używania konsoli szeregowej do uzyskiwania dostępu do grub i trybu jednego użytkownika](serial-console-grub-single-user-mode.md).
Reguły zapory niepoprawne |  Jeśli skonfigurowano dołączenie iptables do blokowania łączności SSH, można użyć konsoli szeregowej do współpracy z maszyną wirtualną bez konieczności używania protokołu SSH. Więcej szczegółów można znaleźć na [stronie dołączenie iptables Man](https://linux.die.net/man/8/iptables).<br>Podobnie, Jeśli zapora blokuje dostęp SSH, można uzyskać dostęp do maszyny wirtualnej za pośrednictwem konsoli szeregowej i ponownie skonfigurować zaporę. Więcej informacji można znaleźć w [dokumentacji zapory](https://firewalld.org/documentation/).
System plików uszkodzenie/wyboru | Aby uzyskać więcej informacji na temat rozwiązywania problemów z uszkodzonymi systemami plików przy użyciu konsoli szeregowej, zobacz sekcję dotyczącą konsoli szeregowej [maszyny wirtualnej](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) z systemem Linux systemu Azure.
Problemy z konfiguracją SSH | Dostęp do konsoli szeregowej i zmienić ustawienia. Konsola szeregowa można użyć niezależnie od konfiguracji SSH maszyny wirtualnej, ponieważ nie wymaga ona, aby maszyna wirtualna mogła działać. Przewodnik rozwiązywania problemów jest dostępny w [przypadku rozwiązywania problemów z połączeniami SSH z maszyną wirtualną platformy Azure z systemem Linux, która kończy się niepowodzeniem, wystąpiła błędy](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection) Więcej szczegółów można znaleźć w [szczegółowych krokach rozwiązywania problemów SSH w przypadku problemów z połączeniem z maszyną wirtualną z systemem Linux na platformie Azure](./detailed-troubleshoot-ssh-connection.md)
Interakcja z programu inicjującego | Uruchom ponownie maszynę wirtualną z poziomu bloku konsoli szeregowej, aby uzyskać dostęp do usługi GRUB na maszynie wirtualnej z systemem Linux. Aby uzyskać szczegółowe informacje i informacje dotyczące dystrybucji, zobacz [Korzystanie z konsoli szeregowej w celu uzyskania dostępu do grub i trybu jednego użytkownika](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Wyłącz konsolę seryjną
Domyślnie wszystkie subskrypcje mają włączony dostęp do konsoli szeregowej. Konsolę szeregową można wyłączyć na poziomie subskrypcji lub na maszynie wirtualnej/zestawie skalowania maszyn wirtualnych. Należy pamiętać, że Diagnostyka rozruchu musi być włączona na maszynie wirtualnej, aby konsola szeregowa była działać.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Wyłączanie maszyny wirtualnej/zestawu skalowania maszyn wirtualnych
Konsolę szeregową można wyłączyć dla określonej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych, wyłączając ustawienie diagnostyki rozruchu. Wyłącz diagnostykę rozruchu z Azure Portal, aby wyłączyć konsolę seryjną dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. Jeśli używasz konsoli szeregowej w zestawie skalowania maszyn wirtualnych, upewnij się, że Twoje wystąpienia zestawu skalowania maszyn wirtualnych zostały uaktualnione do najnowszego modelu.

> [!NOTE]
> Aby włączyć lub wyłączyć konsoli szeregowej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Te uprawnienia obejmują role administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

### <a name="subscription-level-disable"></a>Wyłącz poziom subskrypcji
Można wyłączyć dla całej subskrypcji, za pośrednictwem konsoli szeregowej [Wyłącz konsoli wywołania interfejsu API REST](/rest/api/serialconsole/console/disableconsole). Ta akcja wymaga dostępu na poziomie współautora lub wyższego do subskrypcji. Możesz użyć **wypróbuj** funkcji dostępnych na tej stronie dokumentacji interfejsu API, wyłączyć lub włączyć konsoli szeregowej dla subskrypcji. Wprowadź identyfikator subskrypcji, wprowadź **wartość domyślną** , a następniewybierz pozycję **Uruchom**. Poleceń interfejsu wiersza polecenia platformy Azure nie są jeszcze dostępne.

Aby ponownie włączyć konsolę szeregową dla subskrypcji, użyj [wywołania interfejsu API REST konsoli](/rest/api/serialconsole/console/enableconsole).

![Wypróbuj interfejs API REST](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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
> Oznacza to, że użytkownik, który jest odłączony nie będzie można wylogować. Możliwość wymuszenia wylogowania przy rozłączeniu (przy użyciu mechanizmu SIGHUP lub podobnego) nadal znajduje się w planie. W przypadku systemu Windows w specjalnej konsoli administracyjnej (SAC) jest włączony automatyczny limit czasu. Jednak w przypadku systemu Linux można skonfigurować ustawienie limitu czasu terminalu. W tym celu należy dodać `export TMOUT=600` do pliku *. bash_profile* lub *. profilu* użytkownika używanego do logowania się w konsoli programu. To ustawienie spowoduje przekroczenie limitu czasu sesji po 10 minutach.

## <a name="accessibility"></a>Ułatwienia dostępu
Ułatwienia dostępu to kluczowy fokus dla konsoli szeregowej platformy Azure. W tym celu upewnij się, że konsola szeregowa jest w pełni dostępna.

### <a name="keyboard-navigation"></a>Nawigowanie przy użyciu klawiatury
Użyj **kartę** kluczowe na klawiaturze, aby przejść w interfejsie konsoli szeregowej w witrynie Azure portal. Twoja lokalizacja zostanie wyróżniony na ekranie. Aby opuścić fokusu okna konsoli szeregowej, naciśnij klawisz **Ctrl**+**F6** na klawiaturze.

### <a name="use-serial-console-with-a-screen-reader"></a>Korzystanie z konsoli szeregowej z czytnikiem ekranu
Konsoli szeregowej ma wbudowaną obsługę czytników zawartości ekranu. Przemieszczać się przy użyciu czytnika zawartości ekranu włączone umożliwi tekst alternatywny dla aktualnie wybranego przycisku zostanie odczytany na głos przez czytnik zawartości ekranu.

## <a name="errors"></a>Błędy
Ponieważ większość błędów przejściowych, ponawianie próby połączenia można często je naprawić. W poniższej tabeli przedstawiono listę błędy i środki zaradcze. Te błędy i środki zaradcze dotyczą zarówno maszyn wirtualnych, jak i wystąpień zestawów skalowania maszyn wirtualnych.

Błąd                            |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu dla  *&lt;VMNAME&gt;* . Aby korzystać z konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostykę rozruchu](boot-diagnostics.md) włączone.
Maszyna wirtualna jest w stanie zatrzymania, przydział zostanie cofnięty. Uruchom maszynę Wirtualną i spróbuj ponownie nawiązać połączenie konsoli szeregowej. | Aby można było uzyskać dostęp do konsoli szeregowej, maszyna wirtualna musi znajdować się w stanie uruchomionym.
Nie masz wymaganych uprawnień do korzystania z tej maszyny wirtualnej za pomocą konsoli szeregowej. Upewnij się, że co najmniej uprawnienia roli Współautor maszyny wirtualnej.| Dostęp do konsoli szeregowej wymaga pewnych uprawnień. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu  *&lt;STORAGEACCOUNTNAME&gt;* . Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga pewnych uprawnień. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).
Gniazda sieci Web został zamknięty lub nie można otworzyć. | Może być konieczne do listy dozwolonych `*.console.azure.com`. Bardziej szczegółowe, ale dłużej podejście jest do listy dozwolonych adresów [zakresów adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są stosunkowo regularnie zmieniane.
Odpowiedź "Dostęp zabroniony" napotkano podczas uzyskiwania dostępu do konta magazynu diagnostyki rozruchu dla tej maszyny Wirtualnej. | Upewnij się, że Diagnostyka rozruchu nie ma zapory konta. Konto magazynu diagnostyki rozruchu dostępny jest niezbędne do konsoli szeregowej funkcjonowania.

## <a name="known-issues"></a>Znane problemy
Mamy świadomość problemy z konsoli szeregowej. Poniżej przedstawiono listę tych problemów oraz kroki dotyczące ograniczania ryzyka. Te problemy i środki zaradcze dotyczą zarówno maszyn wirtualnych, jak i wystąpień zestawów skalowania maszyn wirtualnych.

Problem                           |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Naciśnięcie klawisza **Enter** po transparent połączenia nie powoduje, że monit logowania do wyświetlenia. | Aby uzyskać więcej informacji, zobacz [Hitting wprowadź, nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten problem może wystąpić, jeśli używasz niestandardowej maszyny wirtualnej, urządzenia z ograniczeniami lub konfiguracji GRUB, która powoduje, że system Linux nie może nawiązać połączenia z portem szeregowym.
Konsola szeregowa tekst pobiera tylko część rozmiaru ekranu (często po użyciu edytora tekstów). | Konsole szeregowe nie obsługują negocjowania rozmiaru okna o rozmiarze ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), co oznacza, że sygnał SIGWINCH nie zostanie wysłany do aktualizacji rozmiaru ekranu, a maszyna wirtualna nie będzie miała informacji o rozmiarze terminalu. Zainstaluj xterm lub podobne narzędzie, aby udostępnić `resize` polecenie, a następnie Uruchom. `resize`
Wklejanie ciągów długich nie działa. | Konsoli szeregowej ogranicza długość ciągów w terminalu, aby 2048 znaków, aby zapobiec przeciążeniu przepustowość portu szeregowego.
Konsola szeregowa nie działa z zaporą konta magazynu. | Konsola szeregowa według projektu nie może współpracować z zaporami konta magazynu włączonymi na koncie magazynu diagnostyki rozruchu.
Konsola szeregowa nie działa z kontem magazynu przy użyciu Azure Data Lake Storage Gen2 z hierarchicznymi przestrzeniami nazw. | Jest to znany problem z hierarchicznymi przestrzeniami nazw. Aby rozwiązać problem, upewnij się, że konto magazynu diagnostyki rozruchu maszyny wirtualnej nie zostało utworzone przy użyciu Azure Data Lake Storage Gen2. Tę opcję można ustawić tylko podczas tworzenia konta magazynu. Może być konieczne utworzenie oddzielnego konta magazynu diagnostyki rozruchu bez Azure Data Lake Storage Gen2 włączenia tego problemu.
Błędne dane wejściowe klawiatury w obrazach SLES BYOS. Dane wejściowe z klawiatury są tylko sporadycznie rozpoznawane. | Jest to problem z pakietem Plymouth. Nie należy uruchamiać Plymouth na platformie Azure, ponieważ nie jest potrzebny ekran powitalny, a Plymouth zakłóca możliwości platformy do korzystania z konsoli szeregowej. Usuń Plymouth z `sudo zypper remove plymouth` , a następnie uruchom ponownie. Alternatywnie możesz zmodyfikować wiersz jądra konfiguracji grub, dołączając `plymouth.enable=0` do końca wiersza. Można to zrobić przez [Edytowanie wpisu rozruchu w czasie rozruchu](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)lub przez edycję linii GRUB_CMDLINE_LINUX w programie `/etc/default/grub`, ponowne skompilowanie grub przy użyciu `grub2-mkconfig -o /boot/grub2/grub.cfg`, a następnie ponowne uruchomienie.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**PYTANIA I ODPOWIEDZI. Jak wysłać opinię?**

A. Prześlij opinię, tworząc problem w usłudze GitHub https://aka.ms/serialconsolefeedback w witrynie. Można również (mniej preferowany), możesz wysłać opinię, za pośrednictwem azserialhelp@microsoft.com lub w maszynie wirtualnej kategorii https://feedback.azure.com.

**PYTANIA I ODPOWIEDZI. Konsoli szeregowej obsługuje kopiowania/wklejania?**

A. Tak. Użyj **Ctrl**+**Shift**+**C** i **Ctrl**+**Shift** + **V** do kopiowania i wklejania w terminalu.

**PYTANIA I ODPOWIEDZI. Czy można użyć konsoli szeregowej zamiast połączenia SSH?**

A. Mimo że takie użycie może wydawać się technicznie możliwe, konsola szeregowa jest przeznaczona głównie jako narzędzie do rozwiązywania problemów w sytuacjach, gdy łączność za pośrednictwem protokołu SSH nie jest możliwa. Zalecamy używanie konsoli szeregowej jako zamiennika protokołu SSH z następujących powodów:

- Konsola szeregowa nie ma tak dużej przepustowości jak SSH. Ponieważ jest to połączenie tylko do tekstu, trudne są duże interakcje ze zbyt dużą graficznym interfejsem użytkownika.
- Konsola szeregowa dostęp jest obecnie możliwy tylko przy użyciu nazwy użytkownika i hasła. Ponieważ klucze SSH są znacznie bardziej bezpieczne niż kombinacje nazwy użytkownika/hasła, z punktu widzenia zabezpieczeń logowania zalecamy używanie protokołu SSH za pośrednictwem konsoli szeregowej.

**PYTANIA I ODPOWIEDZI. Kto może włączyć lub wyłączyć konsolę szeregową dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsoli szeregowej na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują role administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

**PYTANIA I ODPOWIEDZI. Kto może uzyskać dostęp do konsoli szeregowej dla maszyny wirtualnej/zestawu skalowania maszyn wirtualnych?**

A. Aby można było uzyskać dostęp do konsoli szeregowej, należy mieć rolę współautor maszyny wirtualnej lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

**PYTANIA I ODPOWIEDZI. Moje konsoli szeregowej nie są wyświetlane wszystko, co należy zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowane, aby uzyskać dostęp do konsoli szeregowej. Aby uzyskać informacje o konfigurowaniu obrazu w celu włączenia konsoli szeregowej, zobacz [konsola szeregowa dostępność dystrybucji systemu Linux](#serial-console-linux-distribution-availability).

**PYTANIA I ODPOWIEDZI. Konsoli szeregowej jest dostępna dla zestawów skalowania maszyn wirtualnych?**

A. Tak! Zobacz [konsolę szeregowa dla Virtual Machine Scale Sets](#serial-console-for-virtual-machine-scale-sets)

**PYTANIA I ODPOWIEDZI. Czy w przypadku skonfigurowania maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych za pomocą tylko uwierzytelniania przy użyciu klucza SSH można nadal używać konsoli szeregowej do nawiązywania połączenia z maszyną wirtualną/wystąpieniem zestawu skalowania maszyn wirtualnych?**

A. Tak. Ponieważ konsola szeregowa nie wymaga kluczy SSH, wystarczy skonfigurować kombinację nazwy użytkownika/hasła. Możesz to zrobić, wybierając pozycję **zresetuj hasło** w Azure Portal i używając tych poświadczeń, aby zalogować się do konsoli szeregowej.

## <a name="next-steps"></a>Następne kroki
* Użyj konsoli szeregowej, aby [uzyskać dostęp do grub i trybu jednego użytkownika](serial-console-grub-single-user-mode.md).
* Użyj konsoli szeregowej dla [wywołań NMI i sysrq](serial-console-nmi-sysrq.md).
* Dowiedz się, jak używać konsoli szeregowej do [włączania Grub w różnych dystrybucjeach](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* Konsola szeregowa jest również dostępna dla [maszyn wirtualnych z systemem Windows](../windows/serial-console.md).
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md).

