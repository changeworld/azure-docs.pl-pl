---
title: Śledź zmiany za pomocą usługi Azure Automation
description: Rozwiązanie Change Tracking pomaga w identyfikacji oprogramowania i zmianami w usłudze Windows, które nastąpiły w Twoim środowisku.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79d64a5a7eb339c6904fe026209292202632f640
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342015"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Śledź zmiany w środowisku przy użyciu rozwiązania Change Tracking

Ten artykuł ułatwia korzystanie rozwiązania Change Tracking można łatwo zidentyfikować zmiany w danym środowisku. Rozwiązanie umożliwia śledzenie zmian do oprogramowania Windows i Linux, Windows i Linux, pliki, klucze rejestru Windows, usługi Windows i demony systemu Linux. Identyfikowanie zmian w konfiguracji ułatwia identyfikowanie problemów operacyjnych.

Zmiany zainstalowanego oprogramowania, usług Windows, plików i rejestru Windows i demonów systemu Linux na monitorowanych serwerach są wysyłane do usługi Log Analytics w chmurze do przetwarzania. Logika jest stosowana do odebranych danych i usługi w chmurze rejestruje dane. Korzystając z informacji podanych na pulpicie nawigacyjnym śledzenia zmian, łatwo widać zmiany wprowadzone w ramach infrastruktury serwera.

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

Aby rozpocząć śledzenie zmian, należy włączyć rozwiązanie śledzenia zmian i spisu dla konta usługi Automation.

1. W witrynie Azure portal przejdź do konta usługi Automation
1. Wybierz **śledzenie zmian** w obszarze **konfiguracji**.
1. Wybierz istniejący obszar roboczy Log analytics lub **Utwórz nowy obszar roboczy** i kliknij przycisk **Włącz**.

Dzięki temu rozwiązaniu dla konta usługi automation. Rozwiązanie może potrwać do 15 minut, aby włączyć. Niebieski Baner powiadamia użytkownika, gdy rozwiązanie jest włączone. Przejdź z powrotem do **Change Tracking** strony na zarządzanie rozwiązaniem.

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurowanie śledzenia zmian i spisu

Aby dowiedzieć się, jak do dołączania komputerów do rozwiązania można znaleźć: [automatyzacji dołączania rozwiązań](automation-onboard-solutions-from-automation-account.md). Po utworzeniu dołączania maszyny, za pomocą rozwiązania śledzenia zmian i spisu można skonfigurować elementy do śledzenia. Po włączeniu nowy plik lub klucz rejestru, aby śledzić włączono śledzenie zmian i spisu.

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
|Linki     | To ustawienie określa, w jaki sposób są obsługiwane linki symboliczne podczas przechodzenia między katalogami.<br> **Ignoruj** — ignoruje linki symboliczne i nie obejmuje pliki/katalogi, do których odwołuje się.<br>**Postępuj zgodnie z** — śledzi linki symboliczne podczas rekursji i zawiera również pliki/katalogi, do których odwołuje się.<br>**Zarządzanie** — śledzi linki symboliczne i umożliwia zmienianie zwracanej zawartości.     |

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
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „c:\temp\mojplik.txt”       |

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurowanie kluczy rejestru Windows do śledzenia

Wykonaj następujące kroki, aby skonfigurować śledzenie kluczy rejestru na komputerach z Windows:

1. Na koncie usługi Automation wybierz **śledzenie zmian** w obszarze **zarządzania konfiguracją**. Kliknij przycisk **edytowanie ustawień** (symbol koła zębatego).
2. Na **Change Tracking** wybierz opcję **rejestru Windows**, następnie kliknij przycisk **+ Dodaj** Aby dodać nowy klucz rejestru, aby śledzić.
3. Na **Dodawanie rejestru Windows do śledzenia zmian**, wprowadź informacje dotyczące klucza do śledzenia, a następnie kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienia została zastosowana.        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony.        |
|Grupa     | Nazwa grupy do logicznego grupowania plików.        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzania pliku. Na przykład: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Ograniczenia

Rozwiązanie Change Tracking nie obsługuje obecnie następujące elementy:

* Śledzenie plików z folderów (katalogów) dla Windows
* Rekursji dla Windows pliku śledzenia
* Śledzenie plików symbole wieloznaczne dla Windows
* Rekursji dla rejestru Windows śledzenia
* Zmienne ścieżek
* Systemy plików sieciowych
* Zawartość pliku

