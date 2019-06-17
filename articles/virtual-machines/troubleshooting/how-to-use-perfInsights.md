---
title: Jak za pomocą programu PerfInsights w systemie Microsoft Azure | Dokumentacja firmy Microsoft
description: Uczy się, jak za pomocą programu PerfInsights Windows VM Rozwiązywanie problemów z wydajnością.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: cb414abcbbf2db7b7cd6a3d724e50010beeef647
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318419"
---
# <a name="how-to-use-perfinsights"></a>Korzystanie z narzędzia PerfInsights

[Program PerfInsights](https://aka.ms/perfinsightsdownload) to narzędzie diagnostyczne samodzielnej pomocy, które zbiera i analizuje dane diagnostyczne i udostępnia raport, aby ułatwić rozwiązywanie problemów z wydajnością maszyny wirtualnej Windows na platformie Azure. Program PerfInsights mogą być uruchamiane na maszynach wirtualnych jako autonomiczne narzędzie bezpośrednio z witryny portal przy użyciu [Diagnostyka wydajności maszyn wirtualnych platformy Azure](performance-diagnostics.md), lub dzięki zainstalowaniu [rozszerzenie maszyny Wirtualnej diagnostyki wydajności platformy Azure ](performance-diagnostics-vm-extension.md).

Jeśli występują problemy z wydajnością z maszynami wirtualnymi, skontaktuj się z pomocą techniczną, uruchom to narzędzie.

## <a name="supported-troubleshooting-scenarios"></a>Obsługiwane scenariusze dotyczące rozwiązywania problemów

Program PerfInsights może zbierać i analizować kilka rodzajów informacji. W poniższych częściach omówiono typowe scenariusze.

### <a name="quick-performance-analysis"></a>Analiza wydajności szybki

Ten scenariusz umożliwia zbieranie informacji o konfiguracji dysku i inne ważne informacje, w tym:

-   Dzienniki zdarzeń

-   Stan sieci dla wszystkich połączeń przychodzących i wychodzących

-   Ustawienia konfiguracji sieci i zapory

-   Lista zadań dla wszystkich aplikacji, które są aktualnie uruchomione w systemie

-   Ustawienia konfiguracji bazy danych programu Microsoft SQL Server (Jeśli maszyna wirtualna jest identyfikowany jako serwera, na którym działa program SQL Server)

-   Liczniki niezawodność magazynu

-   Ważne poprawki Windows

-   Odfiltruj zainstalowane sterowniki

Jest to zbiór pasywnym informacje, które nie powinny mieć wpływ na system. 

>[!Note]
>Ten scenariusz jest automatycznie uwzględniany we wszystkich następujących scenariuszy:

### <a name="benchmarking"></a>Benchmarking

Ten scenariusz jest uruchamiany [narzędzia Diskspd](https://github.com/Microsoft/diskspd) testu (operacje We/Wy i MB/s) dla wszystkich dysków, które są dołączone do maszyny Wirtualnej. 

> [!Note]
> W tym scenariuszu może mieć wpływ na system i nie powinny być uruchamiane w systemie produkcyjnym. Jeśli to konieczne, należy uruchomić w tym scenariuszu w oknie dedykowanej konserwacji, aby uniknąć problemów. Większe obciążenie jest spowodowane przez test ślad lub testów porównawczych może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej.
>

### <a name="performance-analysis"></a>Analiza wydajności

Ten scenariusz jest uruchamiany [licznika wydajności](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) śledzenia przy użyciu liczników, które są określone w pliku RuleEngineConfig.json. Jeśli maszyna wirtualna zostanie zidentyfikowana jako serwera, na którym działa program SQL Server, jest uruchamiana śledzenia licznika wydajności. Robi to przy użyciu liczników, które znajdują się w pliku RuleEngineConfig.json. Ten scenariusz obejmuje także dane dotyczące wydajności diagnostyki.

### <a name="azure-files-analysis"></a>Usługi Azure analysis plików

Ten scenariusz jest uruchamiany przechwytywania wydajności specjalnych licznik wraz z śledzenia sieci. Przechwytywania zawiera wszystkie bloku komunikatów serwera (SMB) udziałów liczników klienta. Poniżej przedstawiono niektóre kluczowe klienta udziału liczniki wydajności protokołu SMB, które są częścią przechwytywania:

| **Typ**     | **Licznik udziały klient SMB** |
|--------------|-------------------------------|
| Operacje wejścia/wyjścia         | Data żądania/s             |
|              | Liczba żądań odczytu/s             |
|              | Zapisu żądania/s            |
| Opóźnienie      | Średni czas żądania s/danych         |
|              | Średni czas odczytu                 |
|              | Średni czas zapisu                |
| Rozmiar we/wy      | Średni Bajty/Data żądania       |
|              | Średni Bajty odczytu               |
|              | Średni Bajty/zapisu              |
| Przepływność   | Bajty danych/s                |
|              | Odczytane bajty/s                |
|              | Zapisane bajty/s               |
| Długość kolejki | Średni Długość kolejki odczytu        |
|              | Średni Długość kolejki zapisu       |
|              | Średni Długość kolejki danych        |

### <a name="advanced-performance-analysis"></a>Analiza wydajności Zaawansowane

Po uruchomieniu analizy wydajności Zaawansowane, możesz wybrać informacje śledzenia do równoległego uruchamiania. Jeśli chcesz, można je też uruchamiać wszystkie (licznika wydajności, narzędzia Xperf, sieci i StorPort).  

> [!Note]
> W tym scenariuszu może mieć wpływ na system i nie powinny być uruchamiane w systemie produkcyjnym. Jeśli to konieczne, należy uruchomić w tym scenariuszu w oknie dedykowanej konserwacji, aby uniknąć problemów. Większe obciążenie jest spowodowane przez test ślad lub testów porównawczych może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jakie informacje są zbierane przez program PerfInsights?

Rejestruje informacje o maszynie Wirtualnej Windows, dyski lub konfigurację pule magazynu, liczniki wydajności, a różne ślady są zbierane. To zależy od scenariusza wydajności, którego używasz. Poniższa tabela zawiera szczegółowe informacje:

|Zebrane dane                              |  |  | Scenariusze wydajności |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Analiza wydajności szybki | Benchmarking | Analiza wydajności | Usługi Azure analysis plików | Analiza wydajności Zaawansowane |
| Informacje z dzienników zdarzeń       | Tak                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Informacje o systemie                | Tak                        | Yes                                | Yes                      | Yes                  | Tak                  |
| Mapa woluminu                        | Yes                        | Yes                                | Yes                      | Yes                  | Tak                  |
| Mapy dysku                          | Tak                        | Yes                                | Yes                      | Yes                  | Tak                  |
| Uruchomione zadania podrzędne                     | Tak                        | Yes                                | Yes                      | Yes                  | Tak                  |
| Liczniki niezawodność magazynu      | Tak                        | Yes                                | Yes                      | Yes                  | Tak                  |
| Informacje dotyczące magazynu               | Tak                        | Yes                                | Yes                      | Yes                  | Tak                  |
| Dane wyjściowe fsutil                     | Tak                        | Yes                                | Yes                      | Yes                  | Tak                  |
| Informacje dotyczące sterownika filtru                | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Dane wyjściowe polecenia netstat                    | Tak                        | Yes                                | Yes                      | Yes                  | Tak                  |
| Konfiguracja sieci             | Tak                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Konfiguracja zapory            | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Konfiguracja programu SQL Server          | Tak                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Ślady diagnostyki wydajności *  | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Śledzenia licznika wydajności **      |                            |                                    | Tak                      |                      | Yes                  |
| Protokół SMB licznik śledzenia **              |                            |                                    |                          | Yes                  |                      |
| Śledzenie licznik programu SQL Server **       |                            |                                    | Tak                      |                      | Tak                  |
| Ślad narzędzia XPerf                       |                            |                                    |                          |                      | Tak                  |
| StorPort śledzenia                    |                            |                                    |                          |                      | Tak                  |
| Śledzenie sieci                     |                            |                                    |                          | Yes                  | Tak                  |
| Śledzenie testów porównawczych narzędzia Diskspd ***       |                            | Tak                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Diagnostyka wydajności śledzenia (*)

Działa to oparty na regułach aparat w tle, aby zbierać dane i diagnozować problemy z wydajnością ciągły. Obecnie obsługiwane są następujące reguły:

- Reguła HighCpuUsage: Wykrywa wysokiej okresów użycia Procesora i przedstawia najwięcej użycia procesora CPU w tych okresach.
- Reguła HighDiskUsage: Wykrywa okresów użycia dysku na dyskach fizycznych i przedstawiono najważniejsze dysku konsumentów użycia tych okresach.
- Reguła HighResolutionDiskMetric: Pokazuje operacje We/Wy, przepustowości i operacji We/Wy metryki czas oczekiwania na 50 MS dla każdego dysku fizycznego. Ułatwia szybkie identyfikowanie dysku ograniczania okresów.
- Reguła HighMemoryUsage: Wykrywa okresów użycia dużą ilość pamięci i przedstawiono najważniejsze pamięci konsumentów użycia tych okresach.

> [!NOTE] 
> Obecnie obsługiwane są wersje Windows, które obejmują .NET Framework 4.5 lub nowszy.

### <a name="performance-counter-trace-"></a>Śledzenia licznika wydajności (*)

Gromadzi informacje o następujących liczników wydajności:

- \Process, \Processor, \Memory \Thread, \PhysicalDisk i \LogicalDisk
- \Server\Pool stron, \Cache\Lazy opróżnienia zapisu/s, \Cache\Dirty niestronicowanej, błędy i awarie \Server\Pool stronicowanej
- Wybrane liczniki w obszarze \Network interfejsu, \IPv4\Datagrams \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network karty, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ QoS Policy\Packets sieci, \Per — działanie karty interfejsu sieci procesora i \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Dla wystąpienia programu SQL Server
- Menedżera serwera: buforów \SQL \SQLServer:Resource puli statystyki i \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, statystyki
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Dla usługi Azure Files
Udziały klient \SMB

### <a name="diskspd-benchmark-trace-"></a>Śledzenie testów porównawczych narzędzia Diskspd (*)
Testy obciążenia We/Wy narzędzia Diskspd ([zapisu]. dysk systemu operacyjnego i dysków puli [odczytu/zapisu])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Uruchom narzędzie program PerfInsights na maszynie Wirtualnej

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co trzeba wiedzieć przed rozpoczęciem I Uruchom narzędzie? 

#### <a name="tool-requirements"></a>Wymagania dotyczące narzędzia

-  To narzędzie należy uruchomić na maszynie Wirtualnej, która ma problem z wydajnością. 

-  Obsługiwane są następujące systemy operacyjne: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i systemu Windows Server 2016; Windows 8.1 i Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Możliwe problemy po uruchomieniu narzędzia na maszynach wirtualnych w środowisku produkcyjnym

-  Scenariusz porównawczych lub "Zaawansowana analiza wydajności" scenariusza, który jest skonfigurowany do używania narzędzia Xperf lub narzędzia Diskspd narzędzie może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej. Te scenariusze nie powinna być uruchamiana w środowisku produkcyjnym.

-  Scenariusz porównawczych lub "Zaawansowana analiza wydajności" scenariusza, który jest skonfigurowany do używania narzędzia Diskspd upewnij się, że nie inne operacje w tle zakłóca obciążenia We/Wy.

-  Domyślnie narzędzie używa dysku magazynu tymczasowego do zbierania danych. Jeśli śledzenie pozostaje włączone przez dłuższy czas, ilość zbieranych danych mogą być istotne. Może ograniczać dostępność miejsca na dysku tymczasowym, a w związku z tym może mieć wpływ na dowolnej aplikacji, która opiera się na tym dysku.

### <a name="how-do-i-run-perfinsights"></a>Jak uruchomić program PerfInsights? 

Program PerfInsights można uruchomić na maszynie wirtualnej, instalując [rozszerzenie maszyny Wirtualnej diagnostyki wydajności Azure](performance-diagnostics-vm-extension.md). Można również uruchomić jako samodzielnego narzędzia. 

**Zainstaluj i uruchom program PerfInsights w witrynie Azure portal**

Aby uzyskać więcej informacji na temat tej opcji, zobacz [zainstalować rozszerzenia maszyny Wirtualnej diagnostyki wydajności Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Uruchom program PerfInsights w trybie autonomicznym**

Aby uruchomić narzędzie program PerfInsights, wykonaj następujące kroki:


1. Pobierz [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Odblokować plik PerfInsights.zip. Aby to zrobić, kliknij prawym przyciskiem myszy plik PerfInsights.zip i wybierz **właściwości**. W **ogólne** zaznacz **odblokowanie**, a następnie wybierz pozycję **OK**. Daje to gwarancję, że narzędzie jest uruchamiane bez dodatkowych zabezpieczeń wyświetla monit o.  

    ![Zrzut ekranu program PerfInsights właściwości, za pomocą odblokowanie wyróżniony](media/how-to-use-perfInsights/unlock-file.png)

3.  Rozwiń skompresowany plik PerfInsights.zip do dysku tymczasowego (domyślnie jest to zazwyczaj dysku D). 

4.  Otwórz okno wiersza polecenia Windows jako administrator, a następnie uruchom PerfInsights.exe w celu wyświetlania parametrów wiersza polecenia dostępne.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Zrzut ekranu program PerfInsights dane wyjściowe z wiersza polecenia](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    Podstawowa składnia do uruchamiania scenariuszy program PerfInsights jest:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Możesz użyć poniżej przykładu tak, aby uruchomić analizy wydajności scenariusz 5 minut:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Skorzystaj z następującego przykładu, aby uruchomić zaawansowanego scenariusza z śladów licznika wydajności i narzędzia Xperf 5 minut:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Możesz użyć poniższym przykładzie, aby uruchomić scenariuszowi analizy wydajności na 5 minut, a następnie przekaż plik zip wynik do konta magazynu:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Możesz wyszukać wszystkie dostępne scenariusze i opcje przy użyciu **/list** polecenia:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Przed uruchomieniem scenariusza, program PerfInsights monituje użytkownika o zgodę na udostępnianie informacji diagnostycznych i zaakceptować umowę licencyjną. Użyj **/AcceptDisclaimerAndShareDiagnostics** opcję, aby pominąć te monity. 
    >
    >Jeśli masz bilet aktywne pomocy technicznej z firmą Microsoft i uruchomiony program PerfInsights na żądanie inżynier pomocy technicznej, w którym pracujesz, upewnij się, że zapewnienia obsługi biletów numer za pomocą **/sr** opcji.
    >
    >Domyślnie program PerfInsights podejmie próbę sam aktualizowanie do najnowszej wersji, jeśli jest dostępny. Użyj **/SkipAutoUpdate** lub **/sau** parametru, aby pominąć aktualizacje automatyczne.  
    >
    >Jeśli przełącznik czas trwania **/d** nie zostanie określony, program PerfInsights spowoduje wyświetlenie monitu do odtworzenia problemu podczas uruchamiania vmslow, migracji i zaawansowanych scenariuszy. 

Po ukończeniu śladów lub operacji w tym samym folderze, co program PerfInsights pojawi się nowy plik. Nazwa pliku jest **PerformanceDiagnostics\_RRRR MM-dd\_hh-mm-ss-fff.zip.** Można wysyłać ten plik z pracownikiem pomocy technicznej do analizy lub Otwórz raport w pliku zip, aby przejrzeć wyniki i zalecenia.

## <a name="review-the-diagnostics-report"></a>Przejrzyj raport diagnostyczny

W ramach **PerformanceDiagnostics\_RRRR MM-dd\_hh-mm-ss-fff.zip** pliku, można znaleźć raport HTML, która szczegółowo ustalenia program PerfInsights. Aby przejrzeć raport, należy rozwinąć **PerformanceDiagnostics\_RRRR MM-dd\_hh-mm-ss-fff.zip** pliku, a następnie otwórz **Report.html program PerfInsights** pliku.

Wybierz **ustalenia** kartę.

![Zrzut ekranu przedstawiający raport program PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![zrzut ekranu przedstawiający raport program PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Ustalenia kategorii wysokiej znane problemy, które mogą spowodować problemy z wydajnością. Ustalenia skategoryzowany jako średnie reprezentują optymalnej konfiguracji, które nie zawsze powodują problemy z wydajnością. Ustalenia kategorii niskie są tylko w instrukcjach zawierającego wiele użytecznych informacji.

Przejrzyj zalecenia i linki do wszystkich ustalenia wysokiej i średniej. Dowiedz się o ich wpływu na wydajność i najlepszymi rozwiązaniami dotyczącymi konfiguracji pod kątem wydajności.

### <a name="storage-tab"></a>Karty Magazyn

**Ustalenia** sekcja wyświetla różne wyniki i zalecenia dotyczące magazynu.

**Mapy dysku** i **mapy woluminu** sekcjach opisano, jak logiczne woluminów i fizycznych dysków są powiązane ze sobą.

W perspektywie dysku fizycznego (dysk Map) w tabeli przedstawiono wszystkie woluminy logiczne, które są uruchomione na tym dysku. W poniższym przykładzie **PhysicalDrive2** jest uruchamiane dwa woluminy logiczne utworzone na wielu partycjach ("j" i H):

![Zrzut ekranu przedstawiający kartę dysk](media/how-to-use-perfInsights/disktab.png)

W perspektywie woluminu (Mapa woluminu) w tabelach przedstawiono wszystkie dyski fizyczne w ramach poszczególnych woluminów logicznych. Należy zauważyć, że w przypadku dysków RAID/dynamiczne, wolumin logiczny może działać na wielu dyskach fizycznych. W poniższym przykładzie *C:\\instalacji* punkt instalacji jest skonfigurowany jako *SpannedDisk* na dyskach fizycznych, 2 i 3:

![Zrzut ekranu przedstawiający kartę woluminu](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Karta SQL

Jeśli docelowa maszyna wirtualna hostuje żadnych wystąpień programu SQL Server, zobacz dodatkową kartę w raporcie o nazwie **SQL**:

![Karta Zrzut ekranu SQL](media/how-to-use-perfInsights/sqltab.png)

Ta sekcja zawiera **ustalenia** kartę, a także dodatkowe karty dla każdego wystąpienia programu SQL Server hostowana na maszynie Wirtualnej.

**Ustalenia** karta zawiera listę wszystkich SQL dotyczące problemów z wydajnością, znaleziono wraz z zaleceniami.

W poniższym przykładzie **PhysicalDrive0** (uruchomionego na dysku C) jest wyświetlana. Jest to spowodowane zarówno **modeldev** i **modellog** pliki znajdują się na dysku C, a następnie są one różne typy (takie jak dziennik transakcji i plików danych, odpowiednio).

![Zrzut ekranu przedstawiający informacje w Dzienniku](media/how-to-use-perfInsights/loginfo.png)

Karty dla konkretnych wystąpień programu SQL Server zawiera sekcja ogólne, który zawiera podstawowe informacje o wybranym wystąpieniu. Karty zawierają również dodatkowe sekcje, aby uzyskać zaawansowane informacje, w tym ustawienia, konfiguracje i opcje użytkownika.

### <a name="diagnostic-tab"></a>Karta diagnostyki
**Diagnostycznych** karta zawiera informacje o najwięcej Procesora, dysku i pamięci na komputerze, na czas trwania uruchomienia program PerfInsights. Można również znaleźć informacje o poprawkach krytycznych, system może być brak, listę zadań i zdarzenia systemowe. 

## <a name="references-to-the-external-tools-used"></a>Odwołania do zewnętrznego narzędzia używane

### <a name="diskspd"></a>Narzędzia Diskspd

Narzędzia Diskspd to magazyn obciążenia generator i wydajności testu narzędzie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [narzędzia Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Narzędzia XPerf jest narzędziem wiersza polecenia do przechwytywania śladów z zestawu narzędzi wydajności Windows. Aby uzyskać więcej informacji, zobacz [Toolkit wydajności systemu Windows — narzędzia Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Kolejne kroki

Możesz przekazać dzienniki diagnostyczne i raporty do firmy Microsoft Support do dalszej analizy. Obsługa może zażądać przesyłanych danych wyjściowych, który jest generowany przez program PerfInsights, aby ułatwić proces rozwiązywania problemów.

Poniższy zrzut ekranu przedstawia komunikat podobny do może zostać wyświetlony:

![Zrzut ekranu przedstawiający przykładowy komunikat z Microsoft Support](media/how-to-use-perfInsights/supportemail.png)

Postępuj zgodnie z instrukcjami w komunikacie dostępu do obszaru roboczego transferu plików. Poziom zabezpieczeń musisz zmienić hasło przy pierwszym użyciu.

Po zalogowaniu, można znaleźć okno dialogowe, aby przekazać **PerformanceDiagnostics\_RRRR MM-dd\_hh-mm-ss-fff.zip** plików, które zostały zebrane przez program PerfInsights.

