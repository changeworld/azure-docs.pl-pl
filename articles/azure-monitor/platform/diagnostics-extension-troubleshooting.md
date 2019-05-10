---
title: Rozwiązywanie problemów z rozszerzenia diagnostyki Azure
description: Rozwiązywanie problemów w przypadku korzystania z diagnostyki platformy Azure w usłudze Azure Virtual Machines, Service Fabric lub usług w chmurze.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: robb
ms.openlocfilehash: 99ac4ffc288773e52183d371ef2c20f6153bc0f3
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471780"
---
# <a name="azure-diagnostics-troubleshooting"></a>Rozwiązywanie problemów z usługi Diagnostyka Azure
W tym artykule opisano informacje dotyczące rozwiązywania problemów, która jest odpowiednia do korzystania z usługi Azure Diagnostics. Aby uzyskać więcej informacji na temat usługi Diagnostyka Azure, zobacz [Omówienie usługi Azure Diagnostics](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logiczne składniki
**Uruchamianie diagnostyki wtyczki programu (DiagnosticsPluginLauncher.exe)**: Uruchamia rozszerzenie diagnostyki platformy Azure. Służy jako wpis punktu procesu.

**Wtyczka diagnostyki (DiagnosticsPlugin.exe)**: Konfiguruje, uruchamia i zarządza czasem istnienia agenta monitorowania. Jest głównego procesu, który jest uruchamiany przez uruchamianie.

**Monitoring Agent (MonAgent\*procesy .exe)**: Monitoruje, zbiera i przesyła dane diagnostyczne.  

## <a name="logartifact-paths"></a>Ścieżki dziennika/artefaktu
Poniżej przedstawiono ścieżki do niektórych ważnych dzienników i artefaktów. Nazywamy te informacje w pozostałej części dokumentu.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | `Path` |
| --- | --- |
| **Plik konfiguracji usługi Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Pliki dziennika** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Magazyn lokalny dla danych diagnostycznych** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Monitorowanie pliku konfiguracji agenta** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pakiet rozszerzenia diagnostyki Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Ścieżka narzędzia kolekcji dziennika** | %SystemDrive%\Packages\GuestAgent\ |
| **Plik dziennika MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Maszyny wirtualne
| Artefakt | `Path` |
| --- | --- |
| **Plik konfiguracji usługi Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Pliki dziennika** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Magazyn lokalny dla danych diagnostycznych** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Monitorowanie pliku konfiguracji agenta** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Plik stanu** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Pakiet rozszerzenia diagnostyki Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Ścieżka narzędzia kolekcji dziennika** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Plik dziennika MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Dane metryk nie pojawia się w witrynie Azure portal
Narzędzie diagnostyczne systemu Azure dostarcza danych metryk, które mogą być wyświetlane w witrynie Azure portal. Jeśli masz problemy z wyświetlaniem danych w portalu, sprawdź WADMetrics\* tabeli konta magazynu diagnostyki platformy Azure, aby sprawdzić, czy odpowiednie rekordy metryki są dostępne.

W tym miejscu **PartitionKey** tabeli jest zasobów zestawu skalowania identyfikator, maszyny wirtualnej lub maszyny wirtualnej. **RowKey** jest nazwa metryki (znany także jako nazwa licznika wydajności).

Jeśli identyfikator zasobu jest nieprawidłowy, sprawdź **diagnostyki** **konfiguracji** > **metryki** > **ResourceId**czy identyfikator zasobu jest ustawione prawidłowo.

Jeśli nie ma danych dla określonej metryki, sprawdź **konfiguracji diagnostyki** > **PerformanceCounter** czy metryki (licznika wydajności) jest dołączony. Domyślnie, firma Microsoft umożliwić następujące liczniki:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- Aplikacje \ASP.NET (__całkowita__) \Requests/Sec
- Aplikacje \ASP.NET (__całkowita__) \Errors liczba bajtów/s
- \ASP.NET\Requests w kolejce
- \ASP.NET\Requests odrzucone
- \Processor(W3wp)\% czas procesora
- Bajty \Private \Process (w3wp)
- \Process(WaIISHost)\% czas procesora
- Bajty \Private \Process (WaIISHost)
- \Process(WaWorkerHost)\% czas procesora
- Bajty \Private \Process (WaWorkerHost)
- \Memory\Page błędy na sekundę
- \.Pamięć NET CLR (_globalnego_)\% czas działania modułu GC
- Bajty zapisu \Disk \LogicalDisk (C:) / s
- \Disk \LogicalDisk (C:) Odczytane bajty/s
- Bajty zapisu \Disk \LogicalDisk (D:) / s
- \Disk \LogicalDisk (D:) Odczytane bajty/s

Jeśli konfiguracja jest ustawiona poprawnie, ale nadal nie widzisz dane metryk, użyj następujących wytycznych, aby ułatwić rozwiązywanie problemów z.


## <a name="azure-diagnostics-is-not-starting"></a>Nie uruchamia usługi Diagnostyka Azure
Aby uzyskać informacji na temat przyczyny niepowodzenia diagnostyki Azure do uruchomienia, zobacz **DiagnosticsPluginLauncher.log** i **DiagnosticsPlugin.log** pliki w lokalizacji plików dziennika, która została dostarczona wcześniej.

Jeśli te dzienniki wskazują `Monitoring Agent not reporting success after launch`, oznacza to, wystąpił błąd podczas uruchamiania MonAgentHost.exe. Sprawdź dzienniki w lokalizacji, która jest wskazywana dla `MonAgentHost log file` w poprzedniej sekcji.

Ostatni wiersz pliki dziennika zawiera kod zakończenia.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Jeśli okaże się **ujemna** kod zakończenia, zapoznaj się [tabeli kodów zakończenia](#azure-diagnostics-plugin-exit-codes) w [odwołuje się do sekcji](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dane diagnostyczne nie jest zalogowany do usługi Azure Storage
Określenia, czy żadne dane jest wyświetlany, i pojawia się niektórych danych.

### <a name="diagnostics-infrastructure-logs"></a>Dzienniki infrastruktury diagnostycznej
Diagnostyka rejestruje wszystkie błędy w dzienniki infrastruktury diagnostycznej. Upewnij się, że włączono [przechwytywania infrastruktury diagnostyki dzienników w konfiguracji](#how-to-check-diagnostics-extension-configuration). Następnie można szybko wyszukać istotne błędy, które pojawiają się w `DiagnosticInfrastructureLogsTable` tabeli w ramach konta skonfigurowanego magazynu.

### <a name="no-data-is-appearing"></a>Żadne dane nie jest wyświetlany.
Najczęstszą przyczyną, że dane zdarzenia nie jest wyświetlana jest, że informacje o koncie magazynu jest niepoprawnie zdefiniowany.

Rozwiązanie: Popraw konfigurację diagnostyki i ponownie zainstaluj diagnostyki.

Jeśli konto magazynu jest poprawnie skonfigurowane, dostęp zdalny do maszyny i upewnij się, że *DiagnosticsPlugin.exe* i *MonAgentCore.exe* są uruchomione. Jeśli nie są uruchomione, wykonaj kroki opisane w [nie uruchamia usługi Azure Diagnostics](#azure-diagnostics-is-not-starting).

Jeśli są uruchomione procesy, przejdź do strony [danych wprowadzenie przechwytywane lokalnie?](#is-data-getting-captured-locally) i postępuj zgodnie z instrukcjami.

Jeśli to nie rozwiąże problemu, spróbuj:

1. Odinstaluj agenta
2. Usuń katalog C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Ponownie zainstaluj agenta


### <a name="part-of-the-data-is-missing"></a>Brakuje części danych
Jeśli otrzymujesz pewne dane, ale nie wszystkich, oznacza to, potok kolekcji/transfer danych jest ustawiona poprawnie. Aby zawęzić ten problem, postępuj zgodnie z podsekcje, w tym miejscu.

#### <a name="is-the-collection-configured"></a>Kolekcja jest skonfigurowana?
Konfiguracja diagnostyki zawiera instrukcje dotyczące określonego typu danych, które mają być zbierane. [Sprawdź konfigurację programu](#how-to-check-diagnostics-extension-configuration) Aby sprawdzić, czy tylko szukasz dane, które zostały skonfigurowane dla kolekcji.

#### <a name="is-the-host-generating-data"></a>Host generuje dane?
- **Liczniki wydajności**: Otwórz monitora wydajności i Sprawdź licznik.

- **Dzienniki śledzenia**:  Zdalny dostęp do maszyny Wirtualnej i Dodaj TextWriterTraceListener do pliku konfiguracyjnego aplikacji.  Zobacz https://msdn.microsoft.com/library/sk36c28t.aspx skonfigurować odbiornik tekstu.  Upewnij się, że `<trace>` element ma `<trace autoflush="true">`.<br />
Jeśli nie widzisz, że generowany w dziennikach śledzenia, zobacz więcej o dziennikach śledzenia Brak.

- **Śladów funkcji ETW**: Dostęp zdalny do maszyny Wirtualnej i instalowanie narzędzia PerfView.  W narzędzia PerfView, uruchom **pliku** > **polecenie użytkownika** > **nasłuchiwania etwprovder1** > **etwprovider2**i tak dalej. **Nasłuchiwania** polecenia jest rozróżniana wielkość liter i nie może być spacji między rozdzielana przecinkami lista dostawców ETW. Jeśli polecenie nie powiedzie się uruchomić, możesz wybrać **dziennika** przycisk w prawym dolnym rogu narzędzia Perfview, aby zobaczyć, jakie podjęto próbę uruchomienia i jakiego wyniku.  Przy założeniu, że dane wejściowe są poprawne, nowe okno podręczne z. W ciągu kilku sekund możesz rozpocząć wyświetlanie śladów funkcji ETW.

- **Dzienniki zdarzeń**: Dostęp zdalny do maszyny Wirtualnej. Otwórz `Event Viewer`, a następnie sprawdź, czy istnieją zdarzenia.

#### <a name="is-data-getting-captured-locally"></a>To wprowadzenie przechwytywane dane lokalnie?
Następnie upewnij się, że dane są wprowadzenie przechwytywane lokalnie.
Dane są przechowywane lokalnie w `*.tsf` pliki w lokalnym magazynie danych diagnostycznych. Różne rodzaje dzienniki Pobierz zebranych w różnych `.tsf` plików. Nazwy są podobne do nazwy tabel w usłudze Azure Storage.

Na przykład `Performance Counters` uzyskać zbierane w `PerformanceCountersTable.tsf`. Dzienniki zdarzeń Pobierz zebranych w `WindowsEventLogsTable.tsf`. Postępuj zgodnie z instrukcjami w [wyodrębniania w lokalnym dzienniku](#local-log-extraction) sekcji, aby otwierać pliki kolekcji lokalnej i sprawdzić, zobacz ich wprowadzenie zebrane na dysku.

Jeśli nie widzisz dzienników wprowadzenie zbierane lokalnie, a już sprawdzeniu, że host jest generowanie danych, należy prawdopodobnie występuje problem z konfiguracją. Uważnie Przejrzyj konfigurację.

Sprawdź również konfiguracji, który został wygenerowany dla MonitoringAgent MaConfig.xml. Sprawdź, czy sekcja, która opisuje źródło odpowiedniego dziennika. Następnie sprawdź, że nie zostaną utracone w tłumaczeniu między konfiguracji diagnostyki i konfiguracji agenta monitorowania.

#### <a name="is-data-getting-transferred"></a>To wprowadzenie przesyłanych danych?
Jeśli upewnieniu się, że dane są wprowadzenie przechwytywane lokalnie, ale nadal nie widać jej w ramach konta magazynu, wykonaj następujące czynności:

- Sprawdź, czy podane konto magazynu poprawną, a nie przywrócono kluczy dla danego konta magazynu. Usług Azure Cloud Services, czasami widzimy, że osoby nie Aktualizuj `useDevelopmentStorage=true`.

- Sprawdź poprawność podanego konta magazynu. Upewnij się, że nie ma ograniczenia sieci, które uniemożliwiają składniki docieranie do publicznych punktów końcowych. Jednym ze sposobów, w tym ma dostęp zdalny do maszyny, a następnie spróbuj zapisać coś, co do tego samego konta magazynu samodzielnie.

- Na koniec można sprawdzić jakie błędy są zgłaszane przez agenta monitorowania. Agent monitorowania zapisuje jej dzienników w `maeventtable.tsf`, który znajduje się w magazynie lokalnym dla danych diagnostycznych. Postępuj zgodnie z instrukcjami w [wyodrębniania w lokalnym dzienniku](#local-log-extraction) sekcji do otwierania tego pliku. Spróbuj ustalić, czy są `errors` wskazujące błędy odczytu do plików lokalnych zapisywanie w magazynie.

### <a name="capturing-and-archiving-logs"></a>Przechwytywanie i archiwizowanie dzienników
Jeśli myślisz o kontaktując się z pomocą techniczną, pierwszą rzeczą, jaką może poprosić Cię jest zbieranie dzienników z Twojego komputera. Aby zaoszczędzić czas, wykonując ten samodzielnie. Uruchom `CollectGuestLogs.exe` narzędzie w ścieżce narzędzia kolekcji dziennika. Generuje plik zip pliku przy użyciu platformy Azure wszystkie istotne rejestruje się w tym samym folderze.

## <a name="diagnostics-data-tables-not-found"></a>Nie można odnaleźć tabel danych diagnostycznych
Tabel w usłudze Azure storage, które zawierają zdarzenia ETW są nazywane przy użyciu następującego kodu:

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

| Zdarzenie | Nazwa tabeli |
| --- | --- |
| Dostawca = "prov1" &lt;zdarzenie o identyfikatorze = "1" /&gt; |WADEvent+MD5(“prov1”)+”1” |
| Dostawca = "prov1" &lt;zdarzenie o identyfikatorze = "2" eventDestination = "dest1" /&gt; |WADdest1 |
| Dostawca = "prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| Dostawca = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt; |WADdest2 |

## <a name="references"></a>Odwołania

### <a name="how-to-check-diagnostics-extension-configuration"></a>Jak sprawdzić konfiguracji rozszerzenia diagnostyki
Najprostszym sposobem, aby sprawdzić konfigurację rozszerzenia jest przejdź do [Eksploratora zasobów Azure](http://resources.azure.com), a następnie przejdź do maszyny wirtualnej lub w chmurze usługi gdzie przez rozszerzenie Diagnostyka Azure (IaaSDiagnostics / PaaDiagnostics) jest.

Alternatywnie pulpitu zdalnego do maszyny i spójrz na plik konfiguracji diagnostyki platformy Azure, opisana w sekcji ścieżki dziennika artefaktów.

W obu przypadkach Wyszukaj **Microsoft.Azure.Diagnostics**, a następnie dla **xmlCfg** lub **WadCfg** pola.

Podczas wyszukiwania na maszynie wirtualnej i **WadCfg** pole jest obecny, oznacza to, konfiguracji jest w formacie JSON. Jeśli **xmlCfg** pole jest obecny, oznacza to, konfiguracji XML i jest kodowany algorytmem Base64. Musisz [zdekodować](https://www.bing.com/search?q=base64+decoder) aby zobaczyć plik XML, który został załadowany przez diagnostykę.

Dla roli usługi w chmurze, w przypadku wybrania konfiguracji z dysku, dane są zakodowane w formacie base64, więc należy [zdekodować](https://www.bing.com/search?q=base64+decoder) aby zobaczyć plik XML, który został załadowany przez diagnostykę.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Kody zakończenia wtyczki w usłudze Azure diagnostyki
Wtyczka zwraca poniższe kody zakończenia:

| Kod zakończenia | Opis |
| --- | --- |
| 0 |Powodzenie. |
| -1 |Błąd ogólny. |
| -2 |Nie można załadować pliku rcf.<p>Ten błąd wewnętrzny tylko powinno nastąpić, jeśli uruchamiania wtyczka agenta gościa jest ręcznego wywołania odbywa się niepoprawnie na maszynie Wirtualnej. |
| -3 |Nie można załadować pliku konfiguracji diagnostyki.<p><p>Rozwiązanie: Spowodowane przez plik konfiguracji nie przeszły sprawdzanie poprawności schematu. To rozwiązanie jest podać plik konfiguracji, który jest zgodny ze schematem. |
| -4 |Inne wystąpienie agenta monitorowania diagnostyki używa już katalog zasobów lokalnych.<p><p>Rozwiązanie: Określ inną wartość dla **LocalResourceDirectory**. |
| -6 |Uruchamianie wtyczka agenta gościa próbowała uruchomić diagnostyki z nieprawidłowy wiersz polecenia.<p><p>Ten błąd wewnętrzny tylko powinno nastąpić, jeśli uruchamiania wtyczka agenta gościa jest ręcznego wywołania odbywa się niepoprawnie na maszynie Wirtualnej. |
| -10 |Wtyczka diagnostyki został zakończony z powodu nieobsługiwanego wyjątku. |
| -11 |Agent gościa nie może utworzyć procesu odpowiedzialnego za uruchamianie i monitorowanie agenta monitorowania.<p><p>Rozwiązanie: Sprawdź, czy wystarczających zasobów systemu są dostępne do uruchomienia nowych procesów.<p> |
| -101 |Nieprawidłowe argumenty podczas wywoływania wtyczki diagnostyki.<p><p>Ten błąd wewnętrzny tylko powinno nastąpić, jeśli uruchamiania wtyczka agenta gościa jest ręcznego wywołania odbywa się niepoprawnie na maszynie Wirtualnej. |
| -102 |Nie można zainicjować sam jest procesu wtyczki.<p><p>Rozwiązanie: Sprawdź, czy wystarczających zasobów systemu są dostępne do uruchomienia nowych procesów. |
| -103 |Nie można zainicjować sam jest procesu wtyczki. W szczególności jest nie można utworzyć obiektu rejestratora.<p><p>Rozwiązanie: Sprawdź, czy wystarczających zasobów systemu są dostępne do uruchomienia nowych procesów. |
| -104 |Nie można załadować pliku rcf dostarczone przez agenta gościa.<p><p>Ten błąd wewnętrzny tylko powinno nastąpić, jeśli uruchamiania wtyczka agenta gościa jest ręcznego wywołania odbywa się niepoprawnie na maszynie Wirtualnej. |
| -105 |Wtyczka diagnostyki nie można otworzyć pliku konfiguracji diagnostyki.<p><p>Ten błąd wewnętrzny tylko powinno nastąpić, jeśli wtyczka diagnostyki jest ręcznego wywołania odbywa się niepoprawnie na maszynie Wirtualnej. |
| -106 |Nie można odczytać pliku konfiguracji diagnostyki.<p><p>Spowodowane przez plik konfiguracji nie przeszły sprawdzanie poprawności schematu. <br><br>Rozwiązanie: Podaj plik konfiguracji, który jest zgodny ze schematem. Aby uzyskać więcej informacji, zobacz [Sprawdzanie konfiguracji rozszerzenia diagnostyki](#how-to-check-diagnostics-extension-configuration). |
| -107 |Zasób katalogu — dostęp próbny agenta monitorowania jest nieprawidłowy.<p><p>Ten błąd wewnętrzny tylko powinno nastąpić, jeśli agent monitorowania jest ręcznego wywołania odbywa się niepoprawnie na maszynie Wirtualnej.</p> |
| -108 |Nie można skonwertować pliku konfiguracji diagnostyki w pliku konfiguracji agenta monitorowania.<p><p>Ten błąd wewnętrzny tylko powinien nastąpić, jeśli wtyczka diagnostyki jest ręcznego wywołania odbywa się przy użyciu pliku nieprawidłowej konfiguracji. |
| -110 |Błąd konfiguracji Diagnostyka ogólnego.<p><p>Ten błąd wewnętrzny tylko powinien nastąpić, jeśli wtyczka diagnostyki jest ręcznego wywołania odbywa się przy użyciu pliku nieprawidłowej konfiguracji. |
| -111 |Nie można uruchomić agenta monitorowania.<p><p>Rozwiązanie: Sprawdź, czy wystarczających zasobów systemu są dostępne. |
| -112 |Błąd ogólny |

### <a name="local-log-extraction"></a>Wyodrębnianie lokalnym dzienniku
Agent monitorowania służy do zbierania dzienników i artefaktów jak `.tsf` plików. `.tsf` Plik nie jest do odczytu, ale możesz przekonwertować go do `.csv` w następujący sposób:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Nowy plik o nazwie `<relevantLogFile>.csv` jest tworzony w tej samej ścieżce co odpowiedni `.tsf` pliku.

>[!NOTE]
> Musisz uruchomić to narzędzie względem pliku .tsf głównego (na przykład PerformanceCountersTable.tsf). Załączonych plików (na przykład PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf i tak dalej) automatycznie są przetwarzane.

### <a name="more-about-missing-trace-logs"></a>Więcej informacji na temat Brak dzienniki śledzenia

>[!NOTE]
> Poniższe informacje dotyczą przede wszystkim do usług Azure Cloud Services, chyba że skonfigurowano DiagnosticsMonitorTraceListener na aplikację, która jest uruchomiona na maszynie Wirtualnej IaaS.

- Upewnij się, że **DiagnosticMonitorTraceListener** jest skonfigurowany w pliku web.config lub app.config.  Jest to konfiguracja domyślna w projekty usługi w chmurze. Jednakże, niektórzy klienci Wypowiedz go poza, co powoduje, że instrukcje śledzenia nie można pobrać przez diagnostykę.

- Jeśli nie wprowadzenie dziennikach z **OnStart** lub **Uruchom** metody, upewnij się, że **DiagnosticMonitorTraceListener** znajduje się w pliku app.config.  Domyślnie jest on w pliku web.config, ale która ma zastosowanie tylko do kodu uruchamianego w ramach w3wp.exe. Dlatego należy go w pliku app.config do przechwytywania śladów, które są uruchomione w WaIISHost.exe.

- Upewnij się, że używasz **Diagnostics.Trace.TraceXXX** zamiast **Diagnostics.Debug.WriteXXX.** Debugować instrukcje są usuwane z kompilacji wydania.

- Upewnij się, skompilowany kod faktycznie zawiera **wierszy Diagnostics.Trace** (umożliwia odblaskowego, ildasm lub użyciu narzędzia do dekompilacji Sprawdź). **Diagnostics.Trace** polecenia są usuwane z skompilowanym plikiem binarnym, chyba że używany jest symbol kompilacji warunkowej śledzenia. Jest to powszechny problem, który występuje, gdy używasz programu msbuild do kompilowania projektu.   

## <a name="known-issues-and-mitigations"></a>Znane problemy i ograniczenia
Oto lista znanych problemów za pomocą znanych środki zaradcze:

**1. .NET 4.5 zależności**

Rozszerzenie diagnostyki platformy Azure Windows ma zależności środowiska uruchomieniowego, w ramach platformy .NET 4.5 lub nowszej. W czasie pisania, wszystkie maszyny, które są udostępniane dla usług Azure Cloud Services, a także wszystkie oficjalne obrazy, które są oparte na maszynach wirtualnych platformy Azure należy zainstalować .NET 4.5 lub nowszy.

Jest nadal możliwe encounter sytuacji, gdy spróbujesz uruchomić rozszerzenia diagnostyki Azure Windows na komputerze, który nie ma .NET 4.5 lub nowszej. Dzieje się tak, po utworzeniu komputera ze starym obrazem lub migawki lub po przywróceniu niestandardowego dysku.

Zazwyczaj jest to manifesty jako kod zakończenia **255** podczas uruchamiania **DiagnosticsPluginLauncher.exe.** Awaria występuje z powodu następującego nieobsługiwanego wyjątku:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Środki zaradcze:** Instalowanie platformy .NET 4.5 lub nowszy na swojej maszynie.

**2. Dane liczników wydajności jest dostępny w magazyn, ale nie są wyświetlane w portalu**

Środowisko pracy w portalu na maszynach wirtualnych przedstawia niektóre liczniki wydajności, domyślnie. Jeśli nie widzisz liczników wydajności, a wiadomo, że danych jest generowanych, ponieważ jest ono dostępne w magazynie, sprawdź następujące informacje:

- Czy dane w magazynie ma nazwy liczników w języku angielskim. Jeśli nazwy liczników nie są w języku angielskim, portalu wykresu metryki nie będą mogli je rozpoznać. **Środki zaradcze**: Zmień język komputera do języka angielskiego, dla konta system. Aby to zrobić, wybierz **Panelu sterowania** > **Region** > **administracyjne** > **ustawień kopii**. Następnie usuń zaznaczenie opcji **Witamy ekranu i system kont** tak, aby języka niestandardowego nie ma zastosowania do konta system.

- Jeśli używane są symbole wieloznaczne (\*) w swojej nazwy licznika wydajności portalu nie będzie możliwość skorelowania skonfigurowany i zebranych liczników, liczniki wydajności wysyłanymi do ujścia usługi Azure Storage. **Środki zaradcze**: Aby upewnić się, można używać symboli wieloznacznych oraz korzystać z portalu, rozwiń węzeł (\*), liczniki wydajności do kierowania [ujścia "Usługi Azure Monitor"](diagnostics-extension-schema.md#diagnostics-extension-111).