Pozostałe ograniczenia:

* **Maksymalny rozmiar pliku** kolumny i wartości są używane w bieżącej implementacji.
* Jeśli zbierasz ponad 2500 pliki w cyklu zbierania 30-minutowe wydajność rozwiązania może być obniżona.
* Przy dużym natężeniu ruchu sieciowego, rekordy zmian może potrwać do 6 godzin do wyświetlenia.
* Jeśli możesz zmodyfikować konfigurację, gdy komputer jest wyłączony, komputer może być Opublikuj zmiany, które należały do poprzedniej konfiguracji.

## <a name="known-issues"></a>Znane problemy

Rozwiązanie Change Tracking jest obecnie następujące problemy:

* Poprawki, aktualizacje nie są zbierane dla systemu Windows 10 Creators Update i Windows Server 2016 Core RS3 maszyny.

## <a name="change-tracking-data-collection-details"></a>Zmiana szczegółów kolekcji danych śledzenia

W poniższej tabeli przedstawiono częstotliwość zbierania danych dla typów zmian. Dla każdego typu danych migawki bieżącego stanu odświeżane co 24 godziny:

| **Zmień typ** | **Częstotliwość** |
| --- | --- |
| Rejestr Windows | 50 minut |
| Plik Windows | 30 minut |
| Plików w systemie Linux | 15 minut |
| Usługi systemu Windows | 10 sekund na 30 minut</br> Wartość domyślna: 30 minut |
| Demony systemu Linux | 5 minut |
| Oprogramowanie Windows | 30 minut |
| Oprogramowania w systemie Linux | 5 minut |

### <a name="windows-service-tracking"></a>Śledzenie usługi Windows

Domyślna częstotliwość zbierania dla usług Windows to 30 minut. Aby skonfigurować częstotliwość przejdź do **Change Tracking**. W obszarze **edytowanie ustawień** na **usług Windows** karcie znajduje się suwak, który pozwala zmienić częstotliwość zbierania dla usług Windows z tak szybko, jak 10 sekund na tak długo, jak 30 minut. Przesuń suwak częstotliwości, który ma i go automatycznie zapisuje.

![Suwak usług Windows](./media/automation-change-tracking/windowservices.png)

Agent tylko śledzi zmiany, powoduje to zoptymalizowanie wydajności agenta. Przez ustawienie zbyt wysoki próg zmiany mogą zostać pominięci, jeśli usługa przywrócony oryginalny stan. Ustawienie częstotliwości mniejszej wartości pozwala przechwytywać zmiany, które mogą zostać pominięci w przeciwnym razie.

> [!NOTE]
> Gdy agent może śledzenie zmian w dół do 10 Drugi interwał, dane nadal zajmuje kilka minut, które mają być wyświetlane w portalu. Zmiany w czasie, aby wyświetlić w portalu nadal są śledzone i rejestrowane.
  
### <a name="registry-key-change-tracking"></a>Śledzenie zmian klucza rejestru

