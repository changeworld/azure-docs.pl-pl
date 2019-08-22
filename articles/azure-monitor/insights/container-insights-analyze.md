---
title: Monitorowanie wydajności klastra AKS przy użyciu usługi Azure Monitor dla kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wyświetlać i analizować dane wydajności i dzienników z usługą Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 154848c33960cb78b10c58e7a39ddec669d4fae0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872987"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Zrozumienie wydajności klastra AKS przy użyciu usługi Azure Monitor dla kontenerów
Za pomocą Azure Monitor dla kontenerów można użyć wykresów wydajności i stanu kondycji do monitorowania obciążenia klastrów usługi Azure Kubernetes Service (AKS) z dwóch perspektyw. Można monitorować bezpośrednio z klastra AKS lub monitorować wszystkie klastry AKS w ramach subskrypcji z Azure Monitor. Wyświetlanie Azure Container Instances jest również możliwe podczas monitorowania określonego klastra AKS.

Ten artykuł pomaga zrozumieć dwie perspektywy i jak Azure Monitor pomaga szybko ocenić, zbadać i rozwiązać wykryte problemy.

Informacje o sposobie włączania Azure Monitor dla kontenerów znajdują się w sekcji Dołączanie [Azure monitor dla kontenerów](container-insights-onboard.md).

Azure Monitor zawiera widok z obsługą wielu klastrów, który pokazuje stan kondycji wszystkich monitorowanych klastrów AKS z systemem Linux i Windows Server 2019 wdrożonych w grupach zasobów w Twoich subskrypcjach. Przedstawia wykryte klastry AKS, które nie są monitorowane przez rozwiązanie. Możesz natychmiast zrozumieć kondycję klastra, a w tym miejscu możesz przejść do szczegółów na stronie wydajność węzła i kontrolera lub przejść do sekcji wykresy wydajności dla klastra. W przypadku klastrów AKS, które zostały odnalezione i zidentyfikowane jako niemonitorowane, można je włączyć w dowolnym momencie. 

Główne różnice w monitorowaniu klastra z systemem Windows Server z Azure Monitor dla kontenerów w porównaniu z klastrem systemu Linux są następujące:

- Metryka RSS pamięci nie jest dostępna dla węzła i kontenerów systemu Windows.
- Informacje o pojemności magazynu dyskowego nie są dostępne dla węzłów systemu Windows.
- Obsługa dzienników na żywo jest dostępna z wyjątkiem dzienników kontenera systemu Windows.
- Monitorowane są tylko środowiska pod środowiskiem, a nie środowiska Docker.
- W wersji zapoznawczej obsługiwane są maksymalnie 30 kontenerów systemu Windows Server. To ograniczenie nie dotyczy kontenerów systemu Linux. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Wyświetlanie wielu klastrów z usługi Azure Monitor

Aby wyświetlić stan kondycji wszystkich wdrożonych klastrów AKS, wybierz pozycję **monitor** w lewym okienku w Azure Portal. W obszarze **Insights** zaznacz **kontenery**. 

