---
title: Śledzenie zmian za pomocą usługi Automatyzacja Azure
description: Rozwiązanie śledzenia zmian pomaga w identyfikacji oprogramowania i zmiany usługi systemu Windows w środowisku.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b110f83274b2b42896bd18fb364c355ecc97a028
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Śledzenie zmian w środowisku z rozwiązaniem do śledzenia zmian

W tym artykule opisano, jak za pomocą rozwiązania śledzenia zmian można łatwo zidentyfikować zmian w środowisku. Rozwiązania śledzi zmiany do oprogramowania systemu Windows i Linux, plików systemu Windows i Linux, kluczy rejestru systemu Windows, usług systemu Windows i demonów systemu Linux. Identyfikowanie zmian konfiguracji może pomóc w określeniu problemy z działaniem.

Zmiany zainstalowanego oprogramowania, usług systemu Windows, plików i rejestru systemu Windows i demonów systemu Linux na monitorowanych serwerach są wysyłane do analizy dzienników usługi w chmurze do przetwarzania. Logika jest stosowany do odebranych danych i usługi w chmurze rejestruje dane. Korzystając z informacji na pulpicie nawigacyjnym śledzenia zmian, można łatwo zobaczyć zmiany wprowadzone w ramach infrastruktury serwera.

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

Aby rozpocząć śledzenie zmian, musisz włączyć śledzenie zmian i spisu rozwiązanie dla konta automatyzacji.

1. W portalu Azure przejdź do swojego konta automatyzacji
1. Wybierz **śledzenie zmian** w obszarze **konfiguracji**.
1. Wybierz istniejący obszar roboczy analizy dzienników lub **Utwórz nowy obszar roboczy** i kliknij przycisk **włączyć**.

Dzięki temu rozwiązania dla Twojego konta automatyzacji. Rozwiązanie może potrwać do 15 minut, aby włączyć. Niebieski transparent powiadamia po włączeniu rozwiązania. Przejdź z powrotem do **śledzenia zmian** strony, aby zarządzać rozwiązania.

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurowanie śledzenia zmian i magazynu

Aby dowiedzieć się, jak dołączyć komputerów do rozwiązania można znaleźć: [rozwiązania Automatyzacja dołączania](automation-onboard-solutions-from-automation-account.md). Po utworzeniu dołączania maszyny, dzięki rozwiązaniu do śledzenia zmian i spisu można skonfigurować elementy do śledzenia. Po włączeniu nowy plik lub klucz rejestru w celu śledzenia jest włączone śledzenie zmian i spisu.

Śledzenie zmian w plikach w systemach Windows i Linux, wartości skrótu MD5 pliki są używane. Skróty te są następnie używane do wykrywania, czy zmiany zostały wprowadzone od ostatniej inwentaryzacji.

### <a name="configure-linux-files-to-track"></a>Konfiguruj pliki Linux do śledzenia

Aby skonfigurować plik śledzenia na komputerach z systemem Linux, wykonaj następujące kroki:

1. Twoje konto usługi Automatyzacja, wybierz **śledzenie zmian** w obszarze **zarządzania konfiguracją**. Kliknij przycisk **edytowanie ustawień** (symbol koło zębate).
2. Na **śledzenia zmian** wybierz pozycję **pliki Linux**, następnie kliknij przycisk **+ Dodaj** można dodać nowego pliku śledzenia.
3. Na **Dodaj plik Linux do śledzenia zmian**, wprowadź informacje dla pliku lub katalogu do śledzenia i kliknij przycisk **zapisać**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienia została zastosowana.        |
|Nazwa elementu     | Przyjazna nazwa pliku, które mają być śledzone.        |
|Grupa     | Nazwa grupy logiczne grupowanie plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzenia pliku. Na przykład: "/etc/*.conf"       |
|Typ ścieżki     | Typ elementu ma być monitorowany, możliwe wartości są plików i katalogów.        |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Użyj polecenia Sudo     | To ustawienie określa, czy podczas sprawdzania elementu jest używane polecenie sudo.         |
|Linki     | To ustawienie określa, w jaki sposób są obsługiwane linki symboliczne podczas przechodzenia między katalogami.<br> **Ignoruj** — ignoruje łącza symbolicznego i nie zawiera plików/katalogów, do których odwołuje się.<br>**Wykonaj** — następuje łącza symbolicznego podczas rekursji, a także plików/katalogów, do których odwołuje się.<br>**Zarządzanie** — wykonał łącza symbolicznego ale umożliwia zmienianie zwrócony zawartości.     |

> [!NOTE]
> Opcja linków „Zarządzaj” nie jest zalecana. Pobieranie zawartości plików nie jest obsługiwane.

### <a name="configure-windows-files-to-track"></a>Konfiguruj pliki systemu Windows do śledzenia

Aby skonfigurować pliki śledzenia na komputerach z systemem Windows, wykonaj następujące kroki:

