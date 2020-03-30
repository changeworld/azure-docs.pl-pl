---
title: Jak korzystać z funkcji PerfInsights na platformie Microsoft Azure| Dokumenty firmy Microsoft
description: Uczy się, jak używać perfInsights do rozwiązywania problemów z wydajnością maszyny Wirtualnej systemu Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 783b479dd3e5f429516799d7d3ea82f363cac2ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250129"
---
# <a name="how-to-use-perfinsights"></a>Korzystanie z narzędzia PerfInsights

[PerfInsights](https://aka.ms/perfinsightsdownload) to samopomocowe narzędzie diagnostyczne, które zbiera i analizuje dane diagnostyczne i udostępnia raport ułatwiające rozwiązywanie problemów z wydajnością maszyn wirtualnych systemu Windows na platformie Azure. PerfInsights można uruchomić na maszynach wirtualnych jako narzędzie autonomiczne, bezpośrednio z portalu przy użyciu [diagnostyki wydajności dla maszyn wirtualnych platformy Azure](performance-diagnostics.md)lub instalując [rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure.](performance-diagnostics-vm-extension.md)

Jeśli występują problemy z wydajnością maszyn wirtualnych, przed skontaktowaniem się z pomocą techniczną, uruchom to narzędzie.

## <a name="supported-troubleshooting-scenarios"></a>Obsługiwane scenariusze rozwiązywania problemów

PerfInsights można zbierać i analizować kilka rodzajów informacji. W poniższych sekcjach otoczy się typowe scenariusze.

### <a name="quick-performance-analysis"></a>Szybka analiza wydajności

W tym scenariuszu zbiera się konfiguracja dysku i inne ważne informacje, w tym:

-   Dzienniki zdarzeń

-   Stan sieci dla wszystkich połączeń przychodzących i wychodzących

-   Ustawienia konfiguracji sieci i zapory

-   Lista zadań dla wszystkich aplikacji, które są aktualnie uruchomione w systemie

-   Ustawienia konfiguracji bazy danych programu Microsoft SQL Server (jeśli maszyna wirtualna jest zidentyfikowana jako serwer z uruchomionym programem SQL Server)

-   Liczniki niezawodności pamięci masowej

-   Ważne poprawki systemu Windows

-   Zainstalowane sterowniki filtrów

Jest to pasywny zbiór informacji, które nie powinny mieć wpływu na system. 

>[!Note]
>Ten scenariusz jest automatycznie uwzględniany w każdym z następujących scenariuszy:

### <a name="benchmarking"></a>Benchmarking

W tym scenariuszu uruchamia [test porównawczy Diskspd](https://github.com/Microsoft/diskspd) (IOPS i MBPS) dla wszystkich dysków, które są podłączone do maszyny Wirtualnej. 

> [!Note]
> Ten scenariusz może mieć wpływ na system i nie powinny być uruchamiane w systemie produkcji na żywo. W razie potrzeby uruchom ten scenariusz w dedykowanym oknie konserwacji, aby uniknąć problemów. Zwiększone obciążenie, które jest spowodowane przez śledzenie lub test porównawczy może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej.
>

### <a name="performance-analysis"></a>Analiza wydajności

W tym scenariuszu uruchamia śledzenia [licznika wydajności](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) przy użyciu liczników, które są określone w pliku RuleEngineConfig.json. Jeśli maszyna wirtualna jest identyfikowana jako serwer z uruchomionym programem SQL Server, zostanie uruchomiony śledzenia licznika wydajności. Robi to przy użyciu liczników, które znajdują się w pliku RuleEngineConfig.json. Ten scenariusz obejmuje również dane diagnostyczne wydajności.

### <a name="azure-files-analysis"></a>Analiza plików platformy Azure

W tym scenariuszu uruchamia specjalny licznik wydajności przechwytywania wraz z śledzenia sieci. Przechwytywanie obejmuje wszystkie liczniki udziałów klienta bloku komunikatów serwera (SMB). Poniżej przedstawiono niektóre kluczowe liczniki wydajności udziału klienta SMB, które są częścią przechwytywania:

| **Typ**     | **Licznik udziałów klienta SMB** |
|--------------|-------------------------------|
| Liczba operacji we/wy na sekundę         | Żądania danych/s             |
|              | Żądania odczytu/s             |
|              | Żądania zapisu/s            |
| Opóźnienie      | Średnia s/żądanie danych         |
|              | Średnia sek/odczyt                 |
|              | Średnia s/zapis                |
| Rozmiar we/wy      | Średnia żądanie bajtów/danych       |
|              | Śr. Bajty/Odczyt               |
|              | Śr. Bajty/Zapis              |
| Przepływność   | Bajty danych/s                |
|              | Bajty odczytu/s                |
|              | Bajty zapisu/s               |
| Długość kolejki | Średnia długość kolejki odczytu        |
|              | Średnia długość kolejki zapisu       |
|              | Średnia długość kolejki danych        |

### <a name="advanced-performance-analysis"></a>Zaawansowana analiza wydajności

Po uruchomieniu zaawansowanej analizy wydajności, należy wybrać ślady do uruchomienia równolegle. Jeśli chcesz, możesz uruchomić je wszystkie (Licznik wydajności, Xperf, Sieć i StorPort).  

> [!Note]
> Ten scenariusz może mieć wpływ na system i nie powinny być uruchamiane w systemie produkcji na żywo. W razie potrzeby uruchom ten scenariusz w dedykowanym oknie konserwacji, aby uniknąć problemów. Zwiększone obciążenie, które jest spowodowane przez śledzenie lub test porównawczy może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jakiego rodzaju informacje są zbierane przez PerfInsights?

Zbierane są informacje o konfiguracji maszyn wirtualnych systemu Windows, dyskach lub pulach magazynowych, licznikach wydajności, dziennikach i różnych śladach. To zależy od scenariusza wydajności, którego używasz. Szczegóły zawiera poniższa tabela:

|Zbierane dane                              |  |  | Scenariusze wydajności |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Szybka analiza wydajności | Benchmarking | Analiza wydajności | Analiza plików platformy Azure | Zaawansowana analiza wydajności |
| Informacje z dzienników zdarzeń       | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Informacje o systemie                | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Mapa woluminów                        | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Mapa dysku                          | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Uruchamianie zadań                     | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Liczniki niezawodności pamięci masowej      | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Informacje o przechowywaniu               | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Wyjście Fsutil                     | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Filtrowanie informacji o sterowniku                | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Wyjście Netstat                    | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Konfiguracja sieci             | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Konfiguracja zapory            | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Konfiguracja programu SQL Server          | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Ślady diagnostyki wydajności *  | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Śledzenie licznika wydajności **      |                            |                                    | Tak                      |                      | Tak                  |
| Śledzenie licznika SMB **              |                            |                                    |                          | Tak                  |                      |
| Śledzenie licznika programu SQL Server **       |                            |                                    | Tak                      |                      | Tak                  |
| Ślad xperf                       |                            |                                    |                          |                      | Tak                  |
| StorPort śledzenia                    |                            |                                    |                          |                      | Tak                  |
| Śledzenie sieci                     |                            |                                    |                          | Tak                  | Tak                  |
| Śledzenie testu porównawczego Diskspd ***       |                            | Tak                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Śledzenie diagnostyki wydajności (*)

Uruchamia aparat oparty na regułach w tle, aby zbierać dane i diagnozować bieżące problemy z wydajnością. Obecnie obsługiwane są następujące reguły:

- Reguła HighCpuUsage: Wykrywa wysokie okresy użycia procesora CPU i pokazuje najlepszych konsumentów użycia procesora CPU w tych okresach.
- HighDiskUsage reguła: Wykrywa okresy użycia dysku na dyskach fizycznych i pokazuje najwyższe użycie dysku konsumentów w tych okresach.
- HighResolutionDiskMetric reguła: Pokazuje we/wy, przepływność i metryki opóźnienia we/wy na 50 milisekund dla każdego dysku fizycznego. Pomaga szybko zidentyfikować okresy ograniczania przepustowości dysku.
- HighMemoryUsage reguła: Wykrywa okresy użycia pamięci i pokazuje najlepsze użycie pamięci konsumentów w tych okresach.

> [!NOTE] 
> Obecnie obsługiwane są wersje systemu Windows, które zawierają wersje .NET Framework 4.5 lub nowsze.

### <a name="performance-counter-trace-"></a>Śledzenie licznika wydajności (**)

Zbiera następujące liczniki wydajności:

- \Proces, \Procesor, \Pamięć, \Wątek, \PhysicalDisk i \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/s, \Server\Pool Nonpaged, Failures, and \Server\Pool Paged Failures \Cache\Lazy Write Flushes/s, \Server\Pool Nonpaged, Failures, and \Server\Pool Paged Failures \Cache\
- Wybrane liczniki w obszarze \Interfejs sieciowy, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \TCPv6\Connection, \ Zasady QoS sieciowe\Pakiety, \Aktywność karty interfejsu sieciowego na procesor i \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Dla wystąpień programu SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Stats puli zasobów i \SQLServer:STATYSTYKI SQL\
- \SQLServer:Locks, \SQLServer:Ogólne, Statystyki
- \SQLServer:Metody dostępu

#### <a name="for-azure-files"></a>Dla plików platformy Azure
\Udziały klientów SMB

### <a name="diskspd-benchmark-trace-"></a>Śledzenie testu porównawczego Diskspd (***)
Testy obciążenia we/wy diskspd (dysk systemu operacyjnego [zapis] i dyski puli [odczyt/zapis])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Uruchamianie narzędzia PerfInsights na maszynie wirtualnej

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co muszę wiedzieć przed uruchomieniem narzędzia? 

#### <a name="tool-requirements"></a>Wymagania dotyczące narzędzi

-  To narzędzie musi być uruchomione na maszynie Wirtualnej, która ma problem z wydajnością. 

-  Obsługiwane są następujące systemy operacyjne: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016; Windows 8.1 i Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Możliwe problemy po uruchomieniu narzędzia na maszynach wirtualnych produkcyjnych

-  W przypadku scenariusza analizy porównawczej lub scenariusza "Zaawansowana analiza wydajności", który jest skonfigurowany do używania programu Xperf lub Diskspd, narzędzie może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej. Te scenariusze nie powinny być uruchamiane w środowisku produkcji na żywo.

-  W przypadku scenariusza analizy porównawczej lub scenariusza "Zaawansowana analiza wydajności", który jest skonfigurowany do używania diskspd, upewnij się, że żadne inne działanie w tle nie koliduje z obciążeniem we/wy.

-  Domyślnie narzędzie używa tymczasowego dysku magazynu do zbierania danych. Jeśli śledzenie pozostaje włączone przez dłuższy czas, ilość gromadzonych danych może być istotna. Może to zmniejszyć dostępność miejsca na dysku tymczasowym i w związku z tym może mieć wpływ na dowolną aplikację, która opiera się na tym dysku.

### <a name="how-do-i-run-perfinsights"></a>Jak uruchomić PerfInsights? 

PerfInsights można uruchomić na maszynie wirtualnej, instalując [rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure.](performance-diagnostics-vm-extension.md) Można również uruchomić go jako narzędzie autonomiczne. 

**Instalowanie i uruchamianie perfInsights z witryny Azure portal**

Aby uzyskać więcej informacji na temat tej opcji, zobacz [Instalowanie rozszerzenia maszyny wirtualnej diagnostyki wydajności platformy Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Uruchamianie perfInsights w trybie autonomicznym**

Aby uruchomić narzędzie PerfInsights, wykonaj następujące kroki:


1. Pobierz [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Odblokuj plik PerfInsights.zip. Aby to zrobić, kliknij prawym przyciskiem myszy plik PerfInsights.zip i wybierz polecenie **Właściwości**. Na karcie **Ogólne** wybierz pozycję **Odblokuj**, a następnie wybierz przycisk **OK**. Gwarantuje to, że narzędzie działa bez żadnych dodatkowych monitów zabezpieczeń.  

    ![Zrzut ekranu przedstawiający właściwości PerfInsights z wyróżnioną pozycją Odblokuj](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Rozwiń skompresowany plik PerfInsights.zip na dysku tymczasowym (domyślnie jest to dysk D). 

4.  Otwórz wiersz polecenia systemu Windows jako administrator, a następnie uruchom program PerfInsights.exe, aby wyświetlić dostępne parametry wiersza polecenia.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Zrzut ekranu przedstawiający wyjście wiersza polecenia PerfInsights](media/how-to-use-perfInsights/pi-commandline.png)
    
    Podstawowa składnia uruchamiania scenariuszy PerfInsights jest:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Poniższy przykład służy do uruchamiania scenariusza analizy wydajności przez 5 minut:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Aby uruchomić zaawansowany scenariusz za pomocą śledzenia liczników Xperf i Performance przez 5 minut, można użyć następującego przykładu:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Poniższy przykład służy do uruchamiania scenariusza analizy wydajności przez 5 minut i przekazywania pliku zip wynikowego do konta magazynu:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Za pomocą polecenia **/list** można wyszukać wszystkie dostępne scenariusze i opcje:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Przed uruchomieniem scenariusza Program PerfInsights monituje użytkownika o wyrażenie zgody na udostępnianie informacji diagnostycznych i wyrażenie zgody na umowy licencyjnej użytkownika. Użyj **/AcceptDisclaimerAndShareDiagnostics** opcja pominąć te monity. 
    >
    >Jeśli masz aktywny bilet pomocy technicznej z firmą Microsoft i uruchomione PerfInsights na żądanie inżyniera pomocy technicznej, z którymi pracujesz, upewnij się, że podanie numeru biletu pomocy technicznej przy użyciu opcji **/sr.**
    >
    >Domyślnie PerfInsights spróbuje zaktualizować się do najnowszej wersji, jeśli jest dostępna. Użyj **parametru /SkipAutoUpdate** lub **/sau,** aby pominąć automatyczną aktualizację.  
    >
    >Jeśli przełącznik czasu trwania **/d** nie jest określony, PerfInsights wyświetli monit o ponowne zaprośnienie problemu podczas uruchamiania vmslow, azurefiles i zaawansowanych scenariuszy. 

Po zakończeniu śledzenia lub operacji nowy plik pojawia się w tym samym folderze co PerfInsights. Nazwa pliku to **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip.** Możesz wysłać ten plik do agenta pomocy technicznej do analizy lub otworzyć raport wewnątrz pliku zip, aby przejrzeć wyniki i zalecenia.

## <a name="review-the-diagnostics-report"></a>Przejrzyj raport diagnostyczny

W pliku **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.zip** można znaleźć raport HTML, który szczegółowo opisuje ustalenia dotyczące PerfInsights. Aby przejrzeć raport, rozwiń plik **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.zip,** a następnie otwórz plik **PerfInsights Report.html.**

Wybierz kartę **Wyniki.**

![Zrzut ekranu przedstawiający raport](media/how-to-use-perfInsights/pi-finding-tab.png)
![PerfInsights Zrzut ekranu przedstawiający raport PerfInsights](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Wyniki sklasyfikowane jako wysokie są znane problemy, które mogą powodować problemy z wydajnością. Wyniki sklasyfikowane jako średnie reprezentują nieoptymalne konfiguracje, które niekoniecznie powodują problemy z wydajnością. Wyniki sklasyfikowane jako niskie są tylko instrukcje informacyjne.

Przejrzyj zalecenia i linki do wszystkich wysokich i średnich ustaleń. Dowiedz się, jak mogą one wpływać na wydajność, a także o najlepszych rozwiązaniach dotyczących konfiguracji zoptymalizowanych pod kątem wydajności.

### <a name="storage-tab"></a>Karta Magazyn

Sekcja **Wyniki** zawiera różne ustalenia i zalecenia dotyczące przechowywania.

Sekcje **Mapy dysku** i **Mapy woluminów** opisują, jak woluminy logiczne i dyski fizyczne są ze sobą powiązane.

W perspektywie dysku fizycznego (Mapa dysku) tabela pokazuje wszystkie woluminy logiczne uruchomione na dysku. W poniższym przykładzie **PhysicalDrive2** uruchamia dwa woluminy logiczne utworzone na wielu partycjach (J i H):

![Zrzut ekranu przedstawiający kartę dysk](media/how-to-use-perfInsights/pi-disk-tab.png)

W perspektywie woluminu (Mapa woluminu) tabele pokazują wszystkie dyski fizyczne pod każdym woluminem logicznym. Należy zauważyć, że w przypadku dysków RAID/Dynamic wolumin logiczny można uruchomić na wielu dyskach fizycznych. W poniższym przykładzie *\\C: mount* jest punktem instalacji skonfigurowanym jako *SpannedDisk* na dyskach fizycznych 2 i 3:

![Zrzut ekranu przedstawiający kartę woluminu](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Karta SQL

Jeśli docelowa maszyna wirtualna obsługuje wystąpienia programu SQL Server, w raporcie zostanie wyświetlna dodatkowa karta o nazwie **SQL:**

![Zrzut ekranu przedstawiający kartę SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Ta sekcja zawiera **wyniki** kartę i dodatkowe karty dla każdego wystąpienia programu SQL Server hostowane na maszynie Wirtualnej.

Karta **Wyniki** zawiera listę wszystkich znalezionych problemów z wydajnością związanych z programem SQL wraz z zaleceniami.

W poniższym przykładzie jest wyświetlany **PhysicalDrive0** (z uruchomionym dyskiem C). Dzieje się tak, ponieważ zarówno **pliki modeldev,** jak i **modellog** znajdują się na dysku C i są różnego typu (takie jak odpowiednio plik danych i dziennik transakcji).

![Zrzut ekranu przedstawiający informacje dziennika](media/how-to-use-perfInsights/pi-log-info.png)

Karty dla określonych wystąpień programu SQL Server zawierają ogólną sekcję, która wyświetla podstawowe informacje o wybranym wystąpieniu. Karty zawierają również dodatkowe sekcje dla zaawansowanych informacji, w tym ustawienia, konfiguracje i opcje użytkownika.

### <a name="diagnostic-tab"></a>Karta Diagnostyka
Karta **Diagnostyka** zawiera informacje o najpopularniejszych konsumentach procesora CPU, dysku i pamięci na komputerze przez czas trwania działania programu PerfInsights. Można również znaleźć informacje o krytycznych poprawkach, których może brakować w systemie, na liście zadań i ważnych zdarzeniach systemowych. 

## <a name="references-to-the-external-tools-used"></a>Odniesienia do używanych narzędzi zewnętrznych

### <a name="diskspd"></a>Dysk

Diskspd to generator obciążenia pamięci masowej i narzędzie testowe wydajności firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>System Xperf

Xperf to narzędzie wiersza polecenia do przechwytywania śladów z zestawu narzędzi wydajności systemu Windows. Aby uzyskać więcej informacji, zobacz [Zestaw narzędzi wydajności systemu Windows — Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Następne kroki

Dzienniki diagnostyczne i raporty można przekazać do pomocy technicznej firmy Microsoft w celu dalszego zapoznania się z tym programem. Pomoc techniczna może zażądać przesyłania danych wyjściowych generowanych przez PerfInsights, aby pomóc w procesie rozwiązywania problemów.

Poniższy zrzut ekranu przedstawia komunikat podobny do tego, co może pojawić się:

![Zrzut ekranu przedstawiający przykładowy komunikat pomocy technicznej firmy Microsoft](media/how-to-use-perfInsights/pi-support-email.png)

Postępuj zgodnie z instrukcjami w komunikacie, aby uzyskać dostęp do obszaru roboczego transferu plików. Aby uzyskać dodatkowe bezpieczeństwo, musisz zmienić hasło przy pierwszym użyciu.

Po zalogowaniu się znajdziesz okno dialogowe, w które można przesłać plik **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.zip,** który został zebrany przez Plik PerfInsights.

