---
title: Śledź zmiany za pomocą usługi Azure Automation
description: Rozwiązanie Change Tracking pomaga w identyfikacji oprogramowania i zmianami w usłudze Windows, które nastąpiły w Twoim środowisku.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e997d9e9f38fee52cd9fc007fe12cac68e3aa5a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008685"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Śledź zmiany w środowisku przy użyciu rozwiązania Change Tracking

Ten artykuł ułatwia korzystanie rozwiązania Change Tracking można łatwo zidentyfikować zmiany w danym środowisku. Rozwiązanie umożliwia śledzenie zmian do oprogramowania Windows i Linux, Windows i Linux, pliki, klucze rejestru Windows, usługi Windows i demony systemu Linux. Identyfikowanie zmian w konfiguracji ułatwia identyfikowanie problemów operacyjnych.

Zmiany zainstalowanego oprogramowania, usług Windows, plików i rejestru Windows i demonów systemu Linux na monitorowanych serwerach są wysyłane do usługi Azure Monitor w chmurze do przetwarzania. Logika jest stosowana do odebranych danych i usługi w chmurze rejestruje dane. Korzystając z informacji podanych na pulpicie nawigacyjnym śledzenia zmian, łatwo widać zmiany wprowadzone w ramach infrastruktury serwera.

