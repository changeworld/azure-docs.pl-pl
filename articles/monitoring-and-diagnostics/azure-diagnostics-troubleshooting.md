---
title: Rozwiązywanie problemów z rozszerzeniem diagnostyki Azure
description: Rozwiązywanie problemów, gdy za pomocą diagnostyki Azure w maszynach wirtualnych platformy Azure, sieci szkieletowej usług lub usługi w chmurze.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/12/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 8f41605114de296b626418d0a868e3ed778c0640
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263850"
---
# <a name="azure-diagnostics-troubleshooting"></a>Rozwiązywanie problemów Diagnostyka Azure
W tym artykule opisano informacje dotyczące rozwiązywania problemów, który dotyczy za pomocą diagnostyki Azure. Aby uzyskać więcej informacji na temat diagnostycznych platformy Azure, zobacz [Omówienie diagnostyki Azure](azure-diagnostics.md).

## <a name="logical-components"></a>Logiczne składniki
**Uruchamianie diagnostyki wtyczki programu (DiagnosticsPluginLauncher.exe)**: uruchamia rozszerzenia diagnostyki Azure. Służy jako wpis punktu procesu.

**Diagnostyka wtyczki (DiagnosticsPlugin.exe)**: konfiguruje, uruchamia i zarządza czasem istnienia agenta monitorowania. To główny proces, który jest uruchamiana przez okno dialogowe Uruchamianie.

**Monitoring Agent (MonAgent\*procesy .exe)**: monitorów, zbiera i transferuje dane diagnostyczne.  

## <a name="logartifact-paths"></a>Ścieżki dziennika/artefaktów
Poniżej przedstawiono ścieżki do niektórych dzienników ważne i artefaktów. Firma Microsoft odwoływać się do tych informacji w dalszej części dokumentu.

### <a name="azure-cloud-services"></a>usług Azure Cloud Services
| Artefaktów | Ścieżka |
| --- | --- |
| **Plik konfiguracji diagnostyki Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Pliki dziennika** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Magazyn lokalny dla danych diagnostycznych** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Plik konfiguracji agenta monitorowania** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pakiet rozszerzeń Azure diagnostyki** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Ścieżka narzędzia kolekcji dziennika** | %SystemDrive%\Packages\GuestAgent\ |
| **Plik dziennika MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Maszyny wirtualne
| Artefaktów | Ścieżka |
| --- | --- |
| **Plik konfiguracji diagnostyki Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Pliki dziennika** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Magazyn lokalny dla danych diagnostycznych** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Plik konfiguracji agenta monitorowania** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Plik stanu** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Pakiet rozszerzeń Azure diagnostyki** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Ścieżka narzędzia kolekcji dziennika** | C:\WindowsAzure\Packages |
| **Plik dziennika MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Dane nie są wyświetlane w portalu Azure
Diagnostyka Azure udostępnia metryki dane, które mogą być wyświetlane w portalu Azure. Jeśli masz problemy wyświetlać dane w portalu, sprawdź, czy WADMetrics\* tabeli diagnostyki Azure konta magazynu, aby sprawdzić, czy są odpowiednie rekordy metryki. 

W tym miejscu **PartitionKey** tabeli jest zasobów zestaw skali Identyfikatora, maszyny wirtualnej lub maszyny wirtualnej. **RowKey** jest nazwa metryki (znany także jako nazwa licznika wydajności).

Sprawdź, czy identyfikator zasobu jest nieprawidłowy, **diagnostyki** **konfiguracji** > **metryki** > **ResourceId**czy identyfikator zasobu jest ustawione prawidłowo.

Sprawdź, czy nie ma żadnych danych dla określonej metryki, **konfiguracji diagnostyki** > **PerformanceCounter** czy metryka (licznika wydajności) jest dołączony. Domyślnie włączyć następujące liczniki:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- Aplikacje \ASP.NET (__całkowita__) \Requests/Sec
- Aplikacje \ASP.NET (__całkowita__) \Errors/s
- \ASP.NET\Requests umieszczonych w kolejce.
- \ASP.NET\Requests odrzucone
- \Processor(W3wp)\% czas procesora
- Bajty \Private \Process (w3wp)
- \Process(WaIISHost)\% czas procesora
- Bajty \Private \Process (WaIISHost)
- \Process(WaWorkerHost)\% czas procesora
- Bajty \Private \Process (WaWorkerHost)
- \Memory\Page błędów na sekundę
- \.NET CLR pamięci (_globalne_)\% czasu odzyskiwania pamięci
- Bajty zapisu \Disk \LogicalDisk (C:) / s
- \Disk \LogicalDisk (C:) do odczytu w bajtach na sekundę
- Bajty zapisu \Disk \LogicalDisk (D:) / s
- \Disk \LogicalDisk (D:) do odczytu w bajtach na sekundę