Monitorowanie zmian w kluczach rejestru ma na celu wykrywanie punkty rozszerzeń, gdzie można aktywować kodu innych firm i złośliwym oprogramowaniem. Na poniższej liście przedstawiono listę kluczy rejestru wstępnie skonfigurowane. Te klucze są skonfigurowane, ale nie jest włączona. Aby śledzić te klucze rejestru, należy włączyć każdej z nich.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory wspólnej autostart wpisy, które podłączyć bezpośrednio do Eksploratora Windows i zazwyczaj wykonywania w procesie z Explorer.exe.    |
> |**HKEY\_lokalnego\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory skryptów, które są uruchamiane przy uruchamianiu.     |
> |**HKEY\_lokalnego\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
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
> |**HKEY\_lokalnego\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser obiekty pomocnicze**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowej wtyczki obiektu pomocnika przeglądarki programu Internet Explorer. Używane do dostępu do modelu DOM (Document Object) bieżącej strony i sterowanie nawigacją.|
> |**HKEY\_lokalnego\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser obiekty pomocnicze**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowej wtyczki obiektu pomocnika przeglądarki programu Internet Explorer. Używane do dostępu do modelu DOM (Document Object) bieżącej strony i sterowanie nawigacją dla 32-bitowe programów na komputerach 64-bitowych.|
> |**HKEY\_lokalnego\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowych rozszerzeń programu Internet Explorer, takie jak niestandardowe narzędzie menu i przycisków paska narzędzi niestandardowych.|
> |**HKEY\_lokalnego\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowych rozszerzeń programu Internet Explorer, takie jak niestandardowe narzędzie menu i przycisków na pasku narzędzi niestandardowych dla 32-bitowe programów na komputerach 64-bitowych.|
> |**HKEY\_lokalnego\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje 32-bitowe sterowniki skojarzone z wavemapper wave1 i wave2, msacm.imaadpcm, .msadpcm, .msgsm610 i vidc. Podobnie jak w sekcji [drivers] w systemie. Pliku INI.|
> |**HKEY\_lokalnego\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory 32-bitowe sterowniki skojarzone z wavemapper wave1 i wave2, msacm.imaadpcm, .msadpcm, .msgsm610 i vidc dla 32-bitowe programów na komputerach 64-bitowych. Podobnie jak w sekcji [drivers] w systemie. Pliku INI.|
> |**HKEY\_lokalnego\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje listę znanych lub często używane system biblioteki dll; Ten system uniemożliwia osób wykorzystanie uprawnienia katalogu aplikacji słabe przez usunięcie koń trojański wersji systemowej biblioteki dll.|
> |**HKEY\_lokalnego\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje listę pakietów mogli otrzymywać powiadomienia o zdarzeniach logowania do systemu Windows, model pomocy technicznej interakcyjnego logowania dla systemu operacyjnego Windows.|

## <a name="use-change-tracking"></a>Korzystanie ze śledzenia zmian

Po włączeniu rozwiązania możesz wyświetlić podsumowanie zmian dla monitorowanych komputerów, wybierając **Change Tracking** w obszarze **zarządzania konfiguracją** na koncie usługi Automation.

Możesz wyświetlić zmiany na komputerach i następnie wejdź do szczegółów dla każdego zdarzenia. Listy rozwijane są dostępne w górnej części wykresu, aby ograniczyć wykresu i szczegółowe informacje, w zależności od zakresów czasu i typ zmiany. Można również kliknąć i przeciągnąć na wykresie, aby wybrać niestandardowy zakres czasu.

![Obraz przedstawiający pulpit nawigacyjny z Change Tracking](./media/automation-change-tracking/change-tracking-dash01.png)

Kliknięcie zmiany lub zdarzenie, wyświetlenie szczegółowych informacji na temat tej zmiany. Jak widać w przykładzie, typ uruchamiania usługi został zmieniony od ręcznie automatycznie.

![Obraz przedstawiający Szczegóły śledzenia zmian](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Dzienniki wyszukiwania

Oprócz szczegółów, które znajdują się w portalu wyszukiwania można wykonać względem dzienników. Za pomocą **Change Tracking** otwarty, kliknij **usługi Log Analytics**, spowoduje to otwarcie **wyszukiwanie w dzienniku** strony.

### <a name="sample-queries"></a>Przykładowe zapytania

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla zmiany rekordów zbieranych przez to rozwiązanie:

|Zapytanie  |Opis  |
|---------|---------|
|Danych konfiguracji<br>&#124;gdzie ConfigDataType == "WindowsServices" i SvcStartupType == "Auto"<br>&#124;gdzie SvcState == "Zatrzymana"<br>&#124;Podsumowanie arg_max(TimeGenerated, *) według typu SoftwareName, komputer         | Pokazuje ostatnich rekordów spisu dla usług Windows, które zostały ustawione na Auto, ale zostały zgłoszone jako zatrzymania<br>Wyniki są ograniczone do ostatnich rekord dla tego typu SoftwareName i Computer      |
|Zmianakonfiguracji<br>&#124;gdzie ConfigChangeType == "Oprogramowanie" i ChangeCategory == "Usunięte"<br>&#124;kolejność według malejącej TimeGenerated|Pokazuje rekordy zmian oprogramowania usunięto|

## <a name="next-steps"></a>Kolejne kroki

Odwiedź samouczka na śledzenie zmian, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów dotyczących zmian w Twoim środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [przeszukiwanie dzienników w usłudze Log Analytics](../log-analytics/log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane rozwiązania change tracking.