![Przykład pulpitu nawigacyjnego wielu klastrów w usłudze Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Na karcie **monitorowane klastry** przedstawiono następujące informacje:

- Liczba klastrów w stanie krytycznym lub złej kondycji, a w przypadku których wiele jest w dobrej kondycji lub nie jest zgłaszanych (nazywanych nieznanym stanem).
- Czy wszystkie wdrożenia [aparatu Azure Kubernetes Engine (AKS-Engine)](https://github.com/Azure/aks-engine) są w dobrej kondycji.
- Liczba węzłów i zasobników użytkowników i systemów wdrożonych na klaster.
- Ilość dostępnego miejsca na dysku oraz ewentualny problem z pojemnością.

Stany kondycji, uwzględnione są następujące: 

* **Dobra kondycja**: Nie wykryto żadnych problemów dla maszyny wirtualnej i działa ona zgodnie z wymaganiami. 
* **Krytyczne**: Wykryto co najmniej jeden krytyczny problem, który należy rozwiązać, aby przywrócić normalny stan działania zgodnie z oczekiwaniami.
* **Ostrzeżenie**: Wykryto co najmniej jeden problem, który musi zostać rozwiązany lub kondycja może stać się krytyczna.
* **Nieznany**: Jeśli usługa nie mogła nawiązać połączenia z węzłem lub pod, stan zmieni się na nieznany.
* **Nie znaleziono**: Usunięto obszar roboczy, grupę zasobów lub subskrypcję zawierającą obszar roboczy tego rozwiązania.
* Brak **autoryzacji**: Użytkownik nie ma wymaganych uprawnień do odczytu danych w obszarze roboczym.
* **Błąd**: Wystąpił błąd podczas próby odczytania danych z obszaru roboczego.
* **Błędna konfiguracja**: Azure Monitor dla kontenerów nie zostały poprawnie skonfigurowane w określonym obszarze roboczym.
* **Brak danych**: Dane nie zostały zgłoszone do obszaru roboczego w ciągu ostatnich 30 minut.

Stan kondycji oblicza ogólny stan klastra jako *najgorszy* z trzech stanów z jednym wyjątkiem. Jeśli którykolwiek z trzech stanów jest nieznany, ogólny stan klastra pokazuje **nieznane**. 

W poniższej tabeli przedstawiono podział obliczeń kontrolujących Stany kondycji monitorowanego klastra w widoku wiele klastrów.

| |State |Dostępność |  
|-------|-------|-----------------|  
|**Użytkownik pod**| | |  
| |W dobrej kondycji |100% |  
| |Ostrzeżenie |90 - 99% |  
| |Krytyczny |< 90% |  
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |  
|**System pod**| | |  
| |W dobrej kondycji |100% |
| |Ostrzeżenie |ND |
| |Krytyczny |< 100% |
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |
|**Node** | | |
| |W dobrej kondycji |> 85% |
| |Ostrzeżenie |60 - 84% |
| |Krytyczny |< 60% |
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |

Z listy klastrów możesz przejść do szczegółów na stronie **klaster** , wybierając nazwę klastra. Następnie przejdź do strony wydajność **węzłów** , wybierając pakiet zbiorczy węzłów w kolumnie **węzły** dla danego klastra. Możesz też przejść do szczegółów na stronie wydajność **kontrolerów** , wybierając zestawienie z kolumną **zasobników użytkownika** lub **system** .  

## <a name="view-performance-directly-from-an-aks-cluster"></a>Wyświetl wydajność bezpośrednio z klastra usługi AKS

Dostęp do Azure Monitor dla kontenerów jest dostępny bezpośrednio w klastrze AKS, wybierając pozycję **szczegółowe** dane w okienku po lewej stronie. Informacje o klastrze AKS są podzielone na cztery perspektywy:

- Klaster
- Węzły 
- Kontrolery 
- Containers

Zostanie otwarta strona domyślna, gdy zostanie > wybrany**klaster**usługi Insights. Cztery liniowe wykresy wydajnościowe przedstawiają kluczowe metryki wydajności klastra. 

![Przykładowe wykresy wydajności na karcie klastra](./media/container-insights-analyze/containers-cluster-perfview.png)

Na wykresach wydajności są wyświetlane cztery metryki wydajności:

- Użycie procesora CPU przez węzeł: **&nbsp;%** Zagregowana perspektywa użycia procesora CPU dla całego klastra. Aby przefiltrować wyniki dla zakresu czasu, wybierz opcję **średnie**, **minimum**, **pięćdziesiąt**, **90**, **używany 95.** lub **Max** w selektorze percentyly powyżej wykresu. Filtry mogą być używane pojedynczo lub łącznie. 
- Użycie pamięci przez węzeł: **&nbsp;%** Zagregowana perspektywa wykorzystania pamięci dla całego klastra. Aby przefiltrować wyniki dla zakresu czasu, wybierz opcję **średnie**, **minimum**, **pięćdziesiąt**, **90**, **używany 95.** lub **Max** w selektorze percentyly powyżej wykresu. Filtry mogą być używane pojedynczo lub łącznie. 
- **Liczba węzłów**: Liczba węzłów i stan z Kubernetes. Stanem reprezentowanego węzła klastra są łącznie, gotowe i niegotowe. Można je filtrować pojedynczo lub łączyć w selektorze powyżej wykresu. 
- **Liczba aktywnych pod**: Liczba pod i stan z Kubernetes. Stany reprezentowanego obszaru są całkowite, oczekujące, uruchomione, nieznane, zakończone powodzeniem lub niepowodzeniem. Można je filtrować pojedynczo lub łączyć w selektorze powyżej wykresu. 

Użyj klawiszy strzałek w lewo i w prawo, aby przechodzić przez każdy punkt danych na wykresie. Użyj klawiszy strzałek w górę i w dół, aby przechodzić do kolejnych wierszy percentylu. Wybierz ikonę pinezki w prawym górnym rogu dowolnego z wykresów, aby przypiąć wybrany wykres do ostatniego wyświetlonego pulpitu nawigacyjnego platformy Azure. Z poziomu pulpitu nawigacyjnego można zmieniać rozmiar wykresu i zmienić jego położenie. Wybranie wykresu z pulpitu nawigacyjnego przekierowuje Cię do Azure Monitor kontenerów i ładuje prawidłowy zakres i widok.

Azure Monitor for Containers obsługują również [Eksploratora metryk](../platform/metrics-getting-started.md)Azure monitor, w którym można tworzyć własne wykresy wykresów, skorelować i badać trendy oraz przypinać do pulpitów nawigacyjnych. W Eksploratorze metryk można również użyć kryteriów ustawionych do wizualizacji metryk jako podstawy [reguły alertu opartej na metrykach](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Wyświetlanie metryk kontenera w Eksploratorze metryk

W Eksploratorze metryk można wyświetlić zagregowane metryki dotyczące węzła i użycia z Azure Monitor dla kontenerów. W poniższej tabeli zestawiono szczegółowe informacje ułatwiające zrozumienie sposobu używania wykresów metryk do wizualizacji metryk kontenera.

|Przestrzeń nazw | Metryka |
|----------|--------|
| Szczegółowe informacje. kontenery/węzły | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| Szczegółowe informacje. kontenery/zasobniki | |
| | PodCount |

Można [podzielić](../platform/metrics-charts.md#apply-splitting-to-a-chart) metrykę, aby wyświetlić ją w wymiarze, i wizualizować, jak różne segmenty są porównywane ze sobą. Dla węzła można podzielić wykres na wymiar *hosta* . Z poziomu usługi można podzielić ją na segmenty według następujących wymiarów:

* Kontroler
* Kubernetes przestrzeń nazw
* Węzeł
* Etap

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizowanie węzłów, kontrolerów i kondycji kontenera

Po przełączeniu do kart **węzły**, **Kontrolery**i **kontenery** okienko właściwości zostanie automatycznie wyświetlone po prawej stronie. Wyświetla właściwości wybranego elementu, w tym etykiety zdefiniowane do organizowania obiektów Kubernetes. Po wybraniu węzła systemu Linux w sekcji **pojemność dysku lokalnego** wyświetlana jest również ilość dostępnego miejsca na dysku oraz wartość procentowa użyta dla każdego dysku prezentowanego w węźle. **>>** Wybierz łącze w okienku, aby wyświetlić lub ukryć okienko.

![Przykładowe okienka właściwości perspektyw Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Podczas rozwijania obiektów w hierarchii, na podstawie aktualizacji okienka właściwości w obiekcie, który został wybrany. W okienku można także wyświetlać zdarzenia Kubernetes ze wstępnie zdefiniowanymi przeszukiwaniem dzienników, wybierając łącze **Wyświetl dzienniki zdarzeń Kubernetes** w górnej części okienka. Aby uzyskać więcej informacji o sposobie wyświetlania danych dziennika Kubernetes, zobacz [dzienniki wyszukiwania do analizowania danych](container-insights-log-search.md). Podczas przeglądania zasobów klastra można zobaczyć dzienniki kontenerów i zdarzenia w czasie rzeczywistym. Aby uzyskać więcej informacji na temat tej funkcji oraz konfiguracji wymaganej do udzielenia i kontroli dostępu, zobacz [Wyświetlanie dzienników w czasie rzeczywistym za pomocą Azure monitor dla kontenerów](container-insights-live-logs.md). 

Użyj opcji **+ Dodaj filtr** w górnej części strony, aby odfiltrować wyniki widoku według **usługi**, **węzła**, **przestrzeni nazw**lub **puli węzłów**. Po wybraniu zakresu filtru wybierz jedną z wartości wyświetlanych w polu **Wybierz wartości** . Po skonfigurowaniu filtru jest on stosowany globalnie podczas wyświetlania dowolnej perspektywy klastra AKS. Formuła obsługuje tylko znaku równości. Możesz dodać dodatkowe filtry na podstawie pierwszy z nich w celu dalszego zawężenia wyników. Na przykład, jeśli określisz **węzeł**Filtruj według, możesz wybrać tylko **usługę** lub **przestrzeń nazw** dla drugiego filtru.

![Przykład przy użyciu filtru, aby zawęzić wyniki](./media/container-insights-analyze/add-filter-option-01.png)

Określanie filtru na jednej karcie jest kontynuowane po wybraniu innej. Jest ona usuwana po wybraniu symbolu **x** obok określonego filtru. 

Przejdź do karty **węzły** i hierarchia wierszy jest zgodna z modelem obiektów Kubernetes, który rozpoczyna się od węzła w klastrze. Rozwiń węzeł, aby wyświetlić jeden lub więcej zasobników uruchomionych w węźle. Jeśli więcej niż jeden kontener jest zgrupowany na pod, są one wyświetlane jako ostatni wiersz w hierarchii. Można także zobaczyć, ile obciążeń związanych z różnymi firmami nie są uruchomione na hoście, Jeśli host ma wykorzystanie procesora lub pamięci.

![Przykład hierarchii węzłów Kubernetes w widoku wydajności](./media/container-insights-analyze/containers-nodes-view.png)

Kontenery systemu Windows Server, na których działa system operacyjny Windows Server 2019, są wyświetlane po wszystkich węzłach na liście z systemem Linux. Po rozwinięciu węzła systemu Windows Server można wyświetlić jeden lub więcej zasobników i kontenerów, które są uruchamiane w węźle. Po zaznaczeniu węzła w okienku właściwości zostanie wyświetlona informacja o wersji. Informacje o agencie są wykluczone, ponieważ w węzłach systemu Windows Server nie zainstalowano agenta. 

![Przykładowa hierarchia węzłów z węzłami systemu Windows Server na liście](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container Instances węzły wirtualne z systemem operacyjnym Linux są wyświetlane po ostatnim węźle klastra AKS na liście. Po rozszerzeniu Container Instances węzła wirtualnego można wyświetlić jeden lub więcej Container Instances i zasobników i kontenerów, które są uruchamiane w węźle. Metryki nie są zbierane i raportowane dla węzłów tylko dla zasobników.

![Przykładowa hierarchia węzła Usługa Container Instances, na liście](./media/container-insights-analyze/nodes-view-aci.png)

Z rozwiniętego węzła możesz przejść do szczegółów z lub kontenera, który jest uruchamiany w węźle, do kontrolera, aby wyświetlić dane wydajności odfiltrowane dla tego kontrolera. Wybierz wartość w kolumnie **kontroler** dla określonego węzła.
 
![Przykład przechodzenia do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-node-controller.png)

Wybierz pozycję Kontrolery lub kontenery w górnej części strony, aby przejrzeć stan i wykorzystanie zasobów dla tych obiektów. Aby przejrzeć użycie pamięci, na liście rozwijanej Metryka wybierz pozycję **pamięć RSS** lub **zestaw roboczy pamięci**. **Pamięć RSS** jest obsługiwany tylko w przypadku rozwiązania Kubernetes w wersji 1.8 i nowszych. W przeciwnym razie do wyświetlania wartości **Min&nbsp; %**  jako *NaN&nbsp;%* , który jest wartość typu danych liczbowych, który reprezentuje niezdefiniowany lub wartość wyniku.

**Zestaw roboczy pamięci** pokazuje zawartą pamięć i pamięć wirtualną (pamięć podręczną) oraz łączną zawartość używaną przez aplikację. W obszarze **RSS pamięci** jest wyświetlana tylko pamięć główna, która jest pamięcią rezydentną. Ta Metryka przedstawia rzeczywistą pojemność dostępnej pamięci.

![Widok wydajności węzłów kontenerów](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Domyślnie dane wydajności bazują na ostatnich sześciu godzinach, ale można zmienić okno przy użyciu opcji **TimeRange** w lewym górnym rogu. Można również filtrować wyniki w zakresie czasu, wybierając wartość **min**, **AVG**, **pięćdziesiąt**, **90**, **używany 95.** i **Max** w selektorze percentylu. 

![Wybór percentyl filtrowanie danych](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Po umieszczeniu wskaźnika myszy na wykresie słupkowym w kolumnie **trend** każdy pasek pokazuje użycie procesora lub pamięci, w zależności od tego, która Metryka została wybrana, w ciągu 15 minut. Po wybraniu wykresu trendu za pomocą klawiatury Użyj klawisza Alt + Page Up lub klawisza Alt + Page Down, aby przełączać poszczególne paski osobno. Te same szczegóły można uzyskać po umieszczeniu wskaźnika myszy na pasku.

![Przykład aktywowania wykresu słupkowego trendu](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

W następnym przykładzie dla pierwszego węzła na liście *AKS-nodepool1-* , wartość dla **kontenerów** wynosi 9. Ta wartość to pakiet zbiorczy całkowitej liczby wdrożonych kontenerów.

![Zestawienie kontenerów — przykład na węzeł](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Te informacje mogą pomóc w szybkim ustaleniu, czy masz odpowiednie równoważenie kontenerów między węzłami w klastrze. 

Informacje przedstawione podczas wyświetlania karty **węzły** są opisane w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa hosta. |
| Stan | Kubernetes widok stanu węzła. |
| Minimum&nbsp;%, średnia&nbsp;%, 50&nbsp;%, 90&nbsp;%, używany 95.&nbsp;%, maksimum&nbsp;%  | Średni procent węzła, w oparciu o procentowy podczas wybranego okresu. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max | Średnia wartość rzeczywista węzłów oparta na percentylu podczas wybranego czasu trwania. Średnia wartość jest mierzona na podstawie limitu procesora CPU/pamięci ustawionego dla węzła. W przypadku zasobników i kontenerów jest to średnia wartość raportowana przez hosta. |
| Containers | Liczba kontenerów. |
| Czas pracy | Reprezentuje czas, ponieważ węzeł uruchomiony lub został ponownie uruchomiony. |
| Kontroler | Tylko w przypadku kontenerów i zasobników. Pokazuje, który kontroler znajduje się w. Nie wszystkie zasobników są w kontrolerze, więc niektóre może być wyświetlany **n/d**. | 
| Minimum&nbsp;trendu%,&nbsp;średnia%,&nbsp;50%,&nbsp;90%,&nbsp;używany 95.%, maksimum&nbsp;% | Trend wykres słupkowy przedstawia procent metryki średni percentyl kontrolera. |

W selektorze, wybierz **kontrolerów**.

![Wybierz widok kontrolerów](./media/container-insights-analyze/containers-controllers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontrolerów, Container Instances kontrolery węzłów wirtualnych lub zasobniki wirtualnego węzła nie są połączone z kontrolerem.

![\<Nazwa > kontroleruje widok wydajności](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchia wierszy rozpoczyna się od kontrolera. Po rozszerzeniu kontrolera należy wyświetlić jeden lub więcej zasobników. Rozwiń węzeł pod, a w ostatnim wierszu jest wyświetlany kontener pogrupowany pod względem elementu. Na rozszerzonym kontrolerze możesz przejść do węzła, w którym jest uruchomiona, aby wyświetlić dane wydajności odfiltrowane dla tego węzła. Container Instances z kontrolerami nie połączono z kontrolerem na liście poniżej.

![Przykładowa hierarchia kontrolerów z zasobników Container Instances, na liście](./media/container-insights-analyze/controllers-view-aci.png)

Wybierz wartość w kolumnie **węzeł** dla określonego kontrolera.

![Przykład przechodzenia do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

Informacje wyświetlane podczas przeglądania kontrolerów są opisane w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| State | Stan zbiorczy kontenerów po zakończeniu działania z stanem takim jak *OK*, *przerwany*, Niepowodzenie, *zatrzymano*lub *wstrzymano*. Jeśli kontener jest uruchomiony, ale stan nie był prawidłowo wyświetlany lub nie został pobrany przez agenta i nie odpowiedział przez dłużej niż 30 minut, stan jest *nieznany*. Dodatkowe szczegóły ikony stanu znajdują się w poniższej tabeli.|
| Minimum&nbsp;%, średnia&nbsp;%, 50&nbsp;%, 90&nbsp;%, używany 95.&nbsp;%, maksimum&nbsp;%| Pakiet zbiorczy średnią średnią wartość procentową poszczególnych jednostek dla wybranej metryki i percentyl. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max  | Pakiet zbiorczy średni Procesora pamięci lub millicore wydajności kontenera dla wybranych percentyl. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Containers | Łączna liczba kontenerów dla kontrolera lub zasobników. |
| Ponowne uruchomienie | Zbiorcze informacje licznika ponowne uruchomienie z kontenerów. |
| Czas pracy | Reprezentuje czas od momentu uruchomienia kontenera. |
| Węzeł | Tylko w przypadku kontenerów i zasobników. Pokazuje, który kontroler znajduje się w. | 
| Minimum&nbsp;trendu%,&nbsp;średnia%,&nbsp;50%,&nbsp;90%,&nbsp;używany 95.%, maksimum&nbsp;% | Wykres słupkowy trend reprezentuje metryki średni percentyl kontrolera. |

Ikony w polu Stan wskazują stan online kontenerów.
 
| Ikona | Stan | 
|--------|-------------|
| ![Gotowe uruchomionej ikona stanu](./media/container-insights-analyze/containers-ready-icon.png) | Uruchamianie (gotowe)|
| ![Ikona stanu oczekiwania lub wstrzymania](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekuje lub wstrzymana|
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnio zgłoszone uruchomione, ale nie udzielono odpowiedzi przez ponad 30 minut|
| ![Ikona stanu powodzenia](./media/container-insights-analyze/containers-green-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|

Ikona stanu przedstawia liczbę oparte na zasobnik udostępnia. Pokazuje dwa najgorsze Stany i po umieszczeniu status Wyświetla stan pakietu zbiorczego z wszystkich zasobników w kontenerze. Jeśli nie ma stanu gotowości, wyświetlana jest wartość stanu **(0)** .

W selektorze, wybierz **kontenery**.

![Wybierz widok kontenerów](./media/container-insights-analyze/containers-containers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontenerów usługi Kubernetes na platformie Azure i usługi Azure Container Instances. 

![\<Nazwa > — Widok wydajności kontenerów](./media/container-insights-analyze/containers-containers-view.png)

Kontener można przejść pod lub węzeł, aby wyświetlić dane dotyczące wydajności przefiltrowana pod kątem tego obiektu. Wybierz wartość w kolumnie **pod** lub **węzeł** dla określonego kontenera.

![Przykład przechodzenia do szczegółów z węzła do kontenerów w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

Informacje wyświetlane podczas wyświetlania kontenerów są opisane w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| Stan | Stan kontenerów, jeśli istnieje. Dodatkowe szczegóły ikona stanu znajdują się w następnej tabeli.|
| Minimum&nbsp;%, średnia&nbsp;%, 50&nbsp;%, 90&nbsp;%, używany 95.&nbsp;%, maksimum&nbsp;% | Pakiet zbiorczy średnią wartość procentową poszczególnych jednostek dla wybranej metryki i percentyl. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max | Pakiet zbiorczy średni wydajność użycia CPU millicore lub pamięci przez kontener dla wybranych percentyl. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Zasobnik | Kontener, w którym znajduje się pod.| 
| Węzeł |  Węzeł, w którym znajduje się kontener. | 
| Ponowne uruchomienie | Reprezentuje czas od momentu uruchomienia kontenera. |
| Czas pracy | Reprezentuje czas, ponieważ kontener został uruchomiony lub ponownie uruchomiony. |
| Minimum&nbsp;trendu%,&nbsp;średnia%,&nbsp;50%,&nbsp;90%,&nbsp;używany 95.%, maksimum&nbsp;% | Trend wykres słupkowy przedstawia procent metryki średni percentyl kontenera. |

Ikony w polu Stan wskazują stan online, zgodnie z opisem w poniższej tabeli.
 
| Ikona | Stan |  
|--------|-------------|  
| ![Gotowe uruchomionej ikona stanu](./media/container-insights-analyze/containers-ready-icon.png) | Uruchamianie (gotowe)|  
| ![Ikona stanu oczekiwania lub wstrzymania](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekuje lub wstrzymana|  
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|  
| ![Ikona stanu zakończone](./media/container-insights-analyze/containers-terminated-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|  
| ![Ikona stanu nie powiodło się](./media/container-insights-analyze/containers-failed-icon.png) | Stan niepowodzenia |  

## <a name="workbooks"></a>Skoroszyty

Skoroszyty łączą tekst, [kwerendy dzienników](../log-query/query-language.md), [metryki](../platform/data-platform-metrics.md)i parametry w rozbudowanych raportach interaktywnych. Skoroszyty są edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Azure Monitor kontenerów zawiera cztery skoroszyty umożliwiające rozpoczęcie pracy:

- **Pojemność dysku**: Przedstawia wykresy interaktywnego użycia dysku dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

    - Procent użycia dysku dla wszystkich dysków.
    - Wolne miejsce na dysku dla wszystkich dysków.
    - Siatka pokazująca dysk każdego węzła, jego procent zajętego miejsca, Trend procentu zajętego miejsca, wolne miejsce na dysku (GiB) i trend wolnego miejsca na dysku (GiB). Gdy wiersz jest zaznaczony w tabeli, procent zajętego miejsca i wolnego miejsca na dysku (GiB) jest pokazywany poniżej wiersza. 

- **We/wy dysku**: Przedstawia wykresy interaktywnego wykorzystania dysku dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

    - Dyskowe operacje we/wy są sumowane na wszystkich dyskach przez odczyt bajtów/s, liczba bajtów/s oraz trendy odczytu i zapisu bajtów/s.
    - Osiem wykresów wydajnościowych przedstawia kluczowe wskaźniki wydajności, które pomagają mierzyć i identyfikować wąskie gardła dyskowych operacji we/wy.

- **Kubelet**: Obejmuje dwie siatki pokazujące statystyki operacyjne węzła klucza:

    - Przegląd według siatki węzła podsumowuje całkowitą operację, łączną liczbę błędów i operacji zakończonych powodzeniem według procentu i trendu dla każdego węzła.
    - Przegląd według typu operacji podsumowuje dla każdej operacji łączną operację, łączną liczbę błędów i operacji zakończonych powodzeniem według procentu i trendu.

- **Sieć**: Przedstawia wykresy interaktywnego wykorzystania sieci dla każdej karty sieciowej każdego węzła, a siatka przedstawia kluczowe wskaźniki wydajności, które ułatwiają mierzenie wydajności kart sieciowych.

Możesz uzyskać dostęp do tych skoroszytów, wybierając je z listy rozwijanej **Wyświetl skoroszyty** .

![Lista rozwijana Wyświetl skoroszyty](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [tworzenie alertów wydajności za pomocą Azure monitor dla kontenerów](container-insights-alerts.md) , aby dowiedzieć się, jak tworzyć alerty dotyczące wysokiego użycia procesora i pamięci w celu obsługi procesów i procedur operacyjnych DevOps.
- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby wyświetlić wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do alertów, wizualizacji lub analizowania klastrów.