1. Twoje konto usługi Automatyzacja, wybierz **śledzenie zmian** w obszarze **zarządzania konfiguracją**. Kliknij przycisk **edytowanie ustawień** (symbol koło zębate).
2. Na **śledzenia zmian** wybierz pozycję **plików systemu Windows**, następnie kliknij przycisk **+ Dodaj** można dodać nowego pliku śledzenia.
3. Na **Dodawanie pliku systemu Windows dla śledzenia zmian**, wprowadź informacje dla pliku śledzenia i kliknij przycisk **zapisać**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienia została zastosowana.        |
|Nazwa elementu     | Przyjazna nazwa pliku, które mają być śledzone.        |
|Grupa     | Nazwa grupy logiczne grupowanie plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „c:\temp\mojplik.txt”       |

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurowanie kluczy rejestru systemu Windows do śledzenia

Konfigurowanie śledzenia klucza rejestru na komputerach z systemem Windows, wykonaj następujące kroki:

1. Twoje konto usługi Automatyzacja, wybierz **śledzenie zmian** w obszarze **zarządzania konfiguracją**. Kliknij przycisk **edytowanie ustawień** (symbol koło zębate).
2. Na **śledzenia zmian** wybierz pozycję **rejestru systemu Windows**, następnie kliknij przycisk **+ Dodaj** Aby dodać nowy klucz rejestru do śledzenia.
3. Na **dodać rejestru systemu Windows dla śledzenia zmian**, wprowadź informacje dla klucza do śledzenia i kliknij przycisk **zapisać**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienia została zastosowana.        |
|Nazwa elementu     | Przyjazna nazwa pliku, które mają być śledzone.        |
|Grupa     | Nazwa grupy logiczne grupowanie plików.        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzenia pliku. Na przykład: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User powłoki Folders\Common uruchamiania"      |

## <a name="limitations"></a>Ograniczenia

Rozwiązanie śledzenia zmian nie obsługuje obecnie następujące elementy:

* Śledzenie plików folderów (katalogów) dla systemu Windows
* Rekursja plików śledzenia
* Śledzenie plików symbole wieloznaczne dla systemu Windows
* Rekursja do śledzenia rejestru systemu Windows
* Zmienne ścieżek
* Systemy plików sieciowych
* Zawartość pliku

Inne ograniczenia:

* **Maksymalny rozmiar pliku** kolumny i wartości są używane w bieżącej implementacji.
* W przypadku zebrania plików więcej niż 2500 w cyklu zbierania 30 minut, może wiązać ze spadkiem wydajności rozwiązania.
* Gdy ruch sieciowy jest wysoka, rejestruje zmiany może potrwać do sześciu godzin do wyświetlenia.
* Po zmodyfikowaniu konfiguracji, gdy komputer jest wyłączony, opublikować zmian, które należały do poprzedniej konfiguracji komputera.

## <a name="known-issues"></a>Znane problemy

Rozwiązanie śledzenia zmian jest obecnie następujące problemy:

* Poprawki, aktualizacje nie są zbierane dla systemu Windows 10 twórców Update i Windows Server 2016 Core RS3 maszyn.

## <a name="change-tracking-data-collection-details"></a>Zmień szczegóły kolekcji danych śledzenia

W poniższej tabeli przedstawiono częstotliwość zbierania danych dla typów zmian. Dla każdego typu migawki danych bieżącego stanu są również odświeżane co najmniej co 24 godziny:

| **Zmień typ** | **Częstotliwość** |
| --- | --- |
| Rejestr systemu Windows | 50 minut |
| Plik systemu Windows | 30 minut |
| Plik systemu Linux | 15 minut |
| Usługi systemu Windows | 30 minut |
| Demonów systemu Linux | 5 minut |
| Oprogramowanie Windows | 30 minut |
| Oprogramowania w systemie Linux | 5 minut |

### <a name="registry-key-change-tracking"></a>Śledzenie zmian klucza rejestru

