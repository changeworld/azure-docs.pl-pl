---
title: Śledzenie zmian za pomocą usługi Azure Automation
description: Rozwiązanie do śledzenia zmian ułatwia identyfikowanie zmian oprogramowania i usługi Systemu Windows, które występują w twoim środowisku.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 83babd65fdf22ab40b0137d93a1cbe7f1fd7ff04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844806"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Śledzenie zmian w środowisku za pomocą rozwiązania do śledzenia zmian

Ten artykuł ułatwia korzystanie z rozwiązania śledzenia zmian, aby łatwo identyfikować zmiany w środowisku. Rozwiązanie śledzi następujące zmiany konfiguracji, aby ułatwić zidentyfikowanie problemów operacyjnych:

- Oprogramowanie systemu Windows
- Oprogramowanie Linux (pakiety)

    >[!NOTE]
    >Śledzenie zmian śledzi tylko oprogramowanie zarządzane za pomocą menedżera pakietów dystrybucji.

- Pliki z systemami Windows i Linux
- Klucze rejestru systemu Windows
- Usługi systemu Windows
- Demony Linuksa

Zmiany w zainstalowanym oprogramowaniu, usługach systemu Windows, rejestrze systemu Windows i plikach oraz demonach systemu Linux na monitorowanych serwerach są wysyłane do usługi Azure Monitor w chmurze w celu przetworzenia. Logika jest stosowana do odebranych danych, a usługa w chmurze rejestruje dane. Korzystając z informacji na pulpicie nawigacyjnym śledzenia zmian, można łatwo zobaczyć zmiany wprowadzone w infrastrukturze serwera.

> [!NOTE]
> Śledzenie zmian automatyzacji platformy Azure śledzi zmiany na maszynach wirtualnych. Aby śledzić zmiany właściwości usługi Azure Resource Manager, zobacz [Historia zmian](../governance/resource-graph/how-to/get-resource-changes.md)w grafie zasobów platformy Azure .

## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows

Następujące wersje systemu operacyjnego Windows są oficjalnie obsługiwane przez agenta systemu Windows:

* Windows Server 2008 R2 lub nowszy

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Następujące dystrybucje Linuksa są oficjalnie obsługiwane. Jednak agent systemu Linux może również działać na innych dystrybucjach, które nie są wymienione. O ile nie zaznaczono inaczej, wszystkie wersje pomocnicze są obsługiwane dla każdej wersji głównej na liście.

### <a name="64-bit"></a>64-bitowy

* CentOS 6 i 7
* Amazon Linux 2017.09
* Oracle Linux 6 i 7
* Serwer Red Hat Enterprise Linux Server 6 i 7
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS i 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32-bitowa

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14.04 LTS i 16.04 LTS

## <a name="enable-change-tracking-and-inventory"></a><a name="onboard"></a>Włącz śledzenie zmian i zapasy

Aby rozpocząć śledzenie zmian, musisz włączyć rozwiązanie śledzenia zmian i zapasów. Istnieje wiele sposobów, aby na pokładzie maszyn, aby zmienić śledzenie i zapasów. Poniżej przedstawiono zalecane i obsługiwane sposoby dołączania rozwiązania.

