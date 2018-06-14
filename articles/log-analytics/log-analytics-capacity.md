---
title: Pojemność i wydajność rozwiązania Azure Log Analytics | Dokumentacja firmy Microsoft
description: Użyj rozwiązania pojemność i wydajność w analizy dzienników ułatwią zrozumienie wydajności serwerów funkcji Hyper-V.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: db38678a05afbc764dec20f2a475e00856a1aeee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170037"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Planowanie pojemności maszyn wirtualnych funkcji Hyper-V z rozwiązaniem pojemność i wydajność (wersja zapoznawcza)

![Symbol pojemność i wydajność](./media/log-analytics-capacity/capacity-solution.png)

> [!NOTE]
> Pojemność i wydajność rozwiązania jest przestarzała.  Klienci, którzy zainstalowali rozwiązania można nadal z niego korzystać, ale pojemność i wydajność, nie można dodać do żadnych nowych obszarów roboczych.

Rozwiązanie pojemność i wydajność w analizy dzienników umożliwia pomaga w zrozumieniu wydajności serwerów funkcji Hyper-V. Rozwiązanie zapewnia wgląd w środowisku funkcji Hyper-V, pokazując całkowitego wykorzystania (Procesora, pamięci i dysku) z hostów i maszyn wirtualnych uruchomionych na tych hostach funkcji Hyper-V. Metryki są zbierane dla Procesora, pamięci i dysków we wszystkich hostów i maszyn wirtualnych uruchomionych na tych.

Rozwiązanie:

-   Pokazuje hostów o najwyższej i najniższej wykorzystanie Procesora i pamięci
-   Pokazuje maszyn wirtualnych o najwyższej i najniższej wykorzystanie Procesora i pamięci
-   Pokazuje maszyn wirtualnych o najwyższej i najniższej wykorzystanie IOPS i przepływności
-   Które maszyny wirtualne są uruchomione na hostach, które zawiera
-   Pokazuje top dysków o wysokiej przepływności, IOPS i opóźnienia w udostępnionych woluminów klastra
- Umożliwia dostosowywanie i filtrowanie na podstawie grup

> [!NOTE]
> Poprzednia wersja pojemność i wydajność rozwiązania o nazwie Zarządzanie wydajnością wymagane zarówno System Center Operations Manager i System Center Virtual Machine Manager. To rozwiązanie zaktualizowany nie ma tych zależności.


## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
|---|---|---|
| [Agenci dla systemu Windows](log-analytics-windows-agent.md) | Yes | Rozwiązania zbiera informacje o danych pojemność i wydajność z agentów systemu Windows. |
| [Agenci dla systemu Linux](log-analytics-linux-agents.md) | Nie    | Rozwiązanie nie zbiera pojemność i wydajność, informacje o danych z bezpośredniej agentów systemu Linux.|
| [Grupa zarządzania programu SCOM](log-analytics-om-agents.md) | Yes |Rozwiązania zbiera dane pojemność i wydajność z agentów w podłączonej grupy zarządzania SCOM. Bezpośrednie połączenie z agenta programu SCOM Log Analytics nie jest wymagane.|
| [Konto usługi Azure Storage](log-analytics-azure-storage.md) | Nie | Usługa Azure storage nie zawiera danych pojemność i wydajność.|

## <a name="prerequisites"></a>Wymagania wstępne

- W systemie Windows Server 2012 lub nowszej hosty funkcji Hyper-V, nie maszyny wirtualne muszą zostać zainstalowane z systemem Windows lub agenty programu Operations Manager.


## <a name="configuration"></a>Konfigurowanie

Wykonaj następujący krok, aby dodać pojemność i wydajność rozwiązania do swojego obszaru roboczego.

- Dodaj rozwiązanie pojemność i wydajność do obszaru roboczego analizy dzienników przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).

## <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupę zarządzania programu SCOM jest podłączony do swojego obszaru roboczego analizy dzienników, następnie następujące pakiety administracyjne będą instalowani w SCOM po dodaniu tego rozwiązania. Nie jest wymagana żadna konfiguracja ani obsługa tych pakietów administracyjnych.

- Microsoft.IntelligencePacks.CapacityPerformance