> [!NOTE]
> Usługa Azure Automation Change Tracking śledzi zmiany w przypadku maszyn wirtualnych. Aby śledzić zmiany właściwości usługi Azure Resource Manager, zobacz wykres zasobów Azure [historię zmian](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows

Windows agent oficjalnie obsługuje następujące wersje systemu operacyjnego Windows:

* Windows Server 2008 R2 lub nowszy

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Oficjalnie obsługiwane są poniższe dystrybucje systemu Linux. Jednak agenta systemu Linux może być również uruchomić na inne dystrybucje nie na liście. Jeśli nie określono inaczej, wszystkie wersje pomocnicze są obsługiwane w przypadku wszystkich wersji głównych, na liście.  

### <a name="64-bit"></a>64-bitowa

* CentOS 6 i 7
* Amazon Linux 2017.09
* Oracle Linux 6 i 7
* Red Hat Enterprise Linux Server 6 i 7
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS i 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32-bitowa

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 i 9
* LTS Ubuntu Linux 14.04 i 16.04 LTS

## <a name="onboard"></a>Włączanie śledzenia zmian i spisu

Aby rozpocząć śledzenie zmian, należy włączyć rozwiązanie śledzenia zmian i spisu. Istnieje wiele sposobów dołączania maszyn do śledzenia zmian i spisu. Poniżej są zalecanym i obsługiwane sposoby na dołączenie rozwiązania.

* [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
* [Z wieloma maszynami przeglądania](automation-onboard-solutions-from-browse.md)
* [Na koncie usługi Automation](automation-onboard-solutions-from-automation-account.md)
* [Za pomocą elementu runbook usługi Azure Automation](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurowanie śledzenia zmian i spisu

Aby dowiedzieć się, jak do dołączania komputerów do rozwiązania można znaleźć: [Automatyzacji dołączania rozwiązań](automation-onboard-solutions-from-automation-account.md). Po utworzeniu dołączania maszyny, za pomocą rozwiązania śledzenia zmian i spisu, można skonfigurować elementy do śledzenia. Po włączeniu nowy plik lub klucz rejestru, aby śledzić włączono śledzenie zmian i spisu.

Aby śledzić zmiany w plikach w systemach Windows i Linux, skróty MD5 plików są używane. Te skróty są następnie używane do wykrywania, jeśli wprowadzono zmiany od ostatniej inwentaryzacji.

### <a name="configure-linux-files-to-track"></a>Konfiguruj pliki systemu Linux do śledzenia

Poniższe kroki umożliwiają konfigurowanie śledzenia plików na komputerach z systemem Linux:

1. Na koncie usługi Automation wybierz **śledzenie zmian** w obszarze **zarządzania konfiguracją**. Kliknij przycisk **edytowanie ustawień** (symbol koła zębatego).
2. Na **Change Tracking** wybierz opcję **pliki systemu Linux**, następnie kliknij przycisk **+ Dodaj** można dodać nowego pliku do śledzenia.
3. Na **Dodawanie pliku systemu Linux dla śledzenia zmian**, wprowadź informacje o pliku lub katalogu, aby śledzić, a następnie kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienia została zastosowana.        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony.        |
|Grupa     | Nazwa grupy do logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku. Na przykład: "/etc/*.conf"       |
|Typ ścieżki     | Typ elementu ma być monitorowany; możliwe wartości to plików i katalogów.        |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Użyj polecenia Sudo     | To ustawienie określa, czy podczas sprawdzania elementu jest używane polecenie sudo.         |
|Linki     | To ustawienie określa, w jaki sposób są obsługiwane linki symboliczne podczas przechodzenia między katalogami.<br> **Ignoruj** — ignoruje linki symboliczne i nie uwzględnia plików/katalogów, do których odwołuje się.<br>**Postępuj zgodnie z** — śledzi linki symboliczne podczas rekursji i zawiera również pliki/katalogi, do których odwołuje się.<br>**Zarządzanie** — śledzi linki symboliczne i umożliwia zmienianie zwracanej zawartości.     |
|Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: **True** lub **False**.|

> [!NOTE]
> Opcja linków „Zarządzaj” nie jest zalecana. Pobieranie zawartości plików nie jest obsługiwane.

### <a name="configure-windows-files-to-track"></a>Konfigurowanie plików Windows do śledzenia

Aby skonfigurować plików śledzenia na komputerach z Windows, wykonaj następujące kroki:

1. Na koncie usługi Automation wybierz **śledzenie zmian** w obszarze **zarządzania konfiguracją**. Kliknij przycisk **edytowanie ustawień** (symbol koła zębatego).
2. Na **Change Tracking** wybierz opcję **pliki Windows**, następnie kliknij przycisk **+ Dodaj** można dodać nowego pliku do śledzenia.
3. Na **Add Windows File do śledzenia zmian**, wprowadź informacje o pliku śledzenia, a następnie kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienia została zastosowana.        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony.        |
|Grupa     | Nazwa grupy do logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „c:\temp\\\*.txt”<br>Możesz użyć również zmiennych środowiskowych, takich jak „%winDir%\System32\\\*.*”       |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: **True** lub **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Ustawienia symboli wieloznacznych, rekursji i środowiska

Rekursja można określić symbole wieloznaczne ułatwiają śledzenie katalogów i zmiennych środowiskowych, które umożliwiają śledzenie plików w środowiskach z wieloma lub dynamicznych dysków nazwy. Na poniższej liście przedstawiono typowe informacje, które należy wiedzieć podczas konfigurowania rekursji:

* Symbole wieloznaczne są wymagane do śledzenia wielu plików
* Jeśli przy użyciu symboli wieloznacznych, ich można używać tylko w ostatnim segmencie ścieżki. (takie jak `c:\folder\*file*` lub `/etc/*.conf`)
* Jeśli zmienna środowiskowa zawiera nieprawidłową ścieżkę, weryfikacja zakończy się powodzeniem, ale tej ścieżki nie powiedzie się po uruchomieniu spisu.
* Takie jak uniknąć ogólne ścieżki `c:\*.*` podczas ustawiania ścieżki, ponieważ spowodowałoby to zbyt wiele folderów-są przenoszone.

## <a name="configure-file-content-tracking"></a>Konfigurowanie śledzenia zawartość pliku

Można wyświetlić zawartość, przed i po zmianie pliku przy użyciu pliku zawartości śledzenie zmian. Jest on dostępny w przypadku plików Windows i Linux, dla każdej zmiany w pliku, zawartość pliku jest przechowywany na koncie magazynu i przedstawia plik przed i po nim zmiany, wbudowane lub obok siebie. Aby dowiedzieć się więcej, zobacz [wyświetlić zawartość pliku śledzonych](change-tracking-file-contents.md).

![Wyświetl zmiany w pliku](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurowanie kluczy rejestru Windows do śledzenia

Wykonaj następujące kroki, aby skonfigurować śledzenie kluczy rejestru na komputerach z Windows:

1. Na koncie usługi Automation wybierz **śledzenie zmian** w obszarze **zarządzania konfiguracją**. Kliknij przycisk **edytowanie ustawień** (symbol koła zębatego).
2. Na **Change Tracking** wybierz opcję **rejestru Windows**, następnie kliknij przycisk **+ Dodaj** Aby dodać nowy klucz rejestru, aby śledzić.
3. Na **Dodawanie rejestru Windows do śledzenia zmian**, wprowadź informacje dotyczące klucza do śledzenia, a następnie kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienia została zastosowana.        |
|Nazwa elementu     | Przyjazna nazwa klucza rejestru, które mają być śledzone.        |
|Grupa     | Nazwa grupy do logicznego grupowania kluczy rejestru.        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzania dla klucza rejestru. Na przykład: „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

## <a name="limitations"></a>Ograniczenia

Rozwiązanie Change Tracking nie obsługuje obecnie następujące elementy:

* Rekursji dla rejestru Windows śledzenia
* Systemy plików sieciowych

Pozostałe ograniczenia:

* **Maksymalny rozmiar pliku** kolumny i wartości są używane w bieżącej implementacji.
* Jeśli zbierasz ponad 2500 pliki w cyklu zbierania 30-minutowe wydajność rozwiązania może być obniżona.
* Przy dużym natężeniu ruchu sieciowego, rekordy zmian może potrwać do 6 godzin do wyświetlenia.
* Jeśli możesz zmodyfikować konfigurację, gdy komputer jest wyłączony, komputer może być Opublikuj zmiany, które należały do poprzedniej konfiguracji.

## <a name="known-issues"></a>Znane problemy

Rozwiązanie Change Tracking jest obecnie następujące problemy:

* Poprawki, aktualizacje nie są zbierane na komputerach z systemem Windows Server 2016 Core RS3.
* Demony systemu Linux mogą być wyświetlane zmiany stanu, nawet jeśli nie było zmiany. Jest to ze względu na sposób, w jaki `SvcRunLevels` pola są przechwytywane.

## <a name="change-tracking-data-collection-details"></a>Zmiana szczegółów kolekcji danych śledzenia

W poniższej tabeli przedstawiono częstotliwość zbierania danych dla typów zmian. Dla każdego typu danych migawki bieżącego stanu odświeżane co 24 godziny:

| **Zmień typ** | **Częstotliwość** |
| --- | --- |
| Rejestr Windows | 50 minut |
| Plik Windows | 30 minut |
| Plików w systemie Linux | 15 minut |
| Usługi systemu Windows | 10 sekund na 30 minut</br> Domyślne: 30 minut |
| Demony systemu Linux | 5 minut |
| Oprogramowanie Windows | 30 minut |
| Oprogramowania w systemie Linux | 5 minut |

W poniższej tabeli przedstawiono limity elementu śledzonych dla poszczególnych komputerów do śledzenia zmian.

| **Zasób** | **Limit**| **Uwagi** |
|---|---|---|
|Plik|500||
|Rejestr|250||
|Oprogramowanie Windows|250|Nie zawiera aktualizacji oprogramowania|
|Pakiety systemu Linux|1250||
|Usługi|250||
|Demon|250||

Średnia użycie danych usługi Log Analytics przy użyciu śledzenia zmian i spisu maszyny jest około 40MB miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie w zależności od używanego środowiska. Zaleca się, że monitorować swoje środowisko, aby sprawdzić użycie dokładnie, czy masz.

### <a name="windows-service-tracking"></a>Śledzenie usługi Windows

Domyślna częstotliwość zbierania dla usług Windows to 30 minut. Aby skonfigurować częstotliwość, przejdź do **Change Tracking**. W obszarze **edytowanie ustawień** na **usług Windows** karcie znajduje się suwak, który pozwala zmienić częstotliwość zbierania dla usług Windows z tak szybko, jak 10 sekund na tak długo, jak 30 minut. Przesuń suwak częstotliwości, który ma i go automatycznie zapisuje.

![Suwak usług Windows](./media/automation-change-tracking/windowservices.png)

Agent tylko śledzi zmiany, powoduje to zoptymalizowanie wydajności agenta. Ustawienia wysokiej wartości progowej może brakować zmian, jeśli usługa przywrócony oryginalny stan. Ustawienie częstotliwości mniejszej wartości pozwala przechwytywać zmiany, które mogą zostać pominięci w przeciwnym razie.

> [!NOTE]
> Gdy agent może śledzenie zmian w dół do 10 Drugi interwał, dane nadal zajmuje kilka minut, które mają być wyświetlane w portalu. Zmiany w czasie, aby wyświetlić w portalu nadal są śledzone i rejestrowane.
  
### <a name="registry-key-change-tracking"></a>Śledzenie zmian klucza rejestru

Monitorowanie zmian w kluczach rejestru ma na celu wykrywanie punkty rozszerzeń, gdzie można aktywować kodu innych firm i złośliwym oprogramowaniem. Na poniższej liście przedstawiono listę kluczy rejestru wstępnie skonfigurowane. Te klucze są skonfigurowane, ale nie jest włączona. Aby śledzić te klucze rejestru, należy włączyć każdej z nich.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory wspólnej autostart wpisy, które podłączyć bezpośrednio do Eksploratora Windows i zazwyczaj wykonywania w procesie z Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory skryptów, które są uruchamiane przy uruchamianiu.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory skryptów uruchamiania podczas zamykania systemu.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje klucze, które zostały załadowane przed zalogowaniem się użytkownika do swojego konta Windows. Klucz jest używany dla 32-bitowe programów na komputerach 64-bitowych.    |
> |**HKEY\_lokalnego\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed składników**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory zmian w ustawieniach aplikacji.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory wspólnej autostart wpisy, które podłączyć bezpośrednio do Eksploratora Windows i zazwyczaj wykonywania w procesie z Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory wspólnej autostart wpisy, które podłączyć bezpośrednio do Eksploratora Windows i zazwyczaj wykonywania w procesie z Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla ikony nakładki obsługi rejestracji.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla ikony nakładki obsługi rejestracji dla 32-bitowe programów na komputerach 64-bitowych.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowej wtyczki obiektu pomocnika przeglądarki programu Internet Explorer. Używane do dostępu do modelu DOM (Document Object) bieżącej strony i sterowanie nawigacją.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowej wtyczki obiektu pomocnika przeglądarki programu Internet Explorer. Używane do dostępu do modelu DOM (Document Object) bieżącej strony i sterowanie nawigacją dla 32-bitowe programów na komputerach 64-bitowych.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowych rozszerzeń programu Internet Explorer, takie jak niestandardowe narzędzie menu i przycisków paska narzędzi niestandardowych.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowych rozszerzeń programu Internet Explorer, takie jak niestandardowe narzędzie menu i przycisków na pasku narzędzi niestandardowych dla 32-bitowe programów na komputerach 64-bitowych.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje 32-bitowe sterowniki skojarzone z wavemapper wave1 i wave2, msacm.imaadpcm, .msadpcm, .msgsm610 i vidc. Podobnie jak w sekcji [drivers] w systemie. Pliku INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory 32-bitowe sterowniki skojarzone z wavemapper wave1 i wave2, msacm.imaadpcm, .msadpcm, .msgsm610 i vidc dla 32-bitowe programów na komputerach 64-bitowych. Podobnie jak w sekcji [drivers] w systemie. Pliku INI.|
> |**HKEY\_lokalnego\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje listę znanych lub często używane system biblioteki dll; Ten system uniemożliwia osób wykorzystanie uprawnienia katalogu aplikacji słabe przez usunięcie koń trojański wersji systemowej biblioteki dll.|
> |**HKEY\_lokalnego\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje listę pakietów mogli otrzymywać powiadomienia o zdarzeniach logowania do systemu Windows, model pomocy technicznej interakcyjnego logowania dla systemu operacyjnego Windows.|

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Następujące adresy są wymagane dla śledzenia zmian. Komunikacja z tych adresów odbywa się za pośrednictwem portu 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Korzystanie ze śledzenia zmian

Po włączeniu rozwiązania możesz wyświetlić podsumowanie zmian dla monitorowanych komputerów, wybierając **Change Tracking** w obszarze **zarządzania konfiguracją** na koncie usługi Automation.

Możesz wyświetlić zmiany na komputerach i następnie wejdź do szczegółów dla każdego zdarzenia. Listy rozwijane są dostępne w górnej części wykresu, aby ograniczyć wykresu i szczegółowe informacje, w zależności od zakresów czasu i typ zmiany. Można również kliknąć i przeciągnąć na wykresie, aby wybrać niestandardowy zakres czasu.

![Obraz przedstawiający pulpit nawigacyjny z Change Tracking](./media/automation-change-tracking/change-tracking-dash01.png)

Kliknięcie zmiany lub zdarzenie, wyświetlenie szczegółowych informacji na temat tej zmiany. Jak widać w przykładzie, typ uruchamiania usługi został zmieniony od ręcznie automatycznie.

![Obraz przedstawiający Szczegóły śledzenia zmian](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Przeszukiwanie dzienników

Oprócz szczegółów, które znajdują się w portalu wyszukiwania można wykonać względem dzienników. Za pomocą **Change Tracking** otwarty, kliknij **usługi Log Analytics**, spowoduje to otwarcie **dzienniki** strony.

### <a name="sample-queries"></a>Przykładowe zapytania

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla zmiany rekordów zbieranych przez to rozwiązanie:

|Zapytanie  |Opis  |
|---------|---------|
|ConfigurationData<br>&#124;gdzie ConfigDataType == "WindowsServices" i SvcStartupType == "Auto"<br>&#124;gdzie SvcState == "Zatrzymana"<br>&#124;Podsumowanie arg_max(TimeGenerated, *) według typu SoftwareName, komputer         | Pokazuje ostatnich rekordów spisu dla usług Windows, które zostały ustawione na Auto, ale zostały zgłoszone jako zatrzymania<br>Wyniki są ograniczone do ostatnich rekord dla tego typu SoftwareName i Computer      |
|ConfigurationChange<br>&#124;gdzie ConfigChangeType == "Oprogramowanie" i ChangeCategory == "Usunięte"<br>&#124;kolejność według malejącej TimeGenerated|Pokazuje rekordy zmian oprogramowania usunięto|

## <a name="alert-on-changes"></a>Alert po wystąpieniu zmian

To kluczowa możliwość śledzenia zmian i spisu jest możliwość alert po wystąpieniu stanu konfiguracji i wszelkie zmiany w stan konfiguracji w środowisku hybrydowym.  

W poniższym przykładzie zrzut ekranu pokazuje, że plik `C:\windows\system32\drivers\etc\hosts` został zmodyfikowany na maszynie. Ten plik jest ważne, ponieważ w pliku Hosts jest używany przez Windows, do rozpoznawania nazw hostów IP adresów, a nawet DNS, który może powodować problemy z łącznością lub przekierowywanie ruchu do złośliwych lub w przeciwnym razie niebezpiecznych witryn sieci Web ma pierwszeństwo przed.

![Wykres przedstawiający hosty zmian plików](./media/automation-change-tracking/changes.png)

W celu analizy dalsze tę zmianę, przejdź do wyszukiwania w dziennikach od kliknięcia **usługi Log Analytics**. Raz podczas wyszukiwania dziennika Wyszukaj zmiany zawartości w pliku Hosts z zapytaniem `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. To zapytanie wyszukuje zmiany, które uwzględnione zmiany zawartości pliku dla plików, których w pełni kwalifikowana ścieżka zawiera wyraz "hosts". Możesz również poprosić określonych plików, zmieniając jego w pełni kwalifikowany składnik path (takie jak `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Po zapytanie zwraca zakładanych wyników, kliknij przycisk **Nowa reguła alertu** przycisku środowisko wyszukiwania dziennika, aby otworzyć stronę tworzenia alertu. Można również przejściu do tego środowiska za pomocą **usługi Azure Monitor** w witrynie Azure portal. W środowisku tworzenia alertu Sprawdź ponownie zapytanie i zmodyfikuj alert Logic Apps. W tym przypadku chcesz alert, aby zostać wyzwolony, jeśli nie nastąpiła zmiana nawet wykryte na wszystkich maszynach w środowisku.

![Obraz przedstawiający zapytania zmiany, śledzić zmiany w pliku hosts](./media/automation-change-tracking/change-query.png)

Przypisywanie grup akcji do wykonania akcji w odpowiedzi na alert jest wyzwalany, po ustawieniu logiki warunkowej. W tym przypadku masz skonfigurować wiadomości e-mail do wysłania i bilet ITSM ma zostać utworzony.  Wielu innych czynności przydatne może być również uwzględniony, takie jak wyzwolenie funkcji platformy Azure, automatyzacji elementu runbook, element webhook lub aplikacji logiki.

![Obraz konfigurowania grupy akcji alert w przypadku zmiany](./media/automation-change-tracking/action-groups.png)

Po ustawieniu wszystkich parametrów oraz Logic Apps możemy zastosować alertu do środowiska.

### <a name="alert-suggestions"></a>Sugestie dotyczące alertów

Alerty na temat zmian w pliku Hosts jest jeden z dobrą aplikacją alertów dla danych śledzenia zmian i spisu, istnieje wiele scenariuszy więcej alertów, w tym przypadku zdefiniowany wraz z ich przykładowych zapytań w dalszej części tego artykułu.

|Zapytanie  |Opis  |
|---------|---------|
|ConfigurationChange <br>&#124;gdzie ConfigChangeType == "Pliki" i FileSystemPath zawiera "c:\\windows\\system32\\sterowniki\\"|Przydatne do śledzenia zmian krytycznych plików systemowych|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Używane do śledzenia zmian w plikach konfiguracji klucza|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Przydatne do śledzenia zmian systemu krytycznych usług|
|ConfigurationChange <br>&#124;gdzie ConfigChangeType == "Demonów" i SvcName zawiera "ssh" oraz SvcState! = "Uruchomiona"|Przydatne do śledzenia zmian systemu krytycznych usług|
|ConfigurationChange <br>&#124;gdzie ConfigChangeType == "Oprogramowanie" i ChangeCategory == "Dodaje"|Przydatne w przypadku środowisk wymagających zablokowana konfiguracji oprogramowania|
|ConfigurationData <br>&#124;gdy SoftwareName zawiera "Monitoring Agent" i CurrentVersion! = "8.0.11081.0"|Przydatne przy przeglądaniu, które maszyny mają zainstalowaną wersję oprogramowania nieaktualne lub braku zgodności. Ostatni stan konfiguracji zgłoszone, zmiany nie wysyła raporty.|
|ConfigurationChange <br>&#124;gdzie element RegistryKey == "HKEY_LOCAL_MACHINE\\oprogramowania\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Przydatne do śledzenia zmian do ważnych kluczy oprogramowanie antywirusowe|
|ConfigurationChange <br>&#124;gdy zawiera element RegistryKey "HKEY_LOCAL_MACHINE\\systemu\\CurrentControlSet\\usług\\SharedAccess\\parametry\\FirewallPolicy"| Przydatne do śledzenia zmian w ustawieniach zapory|

## <a name="next-steps"></a>Kolejne kroki

Odwiedź samouczka na śledzenie zmian, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów dotyczących zmian w Twoim środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [przeszukiwania dzienników w dzienniki usługi Azure Monitor](../log-analytics/log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane rozwiązania change tracking.

