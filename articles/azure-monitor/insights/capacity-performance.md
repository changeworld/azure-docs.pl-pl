---
title: Capacity and Performance rozwiązanie w Azure Monitor | Microsoft Docs
description: Użyj rozwiązania Capacity and Performance w monitorze, aby ułatwić zrozumienie pojemności serwerów funkcji Hyper-V.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666947"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Zaplanuj pojemność maszyny wirtualnej funkcji Hyper-V za pomocą rozwiązania Capacity and Performance (przestarzałe)

![Symbol pojemność i wydajność](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Rozwiązanie pojemność i wydajność jest przestarzała.  Klienci, którzy już zainstalowano rozwiązania można nadal z niego korzystać, ale pojemności i wydajności nie można dodać do żadnych nowych obszarów roboczych.

Możesz użyć rozwiązania Capacity and Performance w monitorze, aby ułatwić zrozumienie pojemności serwerów funkcji Hyper-V. Rozwiązanie zapewnia wgląd w środowisku funkcji Hyper-V, pokazując ogólnego użycia (procesor CPU, pamięci i dysku) hostów i maszyn wirtualnych uruchomionych na tych hostach funkcji Hyper-V. Metryki są zbierane dla procesora CPU, pamięci i dysków na wszystkie hosty i maszyny wirtualne uruchomione na nich.

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
| [Grupa zarządzania programu SCOM](../../azure-monitor/platform/om-agents.md) | Yes |Rozwiązanie zbiera dane dotyczące pojemności i wydajności z agentów w połączonej grupie zarządzania programu SCOM. Bezpośrednie połączenie agenta programu SCOM do usługi Log Analytics nie jest wymagane.|
| [Konto usługi Azure Storage](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure storage nie obejmuje dane dotyczące pojemności i wydajności.|

## <a name="prerequisites"></a>Wymagania wstępne

- Windows lub agentów programu Operations Manager musi być zainstalowany w systemie Windows Server 2012 lub wyższy hosty funkcji Hyper-V, nie maszyn wirtualnych.


## <a name="configuration"></a>Konfiguracja

Wykonaj następujący krok, aby dodać rozwiązanie pojemność i wydajność do obszaru roboczego.

- Dodaj Capacity and Performance rozwiązanie do obszaru roboczego Log Analytics przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań log Analytics z Galeria rozwiązań](../../azure-monitor/insights/solutions.md).

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

- **Pracując**
    - **Użycie procesora CPU hosta** Przedstawia graficzny trend użycia procesora CPU przez komputery hosta i listę hostów w oparciu o wybrany okres. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę hosta, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika procesora CPU dla maszyn wirtualnych w hostowanej.
    - **Użycie pamięci hosta** Przedstawia graficzny trend wykorzystania pamięci przez komputery hosta i listę hostów w oparciu o wybrany okres. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę hosta, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika pamięci dla maszyn wirtualnych hostowanych.
- **Virtual Machines**
    - **Użycie procesora CPU maszyny wirtualnej** Przedstawia graficzny trend użycia procesora CPU przez maszyny wirtualne i listę maszyn wirtualnych w oparciu o wybrany okres. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie dla najważniejszych 3 maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić zagregowane szczegóły licznika Procesora dla maszyny Wirtualnej.
    - **Wykorzystanie pamięci maszyny wirtualnej** Przedstawia graficzny trend wykorzystania pamięci maszyn wirtualnych i listy maszyn wirtualnych w oparciu o wybrany okres. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie dla najważniejszych 3 maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika zagregowane pamięci dla maszyny Wirtualnej.
    - **Całkowita liczba operacji we/wy dysku maszyny wirtualnej** Przedstawia graficzny trend łącznej liczby operacji we/wy na sekundę dla maszyn wirtualnych i listy maszyn wirtualnych z każdą liczbą operacji we/wy na sekundę w oparciu o wybrany okres. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie dla najważniejszych 3 maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dziennikach i widoku zagregowane dysku, operacje We/Wy licznik szczegóły dla maszyny Wirtualnej.
    - **Całkowita przepływność dysku maszyny wirtualnej** Przedstawia graficzny trend łącznej przepływności dysków dla maszyn wirtualnych i listy maszyn wirtualnych z łączną przepływność dysku dla każdej z nich w oparciu o wybrany okres. Umieść kursor nad wykres liniowy, aby wyświetlić szczegóły dotyczące określonego punktu w czasie dla najważniejszych 3 maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów podczas wyszukiwania dziennika. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika przepływności zagregowane całkowitego miejsca na dysku dla maszyny Wirtualnej.
- **Udostępnione woluminy klastrowane**
    - **Łączna przepływność** Pokazuje sumę operacji odczytu i zapisu na udostępnionych woluminach klastra.
    - **Całkowita liczba operacji we/wy** Pokazuje sumę operacji wejścia/wyjścia na sekundę w klastrowanych woluminach udostępnionych.
    - **Łączne opóźnienie** Pokazuje łączne opóźnienie na udostępnionych woluminach klastra.
- **Gęstość hosta** Na górnym kafelku przedstawiono łączną liczbę hostów i maszyn wirtualnych dostępnych dla rozwiązania. Kliknij górny Kafelek, aby wyświetlić dodatkowe informacje podczas wyszukiwania dziennika. Zawiera również listę wszystkich hostów i liczbę maszyn wirtualnych, które znajdują się. Kliknij wybranego hosta, aby przejść do szczegółów wyników maszyny Wirtualnej podczas wyszukiwania dziennika.


![Blok hosty pulpitu nawigacyjnego](./media/capacity-performance/dashboard-hosts.png)

![Blok maszyny wirtualne pulpitu nawigacyjnego](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Ocena wydajności

Produkcja środowisk przetwarzania różnią się znacznie od jednej z organizacji do innego. Ponadto pojemność i wydajność obciążeń może zależeć od jak maszyny wirtualne są uruchomione, i co należy wziąć pod uwagę normalny. Konkretne procedury, aby zmierzyć wydajność, prawdopodobnie nie będzie dotyczyć środowiska użytkownika. Dlatego uogólniony więcej normatywnych wskazówki lepiej nadaje się do pomocy. Firma Microsoft publikuje wiele artykułów wskazówki ułatwiające pomiaru wydajności.

Aby podsumować, rozwiązanie zbiera dane dotyczące pojemności i wydajności z różnych źródeł takich jak liczniki wydajności. Użyj tej pojemności i danych wydajności, które są prezentowane na różnych urządzeniach w rozwiązaniu, a następnie Porównaj wyniki z wynikami [pomiaru wydajności w artykule dotyczącym funkcji Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) . Mimo że artykuł został opublikowany pewien czas temu, metryki, zagadnienia i wytyczne są nadal ważne. Ten artykuł zawiera linki do innych przydatnych zasobów.


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


## <a name="next-steps"></a>Następne kroki
* Użyj [przeszukiwania dzienników w log Analytics](../../azure-monitor/log-query/log-query-overview.md) , aby wyświetlić szczegółowe dane Capacity and Performance.
