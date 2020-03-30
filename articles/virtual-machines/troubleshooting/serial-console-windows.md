---
title: Konsola szeregowa platformy Azure dla systemu Windows | Dokumenty firmy Microsoft
description: Dwukierunkowa konsola szeregowa dla maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267003"
---
# <a name="azure-serial-console-for-windows"></a>Konsola szeregowa platformy Azure dla systemu Windows

Konsola szeregowa w witrynie Azure portal zapewnia dostęp do konsoli tekstowej dla maszyn wirtualnych systemu Windows (maszyny wirtualne) i wystąpień zestawu skalowania maszyny wirtualnej. To połączenie szeregowe łączy się z portem szeregowym COM1 wystąpienia zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko za pomocą witryny Azure portal i jest dozwolony tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższego do zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej.

Konsola szeregowa działa w ten sam sposób dla maszyn wirtualnych i wystąpień zestawu skalowania maszyny wirtualnej. W tym doc wszystkie wzmianki do maszyn wirtualnych niejawnie będzie zawierać wystąpienia zestawu skalowania maszyny wirtualnej, chyba że określono inaczej.

Aby uzyskać dokumentację konsoli szeregowej dla systemu Linux, zobacz [Azure Serial Console for Linux](serial-console-linux.md).

> [!NOTE]
> Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure i w publicznej wersji zapoznawczej w usłudze Azure Dla Instytucji Rządowych. Nie jest jeszcze dostępna w chmurze Azure China.


## <a name="prerequisites"></a>Wymagania wstępne

* Wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej musi używać modelu wdrażania zarządzania zasobami. Wdrożenia klasyczne nie są obsługiwane.

- Twoje konto korzystające z konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny wirtualnej i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md)

- Wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej musi mieć użytkownika opartego na hasłach. Można go utworzyć z funkcją [resetowania hasła](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozszerzenia dostępu do maszyny Wirtualnej. Wybierz **pozycję Resetuj hasło** z sekcji Pomoc **techniczna + rozwiązywanie problemów.**

* Maszyna wirtualna dla środowiska wirtualnego skalowania zestaw wystąpienie musi mieć [diagnostykę rozruchu](boot-diagnostics.md) włączone.

    ![Ustawienia diagnostyki rozruchu](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Włącz funkcję konsoli szeregi dla systemu Windows Server

> [!NOTE]
> Jeśli nie widzisz niczego w konsoli szeregowej, upewnij się, że diagnostyka rozruchu jest włączona na maszynie wirtualnej lub zestaw skali maszyny wirtualnej.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Włączanie konsoli szeregowej w obrazach niestandardowych lub starszych
Nowsze obrazy systemu Windows Server na platformie Azure mają domyślnie włączoną [specjalną konsolę administracyjną](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC). Usługa SAC jest obsługiwana w wersjach systemu Windows na serwerze, ale nie jest dostępna w wersjach klienckich (na przykład Windows 10, Windows 8 lub Windows 7).

W przypadku starszych obrazów systemu Windows Server (utworzonych przed lutym 2018 r.) można automatycznie włączyć konsolę szeregową za pomocą funkcji polecenia uruchamiania portalu Azure. W portalu Azure wybierz **polecenie Uruchom**, a następnie wybierz polecenie o nazwie **EnableEMS** z listy.

![Uruchom listę poleceń](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Alternatywnie, aby ręcznie włączyć konsolę szeregową dla zestawu skalowania maszyn wirtualnych systemu Windows/maszyny wirtualnej utworzonego przed lutym 2018 r., wykonaj następujące kroki:

1. Łączenie się z maszyną wirtualną systemu Windows przy użyciu pulpitu zdalnego
1. W wierszu polecenia administracyjnego uruchom następujące polecenia:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Uruchom ponownie system, aby włączyć konsolę SAC.

    ![Konsola SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

W razie potrzeby sac można włączyć również w trybie offline:

1. Dołącz dysk systemu Windows, dla którego sac ma być skonfigurowany jako dysk danych do istniejącej maszyny Wirtualnej.

1. W wierszu polecenia administracyjnego uruchom następujące polecenia:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Skąd mam wiedzieć, czy sac jest włączony?

Jeśli [sac](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nie jest włączona, konsola szeregowa nie wyświetli monitu SAC. W niektórych przypadkach informacje o kondycji maszyny Wirtualnej są wyświetlane, a w innych przypadkach są puste. Jeśli używasz obrazu systemu Windows Server utworzonego przed lutym 2018 r., sac prawdopodobnie nie zostanie włączona.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Włączanie menu rozruchu systemu Windows w konsoli szeregowej

Jeśli chcesz włączyć wyświetlanie monitów z modułem ładującego systemu Windows w konsoli szeregowej, możesz dodać następujące dodatkowe opcje do danych konfiguracji rozruchu. Aby uzyskać więcej informacji, zobacz [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Połącz się z wystąpieniem zestawu skalowania maszyny wirtualnej systemu Windows lub maszyny wirtualnej przy użyciu pulpitu zdalnego.

1. W wierszu polecenia administracyjnego uruchom następujące polecenia:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Ponowne uruchomienie systemu, aby włączyć menu rozruchowe

> [!NOTE]
> Limit czasu ustawiony dla menu menedżera rozruchu będzie miał wpływ na czas rozruchu systemu operacyjnego. Jeśli uważasz, że 10-sekundowa wartość limitu czasu jest zbyt krótka lub zbyt długa, ustaw ją na inną wartość.

## <a name="use-serial-console"></a>Korzystanie z konsoli szeregowego

### <a name="use-cmd-or-powershell-in-serial-console"></a>Używanie cmd lub programu PowerShell w konsoli szeregowej

1. Połącz się z konsolą szeregową. Jeśli połączenie zostanie pomyślnie nawiązanie, monitem jest **SAC>: **

    ![Połącz się z sac](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Wprowadź, `cmd` aby utworzyć kanał, który ma wystąpienie CMD.

1.  Wprowadź `ch -si 1` lub `<esc>+<tab>` naciśnij klawisze skrótów, aby przełączyć się do kanału, na który jest uruchomione wystąpienie CMD.

1.  Naciśnij **klawisz Enter**, a następnie wprowadź poświadczenia logowania z uprawnieniami administracyjnymi.

1.  Po wprowadzeniu prawidłowych poświadczeń zostanie otwarte wystąpienie CMD.

1.  Aby uruchomić wystąpienie programu `PowerShell` PowerShell, wprowadź instancję CMD, a następnie naciśnij klawisz **Enter**.

    ![Otwórz wystąpienie programu PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Używanie konsoli szeregowej do połączeń NMI
Przerwanie niemaskowane (NMI) ma na celu utworzenie sygnału, że oprogramowanie na maszynie wirtualnej nie będzie ignorować. W przeszłości nmi były używane do monitorowania problemów sprzętowych w systemach, które wymagają określonych czasów odpowiedzi. Obecnie programiści i administratorzy systemu często używają NMI jako mechanizmu do debugowania lub rozwiązywania problemów z systemami, które nie odpowiadają.

Konsoli szeregowej można wysłać NMI do maszyny wirtualnej platformy Azure przy użyciu ikony klawiatury na pasku poleceń. Po dostarczeniu nmi konfiguracji maszyny wirtualnej będzie kontrolować, jak system reaguje. System Windows można skonfigurować tak, aby ulegał awarii i tworzył plik zrzutu pamięci podczas odbierania pliku NMI.

![Wyślij nmi](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Aby uzyskać informacje dotyczące konfigurowania systemu Windows do tworzenia pliku zrzutu awaryjnego po odebraniu pliku NMI, zobacz [Jak wygenerować plik zrzutu awaryjnego przy użyciu pliku NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Używanie klawiszy funkcyjnych w konsoli szeregu
Klawisze funkcyjne są włączone do użycia konsoli szerega na maszynach wirtualnych systemu Windows. F8 w rozwijaniu konsoli szeregowej zapewnia wygodę łatwego wejścia do menu Zaawansowane ustawienia rozruchu, ale konsola szeregowa jest kompatybilna ze wszystkimi innymi klawiszami funkcyjnymi. W zależności od komputera, z którego korzystasz z konsoli szeregowej, może być konieczne naciśnięcie klawisza **Fn** + **F1** (lub F2, F3 itp.) na klawiaturze.

### <a name="use-wsl-in-serial-console"></a>Używanie funkcji WSL w konsoli szeregowych
Podsystem Windows dla systemu Linux (WSL) został włączony dla systemu Windows Server 2019 lub nowszego, więc można również włączyć protokół WSL do użycia w konsoli szeregowej, jeśli jest uruchomiony system Windows Server 2019 lub nowszy. Może to być korzystne dla użytkowników, którzy również znają polecenia Systemu Linux. Instrukcje dotyczące włączania usługi WSL dla systemu Windows Server można znaleźć w [Podręczniku instalacji](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Ponowne uruchamianie wystąpienia zestawu skalowania maszyny wirtualnej systemu Windows/maszyny wirtualnej w konsoli szeregowej
Możesz zainicjować ponowne uruchomienie w konsoli szeregowej, przechodząc do przycisku zasilania i klikając "Uruchom ponownie maszynę wirtualną". Spowoduje to zainicjowanie ponownego uruchomienia maszyny Wirtualnej, a w witrynie Azure portal zostanie wyświetlone powiadomienie dotyczące ponownego uruchomienia.

Jest to przydatne w sytuacjach, w których można uzyskać dostęp do menu rozruchowego bez opuszczania konsoli szeregowej.

![Ponowne uruchamianie konsoli szeregowego systemu Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Wyłączanie konsoli szeregowej
Domyślnie wszystkie subskrypcje mają włączony dostęp do konsoli szeregowego. Można wyłączyć konsolę szeregową na poziomie subskrypcji lub na poziomie zestawu skalowania maszyny wirtualnej/maszyny wirtualnej. Aby uzyskać szczegółowe instrukcje, odwiedź stronę [Włącz i wyłącz konsolę szeregową platformy Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowych

### <a name="access-security"></a>Zabezpieczenia dostępu
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają rolę dostępu [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub wyższej do maszyny wirtualnej. Jeśli dzierżawa usługi Azure Active Directory wymaga uwierzytelniania wieloskładnikowego (MFA), dostęp do konsoli szeregowej będzie również wymagał uwierzytelnienia wieloskładnikowego, ponieważ dostęp konsoli szeregowej odbywa się za pośrednictwem [portalu Azure.](https://portal.azure.com)

### <a name="channel-security"></a>Zabezpieczenia kanałów
Wszystkie dane wysyłane tam iz powrotem są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Cały dostęp do konsoli szeregowej jest obecnie rejestrowany w [dziennikach diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) maszyny wirtualnej. Dostęp do tych dzienników są własnością i są kontrolowane przez administratora maszyny wirtualnej platformy Azure.

> [!CAUTION]
> Nie są rejestrowane żadne hasła dostępu do konsoli. Jeśli jednak polecenia uruchamiane w konsoli zawierają lub wyprowadzają hasła, wpisy tajne, nazwy użytkowników lub inną formę informacji umożliwiających identyfikację użytkownika, zostaną one zapisane w dziennikach diagnostyki rozruchowej maszyny Wirtualnej. Zostaną one napisane wraz z wszystkimi innymi widocznymi tekstami, w ramach implementacji funkcji przewijania konsoli szeregowej. Te dzienniki są okrężne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mają do nich dostęp. Zaleca się jednak stosowanie najlepszych praktyk dotyczących używania pulpitu zdalnego w przypadku wszelkich funkcji, które mogą obejmować wpisy tajne i/lub informacje umożliwiające identyfikację użytkownika.

### <a name="concurrent-usage"></a>Jednoczesne użycie
Jeśli użytkownik jest połączony z konsolą szeregową, a inny użytkownik pomyślnie zażąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie rozłączony, a drugi użytkownik połączony z tą samą sesją.

> [!CAUTION]
> Oznacza to, że użytkownik, który jest rozłączony, nie zostanie wylogowany. Możliwość wymuszenia wylogowania po rozłączeniu (przy użyciu SIGHUP lub podobnego mechanizmu) jest nadal w planie działania. W systemie Windows w systemie SAC jest włączony automatyczny limit czasu; dla Systemu Linux, można skonfigurować ustawienie limitu czasu terminalu.

## <a name="accessibility"></a>Ułatwienia dostępu
Ułatwienia dostępu jest głównym celem konsoli szeregowej platformy Azure. W tym celu zadbaliśmy o to, aby konsola szeregowa była dostępna dla osób niedowidzących i niedosłyszących, a także osób, które mogą nie być w stanie korzystać z myszy.

### <a name="keyboard-navigation"></a>Nawigacja przy użyciu klawiatury
Użyj **klawisza Tab** na klawiaturze, aby poruszać się w interfejsie konsoli szeregowej z witryny Azure portal. Twoja lokalizacja zostanie podświetlona na ekranie. Aby pozostawić fokus okna konsoli szeregowej, naciśnij klawisz **Ctrl**+**F6** na klawiaturze.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Używanie konsoli szeregowej z czytnikiem ekranu
Konsola szeregowa ma wbudowaną obsługę czytnika ekranu. Poruszanie się z włączonym czytnikiem ekranu umożliwi odczytanie tekstu alternatywnego aktualnie wybranego przycisku na głos przez czytnik ekranu.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Typowe scenariusze uzyskiwania dostępu do konsoli szeregowej

Scenariusz          | Akcje w konsoli szeregowej
:------------------|:-----------------------------------------
Nieprawidłowe reguły zapory | Uzyskaj dostęp do konsoli szeregowej i napraw reguły zapory systemu Windows.
Uszkodzenie/sprawdzanie systemu plików | Dostęp do konsoli szeregowej i odzyskiwanie systemu plików.
Problemy z konfiguracją protokołu RDP | Przejdź do konsoli szeregowej i zmień ustawienia. Aby uzyskać więcej informacji, zobacz [dokumentację PROW](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
System blokady sieci | Dostęp do konsoli szeregowej z witryny Azure Portal do zarządzania systemem. Niektóre polecenia sieciowe są wymienione w [poleceniach systemu Windows: CMD i PowerShell](serial-console-cmd-ps-commands.md).
Interakcja z bootloaderem | Dostęp do bcd za pośrednictwem konsoli szeregowej. Aby uzyskać więcej informacji, zobacz [Włączanie menu rozruchu systemu Windows w konsoli szeregowej](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Znane problemy
Zdajemy sobie sprawę z niektórych problemów z konsolą szeregową i systemem operacyjnym maszyny Wirtualnej. Oto lista tych problemów i kroki łagodzenia dla maszyn wirtualnych systemu Windows. Te problemy i środki zaradcze dotyczą zarówno maszyn wirtualnych, jak i wystąpień zestawu skalowania maszyny wirtualnej. Jeśli te błędy nie są zgodne z wyświetlonym błędem, zobacz typowe błędy usługi konsoli szeregowej w [przypadku błędów typowej konsoli szeregowej](./serial-console-errors.md).

Problem                             |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Naciśnięcie **klawisza Enter** po zakończeniu baneru połączenia nie powoduje wyświetlenia monitu o logowanie. | Aby uzyskać więcej informacji, zobacz [Uderzanie enter nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten błąd może wystąpić, jeśli korzystasz z niestandardowej maszyny Wirtualnej, utwardzonego urządzenia lub konfiguracji rozruchowej, która powoduje, że system Windows nie może poprawnie połączyć się z portem szeregowym. Ten błąd wystąpi również w przypadku uruchomienia maszyny wirtualnej z systemem Windows 10, ponieważ tylko maszyny wirtualne z systemem Windows Server są skonfigurowane tak, aby mieć włączoną usługę EMS.
Tylko informacje o kondycji są wyświetlane podczas łączenia się z maszyną wirtualną systemu Windows| Ten błąd występuje, jeśli specjalna konsola administracyjna nie została włączona dla obrazu systemu Windows. Zobacz [Włączanie konsoli szeregowej w obrazach niestandardowych lub starszych,](#enable-the-serial-console-in-custom-or-older-images) aby uzyskać instrukcje dotyczące ręcznego włączania usługi SAC na maszynie Wirtualnej systemu Windows. Aby uzyskać więcej informacji, zobacz [Sygnały kondycji systemu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
SAC nie zajmuje całego obszaru Konsoli szeregowej w przeglądarce | Jest to znany problem z systemem Windows i emulatorem terminala. Śledzimy ten problem z obu zespołów, ale na razie nie ma żadnych środków zaradczych.
Nie można wpisać monitu SAC, jeśli debugowanie jądra jest włączone. | RDP do maszyny `bcdedit /debug {current} off` Wirtualnej i uruchomić z wiersza polecenia z podwyższonym poziomem uprawnień. Jeśli nie można rdp, zamiast tego można dołączyć dysk systemu operacyjnego do innej maszyny Wirtualnej `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`platformy Azure i zmodyfikować go, gdy jest dołączony jako dysk danych, uruchamiając, a następnie zamianę dysku z powrotem.
Wklejanie do programu PowerShell w sac powoduje trzeci znak, jeśli oryginalna zawartość miała powtarzający się znak. | Aby obejść ten `Remove-Module PSReadLine` problem, uruchom, aby zwolnić moduł PSReadLine z bieżącej sesji. Ta akcja nie spowoduje usunięcia ani odinstalowania modułu.
Niektóre wejścia klawiatury wytwarzają dziwne wyjście SAC (na przykład **[A**, **[3~**). | Sekwencje unikowe [VT100](https://aka.ms/vtsequences) nie są obsługiwane przez monit SAC.
Wklejanie długich ciągów nie działa. | Konsola szeregowa ogranicza długość ciągów wklejonych do terminala do 2048 znaków, aby zapobiec przeciążeniu przepustowości portu szeregowego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P. Jak mogę wysłać opinię?**

A. Przekaż opinię, tworząc problem z https://aka.ms/serialconsolefeedbackgithubem w pliku . Alternatywnie (mniej preferowane), można azserialhelp@microsoft.com wysłać opinię za https://feedback.azure.compośrednictwem lub w kategorii maszyny wirtualnej .

**P. Czy konsola szeregowa obsługuje kopiowanie/wklejanie?**

A. Tak. Użyj **klawiszy Ctrl**+**Shift**+**C** i **Ctrl**+**Shift**+**V,** aby skopiować i wkleić do terminala.

**P. Kto może włączyć lub wyłączyć konsolę szeregową dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsolę szeregową na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują role administratora lub właściciela. Role niestandardowe mogą mieć również uprawnienia do zapisu.

**P. Kto może uzyskać dostęp do konsoli szeregowej dla mojej maszyny Wirtualnej?**

A. Aby uzyskać dostęp do konsoli szeregowej maszyny wirtualnej, musisz mieć rolę współautora maszyny wirtualnej lub wyższą.

**P. Moja konsola szeregowa niczego nie wyświetla, co mam zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowany w celu uzyskania dostępu do konsoli szeregowego. Aby uzyskać informacje dotyczące konfigurowania obrazu w celu włączenia konsoli szeregowej, zobacz [Włączanie konsoli szeregowej w obrazach niestandardowych lub starszych](#enable-the-serial-console-in-custom-or-older-images).

**Pyt.: Czy konsola szeregowa jest dostępna dla zestawów skalowania maszyny wirtualnej?**

A. Tak! Zobacz [Konsola szeregowa dla zestawów skalowania maszyny wirtualnej](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowy przewodnik po poleceniach CMD i programu PowerShell, których można używać w usłudze Windows SAC, zobacz [polecenia systemu Windows: CMD i PowerShell](serial-console-cmd-ps-commands.md).
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych [z systemem Linux.](serial-console-linux.md)
* Dowiedz się więcej o [diagnostyce rozruchu](boot-diagnostics.md).
