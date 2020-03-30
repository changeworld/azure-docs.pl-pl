---
title: Jak korzystać z systemu PerfInsights Linux na platformie Microsoft Azure| Dokumenty firmy Microsoft
description: Uczy się, jak używać PerfInsights do rozwiązywania problemów z wydajnością maszyny Wirtualnej systemu Linux.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266990"
---
# <a name="how-to-use-perfinsights"></a>Korzystanie z narzędzia PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) to samopomocowe narzędzie diagnostyczne, które zbiera i analizuje dane diagnostyczne i udostępnia raport ułatwiające rozwiązywanie problemów z wydajnością maszyn wirtualnych systemu Linux na platformie Azure. PerfInsights można uruchomić na obsługiwanych maszynach wirtualnych jako narzędzie autonomiczne lub bezpośrednio z portalu przy użyciu [diagnostyki wydajności dla maszyn wirtualnych platformy Azure.](performance-diagnostics.md)

Jeśli występują problemy z wydajnością maszyn wirtualnych, przed skontaktowaniem się z pomocą techniczną, uruchom to narzędzie.

## <a name="supported-troubleshooting-scenarios"></a>Obsługiwane scenariusze rozwiązywania problemów

PerfInsights można zbierać i analizować kilka rodzajów informacji. W poniższych sekcjach otoczy się typowe scenariusze.

### <a name="quick-performance-analysis"></a>Szybka analiza wydajności

W tym scenariuszu zbiera podstawowe informacje, takie jak magazyn i konfiguracja sprzętu maszyny wirtualnej, różne dzienniki, w tym:

- Informacje o systemie operacyjnym

- Informacje o urządzeniu PCI

- Ogólne dzienniki systemu operacyjnego gościa

- Pliki konfiguracyjne

- Informacje o przechowywaniu

