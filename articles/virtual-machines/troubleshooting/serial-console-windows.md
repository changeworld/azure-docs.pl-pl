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
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 87ccb1c4995337b385f685797980a9fc3962bc6f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381716"
---
# <a name="azure-serial-console-for-windows"></a>Konsola szeregowa platformy Azure dla systemu Windows

Konsola szeregowa w Azure Portal zapewnia dostęp do konsoli opartej na tekście dla maszyn wirtualnych z systemem Windows i wystąpień zestawów skalowania maszyn wirtualnych. To połączenie szeregowe łączy się z portem seryjnym COM1 maszyny wirtualnej lub wystąpienia zestawu skalowania maszyn wirtualnych, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko przy użyciu Azure Portal i jest to dozwolone tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

Konsola szeregowa działa w taki sam sposób w przypadku maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. W tym dokumencie wszystkie wzmianki dotyczące maszyn wirtualnych będą niejawnie obejmować wystąpienia zestawu skalowania maszyn wirtualnych, chyba że określono inaczej.

Aby uzyskać dokumentację konsoli szeregowej dla systemu Linux, zobacz [Azure serial Console for Linux](serial-console-linux.md).

> [!NOTE]
> Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure i w publicznej wersji zapoznawczej w Azure Government. Nie jest jeszcze dostępna w chmurze platformy Azure w Chinach.


## <a name="prerequisites"></a>Wymagania wstępne

* W maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych musi być używany model wdrażania zarządzania zasobami. W przypadku wdrożeń klasycznych nie są obsługiwane.

- Twoje konto używające konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny wirtualnej i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md) .

- Maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych muszą mieć użytkownika opartego na hasłach. Można go utworzyć za pomocą funkcji [resetowania hasła](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozszerzenia dostępu do maszyny wirtualnej. Wybierz pozycję **zresetuj hasło** w sekcji **Pomoc techniczna i rozwiązywanie problemów** .

* Maszyna wirtualna dla wystąpienia zestawu skalowania maszyn wirtualnych musi mieć włączoną [diagnostykę rozruchu](boot-diagnostics.md) .

    ![Ustawienia diagnostyki rozruchu](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Włącz funkcje konsoli szeregowej dla systemu Windows Server

> [!NOTE]
> Jeśli nie widzisz niczego w konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona na maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Włącz konsoli szeregowej na obrazach niestandardowych lub starszy
Nowsze obrazy systemu Windows Server na platformie Azure mają domyślnie włączoną [specjalną konsolę administracyjną](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) . Konsola SAC jest obsługiwane w wersji Windows server, ale nie jest dostępna w wersji klienta (na przykład systemu Windows 10, Windows 8 lub Windows 7).

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

Jeśli [konsola SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nie jest włączona, konsola szeregowa nie będzie wyświetlać monitu dotyczącego konsoli SAC. W niektórych przypadkach wyświetlane są informacje o kondycji maszyny Wirtualnej, a w innych przypadkach jest on pusty. Jeśli używasz obrazu systemu Windows Server, utworzone przed lutym 2018 roku SAC prawdopodobnie nie jest włączony.

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

1. Łączenie z konsolą szeregową. W przypadku pomyślnego nawiązania połączenia zostanie wyświetlony monit **> SAC**:

    ![Nawiązać połączenie z SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Wprowadź `cmd`, aby utworzyć kanał, który ma wystąpienie CMD.

1.  Wprowadź `ch -si 1` lub naciśnij klawisz `<esc>+<tab>` klawiszy skrótów, aby przełączyć się na kanał, na którym działa wystąpienie CMD.

1.  Naciśnij klawisz **Enter**, a następnie wprowadź poświadczenia logowania z uprawnieniami administracyjnymi.

1.  Po wprowadzeniu prawidłowych poświadczeń wystąpienia CMD otwiera.

1.  Aby uruchomić wystąpienie programu PowerShell, wprowadź `PowerShell` w wystąpieniu CMD, a następnie naciśnij klawisz **Enter**.

    ![Otwórz wystąpienie programu PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Użyj konsoli szeregowej NMI wywołań
Niemaskowalnego przerwania (NMI) jest przeznaczone do tworzenia sygnał, który oprogramowania na maszynie wirtualnej nie są ignorowane. W przeszłości NMIs zostały użyte w celu monitorowania problemów ze sprzętem w systemach, które są wymagane określone czasy. Obecnie programiści i Administratorzy systemu często używają NMI jako mechanizmu debugowania lub rozwiązywania problemów z nieodpowiadającymi systemami.

Konsoli szeregowej może służyć do wysyłania NMI na maszynie wirtualnej platformy Azure przy użyciu ikonę klawiatury na pasku poleceń. Po NMI zostało dostarczone, konfiguracja maszyny wirtualnej będzie kontrolować, jak zachowuje się system. Windows można skonfigurować do awarii i tworzenie pliku zrzutu pamięci podczas odbierania NMI.

![Wyślij NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Aby uzyskać informacje na temat konfigurowania systemu Windows w celu utworzenia pliku zrzutu awaryjnego po odebraniu NMI, zobacz [jak wygenerować plik zrzutu awaryjnego przy użyciu NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Użyj klawiszy funkcyjnych w konsoli szeregowej
Klawiszy funkcyjnych są włączone dla użycia konsoli szeregowej na maszynach wirtualnych Windows. F8 na liście rozwijanej konsoli szeregowej udostępnia wygodne łatwe wprowadzanie menu Zaawansowane ustawienia rozruchu, ale konsoli szeregowej jest zgodny z innymi klawiszy funkcyjnych. W zależności od komputera, z którego korzystasz z konsoli szeregowej, może być konieczne naciśnięcie klawisza **Fn** + **F1** (lub F2, F3 itp.).

### <a name="use-wsl-in-serial-console"></a>Użyj WSL w konsoli szeregowej
Podsystem Windows dla systemu Linux (WSL) została włączona dla systemu Windows Server 2019 lub nowszego, więc istnieje również możliwość włączenia WSL do użycia w konsoli szeregowej, jeśli korzystasz z systemu Windows Server 2019 lub nowszej. Może to być przydatne w przypadku użytkowników, którzy mają również znajomość polecenia systemu Linux. Instrukcje dotyczące włączania WSL dla systemu Windows Server znajdują się w [przewodniku instalacji](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Ponowne uruchamianie wystąpienia maszyny wirtualnej z systemem Windows/zestawu skalowania maszyn wirtualnych w konsoli szeregowej
Możesz zainicjować ponowne uruchomienie w konsoli szeregowej, przechodząc do przycisku energia, a następnie klikając pozycję "Uruchom ponownie maszynę wirtualną". Spowoduje to zainicjowanie ponownego uruchomienia maszyny wirtualnej, a w Azure Portal zostanie wyświetlone powiadomienie dotyczące ponownego uruchomienia.

Jest to przydatne w sytuacjach, w których możesz chcieć uzyskać dostęp do menu rozruchu bez opuszczania środowiska konsoli szeregowej.

![Ponowne uruchomienie konsoli szeregowej systemu Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Wyłącz konsolę seryjną
Domyślnie wszystkie subskrypcje mają włączony dostęp do konsoli szeregowej. Konsolę szeregową można wyłączyć na poziomie subskrypcji lub na maszynie wirtualnej/zestawie skalowania maszyn wirtualnych. Aby uzyskać szczegółowe instrukcje, zobacz [Włączanie i wyłączanie konsoli szeregowej platformy Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowej

### <a name="access-security"></a>Zabezpieczenia dostępu
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają rolę dostępu [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub wyższą dla maszyny wirtualnej. Jeśli dzierżawa Azure Active Directory wymaga uwierzytelniania wieloskładnikowego (MFA), dostęp do konsoli szeregowej będzie wymagał również usługi MFA, ponieważ dostęp do konsoli szeregowej odbywa się za pomocą [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane, które są wysyłane w obie strony są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Wszystkie prawa dostępu do konsoli szeregowej są obecnie rejestrowane w dziennikach [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) maszyny wirtualnej. Dostęp do tych dzienników są własnością i kontrolowane przez administratora maszyny wirtualnej platformy Azure.

> [!CAUTION]
> Żadne hasła dostępu do konsoli są rejestrowane. Jednak jeśli polecenia uruchamiane w ramach konsoli zawierają lub danych wyjściowych haseł, kluczy tajnych, nazwy użytkowników lub jakąkolwiek inną formę identyfikowalne dane osobowe (PII), te będą zapisywane do dzienników diagnostyki rozruchu maszyny Wirtualnej. One będą zapisywane wraz z wszystkich innych widocznych tekstu, jako część wykonania wstecz przewijania konsoli szeregowej funkcji. Te dzienniki są cykliczne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mieli do nich dostęp. Jednak zaleca się następujące najlepsze rozwiązanie polegające na przy użyciu pulpitu zdalnego dla wszystkich elementów, które mogą obejmować wpisów tajnych i/lub dane osobowe.

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsoli szeregowej i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie odłączony, a drugi użytkownik nawiązał połączenie z tą samą sesją.

> [!CAUTION]
> Oznacza to, że użytkownik, który został odłączony, nie zostanie wylogowany. Możliwość wymuszenia wylogowania po rozłączeniu (przy użyciu mechanizmu SIGHUP lub podobnego) nadal jest w planie. Dla Windows to automatyczne limitu czasu w SAC; włączone w przypadku systemu Linux można skonfigurować ustawienie limitu czasu w terminalu.

## <a name="accessibility"></a>Ułatwienia dostępu
Dostępność jest kluczowym dla konsoli szeregowej platformy Azure. W tym celu upewniliśmy się, że konsoli szeregowej jest dostępny dla wizualizacji i wysłuchaniu osłabiona, a także osoby, które mogą okazać się niemożliwe przy użyciu myszy.

### <a name="keyboard-navigation"></a>Nawigowanie przy użyciu klawiatury
Użyj klawisza **Tab** na klawiaturze, aby przejść do interfejsu konsoli szeregowej z Azure Portal. Twoja lokalizacja zostanie wyróżniony na ekranie. Aby opuścić fokus okna konsoli szeregowej, naciśnij klawisz **Ctrl**+**F6** na klawiaturze.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Użyj konsoli szeregowej przy użyciu czytnika zawartości ekranu
Konsoli szeregowej ma wbudowaną obsługę czytników zawartości ekranu. Przemieszczać się przy użyciu czytnika zawartości ekranu włączone umożliwi tekst alternatywny dla aktualnie wybranego przycisku zostanie odczytany na głos przez czytnik zawartości ekranu.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Typowe scenariusze dotyczące uzyskiwania dostępu do konsoli szeregowej

Scenariusz          | Akcje w konsoli szeregowej
:------------------|:-----------------------------------------
Reguły zapory niepoprawne | Dostęp do serial konsoli i napraw zasady Windows w zapory.
System plików uszkodzenie/wyboru | Dostęp do konsoli szeregowej i odzyskiwanie systemu plików.
Problemy z konfiguracją protokołu RDP | Dostęp do konsoli szeregowej i zmienić ustawienia. Aby uzyskać więcej informacji, zobacz [dokumentację protokołu RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Blokowanie sieci w systemie | W witrynie Azure portal do zarządzania systemem, należy uzyskać dostęp do konsoli szeregowej. Niektóre polecenia sieciowe są wymienione w [poleceniach systemu Windows: cmd i PowerShell](serial-console-cmd-ps-commands.md).
Interakcja z programu inicjującego | Dostęp do danych konfiguracji rozruchu za pośrednictwem konsoli szeregowej. Aby uzyskać więcej informacji, zobacz [Włączanie menu rozruchu systemu Windows w konsoli szeregowej](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Znane problemy
Mamy świadomość niektórych problemów z konsolą szeregową i systemem operacyjnym maszyny wirtualnej. Poniżej znajduje się lista tych problemów i kroków związanych z eliminowaniem maszyn wirtualnych z systemem Windows. Te problemy i środki zaradcze dotyczą zarówno maszyn wirtualnych, jak i wystąpień zestawów skalowania maszyn wirtualnych. Jeśli nie są one zgodne z wyświetlonym błędem, zobacz Typowe błędy usługi konsoli szeregowej w przypadku [typowych błędów konsoli szeregowej](./serial-console-errors.md).

Problem                             |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Naciśnięcie klawisza **Enter** po banerze połączenia nie spowoduje wyświetlenia monitu logowania. | Aby uzyskać więcej informacji, zobacz [naciśnięcie klawisza ENTER nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten błąd może wystąpić, jeśli używasz niestandardowej maszyny Wirtualnej, urządzenia ze wzmocnionymi zabezpieczeniami lub konfiguracji rozruchu, który powoduje, że Windows niepowodzenie prawidłowo połączenia do portu szeregowego. Ten błąd występuje również w przypadku korzystania z maszyny wirtualnej z systemem Windows 10, ponieważ na maszynach wirtualnych z systemem Windows Server skonfigurowano obsługę usług EMS.
Tylko informacje o kondycji jest wyświetlany podczas nawiązywania połączenia maszyny Wirtualnej z systemem Windows| Ten błąd występuje, jeśli Specjalna konsola administracyjna nie została włączona dla obrazu systemu Windows. Zobacz [Włączanie konsoli szeregowej w obrazach niestandardowych lub starszych,](#enable-the-serial-console-in-custom-or-older-images) Aby uzyskać instrukcje dotyczące ręcznego włączania konsoli SAC na maszynie wirtualnej z systemem Windows. Aby uzyskać więcej informacji, zobacz [sygnały kondycji systemu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Konsola SAC nie przyjmuje całego obszaru konsoli szeregowej w przeglądarce | Jest to znany problem dotyczący systemu Windows i emulatora terminalu. Śledzimy ten problem zarówno z zespołami, ale nie ma żadnych środków zaradczych.
Nie można wpisać w SAC stanie się monit, jeśli włączone jest debugowanie jądra. | Protokół RDP do maszyny wirtualnej i uruchamianie `bcdedit /debug {current} off` z wiersza polecenia z podwyższonym poziomem uprawnień. Jeśli nie możesz użyć protokołu RDP, możesz zamiast tego dołączyć dysk systemu operacyjnego do innej maszyny wirtualnej platformy Azure i zmodyfikować go wraz z dyskiem danych przez uruchomienie `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, a następnie wymienić dysk ponownie.
Wklejając w programie PowerShell powoduje SAC trzeciego znaku gdyby oryginalną zawartość powtarzających się znaków. | Aby obejść obejście, uruchom polecenie `Remove-Module PSReadLine` w celu zwolnienia modułu PSReadLine z bieżącej sesji. Ta akcja nie zostanie skasowana czy odinstalowana modułu.
Niektóre dane wejściowe z klawiatury generują dziwne dane wyjściowe SAC (na przykład **[A**, **[3 ~** ). | Sekwencje unikowe [VT100](https://aka.ms/vtsequences) nie są obsługiwane przez monit konsoli SAC.
Wklejanie ciągów długich nie działa. | Konsoli szeregowej ogranicza długość ciągów w terminalu, aby 2048 znaków, aby zapobiec przeciążeniu przepustowość portu szeregowego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P. Jak mogę wysłać opinię?**

A. Prześlij opinię, tworząc problem w usłudze GitHub w https://aka.ms/serialconsolefeedback. Alternatywnie (mniej preferowany) można wysłać opinię za pośrednictwem azserialhelp@microsoft.com lub z kategorii https://feedback.azure.commaszyny wirtualnej.

**P. czy konsola szeregowa obsługuje kopiowanie/wklejanie?**

A. Tak. Użyj **klawiszy ctrl**+**SHIFT**+**C** i **Ctrl**+**SHIFT**+**V** , aby skopiować i wkleić do terminalu.

**P. kto może włączyć lub wyłączyć konsolę szeregową dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsoli szeregowej na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują role administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

**P. kto może uzyskać dostęp do konsoli szeregowej dla mojej maszyny wirtualnej?**

A. Konieczne jest posiadanie rola Współautor maszyny wirtualnej lub nowszej, aby uzyskać dostęp do konsoli szeregowej maszyny Wirtualnej maszyny Wirtualnej.

**P. Moja konsola szeregowa nie wyświetla niczego, co mam zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowane, aby uzyskać dostęp do konsoli szeregowej. Aby uzyskać informacje o konfigurowaniu obrazu w celu włączenia konsoli szeregowej, zobacz [Włączanie konsoli szeregowej w obrazie niestandardowym lub starszym](#enable-the-serial-console-in-custom-or-older-images).

**P. czy konsola szeregowa jest dostępna dla zestawów skalowania maszyn wirtualnych?**

A. Tak! Zobacz [konsolę szeregowa dla Virtual Machine Scale Sets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowy przewodnik dotyczący poleceń CMD i PowerShell, których można użyć w konsoli SAC systemu Windows, zobacz [polecenia systemu Windows: cmd i PowerShell](serial-console-cmd-ps-commands.md).
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych z [systemem Linux](serial-console-linux.md) .
* Dowiedz się więcej na temat [diagnostyki rozruchu](boot-diagnostics.md).
