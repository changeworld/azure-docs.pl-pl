---
title: Rozwiązanie pojemność i wydajność w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Użyj rozwiązanie pojemność i wydajność w monitorze, aby ułatwić zrozumienie wydajności serwerów funkcji Hyper-V.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: fcf71bf144b559c4867303988d4c1f08b7aa5605
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101918"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Planowanie pojemności maszyn wirtualnych funkcji Hyper-V rozwiązanie pojemność i wydajność (przestarzałe)

![Symbol pojemność i wydajność](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Rozwiązanie pojemność i wydajność jest przestarzała.  Klienci, którzy już zainstalowano rozwiązania można nadal z niego korzystać, ale pojemności i wydajności nie można dodać do żadnych nowych obszarów roboczych.

Rozwiązanie pojemność i wydajność w monitorze można użyć, aby ułatwić zrozumienie wydajności serwerów funkcji Hyper-V. Rozwiązanie zapewnia wgląd w środowisku funkcji Hyper-V, pokazując ogólnego użycia (procesor CPU, pamięci i dysku) hostów i maszyn wirtualnych uruchomionych na tych hostach funkcji Hyper-V. Metryki są zbierane dla procesora CPU, pamięci i dysków na wszystkie hosty i maszyny wirtualne uruchomione na nich.

Rozwiązanie:

-   Pokazuje hostów przy użyciu najwyższej i najniższej wykorzystanie Procesora i pamięci
-   Przedstawia maszyny wirtualne z najwyższej i najniższej wykorzystanie Procesora i pamięci
-   Przedstawia maszyny wirtualne z najwyższej i najniższej wykorzystanie operacji We/Wy i przepływność
-   Pokazuje, które maszyny wirtualne są uruchomione na hostach, które
-   Zawiera najważniejsze dysków o wysokiej przepływności, operacje We/Wy i opóźnienia w udostępnionych woluminów klastra
- Pozwala na dostosowywanie i filtrowanie na podstawie grup

> [!NOTE]
> Poprzednia wersja rozwiązanie pojemność i wydajność, o nazwie Zarządzanie wydajnością wymagane zarówno System Center Operations Manager, jak i System Center Virtual Machine Manager. To rozwiązanie zaktualizowany nie ma tych zależności.


## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
|---|---|---|
| [Agenci dla systemu Windows](../../azure-monitor/platform/agent-windows.md) | Yes | Rozwiązanie zbiera informacje o danych pojemność i wydajność z agentów dla Windows. |
| [Agenci dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nie    | Rozwiązania nie zbiera informacje o danych pojemności i wydajności z bezpośrednich agentów systemu Linux.|
| [Grupy zarządzania SCOM](../../azure-monitor/platform/om-agents.md) | Yes |Rozwiązanie zbiera dane dotyczące pojemności i wydajności z agentów w połączonej grupie zarządzania programu SCOM. Bezpośrednie połączenie agenta programu SCOM do usługi Log Analytics nie jest wymagane.|
| [Konto usługi Azure Storage](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure storage nie obejmuje dane dotyczące pojemności i wydajności.|

## <a name="prerequisites"></a>Wymagania wstępne

- Windows lub agentów programu Operations Manager musi być zainstalowany w systemie Windows Server 2012 lub wyższy hosty funkcji Hyper-V, nie maszyn wirtualnych.


## <a name="configuration"></a>Konfigurowanie

Wykonaj następujący krok, aby dodać rozwiązanie pojemność i wydajność do obszaru roboczego.

- Dodaj rozwiązanie pojemność i wydajność do obszaru roboczego usługi Log Analytics przy użyciu procesu opisanego w [rozwiązań Dodaj usługi Log Analytics z galerii rozwiązań](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupę zarządzania programu SCOM jest połączony z obszarem roboczym usługi Log Analytics, następnie następujące pakiety administracyjne zostanie zainstalowany w SCOM po dodaniu tego rozwiązania. Nie jest wymagana żadna konfiguracja ani obsługa tych pakietów administracyjnych.

- Microsoft.IntelligencePacks.CapacityPerformance

Zdarzenie 1201 podobny:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Po zaktualizowaniu rozwiązanie pojemność i wydajność, zmieni się numer wersji.

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../../azure-monitor/platform/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązanie pojemność i wydajność do swojego obszaru roboczego pojemności i wydajności jest dodawany do pulpitu nawigacyjnego przeglądu. Ten Kafelek zawiera liczbę aktywnych hostów funkcji Hyper-V, a wybrana liczba aktywnych maszyn wirtualnych, które były monitorowane w danym okresie.

![Kafelek pojemność i wydajność](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Przegląd wykorzystania

Kliknij Kafelek pojemności i wydajności, aby otworzyć pulpit nawigacyjny pojemności i wydajności. Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera do dziesięciu elementów spełniających jej kryteria dla podanego zakresu i przedziału czasu. Można uruchomić wyszukiwanie w dzienniku, które zwróci wszystkie rekordy. W tym celu kliknij przycisk **Zobacz wszystko** na dole kolumny lub kliknij nagłówek kolumny.

- **Hosty**
    - **Użycie procesora CPU hosta** pokazuje sposób graficzny trend wykorzystania procesora CPU komputerów-hostów oraz listę hostów, w oparciu o wybrany okres czasu. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę hosta, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika procesora CPU dla maszyn wirtualnych w hostowanej.
    - **Wykorzystanie pamięci hosta** pokazuje sposób graficzny trend wykorzystania pamięci komputerów-hostów oraz listę hostów, w oparciu o wybrany okres czasu. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę hosta, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika pamięci dla maszyn wirtualnych hostowanych.
- **Virtual Machines**
    - **Użycie procesora CPU maszyny Wirtualnej** pokazuje sposób graficzny trend wykorzystania procesora CPU maszyny wirtualne oraz listę maszyn wirtualnych, w oparciu o wybrany okres czasu. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie dla najważniejszych 3 maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić zagregowane szczegóły licznika Procesora dla maszyny Wirtualnej.
    - **Wykorzystanie pamięci maszyny Wirtualnej** pokazuje sposób graficzny trend wykorzystania pamięci maszyn wirtualnych oraz listę maszyn wirtualnych, w oparciu o wybrany okres czasu. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie dla najważniejszych 3 maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika zagregowane pamięci dla maszyny Wirtualnej.
    - **Łączna liczba IOPS dysku maszyny Wirtualnej** pokazuje sposób graficzny trend całkowitego miejsca na dysku na SEKUNDĘ dla maszyn wirtualnych oraz listę maszyn wirtualnych przy użyciu operacji We/Wy dla każdego, oparte na wybrany okres czasu. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie dla najważniejszych 3 maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dziennikach i widoku zagregowane dysku, operacje We/Wy licznik szczegóły dla maszyny Wirtualnej.
    - **Łączna przepływność dysku maszyny Wirtualnej** pokazuje sposób graficzny trend przepływności całkowitego miejsca na dysku dla maszyn wirtualnych oraz listę maszyn wirtualnych z przepływnością całkowitego miejsca na dysku dla każdego, oparte na wybrany okres czasu. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie dla najważniejszych 3 maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika przepływności zagregowane całkowitego miejsca na dysku dla maszyny Wirtualnej.
- **Udostępnione woluminy klastra**
    - **Łączna przepływność** zawiera sumę operacje odczytu i zapisu na udostępnione woluminy klastra.
    - **Łączna liczba operacji We/Wy** przedstawia sumę operacji wejścia/wyjścia na sekundę na udostępnione woluminy klastra.
    - **Łączny czas oczekiwania** przedstawia całkowity czas oczekiwania na udostępnione woluminy klastra.
- **Gęstość hosta** górny Kafelek Pokazuje łączną liczbą hosty i maszyny wirtualne dostępne do rozwiązania. Kliknij górny Kafelek, aby wyświetlić dodatkowe informacje podczas wyszukiwania dziennika. Zawiera również listę wszystkich hostów i liczbę maszyn wirtualnych, które znajdują się. Kliknij wybranego hosta, aby przejść do szczegółów wyników maszyny Wirtualnej podczas wyszukiwania dziennika.


![Blok hosty pulpitu nawigacyjnego](./media/capacity-performance/dashboard-hosts.png)

![Blok maszyny wirtualne pulpitu nawigacyjnego](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Ocena wydajności

Produkcja środowisk przetwarzania różnią się znacznie od jednej z organizacji do innego. Ponadto pojemność i wydajność obciążeń może zależeć od jak maszyny wirtualne są uruchomione, i co należy wziąć pod uwagę normalny. Konkretne procedury, aby zmierzyć wydajność, prawdopodobnie nie będzie dotyczyć środowiska użytkownika. Dlatego uogólniony więcej normatywnych wskazówki lepiej nadaje się do pomocy. Firma Microsoft publikuje wiele artykułów wskazówki ułatwiające pomiaru wydajności.

Aby podsumować, rozwiązanie zbiera dane dotyczące pojemności i wydajności z różnych źródeł takich jak liczniki wydajności. Te dane pojemności i wydajności znajdujące się w różnych powierzchniach w rozwiązaniu i porównać wyniki do tych w [pomiaru wydajności funkcji Hyper-v](https://msdn.microsoft.com/library/cc768535.aspx) artykułu. Mimo że artykuł został opublikowany pewien czas temu, metryki, zagadnienia i wytyczne są nadal ważne. Ten artykuł zawiera linki do innych przydatnych zasobów.


## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla danych pojemności i wydajności, zebranych i obliczana na podstawie tego rozwiązania.


| Zapytanie | Opis |
|:--- |:--- |
| Wszystkie konfiguracje pamięci hosta | Perf &#124; gdzie ObjectName == "Pojemności i wydajności" i CounterName == "MB pamięci przypisana hosta" &#124; Podsumuj MB avg(CounterValue), InstanceName = |
| Wszystkie konfiguracje pamięci maszyn wirtualnych | Perf &#124; gdzie ObjectName == "Pojemności i wydajności" i CounterName == "MB pamięci przypisana maszyny Wirtualnej" &#124; Podsumuj MB = avg(CounterValue) przez InstanceName |
| Podział łączna liczba IOPS na dysk na wszystkich maszynach wirtualnych | Perf &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "Wirtualnego dysku twardego odczyty/s" albo CounterName == "Wirtualnego dysku twardego operacje zapisu/s") &#124; summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |
| Podział łącznej przepływności dysków na wszystkich maszynach wirtualnych | Perf &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "Wirtualnego dysku twardego odczyt MB/s" albo CounterName == "Wirtualnego dysku twardego zapisu MB/s") &#124; summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |
| Podział łączna liczba operacji We/Wy na wszystkie woluminy CSV | Perf &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "CSV odczyty/s" albo CounterName == "CSV operacje zapisu/s") &#124; summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |
| Podział łącznej przepływności na wszystkie woluminy CSV | Perf &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "CSV odczyty/s" albo CounterName == "CSV operacje zapisu/s") &#124; summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |
| Podział łącznego opóźnienia na wszystkie woluminy CSV | Perf &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "Opóźnienie odczytu CSV" lub CounterName == "Opóźnienie zapisu CSV") &#124; summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |


## <a name="next-steps"></a>Kolejne kroki
* Użyj [przeszukiwanie dzienników w usłudze Log Analytics](../../azure-monitor/log-query/log-query-overview.md) do wyświetlania szczegółowych danych o pojemności i wydajności.