Jeśli konfiguracja została poprawnie ustawiona, ale nadal nie widzisz dane metryk, użyj poniższe wskazówki ułatwiają rozwiązywanie.


## <a name="azure-diagnostics-isnt-starting"></a>Nie jest uruchomienie diagnostyki Azure
Informacje, dlaczego diagnostyki Azure nie powiodło się, zobacz **DiagnosticsPluginLauncher.log** i **DiagnosticsPlugin.log** pliki w lokalizacji plików dziennika, podany wcześniej. 

Jeśli te dzienniki wskazują `Monitoring Agent not reporting success after launch`, oznacza to, wystąpił błąd podczas uruchamiania MonAgentHost.exe. Sprawdź dzienniki w lokalizacji określonej dla `MonAgentHost log file` w poprzedniej sekcji.

Ostatni wiersz w pliku dziennika zawiera kod wyjścia.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Jeśli okaże się **ujemna** kod zakończenia, zapoznaj się [tabeli Kod zakończenia](#azure-diagnostics-plugin-exit-codes) w [odwołuje się do sekcji](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dane diagnostyczne nie jest zalogowany do usługi Azure Storage
Ustal, czy jest wyświetlane żadne dane, czy pojawia się niektórych danych.

### <a name="diagnostics-infrastructure-logs"></a>Dzienniki infrastruktury diagnostyki
Diagnostyka rejestruje wszystkie błędy w dziennikach diagnostycznych infrastruktury. Upewnij się, że włączono [przechwytywania infrastruktury diagnostyki dzienniki w konfiguracji](#how-to-check-diagnostics-extension-configuration). Następnie można szybko odnaleźć odpowiednie błędów, które są wyświetlane w `DiagnosticInfrastructureLogsTable` tabeli na koncie skonfigurowany magazyn.

### <a name="no-data-is-appearing"></a>Pojawia się bez danych
Najczęstszym powodem, dla którego danych zdarzeń nie jest wyświetlana jest, że informacje o koncie magazynu jest nieprawidłowo zdefiniowany.

Rozwiązanie: Poprawić konfigurację diagnostyki i ponowne zainstalowanie diagnostyki.

Jeśli konto magazynu jest poprawnie skonfigurowana, zdalny dostęp do komputera i sprawdź, czy z DiagnosticsPlugin.exe i MonAgentCore.exe. Jeśli nie są one uruchomione, postępuj zgodnie z instrukcjami [nie uruchamia się diagnostyki Azure](#azure-diagnostics-is-not-starting). 

Jeśli są uruchomione procesy, przejdź do [dane pierwsze przechwycone lokalnie?](#is-data-getting-captured-locally) i postępuj zgodnie z instrukcjami.

### <a name="part-of-the-data-is-missing"></a>Brakuje części danych
W przypadku uzyskiwania niektórych danych, ale nie wszystkie, oznacza to, że potoku kolekcji/transfer danych jest ustawiony poprawnie. Podpunkty w tym miejscu należy wykonać, aby zawęzić problem.

#### <a name="is-the-collection-configured"></a>Skonfigurowano kolekcji? 
Konfiguracja diagnostyki zawiera instrukcje dotyczące określonego typu danych, które mają być zbierane. [Przejrzyj konfigurację](#how-to-check-diagnostics-extension-configuration) Aby sprawdzić, czy tylko szukasz dane, które zostały skonfigurowane dla kolekcji.

#### <a name="is-the-host-generating-data"></a>Host generuje dane?
- **Liczniki wydajności**: Otwórz monitora wydajności i Sprawdź licznik.

- **Dzienniki śledzenia**: zdalny dostęp do maszyny Wirtualnej, a następnie dodaj TextWriterTraceListener do pliku konfiguracji aplikacji.  Zobacz http://msdn.microsoft.com/library/sk36c28t.aspx do skonfiguruj odbiornik tekstu.  Upewnij się, że `<trace>` element ma `<trace autoflush="true">`.<br />
Jeśli nie widzisz generowaną dzienniki śledzenia, zobacz [więcej informacji na temat dzienników śledzenia Brak](#more-about-trace-logs-missing).

- **Ślady ETW**: dostępu zdalnego do maszyny Wirtualnej i zainstaluj narzędzia PerfView.  W narzędzia PerfView, uruchom **pliku** > **polecenia User** > **nasłuchiwania etwprovder1** > **etwprovider2**i tak dalej. **Nasłuchiwania** polecenia jest rozróżniana wielkość liter i nie może być spacji między rozdzielana przecinkami lista dostawców ETW. Jeśli polecenie nie powiedzie się uruchomić, można wybrać **dziennika** przycisk w prawym dolnym rogu narzędzia narzędzia Perfview co próbował uruchomić i jakie wynik był.  Zakładając, że dane wejściowe są poprawne, nowe okno podręczne z. W ciągu kilku sekund rozpoczęciem wyświetlanie śladów ETW.

- **Dzienniki zdarzeń**: dostępu zdalnego do maszyny Wirtualnej. Otwórz `Event Viewer`, a następnie upewnij się, że istnieje zdarzenia.

#### <a name="is-data-getting-captured-locally"></a>Danych jest pierwsze przechwycone lokalnie?
Następnie upewnij się, że pierwsze lokalnie przechwyceniu danych.
Dane lokalne są przechowywane w `*.tsf` plików [lokalnego magazynu dla danych diagnostycznych](#log-artifacts-path). Różne rodzaje dzienniki uzyskać zebrane w różnych `.tsf` plików. Nazwy są podobne do nazwy tabeli w usłudze Azure Storage. 

Na przykład `Performance Counters` uzyskać zbierane w `PerformanceCountersTable.tsf`. Dzienniki zdarzeń uzyskać zebrane w `WindowsEventLogsTable.tsf`. Postępuj zgodnie z instrukcjami w [wyodrębniania lokalnym dzienniku](#local-log-extraction) sekcji, aby otworzyć pliki kolekcji lokalnej i sprawdź, zobacz ich pobierania zebrane na dysku.

Jeśli nie widzisz dzienników pobierania zebranych lokalnie, a już sprawdzeniu, że host jest generowania danych, prawdopodobnie jest problem z konfiguracją. Należy dokładnie przejrzeć konfigurację. 

Sprawdź również konfiguracji, który został wygenerowany dla MonitoringAgent [MaConfig.xml](#log-artifacts-path). Sprawdź, czy sekcja, która opisuje źródło dziennika odpowiednie. Następnie zweryfikuj, że nie zostaną utracone w tłumaczeniu między konfiguracji diagnostyki i konfiguracji agenta monitorowania.

#### <a name="is-data-getting-transferred"></a>Jest wprowadzenie przesyłanych danych?
Jeśli po sprawdzeniu danych jest pierwsze przechwycone lokalnie, ale nadal nie widać na koncie magazynu, należy wykonać następujące czynności: 

- Sprawdź podane konto magazynu poprawne, a nie przywrócono kluczy dla konta magazynu podanego. Dla usług Azure Cloud Services czasami widzimy, że osoby nie Aktualizuj `useDevelopmentStorage=true`.

- Sprawdź, czy konta magazynu podana jest poprawna. Upewnij się, że nie ma ograniczenia sieci, które uniemożliwiają dotarcie magazynu publiczne punkty końcowe składników. Jeden sposób jest dostępu zdalnego do komputera, a następnie spróbuj zapisać element do tego samego konta magazynu samodzielnie.

- Na koniec można przyjrzeć się błędów, jakie są zgłaszane przez agenta monitorowania. Agent monitorowania zapisuje dzienniki jej `maeventtable.tsf`, który znajduje się w [lokalnego magazynu dla danych diagnostycznych](#log-artifacts-path). Postępuj zgodnie z instrukcjami [wyodrębniania lokalnym dzienniku](#local-log-extraction) sekcji do otwarcia tego pliku. Spróbuj określić, czy istnieją `errors` wskazujących niepowodzenia odczytu do plików lokalnych zapisywania w magazynie.

### <a name="capturing-and-archiving-logs"></a>Przechwytywanie i archiwizowanie dzienników
Jeśli myślisz o skontaktowaniu się z pomocą techniczną, w pierwszej kolejności pracownik może poprosić o jest zbieranie dzienników z komputera. Wykonując tego użytkownika, można zaoszczędzić czas. Uruchom `CollectGuestLogs.exe` narzędzia na [ścieżka narzędzie kolekcji dziennika](#log-artifacts-path). Generuje zip pliku z platformy Azure, wszelkie istotne dzienników w tym samym folderze.

## <a name="diagnostics-data-tables-not-found"></a>Nie można odnaleźć tabel danych diagnostycznych
Tabel w magazynie Azure, które zawierają zdarzenia ETW są nazywane przy użyciu następującego kodu:

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
Ten kod generuje czterech tabel:

| Wydarzenie | Nazwa tabeli |
| --- | --- |
| Dostawca = "prov1" &lt;identyfikator zdarzenia = "1" /&gt; |WADEvent+MD5(“prov1”)+”1” |
| Dostawca = "prov1" &lt;identyfikator zdarzenia = "2" eventDestination = "dest1" /&gt; |WADdest1 |
| Dostawca = "prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| Dostawca = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt; |WADdest2 |

## <a name="references"></a>Dokumentacja

### <a name="how-to-check-diagnostics-extension-configuration"></a>Sprawdzanie konfiguracji rozszerzenia diagnostyki
Najprostszym sposobem, aby sprawdzić konfigurację rozszerzenia jest przejdź do [Eksploratora zasobów Azure](http://resources.azure.com), a następnie przejdź do maszyny wirtualnej lub w chmurze usługi where rozszerzenie Azure Diagnostics (IaaSDiagnostics / PaaDiagnostics) jest.

Alternatywnie pulpitu zdalnego do maszyny i znajduje się w pliku konfiguracji diagnostyki Azure, który jest opisany w [dziennika artefakty pierwszą sekcją ścieżki](#log-artifacts-path).

W obu przypadkach Wyszukaj **Microsoft.Azure.Diagnostics**, a następnie **xmlCfg** lub **WadCfg** pola. 

Podczas wyszukiwania na maszynie wirtualnej i **WadCfg** pola, oznacza to konfiguracji jest w formacie JSON. Jeśli **xmlCfg** pola, oznacza to konfiguracji XML i jest zakodowany w formacie base64. Musisz [zdekodować](http://www.bing.com/search?q=base64+decoder) aby zobaczyć plik XML, który został załadowany przez diagnostyki.

Dla roli usługi w chmurze, w przypadku wybrania konfiguracji z dysku, dane są zakodowane w formacie base64, więc należy [zdekodować](http://www.bing.com/search?q=base64+decoder) aby zobaczyć plik XML, który został załadowany przez diagnostyki.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Kody zakończenia wtyczki w usłudze Azure diagnostyki
Wtyczka zwraca następujący kod zakończenia:

| Kod zakończenia | Opis |
| --- | --- |
| 0 |Powodzenie. |
| -1 |Błąd rodzajowy. |
| -2 |Nie można załadować pliku rcf.<p>Ten błąd wewnętrzny tylko powinno się zdarzyć, jeśli uruchamianie wtyczki agenta gościa ręcznie jest wywoływany niepoprawnie na maszynie Wirtualnej. |
| -3 |Nie można załadować pliku konfiguracji diagnostyki.<p><p>Rozwiązanie: Spowodowane nie przeszły sprawdzanie poprawności schematu pliku konfiguracji. Rozwiązanie jest zapewnienie pliku konfiguracji, który jest zgodny ze schematem. |
| -4 |Inne wystąpienie agenta monitorowania diagnostyki już używa zasobu lokalnego katalogu.<p><p>Rozwiązanie: Określ inną wartość dla **LocalResourceDirectory**. |
| -6 |Uruchamianie wtyczki agenta gościa próba uruchomienie diagnostyki z nieprawidłowy wiersz polecenia.<p><p>Ten błąd wewnętrzny tylko powinno się zdarzyć, jeśli uruchamianie wtyczki agenta gościa ręcznie jest wywoływany niepoprawnie na maszynie Wirtualnej. |
| -10 |Wtyczki diagnostyki został zakończony z powodu nieobsługiwanego wyjątku. |
| -11 |Agent gościa nie może utworzyć procesu odpowiedzialnego za uruchamianie i monitorowanie agenta monitorowania.<p><p>Rozwiązanie: Sprawdź, czy wystarczające zasoby systemowe są dostępne na uruchomienie nowych procesów.<p> |
| -101 |Nieprawidłowe argumenty podczas wywoływania metody wtyczki diagnostyki.<p><p>Ten błąd wewnętrzny tylko powinno się zdarzyć, jeśli uruchamianie wtyczki agenta gościa ręcznie jest wywoływany niepoprawnie na maszynie Wirtualnej. |
| -102 |Proces wtyczki nie jest w stanie się zainicjować.<p><p>Rozwiązanie: Sprawdź, czy wystarczające zasoby systemowe są dostępne na uruchomienie nowych procesów. |
| -103 |Proces wtyczki nie jest w stanie się zainicjować. W szczególności jest nie można utworzyć obiektu rejestratora.<p><p>Rozwiązanie: Sprawdź, czy wystarczające zasoby systemowe są dostępne na uruchomienie nowych procesów. |
| -104 |Nie można załadować pliku rcf dostarczonej przez agenta gościa.<p><p>Ten błąd wewnętrzny tylko powinno się zdarzyć, jeśli uruchamianie wtyczki agenta gościa ręcznie jest wywoływany niepoprawnie na maszynie Wirtualnej. |
| -105 |Dodatek diagnostyki nie można otworzyć pliku konfiguracji diagnostyki.<p><p>Ten błąd wewnętrzny tylko powinno się zdarzyć, jeśli wtyczki diagnostyki ręcznie jest niepoprawnie wywołana na maszynie Wirtualnej. |
| -106 |Nie można odczytać pliku konfiguracji diagnostyki.<p><p>Przyczyną nie przeszły sprawdzanie poprawności schematu pliku konfiguracji. <br><br>Rozwiązanie: Podaj plik konfiguracji, który jest zgodny ze schematem. Aby uzyskać więcej informacji, zobacz [Sprawdzanie konfiguracji rozszerzenia diagnostyki](#how-to-check-diagnostics-extension-configuration). |
| -107 |Do agenta monitorowania przebiegu katalogu zasobu jest nieprawidłowy.<p><p>Ten błąd wewnętrzny tylko powinno się zdarzyć, jeśli agent monitorowania ręcznie jest niepoprawnie wywołana na maszynie Wirtualnej.</p> |
| -108 |Nie można przekonwertować pliku konfiguracji diagnostyki w pliku konfiguracji agenta monitorowania.<p><p>Ten błąd wewnętrzny tylko powinno się zdarzyć, jeśli wtyczka diagnostyki ręcznie została wywołana z nieprawidłowy plik konfiguracji. |
| -110 |Błąd konfiguracji diagnostyki ogólne.<p><p>Ten błąd wewnętrzny tylko powinno się zdarzyć, jeśli wtyczka diagnostyki ręcznie została wywołana z nieprawidłowy plik konfiguracji. |
| -111 |Nie można uruchomić agenta monitorowania.<p><p>Rozwiązanie: Sprawdź, czy dostępne są wystarczające zasoby systemu. |
| -112 |Błąd ogólny |

### <a name="local-log-extraction"></a>Wyodrębnianie lokalnym dzienniku
Agent monitorowania zbiera dzienniki i artefaktów jak `.tsf` plików. `.tsf` Plik nie jest do odczytu, ale można przekonwertować go do `.csv` w następujący sposób: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Nowy plik o nazwie `<relevantLogFile>.csv` jest tworzony w tej samej ścieżce jako odpowiednie `.tsf` pliku.

>[!NOTE] 
> Musisz uruchomić to narzędzie dla pliku .tsf głównego (na przykład PerformanceCountersTable.tsf). Załączonych plików (na przykład PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf i tak dalej) są przetwarzane automatycznie.

### <a name="more-about-missing-trace-logs"></a>Więcej informacji na temat Brak dzienniki śledzenia 

>[!NOTE]
> Poniższe informacje dotyczą przede wszystkim do usługi w chmurze Azure, chyba że skonfigurowano DiagnosticsMonitorTraceListener przez aplikację, która działa na maszynie Wirtualnej IaaS. 

- Upewnij się, że **DiagnosticMonitorTraceListener** jest skonfigurowany w pliku web.config lub app.config.  Jest to konfiguracja domyślna w projekty usługi w chmurze. Jednak niektórzy klienci komentarz go out, co powoduje, że instrukcji śledzenia do nie zostać zebrane przez diagnostyki. 

- Jeśli nie pobierania dziennikach z **OnStart** lub **Uruchom** metody, upewnij się, że **DiagnosticMonitorTraceListener** znajduje się w pliku app.config.  Domyślnie jest w pliku web.config, ale dotyczą tylko kodu uruchamianego w w3wp.exe. Dlatego należy go w pliku app.config do przechwytywania danych śledzenia, które są uruchomione w WaIISHost.exe.

- Upewnij się, że używasz **Diagnostics.Trace.TraceXXX** zamiast **Diagnostics.Debug.WriteXXX.** Instrukcje debugowania są usuwane z kompilacji wydania.

- Upewnij się, że faktycznie został skompilowany kod **wierszy Diagnostics.Trace** (Użyj reflektora, narzędzia ildasm lub ILSpy można zweryfikować). **Diagnostics.Trace** polecenia są usuwane z skompilowanym plikiem binarnym, chyba że używany jest symbol kompilacji warunkowej śledzenia. Jest to powszechny problem, który występuje, gdy używasz programu msbuild do tworzenia projektu.   

## <a name="known-issues-and-mitigations"></a>Znane problemy i środki zaradcze
Poniżej przedstawiono listę znanych problemów z znane środki zaradcze:

**1. .NET 4.5 zależności**

Windows Azure Diagnostics rozszerzenia ma zależności aparatu plików wykonywalnych, framework .NET 4.5 lub nowszy. W czasie zapisywania, wszystkie komputery, które są udostępniane dla usług Azure Cloud Services, a także wszystkie oficjalnego obrazów, które są oparte na maszynach wirtualnych platformy Azure zainstalować programy .NET 4.5 lub nowszy. 

Jest nadal możliwe podczas potyczki sytuacji, w którym zostanie podjęta próba uruchomienia systemu Windows Azure Diagnostics rozszerzenia na maszynie, która nie ma .NET 4.5 lub nowszej. Dzieje się tak, podczas tworzenia komputera z stary obraz lub migawki lub po przywróceniu niestandardowego dysku.

Zazwyczaj jest to manifesty jako kod zakończenia **255** podczas uruchamiania **DiagnosticsPluginLauncher.exe.** Błąd odbywa się z powodu następującego nieobsługiwanego wyjątku: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Ograniczenie:** instalacji platformy .NET 4.5 lub nowszy na tym komputerze.

**2. Dane liczników wydajności znajduje się w pamięci masowej, ale nie są wyświetlane w portalu**

Środowisko portalu na maszynach wirtualnych przedstawia niektóre liczniki wydajności domyślnie. Jeśli nie widzisz liczników wydajności i wiesz, że dane są pobierania generowane, ponieważ jest ona dostępna w magazynie, sprawdź następujące informacje:

- Określa, czy dane w magazynie ma nazwy liczników w języku angielskim. Jeśli nazwy liczników nie są w języku angielskim, portalu metryki wykresu nie można go rozpoznać.

- Jeśli używasz symboli wieloznacznych (\*) w Twojej nazwy licznika wydajności portalu nie będzie możliwe do skorelowania skonfigurowane i zebranych liczników.

**Środki zaradcze**: zmiana języka na komputerze język angielski dla konta system. Aby to zrobić, wybierz **Panelu sterowania** > **Region** > **administracyjne** > **ustawień kopii**. Następnie usuń zaznaczenie **Witamy ekranu i systemu kont** tak, aby języka niestandardowej nie ma zastosowania do konta systemu. Upewnij się, że nie należy używać symboli wieloznacznych, jeśli ma portalu, aby być środowiska głównej zużycia.