- Konfiguracja maszyny wirtualnej platformy Azure (zbierane przy użyciu [usługi metadanych wystąpienia platformy Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

- Lista uruchomionych procesów, dysków, pamięci i użycia procesora CPU

- Informacje o sieci

Jest to pasywny zbiór informacji, które nie powinny mieć wpływu na system.

>[!Note]
>Scenariusz szybkiej analizy wydajności jest automatycznie uwzględniany w każdym z następujących scenariuszy:

### <a name="performance-analysis"></a>Analiza wydajności

Ten scenariusz jest podobny do szybkiej analizy wydajności, ale umożliwia przechwytywanie informacji diagnostycznych przez dłuższy czas.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jakiego rodzaju informacje są zbierane przez PerfInsights

Zbierane są informacje o maszynie wirtualnej, systemie operacyjnym, urządzeniach blokowych, wysokowydajnych konsumentach zasobów, konfiguracji i różnych dziennikach. Poniżej przedstawiono więcej informacji:

- System operacyjny
  - Dystrybucja i wersja Linuksa
  - Informacje o jądrze
  - Informacje o kierowcy

- Sprzęt
  - Urządzenia PCI`*`[ ]

- Procesy i pamięć
  - Lista procesów (nazwa zadania, używana pamięć, otwarte pliki)
  - Całkowita, dostępna i wolna pamięć fizyczna
  - Całkowita, dostępna i bezpłatna pamięć wymiany
  - Profilowanie przechwytywania procesora i przetwarza użycie procesora w odstępach 5-sekundowych
  - Profilowanie przechwytywania wykorzystania pamięci procesów w odstępach 5-sekundowych

- Obsługa sieci  
  - Lista kart sieciowych ze statystykami kart
  - Tabela routingu sieciowego
  - Otwarte porty i stan

- Magazyn
  - Lista zablokowanych urządzeń
  - Lista partycji
  - Lista punktów instalacji
  - Informacje o woluminie MDADM
  - Informacje o woluminie LVM
  - Przechwytywanie profilowania na wszystkich dyskach w odstępie 5 sekund

- Dzienniki
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[folder rozszerzenia]/\*dziennik\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Dane wyjściowe dziennika w ciągu ostatnich pięciu dni

- [Metadane wystąpienia maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] Informacje PCI nie są jeszcze zbierane w dystrybucjach Debiana i SLES

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Uruchamianie systemu PerfInsights Linux na maszynie wirtualnej

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co muszę wiedzieć przed uruchomieniem narzędzia

#### <a name="tool-requirements"></a>Wymagania dotyczące narzędzi

- To narzędzie musi być uruchomione na maszynie Wirtualnej, która ma problem z wydajnością.
- Python 2.7 musi być zainstalowany na maszynie Wirtualnej

- Obecnie obsługiwane są następujące dystrybucje:

    | Dystrybucja               | Wersja                                         |
    |----------------------------|-------------------------------------------------|
    | Serwer Oracle Linux        | 6.10`*`[ ], 7.3, 7.6, 7.5 (Obraz rynku Oracle-Database-Ee 13.8)|
    | CentOS                     | 6.5`*`[ ], 7.6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4`*`[ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Zapoznaj się z sekcją [Znane problemy](#known-issues)

### <a name="known-issues"></a>Znane problemy

- RHEL 8 nie ma domyślnie zainstalowanego języka Python. Aby uruchomić PerfInsights Linux, musisz najpierw zainstalować Pythona 2.7

- Zbieranie informacji przez agenta gościa może zakończyć się niepowodzeniem w centos 6.x

- Informacje o urządzeniach PCI nie są zbierane w dystrybucjach opartych na Debianie

- Informacje LVM są częściowo zbierane w niektórych dystrybucjach

### <a name="how-do-i-run-perfinsights"></a>Jak uruchomić PerfInsights

PerfInsights można uruchomić na maszynie wirtualnej, instalując diagnostykę wydajności platformy Azure z witryny Azure portal. Można również uruchomić go jako narzędzie autonomiczne.

>[!Note]
>PerfInsights po prostu zbiera i analizuje dane. Nie wprowadzać żadnych zmian w systemie.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Instalowanie i uruchamianie perfInsights z witryny Azure portal

Aby uzyskać więcej informacji na temat tej opcji, zobacz [Diagnostyka wydajności platformy Azure](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Uruchamianie perfInsights w trybie autonomicznym

Aby uruchomić narzędzie PerfInsights, wykonaj następujące kroki:

1. Pobierz [plik PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) do folderu na maszynie wirtualnej i wyodrębnij zawartość za pomocą poniższych poleceń z terminala.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Przejdź do folderu `perfinsights.py` zawierającego plik, a następnie uruchom, `perfinsights.py` aby wyświetlić dostępne parametry wiersza polecenia.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Zrzut ekranu przedstawiający dane wyjściowe wiersza polecenia PerfInsights Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    Podstawowa składnia uruchamiania scenariuszy PerfInsights jest:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Poniższy przykład służy do uruchamiania scenariusza szybkiej analizy wydajności przez 1 minutę i tworzenia wyników w folderze /tmp/output:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Poniższy przykład służy do uruchamiania scenariusza analizy wydajności przez 5 minut i przesyłania kuli smoły wynik do konta magazynu:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Przed uruchomieniem scenariusza Program PerfInsights monituje użytkownika o wyrażenie zgody na udostępnianie informacji diagnostycznych i wyrażenie zgody na umowy licencyjnej użytkownika. Użyj opcji **-a lub --accept-disclaimer-and-share-diagnostics,** aby pominąć te monity.
    >
    >Jeśli masz aktywny bilet pomocy technicznej z firmą Microsoft i uruchomione PerfInsights na żądanie inżyniera pomocy technicznej, z którymi pracujesz, upewnij się, że podanie numeru biletu pomocy technicznej przy użyciu opcji **-s lub --support-request.**

Po zakończeniu uruchamiania nowy plik tar pojawia się w tym samym folderze co PerfInsights, chyba że nie określono folderu wyjściowego. Nazwa pliku to **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz.** Możesz wysłać ten plik do agenta pomocy technicznej do analizy lub otworzyć raport wewnątrz pliku, aby przejrzeć wyniki i zalecenia.

## <a name="review-the-diagnostics-report"></a>Przejrzyj raport diagnostyczny

W pliku **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz** można znaleźć raport HTML, który szczegółowo opisuje ustalenia dotyczące perfInsights. Aby przejrzeć raport, rozwiń plik **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz,** a następnie otwórz plik **PerfInsights Report.html.**

### <a name="overview-tab"></a>Karta Przegląd

Karta **Przegląd** zawiera podstawowe szczegóły przebiegu i informacje o maszynie wirtualnej. Na karcie **Wyniki** jest wyświetlane podsumowanie zaleceń ze wszystkich sekcji raportu PerfInsights.

![Zrzut ekranu przedstawiający raport PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Zrzut ekranu przedstawiający raport PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Wyniki sklasyfikowane jako wysokie są znane problemy, które mogą powodować problemy z wydajnością. Wyniki sklasyfikowane jako średnie reprezentują nieoptymalne konfiguracje, które niekoniecznie powodują problemy z wydajnością. Wyniki sklasyfikowane jako niskie są tylko instrukcje informacyjne.

Przejrzyj zalecenia i linki do wszystkich wysokich i średnich ustaleń. Dowiedz się, jak mogą one wpływać na wydajność, a także o najlepszych rozwiązaniach dotyczących konfiguracji zoptymalizowanych pod kątem wydajności.

### <a name="cpu-tab"></a>Karta Procesora

Karta **Procesora CPU** zawiera informacje o zużyciu procesora CPU w całym systemie podczas uruchamiania PerfInsights. Informacje o okresach wysokiego użycia procesora CPU i najlepszych długo działających konsumentów procesora CPU będą pomocne w rozwiązywaniu problemów związanych z wysokim procesorem CPU.

![Zrzut ekranu przedstawiający kartę Procesor z raportem perfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Karta Magazyn

Sekcja **Wyniki** zawiera różne ustalenia i zalecenia dotyczące przechowywania.

Karty **Blokuj urządzenia** i inne powiązane sekcje, takie jak **Partycje,** **LVM**i **MDADM,** opisują sposób konfigurowania i powiązanych ze sobą urządzeń blokowych.

![Zrzut ekranu przedstawiający kartę magazynowania](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Zrzut ekranu przedstawiający kartę MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Karta Linux

Karta **Linux** zawiera informacje o sprzęcie i systemie operacyjnym uruchomionym na maszynie wirtualnej. Szczegóły obejmują listę uruchomionych procesów i informacje o sterownikach Agent gościa, PCI, CPU, Drivers i LIS.

![Zrzut ekranu przedstawiający kartę Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Następne kroki

Dzienniki diagnostyczne i raporty można przekazać do pomocy technicznej firmy Microsoft w celu dalszego zapoznania się z tym programem. Podczas pracy z personelem pomocy technicznej firmy Microsoft mogą zażądać przesłania danych wyjściowych generowanych przez program PerfInsights w celu pomocy w procesie rozwiązywania problemów.

Poniższy zrzut ekranu przedstawia komunikat podobny do tego, co może pojawić się:

![Zrzut ekranu przedstawiający przykładowy komunikat pomocy technicznej firmy Microsoft](media/how-to-use-perfinsights-linux/support-email.png)

Postępuj zgodnie z instrukcjami w komunikacie, aby uzyskać dostęp do obszaru roboczego transferu plików. Aby uzyskać dodatkowe bezpieczeństwo, musisz zmienić hasło przy pierwszym użyciu.

Po zalogowaniu się znajdziesz okno dialogowe, w które można przesłać plik **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz,** który został zebrany przez Plik PerfInsights.
