---
title: Rozwiązywanie problemów z rozszerzeniem diagnostyki platformy Azure
description: Rozwiązywanie problemów podczas korzystania z diagnostyki platformy Azure w maszynach wirtualnych platformy Azure, sieci szkieletowej usług lub usług w chmurze.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 043369bd6112c4cac36539bbd764393d889439c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274582"
---
# <a name="azure-diagnostics-troubleshooting"></a>Rozwiązywanie problemów za pomocą Diagnostyki Azure
W tym artykule opisano informacje dotyczące rozwiązywania problemów, które są istotne dla korzystania z diagnostyki platformy Azure. Aby uzyskać więcej informacji na temat diagnostyki platformy Azure, zobacz [omówienie diagnostyki platformy Azure](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Składniki logiczne
**Diagnostics Plugin Launcher (DiagnosticsPluginLauncher.exe)**: uruchamia rozszerzenie diagnostyki platformy Azure. Służy jako proces punktu wejścia.

**Diagnostyka Plugin (DiagnosticsPlugin.exe)**: Konfiguruje, uruchamia i zarządza okresem istnienia agenta monitorowania. Jest to główny proces, który jest uruchamiany przez wyrzutnię.

**Agent monitorowania (MonAgent\*.exe processes)**: Monitoruje, zbiera i przesyła dane diagnostyczne.  

## <a name="logartifact-paths"></a>Ścieżki dziennika/artefaktów
Poniżej przedstawiono ścieżki do niektórych ważnych dzienników i artefaktów. Odnosimy się do tych informacji w pozostałej części dokumentu.

### <a name="azure-cloud-services"></a>usług Azure Cloud Services
| Artefakt | Ścieżka |
| --- | --- |
| **Plik konfiguracji diagnostyki platformy Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Pliki dziennika** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<wersja>\ |
| **Lokalny magazyn danych diagnostycznych** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<> funkcji RoleName. DiagnosticStore\WAD0107\Tabele |
| **Plik konfiguracji agenta monitorowania** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<> funkcji RoleName. DiagnosticStore\WAD0107\Konfiguracja\MaConfig.xml |
| **Pakiet rozszerzenia diagnostyki platformy Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<wersja> |
| **Ścieżka narzędzia zbierania dzienników** | %SystemDrive%\Pakiety\Gość\ |
| **Plik dziennika MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<> funkcji RoleName. DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Maszyny wirtualne
| Artefakt | Ścieżka |
| --- | --- |
| **Plik konfiguracji diagnostyki platformy Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics version>\RuntimeSettings C:\Packages\ |
| **Pliki dziennika** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Lokalny magazyn danych diagnostycznych** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Plik konfiguracji agenta monitorowania** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Plik stanu** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<wersja>\Status |
| **Pakiet rozszerzenia diagnostyki platformy Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Ścieżka narzędzia zbierania dzienników** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Plik dziennika MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Dane metryki nie są wyświetlane w witrynie Azure portal
Usługa Azure Diagnostics udostępnia dane metryki, które mogą być wyświetlane w witrynie Azure portal. Jeśli masz problemy z wyświetlaniem danych w\* portalu, sprawdź tabelę WADMetrics na koncie magazynu diagnostyki platformy Azure, aby sprawdzić, czy istnieją odpowiednie rekordy metryk i upewnij się, że [dostawca zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) Microsoft.Insights jest zarejestrowany.

W tym miejscu **PartitionKey** tabeli jest identyfikator zasobu, maszyny wirtualnej lub maszyny wirtualnej zestaw skalowania. **RowKey** to nazwa metryki (znana również jako nazwa licznika wydajności).

Jeśli identyfikator zasobu jest niepoprawny, sprawdź identyfikator**metryki** >  **konfiguracji** > **ResourceId** **diagnostyki,** aby sprawdzić, czy identyfikator zasobu jest ustawiony poprawnie.

Jeśli nie ma żadnych danych dla określonej metryki, sprawdź **diagnostyki configuration** > **performanceCounter,** aby sprawdzić, czy metryka (licznik wydajności) jest uwzględniony. Domyślnie włączamy następujące liczniki:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET applications(__Total__)\Requests/S
- \ASP.NET aplikacje(__Całkowita__suma {\Całkowita liczba błędów/s
- \ASP.NET\Żądania w kolejce
- \ASP.NET\Żądania odrzucone
- \Procesor(w3wp)\% Czas procesora
- \Proces(w3wp)\Bajty prywatne
- \Process(WaIISHost)\% Czas procesora
- \Proces(WaIISHost)\Bajty prywatne
- \Process(WaWorkerHost)\% Czas procesora
- \Proces(WaWorkerHost)\Bajty prywatne
- \Pamięć\Błędy strony/s
- \.Net CLR_Memory(Globalny_)\% Czas w GC
- \LogicalDisk(C:)\Bajty zapisu dysku/s
- \LogicalDisk(C:)\Bajty odczytu dysku/s
- \LogicalDisk(D:)\Bajty zapisu dysku/s
- \LogicalDisk(D:)\Bajty odczytu dysku/s

Jeśli konfiguracja jest ustawiona poprawnie, ale nadal nie widać danych metrycznych, skorzystaj z poniższych wskazówek, aby ułatwić rozwiązywanie problemów.


## <a name="azure-diagnostics-is-not-starting"></a>Diagnostyka platformy Azure nie uruchamia się
Aby uzyskać informacje o tym, dlaczego nie można uruchomić diagnostyki platformy Azure, zobacz **diagnosticsPluginLauncher.log** i **DiagnosticsPlugin.log** plików w lokalizacji plików dziennika, który został dostarczony wcześniej.

Jeśli te dzienniki wskazują `Monitoring Agent not reporting success after launch`, oznacza to, że wystąpił błąd uruchamiania MonAgentHost.exe. Spójrz na dzienniki w lokalizacji, która `MonAgentHost log file` jest wskazana w poprzedniej sekcji.

Ostatni wiersz plików dziennika zawiera kod zakończenia.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Jeśli znajdziesz **ujemny** kod zakończenia, zapoznaj się z [tabelą kodów zakończenia](#azure-diagnostics-plugin-exit-codes) w [sekcji Odwołania](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dane diagnostyczne nie są rejestrowane w usłudze Azure Storage
Określ, czy żadne dane nie są wyświetlane lub niektóre dane są wyświetlane.

### <a name="diagnostics-infrastructure-logs"></a>Dzienniki infrastruktury diagnostycznej
Diagnostyka rejestruje wszystkie błędy w dziennikach infrastruktury diagnostyki. Upewnij się, że włączono [przechwytywanie dzienników infrastruktury diagnostyki w konfiguracji](#how-to-check-diagnostics-extension-configuration). Następnie można szybko wyszukać wszystkie istotne `DiagnosticInfrastructureLogsTable` błędy, które pojawiają się w tabeli na skonfigurowanym koncie magazynu.

### <a name="no-data-is-appearing"></a>Nie są wyświetlane żadne dane
Najczęstszą przyczyną, że dane zdarzenia nie pojawiają się w ogóle jest, że informacje o koncie magazynu jest zdefiniowany niepoprawnie.

Rozwiązanie: Popraw konfigurację diagnostyki i zainstaluj ponownie diagnostykę.

Jeśli konto magazynu jest poprawnie skonfigurowane, dostęp zdalny do komputera i sprawdź, czy *programy DiagnosticsPlugin.exe* i *MonAgentCore.exe* są uruchomione. Jeśli nie są uruchomione, wykonaj kroki opisane w [usłudze Azure Diagnostics nie uruchamia się.](#azure-diagnostics-is-not-starting)

Jeśli procesy są uruchomione, przejdź do [Jest dane są przechwytywane lokalnie?](#is-data-getting-captured-locally) i postępuj zgodnie z instrukcjami tam.

Jeśli to nie rozwiąże problemu, spróbuj:

1. Odinstalowywanie agenta
2. Usuwanie katalogu C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Ponowne instalowanie agenta


### <a name="part-of-the-data-is-missing"></a>Brakuje części danych
Jeśli otrzymujesz niektóre dane, ale nie wszystkie, oznacza to, że potok zbierania/transferu danych jest ustawiony poprawnie. Postępuj zgodnie z podsekcjami tutaj, aby zawęzić problem.

#### <a name="is-the-collection-configured"></a>Czy kolekcja jest skonfigurowana?
Konfiguracja diagnostyki zawiera instrukcje dotyczące określonego typu danych, które mają być zbierane. [Przejrzyj konfigurację,](#how-to-check-diagnostics-extension-configuration) aby sprawdzić, czy szukasz tylko danych skonfigurowanych dla kolekcji.

#### <a name="is-the-host-generating-data"></a>Czy host generuje dane?
- **Liczniki wydajności**: Otwórz perfmon i sprawdź licznik.

- **Dzienniki śledzenia:** Zdalny dostęp do maszyny Wirtualnej i dodaj TextWriterTraceListener do pliku konfiguracyjnego aplikacji.  Zobacz, https://msdn.microsoft.com/library/sk36c28t.aspx aby skonfigurować odbiornik tekstu.  Upewnij się, `<trace>` `<trace autoflush="true">`że element ma .<br />
Jeśli nie widzisz dzienników śledzenia generowanych, zobacz Więcej informacji o dzienników śledzenia brakuje.

- **Ślady ETW: Zdalny**dostęp do maszyny Wirtualnej i zainstalować PerfView.  W programie PerfView uruchom**polecenie** >  **użytkownika pliku** > **Nasłuchij etwprovder1** > **etwprovider2**i tak dalej. W poleceniu **Nasłuchij** rozróżniana jest wielkość liter i nie może istnieć odstępy między listą dostawców ETW oddzielonych przecinkami. Jeśli polecenie nie powiedzie się, można wybrać przycisk **Log** w prawym dolnym rogu narzędzia Perfview, aby zobaczyć, co próbowało uruchomić i jaki był wynik.  Zakładając, że dane wejściowe są poprawne, pojawi się nowe okno. W ciągu kilku sekund zaczniesz widzieć ślady ETW.

- **Dzienniki zdarzeń:** Dostęp zdalny do maszyny Wirtualnej. Otwórz `Event Viewer`, a następnie upewnij się, że zdarzenia istnieją.

#### <a name="is-data-getting-captured-locally"></a>Czy dane są przechwytywane lokalnie?
Następnie upewnij się, że dane są przechwytywane lokalnie.
Dane są lokalnie `*.tsf` przechowywane w plikach w magazynie lokalnym dla danych diagnostycznych. Różne rodzaje dzienników są `.tsf` zbierane w różnych plikach. Nazwy są podobne do nazw tabel w usłudze Azure Storage.

Na przykład `Performance Counters` pobierz `PerformanceCountersTable.tsf`zebrane w . Dzienniki zdarzeń są `WindowsEventLogsTable.tsf`zbierane w pliku . Użyj instrukcji w [sekcji Odciągnięcie dziennika lokalnego,](#local-log-extraction) aby otworzyć pliki kolekcji lokalnej i sprawdzić, czy są one zbierane na dysku.

Jeśli nie widzisz dzienników pobierających zebrane lokalnie i już zweryfikowałeś, że host generuje dane, prawdopodobnie masz problem z konfiguracją. Dokładnie przejrzyj konfigurację.

Przejrzyj również konfigurację, która została wygenerowana dla MonitoringAgent MaConfig.xml. Sprawdź, czy istnieje sekcja opisująca odpowiednie źródło dziennika. Następnie sprawdź, czy nie jest tracona w tłumaczeniu między konfiguracją diagnostyki a konfiguracją agenta monitorowania.

#### <a name="is-data-getting-transferred"></a>Czy dane są przesyłane?
Jeśli zweryfikowano, że dane są przechwytywane lokalnie, ale nadal nie widzisz ich na koncie magazynu, wykonać następujące czynności:

- Sprawdź, czy podano prawidłowe konto magazynu i czy nie przetoczyłeś kluczy dla danego konta magazynu. W przypadku usług w chmurze platformy Azure `useDevelopmentStorage=true`czasami widzimy, że użytkownicy nie aktualizują .

- Sprawdź, czy podane konto magazynu jest poprawne. Upewnij się, że nie masz ograniczeń sieciowych, które uniemożliwiają składnikom dotarcie do punktów końcowych magazynu publicznego. Jednym ze sposobów, aby to zrobić, jest zdalny dostęp do komputera, a następnie spróbuj napisać coś na tym samym koncie magazynu samodzielnie.

- Na koniec można sprawdzić, jakie błędy są zgłaszane przez agenta monitorowania. Agent monitorowania zapisuje swoje `maeventtable.tsf`dzienniki w , który znajduje się w lokalnym magazynie danych diagnostycznych. Postępuj zgodnie z instrukcjami w [sekcji Odciąganie dziennika lokalnego,](#local-log-extraction) aby otworzyć ten plik. Następnie spróbuj ustalić, `errors` czy istnieją, które wskazują błędy odczytu do plików lokalnych zapisu do magazynu.

### <a name="capturing-and-archiving-logs"></a>Przechwytywanie i archiwizowanie dzienników
Jeśli myślisz o skontaktowaniu się z pomocą techniczną, pierwszą rzeczą, o którą mogą cię poprosić, jest zbieranie dzienników z komputera. Możesz zaoszczędzić czas, robiąc to samodzielnie. Uruchom `CollectGuestLogs.exe` narzędzie w ścieżce narzędzia zbieranie dzienników. Generuje plik zip ze wszystkimi odpowiednimi dziennikami platformy Azure w tym samym folderze.

## <a name="diagnostics-data-tables-not-found"></a>Nie znaleziono tabel danych diagnostycznych
Tabele w magazynie platformy Azure, które przechowują zdarzenia ETW są nazwane przy użyciu następującego kodu:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Oto przykład:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Ten kod generuje cztery tabele:

| Wydarzenie | Nazwa tabeli |
| --- | --- |
| provider="prov1" &lt;Event id="1" /&gt; |WADEvent+MD5("prov1")+"1" |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 ( WADdest1 ) |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider="prov2" &lt;DefaultEvents eventSestination="dest2" /&gt; |WADdest2 ( WADdest2 ) |

## <a name="references"></a>Dokumentacja

### <a name="how-to-check-diagnostics-extension-configuration"></a>Jak sprawdzić konfigurację rozszerzenia diagnostyki
Najprostszym sposobem sprawdzenia konfiguracji rozszerzenia jest przejdź do [Usługi Azure Resource Explorer](https://resources.azure.com), a następnie przejdź do maszyny wirtualnej lub usługi w chmurze, gdzie znajduje się rozszerzenie diagnostyki platformy Azure (IaaSDiagnostics / PaaDiagnostics).

Alternatywnie pulpit zdalny do komputera i spójrz na plik konfiguracji diagnostyki platformy Azure, który jest opisany w sekcji Ścieżka artefaktów dziennika.

W obu przypadkach wyszukaj witrynę **Microsoft.Azure.Diagnostics**, a następnie pole **xmlCfg** lub **WadCfg.**

Jeśli szukasz na maszynie wirtualnej i pole **WadCfg** jest obecny, oznacza to, że config jest w formacie JSON. Jeśli pole **xmlCfg** jest obecne, oznacza to, że config jest w formacie XML i jest zakodowany base64. Należy [go zdekodować,](https://www.bing.com/search?q=base64+decoder) aby wyświetlić kod XML, który został załadowany przez diagnostykę.

Dla roli usługi w chmurze, jeśli wybierzesz konfigurację z dysku, dane są zakodowane base64, więc należy [go zdekodować,](https://www.bing.com/search?q=base64+decoder) aby zobaczyć kod XML, który został załadowany przez diagnostykę.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Kody zakończenia wtyczki usługi Azure Diagnostics
Wtyczka zwraca następujące kody zakończenia:

| Kod zakończenia | Opis |
| --- | --- |
| 0 |Powodzenie. |
| -1 |Błąd ogólny. |
| -2 |Nie można załadować pliku rcf.<p>Ten błąd wewnętrzny powinien mieć miejsce tylko wtedy, gdy uruchamianie wtyczki agenta gościa jest ręcznie wywoływane niepoprawnie na maszynie Wirtualnej. |
| -3 |Nie można załadować pliku konfiguracji diagnostyki.<p><p>Rozwiązanie: Spowodowane przez plik konfiguracji nie przekazywania sprawdzania poprawności schematu. Rozwiązaniem jest zapewnienie pliku konfiguracyjnego zgodnego ze schematem. |
| -4 |Innym wystąpieniem programu Diagnostyka agenta monitorowania jest już przy użyciu katalogu zasobów lokalnych.<p><p>Rozwiązanie: Określ inną wartość **localresourceDirectory**. |
| -6 |Uruchamianie wtyczki agenta gościa próbowało uruchomić diagnostykę z nieprawidłowym wierszem polecenia.<p><p>Ten błąd wewnętrzny powinien mieć miejsce tylko wtedy, gdy uruchamianie wtyczki agenta gościa jest ręcznie wywoływane niepoprawnie na maszynie Wirtualnej. |
| -10 |Wtyczka Diagnostyka została zakończona z nieobsługiwał wyjątek. |
| -11 |Agent gościa nie był w stanie utworzyć procesu odpowiedzialnego za uruchamianie i monitorowanie agenta monitorowania.<p><p>Rozwiązanie: Sprawdź, czy dostępne są wystarczające zasoby systemowe do uruchamiania nowych procesów.<p> |
| -101 |Nieprawidłowe argumenty podczas wywoływania wtyczki diagnostyki.<p><p>Ten błąd wewnętrzny powinien mieć miejsce tylko wtedy, gdy uruchamianie wtyczki agenta gościa jest ręcznie wywoływane niepoprawnie na maszynie Wirtualnej. |
| -102 |Proces wtyczki nie może się zainicjować.<p><p>Rozwiązanie: Sprawdź, czy dostępne są wystarczające zasoby systemowe do uruchamiania nowych procesów. |
| -103 |Proces wtyczki nie może się zainicjować. W szczególności nie można utworzyć obiektu rejestratora.<p><p>Rozwiązanie: Sprawdź, czy dostępne są wystarczające zasoby systemowe do uruchamiania nowych procesów. |
| -104 |Nie można załadować pliku rcf dostarczonego przez agenta gościa.<p><p>Ten błąd wewnętrzny powinien mieć miejsce tylko wtedy, gdy uruchamianie wtyczki agenta gościa jest ręcznie wywoływane niepoprawnie na maszynie Wirtualnej. |
| -105 |Wtyczka Diagnostyka nie może otworzyć pliku konfiguracji diagnostyki.<p><p>Ten błąd wewnętrzny powinien mieć miejsce tylko wtedy, gdy wtyczka diagnostyka jest ręcznie wywoływana niepoprawnie na maszynie Wirtualnej. |
| -106 |Nie można odczytać pliku konfiguracji diagnostyki.<p><p>Spowodowane przez plik konfiguracji nie przekazywania sprawdzania poprawności schematu. <br><br>Rozwiązanie: Podaj plik konfiguracyjny zgodny ze schematem. Aby uzyskać więcej informacji, zobacz [Jak sprawdzić konfigurację rozszerzenia diagnostyki](#how-to-check-diagnostics-extension-configuration). |
| -107 |Przekaz katalogu zasobów do agenta monitorowania jest nieprawidłowy.<p><p>Ten błąd wewnętrzny powinien mieć miejsce tylko wtedy, gdy agent monitorowania jest ręcznie wywoływany niepoprawnie na maszynie Wirtualnej.</p> |
| -108 |Nie można przekonwertować pliku konfiguracji diagnostyki na plik konfiguracyjny agenta monitorowania.<p><p>Ten błąd wewnętrzny powinien mieć miejsce tylko wtedy, gdy wtyczka diagnostyka jest wywoływana ręcznie z nieprawidłowym plikiem konfiguracji. |
| -110 |Błąd konfiguracji diagnostyki ogólnej.<p><p>Ten błąd wewnętrzny powinien mieć miejsce tylko wtedy, gdy wtyczka diagnostyka jest wywoływana ręcznie z nieprawidłowym plikiem konfiguracji. |
| -111 |Nie można uruchomić agenta monitorowania.<p><p>Rozwiązanie: Sprawdź, czy dostępne są wystarczające zasoby systemowe. |
| -112 |Błąd ogólny |

### <a name="local-log-extraction"></a>Lokalne wyodrębnianie dziennika
Agent monitorowania zbiera dzienniki i `.tsf` artefakty jako pliki. Plik `.tsf` nie jest czytelny, ale można `.csv` go przekonwertować na następujący plik:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Nowy plik `<relevantLogFile>.csv` o nazwie jest tworzony w `.tsf` tej samej ścieżce co odpowiedni plik.

>[!NOTE]
> Wystarczy uruchomić to narzędzie względem głównego pliku tsf (na przykład PerformanceCountersTable.tsf). Pliki towarzyszące (na przykład PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf itd.) są przetwarzane automatycznie.

### <a name="more-about-missing-trace-logs"></a>Więcej informacji o brakujących dziennikach śledzenia

>[!NOTE]
> Poniższe informacje dotyczą głównie usług w chmurze azure, chyba że skonfigurowano DiagnostykaMonitorTraceListener w aplikacji, która jest uruchomiona na maszynie wirtualnej IaaS.

- Upewnij się, że **DiagnosticMonitorTraceListener** jest skonfigurowany w web.config lub app.config.  Jest to domyślnie skonfigurowane w projektach usług w chmurze. Jednak niektórzy klienci komentują to, co powoduje, że instrukcje śledzenia nie są zbierane przez diagnostykę.

- Jeśli dzienniki nie są zapisywane z **OnStart** lub **Run** metody, upewnij **się, że DiagnosticMonitorTraceListener** znajduje się w app.config.  Domyślnie znajduje się w web.config, ale dotyczy tylko kodu uruchomionego w pliku w3wp.exe. Więc trzeba go w app.config do przechwytywania śladów, które są uruchomione w WaIISHost.exe.

- Upewnij się, że używasz **Diagnostics.Trace.TraceXXX** zamiast **Diagnostics.Debug.WriteXXX.** Instrukcje debugowania są usuwane z kompilacji wydania.

- Upewnij się, że skompilowany kod faktycznie ma **diagnostics.trace wiersze** (użyj reflektora, ildasm lub ILSpy, aby sprawdzić). **Diagnostics.Trace** polecenia są usuwane ze skompilowanego pliku binarnego, chyba że używasz symbol kompilacji warunkowej ŚLEDZENIA. Jest to typowy problem, który występuje, gdy używasz msbuild do tworzenia projektu.   

## <a name="known-issues-and-mitigations"></a>Znane problemy i środki zaradcze
Oto lista znanych problemów ze znanymi czynnikami zaradczymi:

**1. Zależność .NET 4.5**

Rozszerzenie diagnostyki platformy Windows Azure ma zależność środowiska uruchomieniowego w ramach platformy .NET 4.5 lub nowszej. W momencie pisania wszystkie maszyny, które są aprowidzone dla usług Azure Cloud Services, a także wszystkie oficjalne obrazy, które są oparte na maszynach wirtualnych platformy Azure, mają zainstalowany plik .NET 4.5 lub nowszy.

Nadal jest możliwe napotkanie sytuacji, w której można spróbować uruchomić rozszerzenie diagnostyki systemu Windows Azure na komputerze, który nie ma .NET 4.5 lub nowszego. Dzieje się tak podczas tworzenia komputera ze starego obrazu lub migawki lub podczas przynoszenia własnego dysku niestandardowego.

Zazwyczaj jest to manifestowane jako kod zakończenia **255** podczas **uruchamiania pliku DiagnosticsPluginLauncher.exe.** Błąd występuje z powodu następującego nieobsługiwał wyjątek:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Łagodzenie:** Zainstaluj .NET 4.5 lub nowszą na komputerze.

**2. Dane liczników wydajności są dostępne w magazynie, ale nie są wyświetlane w portalu**

Środowisko portalu na maszynach wirtualnych domyślnie pokazuje pewne liczniki wydajności. Jeśli nie widzisz liczników wydajności i wiesz, że dane są generowane, ponieważ są dostępne w magazynie, sprawdź następujące kwestie:

- Czy dane w magazynie ma nazwy liczników w języku angielskim. Jeśli nazwy liczników nie są w języku angielskim, wykres metryki portalu nie będzie w stanie go rozpoznać. **Łagodzenie**: Zmień język maszyny na angielski dla kont systemowych. Aby to zrobić, wybierz pozycję**Ustawienia kopiowania****administracyjnego** > **regionu** >  **panelu sterowania** > . Następnie usuń zaznaczenie **ekranu powitalnego i kont systemowych,** aby język niestandardowy nie został zastosowany do konta systemowego.

- Jeśli używasz symboli\*wieloznacznych ( ) w nazwach liczników wydajności, portal nie będzie w stanie skorelować skonfigurowanego i zebranego licznika, gdy liczniki wydajności są wysyłane do ujścia usługi Azure Storage. **Środki zaradcze:** Aby upewnić się, że można\*użyć symboli wieloznacznych i portalu rozwinąć ( ), trasy liczników wydajności do ujścia usługi Azure Monitor.