Monitorowanie zmian w kluczach rejestru ma na celu wskazanie punkty rozszerzeń, gdzie można uaktywnić kodu innych firm i złośliwego oprogramowania. Poniższa lista zawiera listę kluczy rejestru wstępnie skonfigurowane. Klucze te są skonfigurowane, ale nie jest włączona. Aby śledzić te klucze rejestru, należy włączyć każdej z nich.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory wspólnej autostart wpisów, które przyłączanie się bezpośrednio do Eksploratora Windows i zwykle uruchamiania w procesie z Explorer.exe.    |
> |**Klucz HKEY\_lokalnego\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory skrypty uruchamiane automatycznie.     |
> |**Klucz HKEY\_lokalnego\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Skrypty monitorów Uruchom przy zamykaniu.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje klucze, które są ładowane przed użytkownik loguje się ich kont systemu Windows. Klucz jest używany dla 32-bitowe programów na komputerach 64-bitowych.    |
> |**Klucz HKEY\_lokalnego\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed składników**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje zmiany w ustawieniach aplikacji.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory wspólnej autostart wpisów, które przyłączanie się bezpośrednio do Eksploratora Windows i zwykle uruchamiania w procesie z Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory wspólnej autostart wpisów, które przyłączanie się bezpośrednio do Eksploratora Windows i zwykle uruchamiania w procesie z Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla ikony nakładki obsługi rejestracji.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla ikony nakładki rejestracji programu obsługi dla 32-bitowe programów na komputerach 64-bitowych.|
> |**Klucz HKEY\_lokalnego\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser obiekty pomocnicze**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory nowych wtyczek obiektu pomocnika przeglądarki Internet Explorer. Używane do dostępu do modelu DOM (Document Object) bieżącej strony i sterowania nawigacji.|
> |**Klucz HKEY\_lokalnego\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser obiekty pomocnicze**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory nowych wtyczek obiektu pomocnika przeglądarki Internet Explorer. Używane do dostępu do modelu DOM (Document Object) bieżącej strony i sterowanie nawigacją dla 32-bitowe programów na komputerach 64-bitowych.|
> |**Klucz HKEY\_lokalnego\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowych rozszerzeń programu Internet Explorer, takie jak narzędzie niestandardowe menu i niestandardowe przyciski paska narzędzi.|
> |**Klucz HKEY\_lokalnego\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory dla nowych rozszerzeń programu Internet Explorer, takie jak narzędzie niestandardowe menu i przycisków paska narzędzi niestandardowych dla 32-bitowe programów na komputerach 64-bitowych.|
> |**Klucz HKEY\_lokalnego\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje 32-bitowe sterowniki skojarzone z wavemapper, wave1 i wave2 msacm.imaadpcm, .msadpcm, .msgsm610 i vidc. Podobnie jak w sekcji [sterowników] w systemie. Pliku INI.|
> |**Klucz HKEY\_lokalnego\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitory 32-bitowe sterowniki są skojarzone z wavemapper, wave1 i wave2 msacm.imaadpcm, .msadpcm, .msgsm610 i vidc dla 32-bitowe programów na komputerach 64-bitowych. Podobnie jak w sekcji [sterowników] w systemie. Pliku INI.|
> |**Klucz HKEY\_lokalnego\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje listę znanych lub często używane systemowej biblioteki dll; Ten system uniemożliwia osób wykorzystania uprawnienia katalogu aplikacji słabe przez usunięcie koń trojański wersji systemowej biblioteki dll.|
> |**Klucz HKEY\_lokalnego\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoruje listy pakietów mogli otrzymywać powiadomienia o zdarzeniach usługi Winlogon, logowania interakcyjnego modelu pomocy technicznej dla systemu operacyjnego Windows.|

## <a name="use-change-tracking"></a>Użyj śledzenie zmian

Po włączeniu rozwiązania, wybierając można wyświetlić podsumowanie zmian monitorowanych komputerów **śledzenia zmian** w obszarze **zarządzania konfiguracją** na Twoim koncie automatyzacji.

Można wyświetlić zmiany na komputerach i następnie przechodzenie do szczegółów dla każdego zdarzenia. Listy rozwijane są dostępne w górnej części na wykresie, aby ograniczyć wykres i szczegółowe informacje, na podstawie zmiany typu i czas zakresów. Można również kliknij i przeciągnij na wykresie, aby wybrać zakres czasu niestandardowych.

![Obraz śledzenia zmian pulpitu nawigacyjnego](./media/automation-change-tracking/change-tracking-dash01.png)

Kliknięcie na zmianę lub wyświetlenie szczegółowych informacji na temat tej zmiany. Jak widać w przykładzie typ uruchamiania usługi został zmieniony z podręcznika Auto.

![Obraz szczegółowe informacje śledzenia zmian](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Dzienniki wyszukiwania

Oprócz szczegółów, które zostały opublikowane w portalu można przed dzienniki wyszukiwania. Z **śledzenia zmian** otwarty, kliknij **analizy dzienników**, spowoduje to otwarcie **wyszukiwania dziennika** strony.

### <a name="sample-queries"></a>Przykładowe zapytania

W poniższej tabeli przedstawiono przykładowy dziennik wyszukiwania Zmień rekordy zebrane przez to rozwiązanie:

|Zapytanie  |Opis  |
|---------|---------|
|ConfigurationData<br>&#124;gdzie ConfigDataType == "WindowsServices" i SvcStartupType == "Auto"<br>&#124;gdzie SvcState == "Zatrzymana"<br>&#124;Podsumuj arg_max(TimeGenerated, *) przez SoftwareName, komputer         | Zawiera najnowsze rekordów spisu dla usług systemu Windows, zostały ustawione na Auto, które zostały zgłoszone jako zatrzymania<br>Wyniki są ograniczone do ostatniego rekordu tego SoftwareName i komputera      |
|Zmianakonfiguracji<br>&#124;gdzie ConfigChangeType == "Oprogramowanie" i ChangeCategory == "Usunięte"<br>&#124;kolejność według TimeGenerated desc|Pokazuje rekordów zmian oprogramowania usunięty|

## <a name="next-steps"></a>Kolejne kroki

Odwiedź samouczka na śledzenia zmian, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów z zmian w środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [Zaloguj wyszukiwania analizy dzienników](../log-analytics/log-analytics-log-searches.md) aby zobaczyć zmianę szczegółowe dane śledzenia.