Podobne zdarzenia 1201:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Po zaktualizowaniu pojemność i wydajność rozwiązania zmieni się numer wersji.

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](log-analytics-om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu pojemność i wydajność rozwiązania do swojego obszaru roboczego pojemność i wydajność, jest dodawana do pulpitu nawigacyjnego przeglądu. Ten Kafelek jest wyświetlana liczba Liczba aktywnych hostów funkcji Hyper-V i wybrana liczba aktywnych maszyn wirtualnych, monitorowane w danym okresie.

![Kafelek pojemność i wydajność](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>Przegląd wykorzystania

Kliknij Kafelek pojemność i wydajność, aby otworzyć pulpit nawigacyjny pojemność i wydajność. Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera do dziesięciu elementów spełniających jej kryteria dla podanego zakresu i przedziału czasu. Można uruchomić wyszukiwanie w dzienniku, które zwróci wszystkie rekordy. W tym celu kliknij przycisk **Zobacz wszystko** na dole kolumny lub kliknij nagłówek kolumny.

- **Hosty**
    - **Użycie procesora CPU hosta** zawiera graficzny trend wykorzystania procesora CPU hostów oraz listę hostów, w oparciu o wybrany okres czasu. Umieść wskaźnik myszy wykres liniowy, aby wyświetlić szczegóły dla określonego punktu w czasie. Kliknij na wykresie, aby wyświetlić więcej szczegółów w dzienniku wyszukiwania. Kliknij dowolną nazwę hosta Otwórz dziennik wyszukiwania i Wyświetl szczegóły licznika procesora CPU dla obsługiwanych maszyn wirtualnych.
    - **Użycie pamięci hosta** zawiera graficzny trend wykorzystania pamięci hostów oraz listę hostów, w oparciu o wybrany okres czasu. Umieść wskaźnik myszy wykres liniowy, aby wyświetlić szczegóły dla określonego punktu w czasie. Kliknij na wykresie, aby wyświetlić więcej szczegółów w dzienniku wyszukiwania. Kliknij dowolną nazwę hosta, można otworzyć dziennika wyszukiwania oraz szczegóły licznika pamięci dla maszyn wirtualnych w hostowanej.
- **Virtual Machines**
    - **Użycie procesora CPU VM** zawiera graficzny trend wykorzystania procesora CPU maszyn wirtualnych oraz listę maszyn wirtualnych w wybranym okresie. Umieść wskaźnik myszy wykres liniowy, aby wyświetlić szczegóły dla określonego punktu w czasie top 3 maszyn wirtualnych. Kliknij na wykresie, aby wyświetlić więcej szczegółów w dzienniku wyszukiwania. Kliknij dowolną nazwę maszyny Wirtualnej do otwierania dziennik wyszukiwania i przeglądania zagregowane szczegóły licznika Procesora dla maszyny Wirtualnej.
    - **Użycie pamięci maszyny Wirtualnej** zawiera graficzny trend wykorzystania pamięci maszyn wirtualnych oraz listę maszyn wirtualnych w wybranym okresie. Umieść wskaźnik myszy wykres liniowy, aby wyświetlić szczegóły dla określonego punktu w czasie top 3 maszyn wirtualnych. Kliknij na wykresie, aby wyświetlić więcej szczegółów w dzienniku wyszukiwania. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć dziennika wyszukiwania i wyświetlić szczegóły licznika zagregowane pamięci dla maszyny Wirtualnej.
    - **Całkowita liczba IOPS dysku maszyny Wirtualnej** zawiera graficzny trend dysku łączna wartość IOPS dla maszyn wirtualnych oraz listę maszyn wirtualnych z IOPS dla poszczególnych usług, oparte na wybrany okres czasu. Umieść wskaźnik myszy wykres liniowy, aby wyświetlić szczegóły dla określonego punktu w czasie top 3 maszyn wirtualnych. Kliknij na wykresie, aby wyświetlić więcej szczegółów w dzienniku wyszukiwania. Kliknij dowolną nazwę maszyny Wirtualnej, można otworzyć dziennika wyszukiwania i widoku zagregowane dysku IOPS licznika szczegółów dla maszyny Wirtualnej.
    - **Całkowita liczba przepływność dysku maszyny Wirtualnej** zawiera graficzny trend przepływność dysku dla maszyn wirtualnych oraz listę maszyn wirtualnych o przepływności dysku dla każdego z nich, oparte na wybrany okres czasu. Umieść wskaźnik myszy wykres liniowy, aby wyświetlić szczegóły dla określonego punktu w czasie top 3 maszyn wirtualnych. Kliknij na wykresie, aby wyświetlić więcej szczegółów w dzienniku wyszukiwania. Kliknij dowolną nazwę maszyny Wirtualnej, aby otworzyć dziennika wyszukiwania i wyświetlić szczegóły licznika przepływności zagregowanych całkowita dysku dla maszyny Wirtualnej.
- **Udostępnione woluminy klastra**
    - **Łączna liczba przepływności** zawiera sumę zarówno odczytuje i zapisuje na udostępnione woluminy klastra.
    - **Łączna liczba IOPS** zawiera sumę liczby operacji wejścia/wyjścia na sekundę na udostępnione woluminy klastra.
    - **Całkowity czas oczekiwania** przedstawia całkowity czas oczekiwania na udostępnione woluminy klastra.
- **Host gęstość** górny Kafelek zawiera całkowitą liczbę hostów i maszyn wirtualnych dostępne do rozwiązania. Kliknij przycisk najwyższego kafelka w celu wyświetlenia dodatkowych szczegółów w wyszukiwania dziennika. Zawiera także listę wszystkich hostów oraz liczbę maszyn wirtualnych, które są obsługiwane. Kliknij hosta, aby przejść do szczegółów w wyniki wyszukiwania dziennika do maszyny Wirtualnej.


![pulpit nawigacyjny hostów bloku](./media/log-analytics-capacity/dashboard-hosts.png)

![pulpit nawigacyjny bloku maszyny wirtualne](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>Ocena wydajności

Środowiska obliczeniowe o produkcyjnym znacznie z jednej organizacji różnią się od innego. Ponadto pojemność i wydajność obciążeń może być zależna od jak maszyny wirtualne są uruchomione, i co należy wziąć pod uwagę normalne. Szczegółowe procedury ułatwiające miar wydajności prawdopodobnie nie będą miały zastosowania w danym środowisku. Dlatego więcej uogólniony przetestowanego wskazówki lepiej nadaje się do pomocy. Firma Microsoft publikuje wiele artykułów wskazówki ułatwiające pomiaru wydajności.

Podsumowując, rozwiązania zbiera dane pojemności i wydajności z różnych źródeł, łącznie z liczników wydajności. Użyć tych danych pojemność i wydajność w różnych powierzchni w rozwiązaniu i porównaj wyniki do tych na [pomiaru wydajności w ramach funkcji Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) artykułu. Mimo że artykuł opublikowano pewien czas temu, metryki zagadnienia oraz wskazówki są nadal ważne. Ten artykuł zawiera łącza do inne przydatne zasoby.


## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

W poniższej tabeli przedstawiono przykładowy dziennik wyszukuje pojemność i wydajność danych zebranych i obliczana na podstawie tego rozwiązania.


| Zapytanie | Opis |
|:--- |:--- |
| Wszystkie konfiguracje pamięci hosta | Wydajności &#124; gdzie ObjectName == "Pojemności i wydajności" i CounterName == "Hosta przypisane pamięć (MB)" &#124; Podsumuj MB = avg(CounterValue) przez InstanceName |
| Wszystkie konfiguracje pamięci maszyny Wirtualnej | Wydajności &#124; gdzie ObjectName == "Pojemności i wydajności" i CounterName == "MB pamięci przypisana maszyny Wirtualnej" &#124; Podsumuj MB = avg(CounterValue) przez InstanceName |
| Podział całkowita IOPS dysku na wszystkich maszynach wirtualnych | Wydajności &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "Wirtualnego dysku twardego odczyty/s" albo CounterName == "Wirtualnego dysku twardego zapisy/s") &#124; Podsumuj AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |
| Podział całkowitą przepływność dysku na wszystkich maszynach wirtualnych | Wydajności &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "Wirtualnego dysku twardego odczytu MB/s" albo CounterName == "MB/s zapisu dysku VHD") &#124; Podsumuj AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |
| Podział całkowita IOPS przez wszystkie woluminy CSV | Wydajności &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "CSV odczyty/s" albo CounterName == "CSV zapisy/s") &#124; Podsumuj AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |
| Podział ogólnej przepustowości przez wszystkie woluminy CSV | Wydajności &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "CSV odczyty/s" albo CounterName == "CSV zapisy/s") &#124; Podsumuj AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |
| Podział łączny czas oczekiwania przez wszystkie woluminy CSV | Wydajności &#124; gdzie ObjectName == "Pojemności i wydajności" i (CounterName == "Opóźnienie odczytu CSV" lub CounterName == "Opóźnienie zapisu CSV") &#124; Podsumuj AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), CounterName, InstanceName |


## <a name="next-steps"></a>Kolejne kroki
* Użyj [Zaloguj wyszukiwania analizy dzienników](log-analytics-log-search.md) Aby wyświetlić szczegółowe dane pojemność i wydajność.
