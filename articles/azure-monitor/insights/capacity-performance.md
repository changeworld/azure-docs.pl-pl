---
title: Rozwiązanie dotyczące pojemności i wydajności w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Użyj rozwiązania Pojemność i wydajność w monitorze, aby ułatwić zrozumienie pojemności serwerów hyper-V.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666947"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Planowanie pojemności maszyny wirtualnej funkcji Hyper-V za pomocą rozwiązania Capacity and Performance (przestarzałe)

![Symbol pojemności i wydajności](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Rozwiązanie Capacity and Performance zostało przestarzałe.  Klienci, którzy już zainstalowali rozwiązanie, mogą nadal z niego korzystać, ale nie można dodać pojemności i wydajności do żadnych nowych obszarów roboczych.

Rozwiązanie Pojemność i wydajność w monitorze ułatwia zrozumienie pojemności serwerów funkcji Hyper-V. Rozwiązanie zapewnia wgląd w środowisko funkcji Hyper-V, pokazując ogólne wykorzystanie (procesora CPU, pamięci i dysku) hostów i maszyn wirtualnych działających na tych hostach funkcji Hyper-V. Metryki są zbierane dla procesora CPU, pamięci i dysków na wszystkich hostach i maszynach wirtualnych działających na nich.

Rozwiązanie:

-   Pokazuje hosty o najwyższym i najniższym wykorzystaniu procesora i pamięci
-   Pokazuje maszyny wirtualne z najwyższym i najniższym wykorzystaniem procesora i pamięci
-   Pokazuje maszyny wirtualne z najwyższym i najniższym wykorzystaniem we/wy i przepustowością
-   Pokazuje, które maszyny wirtualne są uruchomione na których hostach
-   Pokazuje górne dyski o wysokiej przepływności, operacje We/Wy i opóźnienia w udostępnionych woluminach klastra
- Umożliwia dostosowywanie i filtrowanie na podstawie grup

> [!NOTE]
> Poprzednia wersja rozwiązania Capacity and Performance o nazwie Zarządzanie pojemnością wymagała zarówno menedżera operacyjnego centrum systemu, jak i menedżera maszyn wirtualnych w centrum systemu. To zaktualizowane rozwiązanie nie ma tych zależności.


## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
|---|---|---|
| [Agenci dla systemu Windows](../../azure-monitor/platform/agent-windows.md) | Tak | Rozwiązanie zbiera informacje o pojemności i wydajności danych od agentów systemu Windows. |
| [Agenci dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nie    | Rozwiązanie nie zbiera informacji o pojemności i wydajności danych od bezpośrednich agentów systemu Linux.|
| [Grupa zarządzająca SCOM](../../azure-monitor/platform/om-agents.md) | Tak |Rozwiązanie zbiera dane o pojemności i wydajności od agentów z połączonej grupy zarządzania SCOM. Bezpośrednie połączenie z agentem SCOM z usługą Log Analytics nie jest wymagane.|
| [Konto magazynu platformy Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure Storage nie zawiera danych o pojemności i wydajności.|

## <a name="prerequisites"></a>Wymagania wstępne

- Agenci systemu Windows lub programu Operations Manager muszą być zainstalowane na hostach funkcji Hyper-V systemu Windows Server 2012 lub nowszych, a nie na maszynach wirtualnych.


## <a name="configuration"></a>Konfigurowanie

Wykonaj następujący krok, aby dodać rozwiązanie Capacity and Performance do obszaru roboczego.

- Dodaj rozwiązanie pojemności i wydajności do obszaru roboczego usługi Log Analytics, korzystając z procesu opisanego w [dodatku rozwiązań analizy dzienników z Galerii rozwiązań.](../../azure-monitor/insights/solutions.md)

## <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania SCOM jest połączona z obszarem roboczym usługi Log Analytics, następujące pakiety administracyjne zostaną zainstalowane w aplikacji SCOM po dodaniu tego rozwiązania. Nie jest wymagana żadna konfiguracja ani obsługa tych pakietów administracyjnych.

- Microsoft.IntelligencePacks.CapacityWydajność

Wydarzenie z 1201 roku przypomina:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Po zaktualizowaniu rozwiązania Capacity and Performance numer wersji ulegnie zmianie.

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../../azure-monitor/platform/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązania Pojemność i wydajność do obszaru roboczego, pojemność i wydajność jest dodawany do pulpitu nawigacyjnego przegląd. Ten kafelek wyświetla liczbę aktualnie aktywnych hostów funkcji Hyper-V oraz liczbę aktywnych maszyn wirtualnych, które były monitorowane przez wybrany okres czasu.

![Płytka Pojemność i wydajność](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Przegląd wykorzystania

Kliknij kafelek Pojemność i wydajność, aby otworzyć pulpit nawigacyjny Pojemności i Wydajności. Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera do dziesięciu elementów spełniających jej kryteria dla podanego zakresu i przedziału czasu. Można uruchomić wyszukiwanie w dzienniku, które zwróci wszystkie rekordy. W tym celu kliknij przycisk **Zobacz wszystko** na dole kolumny lub kliknij nagłówek kolumny.

- **Hostów**
    - **Wykorzystanie procesora CPU hosta** Pokazuje graficzny trend wykorzystania procesora CPU komputerów hosta i listę hostów, na podstawie wybranego okresu. Umieść wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie. Kliknij wykres, aby wyświetlić więcej szczegółów w wyszukiwaniu dzienników. Kliknij dowolną nazwę hosta, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika procesora CPU dla hostowanych maszyn wirtualnych.
    - **Wykorzystanie pamięci hosta** Pokazuje graficzny trend wykorzystania pamięci komputerów hosta i listę hostów na podstawie wybranego okresu. Umieść wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie. Kliknij wykres, aby wyświetlić więcej szczegółów w wyszukiwaniu dzienników. Kliknij dowolną nazwę hosta, aby otworzyć wyszukiwanie dzienników i wyświetlić szczegóły licznika pamięci hostowanych maszyn wirtualnych.
- **Maszyny wirtualne**
    - **Wykorzystanie procesora maszyn wirtualnych** Pokazuje graficzny trend wykorzystania procesora CPU maszyn wirtualnych i listę maszyn wirtualnych, na podstawie wybranego okresu. Umieść wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie dla 3 najlepszych maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów w wyszukiwaniu dzienników. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie dzienników i wyświetlić zagregowane szczegóły licznika procesora CPU dla maszyny Wirtualnej.
    - **Wykorzystanie pamięci maszyny Wirtualnej** Pokazuje graficzny trend wykorzystania pamięci maszyn wirtualnych i listę maszyn wirtualnych na podstawie wybranego okresu. Umieść wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie dla 3 najlepszych maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów w wyszukiwaniu dzienników. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie dzienników i wyświetlić szczegóły licznika pamięci zagregowanej dla maszyny Wirtualnej.
    - **We/Wy całkowitego dysku maszyny Wirtualnej** Pokazuje trend graficzny całkowitych we/wy dysku dla maszyn wirtualnych i listę maszyn wirtualnych z usługami We/Wy dla każdego z nich, na podstawie wybranego okresu. Umieść wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie dla 3 najlepszych maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów w wyszukiwaniu dzienników. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika zagregowanych we/wy dysku dla maszyny Wirtualnej.
    - **Całkowita przepływność dysku maszyny Wirtualnej** Pokazuje trend graficzny całkowitej przepływności dysku dla maszyn wirtualnych i listę maszyn wirtualnych z całkowitą przepływnością dysku dla każdego z nich, na podstawie wybranego okresu. Umieść wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie dla 3 najlepszych maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów w wyszukiwaniu dzienników. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć wyszukiwanie dzienników i wyświetlić zagregowane szczegóły licznika przepływności całego dysku dla maszyny Wirtualnej.
- **Klastrowane udostępnione woluminy**
    - **Całkowita przepustowość** Pokazuje sumę zarówno odczytów, jak i zapisów na klastrowanych woluminach udostępnionych.
    - **Całkowita liczba we/wy** Pokazuje sumę operacji wejścia/wyjścia na sekundę na klastrowanych woluminach udostępnionych.
    - **Całkowite opóźnienie** Pokazuje całkowite opóźnienie klastrowanych woluminów udostępnionych.
- **Gęstość hosta** Górny kafelek pokazuje całkowitą liczbę hostów i maszyn wirtualnych dostępnych dla rozwiązania. Kliknij górny kafelek, aby wyświetlić dodatkowe szczegóły w wyszukiwaniu w dzienniku. Wyświetla również listę wszystkich hostów i liczbę obsługiwanych maszyn wirtualnych. Kliknij hosta, aby przejść do szczegółów na maszynie Wirtualnej wyniki wyszukiwania dziennika.


![blok hostów pulpitu nawigacyjnego](./media/capacity-performance/dashboard-hosts.png)

![blok maszyn wirtualnych na desce rozdzielczej](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Ocena wydajności

Środowiska obliczeniowe produkcyjne różnią się znacznie w zależności od organizacji. Ponadto obciążenia pojemności i wydajności może zależeć od sposobu pracy maszyn wirtualnych i co uważasz za normalne. Konkretne procedury ułatwiające pomiar wydajności prawdopodobnie nie będą miały zastosowania do danego środowiska. Tak więc, bardziej uogólnione wskazówki nakazowe lepiej nadają się do pomocy. Firma Microsoft publikuje wiele artykułów z nakazami, które ułatwią pomiar wydajności.

Podsumowując, rozwiązanie zbiera dane o pojemności i wydajności z różnych źródeł, w tym liczników wydajności. Użyj tych danych pojemności i wydajności, które są prezentowane na różnych powierzchniach w rozwiązaniu i porównaj wyniki z wynikami w artykule [Pomiar wydajności funkcji Hyper-V.](https://msdn.microsoft.com/library/cc768535.aspx) Chociaż artykuł został opublikowany jakiś czas temu, metryki, uwagi i wytyczne są nadal ważne. Artykuł zawiera łącza do innych przydatnych zasobów.


## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwanie dzienników danych dotyczących pojemności i wydajności zebranych i obliczonych przez to rozwiązanie.


| Zapytanie | Opis |
|:--- |:--- |
| Wszystkie konfiguracje pamięci hosta | Perf &#124; gdzie ObjectName == "Pojemność i wydajność" i Nazwa licznika == "Host Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| Wszystkie konfiguracje pamięci maszyn wirtualnych | Perf &#124; gdzie ObjectName == "Pojemność i wydajność" i Nazwa licznika == "VM Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| Podział całkowitych we/wy dysków dla wszystkich maszyn wirtualnych | Perf &#124; gdzie ObjectName == "Pojemność i wydajność" i (Nazwa licznika == "VHD odczyty/s" lub Nazwa licznika == "VHD Zapisy/s") &#124; podsumuj AggregatedValue = avg(CounterValue) przez bin(TimeGenerated, 1h), Nazwa licznika, Nazwa wystąpienia |
| Podział całkowitej przepływności dysku na wszystkie maszyny wirtualne | Perf &#124; gdzie ObjectName == "Pojemność i wydajność" i (Nazwa licznika == "VHD Read MB/s" lub CounterName == "VHD Write MB/s") &#124; podsumuj AggregatedValue = avg(CounterValue) przez bin(TimeGenerated, 1h), Nazwa zastawu, Nazwa wystąpienia |
| Podział całkowitych we/wy dla wszystkich csv | Perf &#124; gdzie ObjectName == "Capacity and Performance" i (CounterName == "CSV Reads/s" lub CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Podział całkowitej przepustowości we wszystkich csv | Perf &#124; gdzie ObjectName == "Capacity and Performance" i (CounterName == "CSV Reads/s" lub CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| Podział całkowitego opóźnienia we wszystkich csv | Perf &#124; gdzie ObjectName == "Capacity and Performance" i (CounterName == "CSV Read Latency" lub CounterName == "CSV Write Latency") &#124; podsumuj AggregatedValue = avg(CounterValue) przez bin(TimeGenerated, 1h), Nazwa przeciwstawowa, Nazwa wystąpienia |


## <a name="next-steps"></a>Następne kroki
* Użyj [wyszukiwania dzienników w usłudze Log Analytics,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe dane dotyczące pojemności i wydajności.
