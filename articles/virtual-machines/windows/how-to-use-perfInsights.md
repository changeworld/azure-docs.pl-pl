---
title: Jak używać PerfInsights na platformie Microsoft Azure | Dokumentacja firmy Microsoft
description: Uzyskuje informacje o sposobie używania PerfInsights do rozwiązywania problemów z wydajnością maszyny Wirtualnej systemu Windows.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 2f496f906eef416b35e2e59b2db93481ce65acb1
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946541"
---
# <a name="how-to-use-perfinsights"></a>Jak używać PerfInsights

[PerfInsights](http://aka.ms/perfinsightsdownload) to narzędzie Diagnostyka samodzielnej pomocy, które zbiera i analizuje dane diagnostyczne i udostępnia raport, aby ułatwić rozwiązywanie problemów z wydajnością maszyny wirtualnej systemu Windows na platformie Azure. PerfInsights może działać na maszynach wirtualnych jako autonomicznego narzędzia lub bezpośrednio z portalu, instalując [rozszerzenia maszyny Wirtualnej systemu Azure wydajności diagnostyki](performance-diagnostics-vm-extension.md).

Jeśli występują problemy z wydajnością maszyn wirtualnych przed skontaktowaniem się z pomocy technicznej, uruchom to narzędzie.

## <a name="supported-troubleshooting-scenarios"></a>Obsługiwane scenariusze rozwiązywania problemów

PerfInsights można zbierać i analizować kilka rodzajów informacji. W poniższych częściach omówiono typowe scenariusze.

### <a name="quick-performance-analysis"></a>Analiza wydajności szybki

W tym scenariuszu zbiera konfiguracji dysku i inne ważne informacje, w tym:

-   Dzienniki zdarzeń

-   Stan sieci dla wszystkich połączeń przychodzących i wychodzących

-   Ustawienia konfiguracji sieci i zapory

-   Listy zadań dla wszystkich aplikacji, które są aktualnie uruchomione w systemie

-   Ustawienia konfiguracji bazy danych programu Microsoft SQL Server (Jeśli maszyna wirtualna została zidentyfikowana jako serwer, na którym działa program SQL Server)

-   Liczniki niezawodności magazynu

-   Ważne poprawki systemu Windows

-   Sterowniki filtrów zainstalowany

To jest pasywny zbiór informacje, które nie powinny mieć wpływ na system. 

>[!Note]
>Ten scenariusz jest automatycznie uwzględnione w każdej z następujących scenariuszy:

### <a name="benchmarking"></a>Benchmarking

W tym scenariuszu uruchamia [Diskspd](https://github.com/Microsoft/diskspd) testu (IOPS i MB/s) dla wszystkich dysków dołączonych do maszyny Wirtualnej. 

> [!Note]
> W tym scenariuszu może mieć wpływ na system i nie powinny być uruchamiane na komputerze produkcyjnym. W razie potrzeby uruchom ten scenariusz, w oknie obsługi dedykowanych Aby uniknąć problemów. Większe obciążenie jest spowodowane testu śledzenia lub testu porównawczego może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej.
>

### <a name="slow-vm-analysis"></a>Analiza powoli maszyny Wirtualnej

W tym scenariuszu uruchamia [licznika wydajności](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) śledzenia przy użyciu liczniki, które są określone w pliku RuleEngineConfig.json. Jeśli maszyna wirtualna zostanie zidentyfikowana jako serwer, na którym działa program SQL Server, jest uruchamiany śledzenia licznika wydajności. Robi to przy użyciu liczniki, które znajdują się w pliku RuleEngineConfig.json. Taki scenariusz obejmuje również dane dotyczące wydajności diagnostyki.

### <a name="azure-files-analysis"></a>Usługa pliki Azure analizy

W tym scenariuszu uruchamia przechwytywania licznika wydajności specjalne wraz z śledzenia ścieżek połączeń sieciowych. Przechwytywania zawiera wszystkie bloku komunikatów serwera (SMB) udziałów liczników klienta. Poniżej przedstawiono niektóre kluczowe klienta udziału liczniki wydajności protokołu SMB należących do przechwytywania:

| **Typ**     | **Licznik udziałów klienta protokołu SMB** |
|--------------|-------------------------------|
| Operacje wejścia/wyjścia         | Dane żądania/s             |
|              | Liczba żądań odczytu/s             |
|              | Zapisu żądania/s            |
| Opóźnienie      | Średnia liczba żądań danych/s         |
|              | Średni czas odczytu s                 |
|              | Średni czas zapisu s                |
| Rozmiar operacji We/Wy      | Średni Bajty/danych żądania       |
|              | Średni Bajty odczytu               |
|              | Średni Bajty/zapisu              |
| Przepływność   | Dane bajty/s                |
|              | Odczytu dysku/s                |
|              | Bajty zapisane/s               |
| Długość kolejki | Średni Długość kolejki odczytu        |
|              | Średni Długość kolejki zapisu       |
|              | Średni Długość kolejki danych        |

### <a name="advanced-slow-vm-analysis"></a>Zaawansowane analizy powolne maszyny Wirtualnej

Po uruchomieniu zaawansowanej analizy powolne maszyny Wirtualnej, należy wybrać dane śledzenia do uruchomienia równoległego. Jeśli chcesz, można je uruchomić wszystkie (licznika wydajności, program Xperf, sieci i StorPort).  

> [!Note]
> W tym scenariuszu może mieć wpływ na system i nie powinny być uruchamiane na komputerze produkcyjnym. W razie potrzeby uruchom ten scenariusz, w oknie obsługi dedykowanych Aby uniknąć problemów. Większe obciążenie jest spowodowane testu śledzenia lub testu porównawczego może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jakie informacje są zbierane przez PerfInsights?

Rejestruje informacje dotyczące maszyny Wirtualnej systemu Windows, dyski lub konfigurację pule magazynu, liczniki wydajności, a różne dane śledzenia są zbierane. To zależy od używany w scenariuszu wydajności. Poniższa tabela zawiera szczegółowe informacje:

|Zebrane dane                              |  |  | Scenariusze wydajności |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Analiza wydajności szybki | Benchmarking | Analiza powoli maszyny Wirtualnej | Usługa pliki Azure analizy | Zaawansowane analizy powolne maszyny Wirtualnej |
| Informacje z dzienników zdarzeń       | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Informacje o systemie                | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Mapa woluminu                        | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Mapy dysku                          | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Uruchamianie zadań                     | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Liczniki niezawodności magazynu      | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Informacje o magazynu               | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Dane wyjściowe fsutil                     | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Informacje dotyczące sterownik filtru                | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Dane wyjściowe polecenia netstat                    | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Konfiguracja sieci             | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Konfiguracja zapory            | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Konfiguracja programu SQL Server          | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Dane wydajności diagnostyki śledzenia *  | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Śledzenia licznika wydajności **      |                            |                                    | Yes                      |                      | Yes                  |
| SMB licznika śledzenia **              |                            |                                    |                          | Yes                  |                      |
| SQL Server licznika śledzenia **       |                            |                                    | Yes                      |                      | Yes                  |
| Program XPerf śledzenia                       |                            |                                    |                          |                      | Yes                  |
| StorPort śledzenia                    |                            |                                    |                          |                      | Yes                  |
| Śledzenie sieci                     |                            |                                    |                          | Yes                  | Yes                  |
| Śledzenia testu narzędzia Diskspd ***       |                            | Yes                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Diagnostyka wydajności śledzenia (*)

Działa to aparat oparty na regułach w tle do zbierania danych i diagnozowanie problemów z wydajnością stałe. Obecnie obsługiwane są następujące reguły:

- Reguła HighCpuUsage: wykrywa wysokiej okresów użycia Procesora i przedstawia najwięcej użycia procesora CPU podczas tych okresów.
- Reguła HighDiskUsage: wykrywa okresów użycia dysku na dyskach fizycznych i przedstawia top dysku konsumentów użycia tych okresach.
- Reguła HighResolutionDiskMetric: przedstawia metryki opóźnienia IOPS, przepustowości i operacji We/Wy na 50 milisekund dla każdego dysku fizycznego. Ułatwia szybkie identyfikowanie dysku ograniczania okresów.
- Reguła HighMemoryUsage: wykrywa okresów użycia pamięci wysokiej i przedstawia top pamięci konsumentów użycia tych okresach.

> [!NOTE] 
> Obecnie są obsługiwane wersje systemu Windows, które obejmują programu .NET Framework 3.5 lub nowszy.

### <a name="performance-counter-trace-"></a>Śledzenia licznik wydajności (*)

Zbiera następujące liczniki wydajności:

- \Process, \Processor \Memory, \Thread, \PhysicalDisk i \LogicalDisk
- \Server\Pool stron, \Cache\Lazy zapisu opróżnienia/s, \Cache\Dirty niestronicowanej, awarie i błędy \Server\Pool stronicowanej
- Wybrane liczniki w obszarze \Network interfejsu, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network karty, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ QoS Policy\Packets, \Per procesora sieci interfejsu karty działania i Winsock \Microsoft podstawowego dostawcy usługi sieciowej

#### <a name="for-sql-server-instances"></a>Dla wystąpienia programu SQL Server
- Menedżera serwera: buforów \SQL Statystyka puli \SQLServer:Resource i \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, statystyki
- Metody \SQLServer:Access

#### <a name="for-azure-files"></a>W przypadku plików platformy Azure
Udziały klient \SMB

### <a name="diskspd-benchmark-trace-"></a>Narzędzia Diskspd testu porównawczego śledzenia (*)
Testy obciążenia We/Wy narzędzia Diskspd (dysku systemu operacyjnego [zapisu] i dysków puli [odczytu/zapisu])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Uruchom narzędzie PerfInsights na maszynie Wirtualnej

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co trzeba wiedzieć przed rozpoczęciem I Uruchom narzędzie? 

#### <a name="tool-requirements"></a>Wymagania dotyczące narzędzia

-  To narzędzie należy uruchomić na maszynie Wirtualnej, która ma problem z wydajnością. 

-  Obsługiwane są następujące systemy operacyjne: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016; Windows 8.1 i Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Możliwe problemy z po uruchomieniu narzędzia na maszynach wirtualnych w środowisku produkcyjnym

-  Scenariusz najlepszymi lub scenariusza "Zaawansowane analizy powolne maszyny Wirtualnej", który jest skonfigurowany do używania narzędzia Xperf lub narzędzia Diskspd narzędzie może niekorzystnie wpłynąć na wydajność maszyny Wirtualnej. Te scenariusze nie powinna być uruchamiana w środowisku produkcyjnym.

-  Scenariusz najlepszymi lub scenariusza "Zaawansowane analizy powolne maszyny Wirtualnej", który jest skonfigurowany do używania narzędzia Diskspd upewnij się, że nie inne operacje w tle zakłóca obciążenia We/Wy.

-  Domyślnie narzędzie używa dysku tymczasowego magazynu do zbierania danych. Jeśli śledzenie pozostaje włączona przez dłuższy czas, ilość zbieranych danych może być istotne. Można zmniejszyć dostępność miejsca na dysku tymczasowym, a w związku z tym może mieć wpływ na dowolnej aplikacji, która opiera się na tym dysku.

### <a name="how-do-i-run-perfinsights"></a>Jak uruchomić PerfInsights? 

PerfInsights można uruchomić na maszynie wirtualnej, instalując [rozszerzenia maszyny Wirtualnej systemu Azure wydajności diagnostyki](performance-diagnostics-vm-extension.md). Można również uruchomić jako autonomicznego narzędzia. 

**Zainstaluj i uruchom PerfInsights z portalu Azure**

Aby uzyskać więcej informacji na temat tej opcji, zobacz [zainstalować rozszerzenia maszyny Wirtualnej diagnostyki wydajności Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Uruchom PerfInsights w trybie autonomicznym**

Aby uruchomić narzędzie PerfInsights, wykonaj następujące kroki:


1. Pobierz [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Odblokować plik PerfInsights.zip. Aby to zrobić, kliknij prawym przyciskiem myszy plik PerfInsights.zip, a następnie wybierz **właściwości**. W **ogólne** wybierz opcję **Odblokuj**, a następnie wybierz **OK**. Dzięki temu, że narzędzie jest uruchamiane bez dodatkowych zabezpieczeń monituje.  

    ![Zrzut ekranu PerfInsights właściwości, z wyróżnionym Odblokuj](media/how-to-use-perfInsights/unlock-file.png)

3.  Rozwiń skompresowany plik PerfInsights.zip do stacji tymczasowe (domyślnie jest to zazwyczaj na dysku D). 

4.  Otwórz wiersz polecenia systemu Windows jako administrator, a następnie uruchom PerfInsights.exe Aby wyświetlić parametry dostępne w wierszu polecenia.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Zrzut ekranu PerfInsights dane wyjściowe z wiersza polecenia](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    Podstawowa składnia uruchamiania scenariuszy PerfInsights jest:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Można użyć poniższym przykładzie do uruchomienia powolne scenariusz maszyny Wirtualnej w ciągu 5 minut:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Poniższy przykład umożliwia uruchamianie bardziej zaawansowany scenariusz z wydajności i narzędzia Xperf licznika danych śledzenia w ciągu 5 minut:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Można użyć poniższym przykładzie do uruchomienia powolne scenariusz maszyny Wirtualnej w ciągu 5 minut, a następnie przekaż plik zip wynik do konta magazynu:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Można wyszukiwać wszystkie dostępne scenariuszy i opcje przy użyciu **/list** polecenia:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Przed uruchomieniem scenariusza, PerfInsights monituje użytkownika o zgadzają się udostępnianie informacji diagnostycznych i zgodę na umowę licencyjną. Użyj **/AcceptDisclaimerAndShareDiagnostics** opcję pomijania monity. 
    >
    >Jeśli masz biletu active pomocy technicznej firmy Microsoft i PerfInsights uruchomionej na żądanie z pracownikiem pomocy technicznej, w przypadku korzystania z upewnij się, że zapewnienia obsługi biletów numer za pomocą **/sr** opcji.
    >
    >Domyślnie PerfInsights próbuje aktualizowanie się do najnowszej wersji, jeśli jest dostępna. Użyj **/SkipAutoUpdate** lub **/sau** parametru pomijania automatycznej aktualizacji.  
    >
    >Jeśli przełącznik czas trwania **/d** nie zostanie określony, PerfInsights spowoduje wyświetlenie monitu do odtworzenia problemu podczas uruchamiania vmslow, azurefiles i zaawansowanych scenariuszy. 

Po ukończeniu operacji lub dane śledzenia w tym samym folderze co PerfInsights pojawi się nowy plik. Nazwa pliku jest **CollectedData\_RRRR MM-dd\_hh-mm-ss-fff.zip.** Można wysłać ten plik do agenta programu obsługi dla analizy lub otworzyć raport w pliku zip, aby przejrzeć wyniki i zalecenia.

## <a name="review-the-diagnostics-report"></a>Przejrzyj raport diagnostyczny

W ramach **CollectedData\_RRRR MM-dd\_hh-mm-ss-fff.zip** plików, można znaleźć raport HTML ze szczegółami ustalenia PerfInsights. Aby wyświetlić raport, rozwiń węzeł **CollectedData\_RRRR MM-dd\_hh-mm-ss-fff.zip** pliku, a następnie otwórz **PerfInsights Report.html** pliku.

Wybierz **ustalenia** kartę.

![Zrzut ekranu przedstawiający raport PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![zrzut ekranu przedstawiający raport PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Ustalenia sklasyfikowane jako wysoki znane problemy, które mogą powodować problemy z wydajnością. Ustalenia skategoryzowany jako średni reprezentują-optymalne konfiguracje, które nie muszą powodować problemy z wydajnością. Ustalenia skategoryzowane jako niskie są tylko szczegółowych instrukcji.

Przejrzyj zalecenia i linki do wszystkich wyników wysokiej i średniej. Informacje o ich wpływu na wydajność i dotyczących najlepszych rozwiązań dotyczących konfiguracji zoptymalizowana pod kątem wydajności.

### <a name="storage-tab"></a>Karta magazynu

**Ustalenia** sekcja wyświetla różne wyniki i zalecenia dotyczące magazynu.

**Mapy dysku** i **mapy woluminu** sekcjach opisano sposób logicznej woluminów i fizyczne dyski są powiązane ze sobą.

W perspektywie dysk fizyczny (mapy dysku) w tabeli przedstawiono wszystkie woluminy logiczne, które są uruchomione na dysku. W poniższym przykładzie **PhysicalDrive2** uruchamia dwa logiczne woluminy tworzone na wielu partycjach (J i H):

![Zrzut ekranu przedstawiający kartę dysku](media/how-to-use-perfInsights/disktab.png)

W perspektywie woluminu (wolumin Map) w tabelach przedstawiono wszystkie dyski fizyczne w obszarze każdego woluminu logicznego. Zwróć uwagę, że dla dysków dynamicznych/RAID, wolumin logiczny może działać na wielu dyskach fizycznych. W poniższym przykładzie *C:\\instalacji* punkt instalacji jest skonfigurowany jako *SpannedDisk* na dyskach fizycznych, 2 i 3:

![Zrzut ekranu przedstawiający kartę woluminu](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Karta SQL

Jeśli element docelowy maszyny Wirtualnej obsługuje wszystkie wystąpienia programu SQL Server, zobacz dodatkowe karty w raporcie o nazwie **SQL**:

![Zrzut ekranu SQL kartę](media/how-to-use-perfInsights/sqltab.png)

Ta sekcja zawiera **ustalenia** kartę i dodatkowe karty dla wszystkich wystąpień programu SQL Server obsługiwanych na maszynie Wirtualnej.

**Ustalenia** karta zawiera listę wszystkich SQL dotyczące problemów z wydajnością znaleziono wraz z zaleceniami.

W poniższym przykładzie **PhysicalDrive0** (uruchomionego dysk C) jest wyświetlany. Jest to spowodowane zarówno **modeldev** i **modellog** pliki znajdują się na dysku C i są różnych typów (takie jak dziennik transakcji i plików danych, odpowiednio).

![Zrzut ekranu przedstawiający informacje dziennika](media/how-to-use-perfInsights/loginfo.png)

Karty dla określonego wystąpienia programu SQL Server zawierają ogólne sekcja, która zawiera podstawowe informacje o wybranym wystąpieniu. Karty również zawierać dodatkowe sekcje zaawansowane informacje, w tym ustawienia, konfiguracje i opcje użytkownika.

### <a name="diagnostic-tab"></a>Karta diagnostyki
**Diagnostycznych** karta zawiera informacje o najwięcej Procesora, dysku i pamięci na komputerze w czasie trwania działanie PerfInsights. Można również znaleźć informacje o poprawkach krytyczne czy system może być brak, listę zadań i zdarzenia systemowe. 

## <a name="references-to-the-external-tools-used"></a>Odwołania do zewnętrznego narzędzia używane

### <a name="diskspd"></a>Narzędzia Diskspd

Diskspd jest magazynu obciążenia generator i wydajności testu narzędzie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Program XPerf jest narzędziem wiersza polecenia do przechwytywania danych śledzenia z zestawu narzędzi wydajności systemu Windows. Aby uzyskać więcej informacji, zobacz [Toolkit wydajności systemu Windows — program Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Kolejne kroki

Możesz przekazać dzienników diagnostyki i raporty do firmy Microsoft Support do dalszej analizy. Obsługa może zażądać, aby przesyłać dane wyjściowe, który jest generowany przez PerfInsights, aby ułatwić proces rozwiązywania problemów.

Poniższy zrzut ekranu przedstawia podobne do może zostać wyświetlony komunikat:

![Zrzut ekranu przedstawiający przykładowy komunikat z Support firmy Microsoft](media/how-to-use-perfInsights/supportemail.png)

Postępuj zgodnie z instrukcjami w komunikacie dostępu do obszaru roboczego transferu plików. Aby dodatkowo zwiększyć bezpieczeństwo należy zmienić hasło przy pierwszym użyciu.

Po zalogowaniu, znajdują się okno dialogowe, aby przekazać **CollectedData\_RRRR MM-dd\_hh-mm-ss-fff.zip** pliku, który został zebrany przez PerfInsights.