* [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
* [Od przeglądania wielu maszyn](automation-onboard-solutions-from-browse.md)
* [Z konta Automation](automation-onboard-solutions-from-automation-account.md)
* [Z elementem uruchamianym usługi Azure Automation](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurowanie śledzenia zmian i ekwipunku

Aby dowiedzieć się, jak przyp. [Onboarding Automation solutions](automation-onboard-solutions-from-automation-account.md) Po wprowadzeniu komputera z rozwiązaniem Śledzenie zmian i zapasów można skonfigurować elementy do śledzenia. Po włączeniu nowego pliku lub klucza rejestru do śledzenia, jest włączony zarówno śledzenia zmian i zapasów.

Do śledzenia zmian w plikach zarówno w systemie Windows, jak i linux, używane są skróty MD5 plików. Skróty tezy są następnie używane do wykrywania, czy zmiana została wy uczyniona od czasu ostatniego spisu.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorowanie integralności plików w usłudze Azure Security Center

Usługa Azure Security Center dodała monitorowanie integralności plików (FIM) oparte na śledzeniu zmian platformy Azure. Podczas gdy FIM monitoruje tylko pliki i rejestry, pełne rozwiązanie śledzenia zmian obejmuje również:

- Zmiany w oprogramowaniu
- Usługi systemu Windows
- Demony Linuksa

Jeśli masz już włączoną funkcję FIM i chcesz wypróbować pełne rozwiązanie śledzenia zmian, musisz wykonać następujące kroki. Ustawienia nie są usuwane przez ten proces.

> [!NOTE]
> Włączenie pełnego rozwiązania do śledzenia zmian może spowodować dodatkowe opłaty, aby uzyskać więcej informacji, zobacz [Cennik automatyzacji](https://azure.microsoft.com/pricing/details/automation/).

1. Usuń rozwiązanie do monitorowania, przechodząc do obszaru roboczego i lokalizując je na [liście zainstalowanych rozwiązań monitorujących](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania, a następnie kliknij przycisk Usuń, jak opisano w [Usuń rozwiązanie monitorujące](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Włącz ponownie rozwiązanie, przechodząc do konta automatyzacji i wybierając **polecenie Zmień śledzenie** z menu zasobów w obszarze Zarządzanie **konfiguracją**.
4. Potwierdź szczegóły ustawienia obszaru roboczego i kliknij przycisk **Włącz**.

### <a name="configure-linux-files-to-track"></a>Konfigurowanie plików systemu Linux do śledzenia

Aby skonfigurować śledzenie plików na komputerach z systemem Linux, należy wykonać następujące czynności:

1. Na koncie automatyzacji wybierz pozycję **Śledzenie zmian** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**. Kliknij **pozycję Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie **Śledzenie zmian** wybierz pozycję Pliki **systemu Linux**, a następnie kliknij przycisk **+ Dodaj,** aby dodać nowy plik do śledzenia.
3. W polu **Dodaj plik linuksa do śledzenia zmian**wprowadź informacje dotyczące pliku lub katalogu, aby śledzić i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane.        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony.        |
|Grupa     | Nazwa grupy logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzenia pliku. Na przykład: "/etc/*.conf"       |
|Typ ścieżki     | Typ elementu do śledzenia, możliwe wartości to Plik i Katalog.        |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Użyj polecenia Sudo     | To ustawienie określa, czy podczas sprawdzania elementu jest używane polecenie sudo.         |
|Linki     | To ustawienie określa, w jaki sposób są obsługiwane linki symboliczne podczas przechodzenia między katalogami.<br> **Ignoruj** — ignoruje łącza symboliczne i nie zawiera plików/katalogów, do których się odwołuje.<br>**Wykonaj** - Następuje do łączy symbolicznych podczas rekursji, a także zawiera pliki /katalogi, do których się odwołuje.<br>**Zarządzanie** — podąża za dowiązaniem symbolicznym i umożliwia zmianę zwracanej zawartości.     |
|Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: **True** lub **False**.|

> [!NOTE]
> Opcja linków „Zarządzaj” nie jest zalecana. Pobieranie zawartości plików nie jest obsługiwane.

### <a name="configure-windows-files-to-track"></a>Konfigurowanie plików systemu Windows do śledzenia

Aby skonfigurować śledzenie plików na komputerach z systemem Windows, należy wykonać następujące czynności:

1. Na koncie automatyzacji wybierz pozycję **Śledzenie zmian** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**. Kliknij **pozycję Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie **Śledzenie zmian** wybierz pozycję Pliki **systemu Windows**, a następnie kliknij przycisk **+ Dodaj,** aby dodać nowy plik do śledzenia.
3. W polu **Dodawanie pliku systemu Windows do śledzenia zmian**wprowadź informacje dotyczące pliku do śledzenia i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane.        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony.        |
|Grupa     | Nazwa grupy logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „c:\temp\\\*.txt”<br>Możesz użyć również zmiennych środowiskowych, takich jak „%winDir%\System32\\\*.*”       |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: **True** lub **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Symbol wieloznaczny, rekursja i ustawienia środowiska

Rekursja umożliwia określenie symboli wieloznacznych w celu uproszczenia śledzenia między katalogami i zmiennych środowiskowych, aby umożliwić śledzenie plików w środowiskach z wieloma lub dynamicznymi nazwami dysków. Na poniższej liście znajdują się typowe informacje, które należy znać podczas konfigurowania rekursji:

* Symbole wieloznaczne są wymagane do śledzenia wielu plików
* Jeśli używasz symboli wieloznacznych, można ich używać tylko w ostatnim segmencie ścieżki. (np. `c:\folder\*file*` `/etc/*.conf`
* Jeśli zmienna środowiskowa ma nieprawidłową ścieżkę, sprawdzanie poprawności zakończy się pomyślnie, ale ta ścieżka zakończy się niepowodzeniem po uruchomieniu zapasów.
* Unikaj ścieżek ogólnych, takich jak `c:\*.*` podczas ustawiania ścieżki, ponieważ spowodowałoby to zbyt wiele folderów jest przenoszonych.

## <a name="configure-file-content-tracking"></a>Konfigurowanie śledzenia zawartości pliku

Zawartość można wyświetlić przed i po zmianie pliku za pomocą śledzenia zmian zawartości plików. Jest to dostępne dla plików Windows i Linux, dla każdej zmiany pliku, zawartość pliku jest przechowywana na koncie magazynu i pokazuje plik przed i po zmianie, w linii lub obok siebie. Aby dowiedzieć się więcej, zobacz [Wyświetlanie zawartości śledzonego pliku](change-tracking-file-contents.md).

![wyświetlanie zmian w pliku](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurowanie kluczy rejestru systemu Windows do śledzenia

Aby skonfigurować śledzenie kluczy rejestru na komputerach z systemem Windows, należy wykonać następujące czynności:

1. Na koncie automatyzacji wybierz pozycję **Śledzenie zmian** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**. Kliknij **pozycję Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie **Śledzenie zmian** wybierz pozycję Rejestr **systemu Windows**, a następnie kliknij przycisk **+ Dodaj,** aby dodać nowy klucz rejestru do śledzenia.
3. W stronie **Dodaj rejestr systemu Windows do śledzenia zmian**wprowadź informacje dotyczące klucza do śledzenia i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane.        |
|Nazwa elementu     | Przyjazna nazwa klucza rejestru do śledzenia.        |
|Grupa     | Nazwa grupy logicznego grupowania kluczy rejestru.        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzenia klucza rejestru. Na przykład: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Ograniczenia

Rozwiązanie śledzenia zmian nie obsługuje obecnie następujących elementów:

* Rekursja do śledzenia rejestru systemu Windows
* Sieciowe systemy plików
* Różne metody instalacji nie są śledzone
* Pliki *.exe nie są śledzone dla systemu Windows

Inne ograniczenia:

* **Maksymalny rozmiar pliku** kolumny i wartości są nieużywane w bieżącej implementacji.
* Jeśli zbierzesz więcej niż 2500 plików w 30-minutowym cyklu zbierania, wydajność rozwiązania może ulec pogorszeniu.
* Gdy ruch sieciowy jest wysoki, wyświetlanie rekordów zmian może potrwać do sześciu godzin.
* Jeśli zmodyfikujesz konfigurację podczas zamykania komputera, komputer może zaksięgować zmiany należące do poprzedniej konfiguracji.

## <a name="known-issues"></a>Znane problemy

Rozwiązanie śledzenia zmian występuje obecnie następujące problemy:

* Aktualizacje poprawek nie są zbierane na komputerach rs3 systemu Windows Server 2016 Core.
* Demony Linuksa mogą wykazywać zmieniony stan, nawet jeśli nie nastąpiła żadna zmiana. Wynika to z `SvcRunLevels` sposobu przechwytywania pola.

## <a name="change-tracking-data-collection-details"></a>Zmień szczegóły zbierania danych śledzenia

W poniższej tabeli przedstawiono częstotliwość zbierania danych dla typów zmian. Dla każdego typu migawka danych bieżącego stanu jest również odświeżana co najmniej co 24 godziny:

| **Zmień typ** | **Częstotliwości** |
| --- | --- |
| Rejestr systemu Windows | 50 min. |
| Plik systemu Windows | 30 minut |
| Plik Linuksa | 15 minut |
| Usługi systemu Windows | Od 10 sekund do 30 minut</br> Domyślnie: 30 minut |
| Demony Linuksa | 5 minut |
| Oprogramowanie systemu Windows | 30 minut |
| Oprogramowanie Linux | 5 minut |

W poniższej tabeli przedstawiono limity śledzonych towarów na maszynę do śledzenia zmian.

| **Zasobów** | **Limit**| **Uwagi** |
|---|---|---|
|Plik|500||
|Rejestr|250||
|Oprogramowanie systemu Windows|250|Nie zawiera poprawek oprogramowania|
|Pakiety Linuksa|1250||
|Usługi|250||
|Daemon|250||

Średnie użycie danych usługi Log Analytics dla komputera korzystającego z śledzenia zmian i zasobów reklamowych wynosi około 40 MB miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie w zależności od środowiska. Zaleca się monitorowanie środowiska, aby zobaczyć dokładne użycie, które masz.

### <a name="windows-service-tracking"></a>Śledzenie usług systemu Windows

Domyślna częstotliwość zbierania dla usług systemu Windows wynosi 30 minut. Aby skonfigurować częstotliwość, przejdź do **śledzenia zmian**. W obszarze **Edytowanie ustawień** na karcie **Usługi systemu Windows** znajduje się suwak umożliwiający zmianę częstotliwości zbierania dla usług systemu Windows z 10 sekund na 30 minut. Przenieś pasek suwaka do żądanej częstotliwości i automatycznie go zapisuje.

![Suwak usług systemu Windows](./media/change-tracking/windowservices.png)

Agent śledzi tylko zmiany, co optymalizuje wydajność agenta. Ustawienie wysokiego progu może zostać pominięte, jeśli usługa powróci do stanu pierwotnego. Ustawienie częstotliwości na mniejszą wartość pozwala na wychwyt zmian, które mogą zostać pominięte w przeciwnym razie.

> [!NOTE]
> Agent może śledzić zmiany do 10-sekundowego interwału, ale wyświetlanie danych w portalu zajmuje kilka minut. Zmiany w czasie wyświetlania w portalu są nadal śledzone i rejestrowane.

### <a name="registry-key-change-tracking"></a>Śledzenie zmian klucza rejestru

Celem monitorowania zmian w kluczach rejestru jest określenie punktów rozszerzalności, w których można aktywować kod i złośliwe oprogramowanie innych firm. Na poniższej liście przedstawiono listę wstępnie skonfigurowanych kluczy rejestru. Te klucze są skonfigurowane, ale nie włączone. Aby śledzić te klucze rejestru, należy włączyć każdy z nich.

> [!div class="mx-tdBreakAll"]
> |Klucz rejestru | Przeznaczenie |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje typowe wpisy autostartu, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w toku za pomocą explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoruje skrypty uruchamiane podczas uruchamiania.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoruje skrypty uruchamiane przy zamykaniu systemu.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitoruje klucze, które są ładowane, zanim użytkownik zaloguje się do swojego konta systemu Windows. Klucz jest używany w przypadku programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoruje zmiany w ustawieniach aplikacji.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje typowe wpisy autostartu, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w toku za pomocą explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoruje typowe wpisy autostartu, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w toku za pomocą explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitoruje rejestrację programu obsługi nakładek ikon.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitoruje rejestrację programu obsługi nakładek ikon dla programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitory dla nowych wtyczek obiektów pomocnika przeglądarki dla programu Internet Explorer. Służy do uzyskiwania dostępu do modelu obiektowego dokumentu (DOM) bieżącej strony i sterowania nawigacją.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitory dla nowych wtyczek obiektów pomocnika przeglądarki dla programu Internet Explorer. Służy do uzyskiwania dostępu do modelu obiektowego dokumentu (DOM) bieżącej strony i sterowania nawigacją dla programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitory nowych rozszerzeń programu Internet Explorer, takich jak niestandardowe menu narzędzi i niestandardowe przyciski paska narzędzi.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitory nowych rozszerzeń programu Internet Explorer, takich jak niestandardowe menu narzędzi i niestandardowe przyciski paska narzędzi dla programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje sterowniki 32-bitowe związane z wavemapperem, wave1 i wave2, msacm.imaadpcm, .msadpcm, .msgsm610 i vidc. Podobnie jak w sekcji [sterowniki] w SYSTEMIE. ini.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje sterowniki 32-bitowe związane z wavemapperem, wave1 i wave2, msacm.imaadpcm, .msadpcm, .msgsm610 i vidc dla programów 32-bitowych uruchomionych na komputerach 64-bitowych. Podobnie jak w sekcji [sterowniki] w SYSTEMIE. ini.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoruje listę znanych lub powszechnie używanych bibliotek DLL systemu; system ten uniemożliwia ludziom wykorzystywanie słabych uprawnień katalogu aplikacji przez upuszczenie w wersjach DLL systemu dll dla koni trojańskich.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoruje listę pakietów, które mogą odbierać powiadomienia o zdarzeniach od Winlogon, interaktywnego modelu obsługi logowania dla systemu operacyjnego Windows.

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Następujące adresy są wymagane specjalnie dla śledzenia zmian. Komunikacja z tymi adresami odbywa się za 443 portem.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Korzystanie ze śledzenia zmian

Po włączeniu rozwiązania można wyświetlić podsumowanie zmian dla monitorowanych komputerów, wybierając pozycję **Śledzenie zmian** w obszarze **ZARZĄDZANIE KONFIGURACJĄ** na koncie automatyzacji.

Można wyświetlić zmiany na komputerach, a następnie przejść do szczegółów dla każdego zdarzenia. Listy rozwijane są dostępne w górnej części wykresu, aby ograniczyć wykres i szczegółowe informacje na podstawie typu zmiany i zakresów czasu. Można również kliknąć i przeciągnąć na wykresie, aby wybrać niestandardowy zakres czasu. **Zmiana typu** będzie jedną z następujących wartości **Zdarzenia**, **Demony**, **Pliki**, **Rejestr**, **Oprogramowanie**, Usługi **systemu Windows**. Kategoria pokazuje typ zmiany i może być **dodawany, zmodyfikowany**lub **usunięty**. **Added**

![obraz pulpitu nawigacyjnego śledzenia zmian](./media/change-tracking/change-tracking-dash01.png)

Kliknięcie zmiany lub zdarzenia powoduje wyświetlenie szczegółowych informacji o tej zmianie. Jak widać na przykładzie, typ uruchamiania usługi został zmieniony z Ręcznie na Auto.

![obraz szczegółów śledzenia zmian](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Dzienniki wyszukiwania

Oprócz szczegółów, które są dostarczane w portalu, wyszukiwanie można wykonać w dziennikach. Po otwarciu strony **Śledzenie zmian** kliknij pozycję Usługa **Log Analytics**, spowoduje otwarcie strony **Dzienniki.**

### <a name="sample-queries"></a>Przykładowe zapytania

Poniższa tabela zawiera przykładowe wyszukiwanie dzienników rekordów zmian zebranych przez to rozwiązanie:

|Zapytanie  |Opis  |
|---------|---------|
|ConfigurationData (Dane konfiguracyjne)<br>&#124; gdzie ConfigDataType == "WindowsServices" i SvcStartupType == "Auto"<br>&#124; gdzie SvcState == "Zatrzymane"<br>&#124; podsumuj arg_max(TimeGenerated, *) przez SoftwareName, Komputer         | Pokazuje najnowsze rekordy zapasów dla Usług systemu Windows, które zostały ustawione na Automatyczne, ale zostały zgłoszone jako zatrzymane<br>Wyniki są ograniczone do najnowszego rekordu dla tego SoftwareName i Computer      |
|KonfiguracjaZmija<br>&#124; gdzie ConfigChangeType == "Oprogramowanie" i Zmiana Kategorii == "Usunięto"<br>&#124; zamówienie według timegenerated desc|Pokazuje rekordy zmian dla usuniętego oprogramowania|

## <a name="alert-on-changes"></a>Alert o zmianach

Kluczową możliwością śledzenia zmian i zapasów jest możliwość alertów o stanie konfiguracji i wszelkich zmian w stanie konfiguracji środowiska hybrydowego.

W poniższym przykładzie zrzut ekranu `C:\windows\system32\drivers\etc\hosts` pokazuje, że plik został zmodyfikowany na komputerze. Ten plik jest ważny, ponieważ plik Hosts jest używany przez system Windows do rozpoznawania nazw hostów na adresy IP i ma pierwszeństwo przed nawet DNS, co może spowodować problemy z łącznością lub przekierowanie ruchu do złośliwych lub w inny sposób niebezpiecznych witryn sieci Web.

![Wykres przedstawiający zmianę pliku hosts](./media/change-tracking/changes.png)

Aby przeanalizować tę zmianę dalej, przejdź do wyszukiwania dzienników, klikając pozycję **Usługa Log Analytics**. Po zakończeniu wyszukiwania w dzienniku wyszukaj `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`zmiany zawartości w pliku Hosts za pomocą kwerendy . Ta kwerenda wyszukuje zmiany, które obejmowały zmianę zawartości pliku dla plików, których w pełni kwalifikowana ścieżka zawiera słowo "hosty". Można również poprosić o konkretny plik, zmieniając część ścieżki do `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`jego w pełni kwalifikowanego formularza (np. ).

Po zwrócenie żądanych wyników kliknij przycisk **Nowa reguła alertu** w środowisko wyszukiwania dzienników, aby otworzyć stronę tworzenia alertu. Można również przejść do tego środowiska za pośrednictwem **usługi Azure Monitor** w witrynie Azure portal. W środowisko tworzenia alertów sprawdź nasze zapytanie ponownie i zmodyfikuj logikę alertu. W takim przypadku chcesz alertu, które mają być wyzwalane, jeśli istnieje nawet jedna zmiana wykryte we wszystkich komputerach w środowisku.

![Obraz przedstawiający kwerendę zmian do śledzenia zmian w pliku hosts](./media/change-tracking/change-query.png)

Po ustawieniu logiki warunku należy przypisać grupy akcji do wykonywania akcji w odpowiedzi na wyzwalany alert. W tym przypadku założyłem e-maile do wysłania i bilet ITSM do utworzenia.  Wiele innych przydatnych akcji można również podjąć, takich jak wyzwalanie funkcji platformy Azure, element runbook automatyzacji, element webhook lub aplikacji logiki.

![Obraz konfigurujący grupę akcji w celu ostrzeżenia o zmianie](./media/change-tracking/action-groups.png)

Po ustawieniu wszystkich parametrów i logiki możemy zastosować alert do środowiska.

### <a name="alert-suggestions"></a>Sugestie alertów

Podczas alertów o zmianach w pliku Hosts jest jednym dobrym zastosowaniem alertów dla śledzenia zmian lub danych zapasów, istnieje wiele innych scenariuszy alertów, w tym przypadków zdefiniowanych wraz z ich przykładowych zapytań w sekcji poniżej.

|Zapytanie  |Opis  |
|---------|---------|
|KonfiguracjaZmija <br>&#124; gdzie ConfigChangeType == "Pliki" i FileSystemPath\\zawiera\\"\\c:\\windows system32 sterowniki "|Przydatne do śledzenia zmian w systemowych plikach krytycznych|
|KonfiguracjaZmija <br>&#124; gdzie FieldsChanged zawiera "FileContentChecksum" i\\FileSystemPath ==\\"c:\\windows\\system32\\sterowniki itp.|Przydatne do śledzenia modyfikacji kluczowych plików konfiguracyjnych|
|KonfiguracjaZmija <br>&#124; gdzie ConfigChangeType == "WindowsServices" i SvcName zawiera "w3svc" i SvcState == "Stopped"|Przydatne do śledzenia zmian w krytycznych usługach systemowych|
|KonfiguracjaZmija <br>&#124; gdzie ConfigChangeType == "Demony" i SvcName zawiera "ssh" i SvcState != "Running"|Przydatne do śledzenia zmian w krytycznych usługach systemowych|
|KonfiguracjaZmija <br>&#124; gdzie ConfigChangeType == "Oprogramowanie" i Zmiana Kategorii == "Dodano"|Przydatne w środowiskach, które wymagają zablokowanych konfiguracji oprogramowania|
|ConfigurationData (Dane konfiguracyjne) <br>&#124; gdzie SoftwareName zawiera "Agent monitorowania" i CurrentVersion != "8.0.11081.0"|Przydatne do wyświetlania, które maszyny mają zainstalowaną wersję oprogramowania przestarzałą lub niezgodną. Raportuje ostatni zgłoszony stan konfiguracji, a nie zmiany.|
|KonfiguracjaZmija <br>&#124; gdzie RegistryKey == @"HKEY_LOCAL_MACHINE\\OPROGRAMOWANIE\\\\Microsoft\\Windows\\CurrentVersion QualityCompat"| Przydatne do śledzenia zmian w kluczowych klawiszach antywirusowych|
|KonfiguracjaZmija <br>&#124; gdzie RegistryKey zawiera @"HKEY_LOCAL_MACHINE\\SYSTEM\\\\CurrentControlSet\\Services SharedAccess\\Parametry\\FirewallPolicy"| Przydatne do śledzenia zmian ustawień zapory|

## <a name="next-steps"></a>Następne kroki

Odwiedź samouczek na temat śledzenia zmian, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów ze zmianami we własnym środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [wyszukiwania dzienników w dziennikach usługi Azure Monitor,](../log-analytics/log-analytics-log-searches.md) aby wyświetlić szczegółowe dane śledzenia zmian.
