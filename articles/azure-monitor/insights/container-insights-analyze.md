---
title: Monitorowanie wydajności klastra AKS za pomocą Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano, jak można wyświetlać i analizować dane dotyczące wydajności i dzienników za pomocą Azure Monitor dla kontenerów.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/17/2019
ms.openlocfilehash: a65951a90767acce5570244af8a9250845e12def
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554246"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Informacje o wydajności klastra AKS za pomocą Azure Monitor dla kontenerów
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

Zaloguj się do [portalu Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Widok z obsługą wiele klastrów z Azure Monitor

Aby wyświetlić stan kondycji wszystkich wdrożonych klastrów AKS, wybierz pozycję **monitor** w lewym okienku w Azure Portal. W sekcji **szczegółowe informacje** wybierz pozycję **kontenery**. 

![Przykład Azure Monitor pulpitu nawigacyjnego z obsługą kilku klastrów](./media/container-insights-analyze/azmon-containers-multiview.png)

Na karcie **monitorowane klastry** przedstawiono następujące informacje:

- Liczba klastrów w stanie krytycznym lub złej kondycji, a w przypadku których wiele jest w dobrej kondycji lub nie jest zgłaszanych (nazywanych nieznanym stanem).
- Czy wszystkie wdrożenia [aparatu Azure Kubernetes Engine (AKS-Engine)](https://github.com/Azure/aks-engine) są w dobrej kondycji.
- Liczba węzłów i zasobników użytkowników i systemów wdrożonych na klaster.
- Ilość dostępnego miejsca na dysku oraz ewentualny problem z pojemnością.

Uwzględnione są następujące stany kondycji: 

* **Dobra kondycja**: nie wykryto żadnych problemów dla maszyny wirtualnej i działa ona zgodnie z wymaganiami. 
* **Krytyczny**: wykryto co najmniej jeden krytyczny problem, który należy rozwiązać, aby przywrócić normalny stan działania zgodnie z oczekiwaniami.
* **Ostrzeżenie**: wykryto co najmniej jeden problem, który musi zostać rozwiązany lub kondycja może stać się krytyczna.
* **Nieznane**: Jeśli usługa nie mogła nawiązać połączenia z węzłem lub pod, stan zmieni się na nieznany.
* **Nie znaleziono**: Usunięto obszar roboczy, grupę zasobów lub subskrypcję zawierającą obszar roboczy tego rozwiązania.
* **Nieautoryzowane**: użytkownik nie ma wymaganych uprawnień do odczytu danych w obszarze roboczym.
* **Błąd**: Wystąpił błąd podczas próby odczytania danych z obszaru roboczego.
* **Błędna konfiguracja**: Azure monitor dla kontenerów nie zostały poprawnie skonfigurowane w określonym obszarze roboczym.
* **Brak danych**: dane nie zostały zgłoszone do obszaru roboczego w ciągu ostatnich 30 minut.

Stan kondycji oblicza ogólny stan klastra jako *najgorszy* z trzech stanów z jednym wyjątkiem. Jeśli którykolwiek z trzech stanów jest nieznany, ogólny stan klastra pokazuje **nieznane**. 

W poniższej tabeli przedstawiono podział obliczeń kontrolujących Stany kondycji monitorowanego klastra w widoku wiele klastrów.

| |Stan |Dostępność |  
|-------|-------|-----------------|  
|**Użytkownik pod**| | |  
| |W dobrej kondycji |100% |  
| |Ostrzeżenie |90 – 99% |  
| |Krytyczna |< 90% |  
| |Nieznane |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |  
|**System pod**| | |  
| |W dobrej kondycji |100% |
| |Ostrzeżenie |ND |
| |Krytyczna |< 100% |
| |Nieznane |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |
|**Node** | | |
| |W dobrej kondycji |> 85% |
| |Ostrzeżenie |60 – 84% |
| |Krytyczna |< 60% |
| |Nieznane |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |

Z listy klastrów możesz przejść do szczegółów na stronie **klaster** , wybierając nazwę klastra. Następnie przejdź do strony wydajność **węzłów** , wybierając pakiet zbiorczy węzłów w kolumnie **węzły** dla danego klastra. Możesz też przejść do szczegółów na stronie wydajność **kontrolerów** , wybierając zestawienie z kolumną **zasobników użytkownika** lub **system** .  

## <a name="view-performance-directly-from-an-aks-cluster"></a>Wyświetlanie wydajności bezpośrednio z klastra AKS

Dostęp do Azure Monitor dla kontenerów jest dostępny bezpośrednio w klastrze AKS, wybierając pozycję **szczegółowe** dane w okienku po lewej stronie. Informacje o klastrze AKS są podzielone na cztery perspektywy:

- Klaster
- Węzły 
- Rejestrowanie 
- Containers

Zostanie otwarta strona domyślna, gdy wybierzesz pozycję **szczegółowe informacje**  > **klastrze**. Cztery liniowe wykresy wydajnościowe przedstawiają kluczowe metryki wydajności klastra. 

![Przykładowe wykresy wydajności na karcie klaster](./media/container-insights-analyze/containers-cluster-perfview.png)

Na wykresach wydajności są wyświetlane cztery metryki wydajności:

- **Użycie procesora CPU przez węzeł &nbsp; %** : zagregowana perspektywa użycia procesora CPU dla całego klastra. Aby przefiltrować wyniki dla zakresu czasu, wybierz opcję **średnie**, **minimum**, **pięćdziesiąt**, **90**, **używany 95.** lub **Max** w selektorze percentyly powyżej wykresu. Filtry mogą być używane pojedynczo lub łącznie. 
- **Użycie pamięci przez węzeł &nbsp; %** : zagregowana perspektywa wykorzystania pamięci dla całego klastra. Aby przefiltrować wyniki dla zakresu czasu, wybierz opcję **średnie**, **minimum**, **pięćdziesiąt**, **90**, **używany 95.** lub **Max** w selektorze percentyly powyżej wykresu. Filtry mogą być używane pojedynczo lub łącznie. 
- **Liczba węzłów**: liczba węzłów i stan z Kubernetes. Stanem reprezentowanego węzła klastra są łącznie, gotowe i niegotowe. Można je filtrować pojedynczo lub łączyć w selektorze powyżej wykresu. 
- **Liczba aktywnych pod**: liczba i stan z Kubernetes. Stany reprezentowanego obszaru są całkowite, oczekujące, uruchomione, nieznane, zakończone powodzeniem lub niepowodzeniem. Można je filtrować pojedynczo lub łączyć w selektorze powyżej wykresu. 

Użyj klawiszy strzałek w lewo i w prawo, aby przechodzić przez każdy punkt danych na wykresie. Użyj klawiszy strzałek w górę i w dół, aby przechodzić do kolejnych wierszy percentylu. Wybierz ikonę pinezki w prawym górnym rogu dowolnego z wykresów, aby przypiąć wybrany wykres do ostatniego wyświetlonego pulpitu nawigacyjnego platformy Azure. Z poziomu pulpitu nawigacyjnego można zmieniać rozmiar wykresu i zmienić jego położenie. Wybranie wykresu z pulpitu nawigacyjnego przekierowuje Cię do Azure Monitor kontenerów i ładuje prawidłowy zakres i widok.

Azure Monitor for Containers obsługują również [Eksploratora metryk](../platform/metrics-getting-started.md)Azure monitor, w którym można tworzyć własne wykresy wykresów, skorelować i badać trendy oraz przypinać do pulpitów nawigacyjnych. W Eksploratorze metryk można również użyć kryteriów ustawionych do wizualizacji metryk jako podstawy [reguły alertu opartej na metrykach](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Wyświetlanie metryk kontenera w Eksploratorze metryk

W Eksploratorze metryk można wyświetlić zagregowane metryki dotyczące węzła i użycia z Azure Monitor dla kontenerów. W poniższej tabeli zestawiono szczegółowe informacje ułatwiające zrozumienie sposobu używania wykresów metryk do wizualizacji metryk kontenera.

|Przestrzeń nazw | Metryka | Opis | 
|----------|--------|-------------|
| Szczegółowe informacje. kontenery/węzły | |
| | cpuUsageMillicores | Zagregowane pomiary użycia procesora CPU w klastrze. Jest to rdzeń procesora CPU podzielony na 1000 jednostek (Milli = 1000). Służy do określania użycia rdzeni w kontenerze, w którym wiele aplikacji może korzystać z jednego rdzenia.| 
| | cpuUsagePercentage | Zagregowane średnie użycie procesora CPU wyrażone w procentach w klastrze.|
| | memoryRssBytes | Pamięć RSS kontenera użyta w bajtach.| 
| | memoryRssPercentage | Pamięć RSS kontenera użyta w procentach.|
| | memoryWorkingSetBytes | Użyta pamięć zestawu roboczego kontenera.| 
| | memoryWorkingSetPercentage | Pamięć zestawu roboczego kontenera użyta w procentach. | 
| | nodesCount | Liczba węzłów z Kubernetes.|
| Szczegółowe informacje. kontenery/zasobniki | |
| | PodCount | Liczba pod z Kubernetes.|

Można [podzielić](../platform/metrics-charts.md#apply-splitting-to-a-chart) metrykę, aby wyświetlić ją w wymiarze, i wizualizować, jak różne segmenty są porównywane ze sobą. Dla węzła można podzielić wykres na wymiar *hosta* . Z poziomu usługi można podzielić ją na segmenty według następujących wymiarów:

* Kontroler
* Kubernetes przestrzeń nazw
* Węzeł
* Etap

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizowanie węzłów, kontrolerów i kondycji kontenera

Po przełączeniu do kart **węzły**, **Kontrolery**i **kontenery** okienko właściwości zostanie automatycznie wyświetlone po prawej stronie. Wyświetla właściwości wybranego elementu, w tym etykiety zdefiniowane do organizowania obiektów Kubernetes. Po wybraniu węzła systemu Linux w sekcji **pojemność dysku lokalnego** wyświetlana jest również ilość dostępnego miejsca na dysku oraz wartość procentowa użyta dla każdego dysku prezentowanego w węźle. Wybierz łącze **>>** w okienku, aby wyświetlić lub ukryć okienko.

![Przykładowe okienka właściwości perspektyw Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Po rozwinięciu obiektów w hierarchii okienko właściwości jest aktualizowane w zależności od wybranego obiektu. W okienku można także wyświetlać zdarzenia Kubernetes ze wstępnie zdefiniowanymi przeszukiwaniem dzienników, wybierając łącze **Wyświetl dzienniki zdarzeń Kubernetes** w górnej części okienka. Aby uzyskać więcej informacji o sposobie wyświetlania danych dziennika Kubernetes, zobacz [dzienniki wyszukiwania do analizowania danych](container-insights-log-search.md). Podczas przeglądania zasobów klastra można zobaczyć dzienniki kontenerów i zdarzenia w czasie rzeczywistym. Aby uzyskać więcej informacji na temat tej funkcji oraz konfiguracji wymaganej do udzielenia i kontroli dostępu, zobacz [Wyświetlanie dzienników w czasie rzeczywistym za pomocą Azure monitor dla kontenerów](container-insights-live-logs.md). 

Użyj opcji **+ Dodaj filtr** w górnej części strony, aby odfiltrować wyniki widoku według **usługi**, **węzła**, **przestrzeni nazw**lub **puli węzłów**. Po wybraniu zakresu filtru wybierz jedną z wartości wyświetlanych w polu **Wybierz wartości** . Po skonfigurowaniu filtru jest on stosowany globalnie podczas wyświetlania dowolnej perspektywy klastra AKS. Formuła obsługuje tylko znak równości. Możesz dodać dodatkowe filtry od pierwszego z nich, aby jeszcze bardziej zawęzić wyniki. Na przykład, jeśli określisz **węzeł**Filtruj według, możesz wybrać tylko **usługę** lub **przestrzeń nazw** dla drugiego filtru.

![Przykład użycia filtru, aby zawęzić wyniki](./media/container-insights-analyze/add-filter-option-01.png)

Określanie filtru na jednej karcie jest kontynuowane po wybraniu innej. Jest ona usuwana po wybraniu symbolu **x** obok określonego filtru. 

Przejdź do karty **węzły** i hierarchia wierszy jest zgodna z modelem obiektów Kubernetes, który rozpoczyna się od węzła w klastrze. Rozwiń węzeł, aby wyświetlić jeden lub więcej zasobników uruchomionych w węźle. Jeśli więcej niż jeden kontener jest zgrupowany na pod, są one wyświetlane jako ostatni wiersz w hierarchii. Można także zobaczyć, ile obciążeń związanych z różnymi firmami nie są uruchomione na hoście, Jeśli host ma wykorzystanie procesora lub pamięci.

![Przykład hierarchii węzłów Kubernetes w widoku wydajności](./media/container-insights-analyze/containers-nodes-view.png)

Kontenery systemu Windows Server, na których działa system operacyjny Windows Server 2019, są wyświetlane po wszystkich węzłach na liście z systemem Linux. Po rozwinięciu węzła systemu Windows Server można wyświetlić jeden lub więcej zasobników i kontenerów, które są uruchamiane w węźle. Po zaznaczeniu węzła w okienku właściwości zostanie wyświetlona informacja o wersji. Informacje o agencie są wykluczone, ponieważ w węzłach systemu Windows Server nie zainstalowano agenta. 

![Przykładowa hierarchia węzłów z węzłami systemu Windows Server na liście](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container Instances węzły wirtualne z systemem operacyjnym Linux są wyświetlane po ostatnim węźle klastra AKS na liście. Po rozszerzeniu Container Instances węzła wirtualnego można wyświetlić jeden lub więcej Container Instances i zasobników i kontenerów, które są uruchamiane w węźle. Metryki nie są zbierane i raportowane dla węzłów tylko dla zasobników.

![Przykładowa hierarchia węzłów z Container Instances na liście](./media/container-insights-analyze/nodes-view-aci.png)

Z rozwiniętego węzła możesz przejść do szczegółów z lub kontenera, który jest uruchamiany w węźle, do kontrolera, aby wyświetlić dane wydajności odfiltrowane dla tego kontrolera. Wybierz wartość w kolumnie **kontroler** dla określonego węzła.
 
![Przykład przechodzenia do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-node-controller.png)

Wybierz pozycję Kontrolery lub kontenery w górnej części strony, aby przejrzeć stan i wykorzystanie zasobów dla tych obiektów. Aby przejrzeć użycie pamięci, na liście rozwijanej **Metryka** wybierz pozycję **pamięć RSS** lub **zestaw roboczy pamięci**. **Pamięć RSS** jest obsługiwana tylko dla Kubernetes w wersji 1,8 lub nowszej. W przeciwnym razie można wyświetlić wartości dla **minimalnej &nbsp; %** jako *NaN &nbsp; %* , która jest liczbową wartością typu danych reprezentującą niezdefiniowaną lub niereprezentującą wartość.

![Widok wydajności węzłów kontenera](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Zestaw roboczy pamięci** pokazuje zawartą pamięć i pamięć wirtualną (pamięć podręczną) oraz łączną zawartość używaną przez aplikację. W obszarze **RSS pamięci** jest wyświetlana tylko pamięć główna (czyli Nothing, ale w innych wyrazach). Ta Metryka przedstawia rzeczywistą pojemność dostępnej pamięci. Jaka jest różnica między pamięcią rezydentną a pamięcią wirtualną?

- Pamięć rezydentna lub pamięć główna to rzeczywista ilość pamięci maszyny dostępna dla węzłów klastra.

- Pamięć wirtualna jest zarezerwowaną ilością miejsca na dysku twardym (pamięć podręczna) używaną przez system operacyjny do wymiany danych z pamięci na dysk, gdy jest on używany, a następnie pobiera z powrotem do pamięci, jeśli jest to konieczne.

Domyślnie dane wydajności bazują na ostatnich sześciu godzinach, ale można zmienić okno przy użyciu opcji **TimeRange** w lewym górnym rogu. Można również filtrować wyniki w zakresie czasu, wybierając wartość **min**, **AVG**, **pięćdziesiąt**, **90**, **używany 95.** i **Max** w selektorze percentylu. 

![Zaznaczenie percentylu do filtrowania danych](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Po umieszczeniu wskaźnika myszy na wykresie słupkowym w kolumnie **trend** każdy pasek pokazuje użycie procesora lub pamięci, w zależności od tego, która Metryka została wybrana, w ciągu 15 minut. Po wybraniu wykresu trendu za pomocą klawiatury Użyj klawisza Alt + Page Up lub klawisza Alt + Page Down, aby przełączać poszczególne paski osobno. Te same szczegóły można uzyskać po umieszczeniu wskaźnika myszy na pasku.

![Przykład aktywowania wykresu słupkowego trendu](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

W następnym przykładzie dla pierwszego węzła na liście *AKS-nodepool1-* , wartość dla **kontenerów** wynosi 9. Ta wartość to pakiet zbiorczy całkowitej liczby wdrożonych kontenerów.

![Zestawienie kontenerów — przykład na węzeł](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Te informacje mogą pomóc w szybkim ustaleniu, czy masz odpowiednie równoważenie kontenerów między węzłami w klastrze. 

Informacje przedstawione podczas wyświetlania karty **węzły** są opisane w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Nazwa | Nazwa hosta. |
| Stan | Widok Kubernetes stanu węzła. |
| Minimalna &nbsp;%, średnia &nbsp;%, pięćdziesiąt &nbsp;%, &nbsp; 90%, używany 95. &nbsp;%, maks. &nbsp; %  | Średni procent węzła w oparciu o percentyl w wybranym czasie. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max | Średnia wartość rzeczywista węzłów oparta na percentylu podczas wybranego czasu trwania. Średnia wartość jest mierzona na podstawie limitu procesora CPU/pamięci ustawionego dla węzła. W przypadku zasobników i kontenerów jest to średnia wartość raportowana przez hosta. |
| Containers | Liczba kontenerów. |
| Czas | Przedstawia czas od momentu uruchomienia lub ponownego uruchomienia węzła. |
| Kontroler | Tylko dla kontenerów i zasobników. Pokazuje, który kontroler znajduje się w. Nie wszystkie zasobniki znajdują się w kontrolerze, dlatego niektóre mogą wyświetlać **N/a**. | 
| Minimum trendu &nbsp;%, średnia &nbsp;%, pięćdziesiąt &nbsp;%, &nbsp; 90%, używany 95. &nbsp;%, maks. &nbsp; % | Trend wykresu słupkowego reprezentuje procentową metrykę percentylości kontrolera. |

W selektorze wybierz pozycję **Kontrolery**.

![Wybierz widok kontrolerów](./media/container-insights-analyze/containers-controllers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontrolerów, Container Instances kontrolery węzłów wirtualnych lub zasobniki wirtualnego węzła nie są połączone z kontrolerem.

![Widok wydajności kontrolerów > \<Name](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchia wierszy rozpoczyna się od kontrolera. Po rozszerzeniu kontrolera należy wyświetlić jeden lub więcej zasobników. Rozwiń węzeł pod, a w ostatnim wierszu jest wyświetlany kontener pogrupowany pod względem elementu. Na rozszerzonym kontrolerze możesz przejść do węzła, w którym jest uruchomiona, aby wyświetlić dane wydajności odfiltrowane dla tego węzła. Container Instances z kontrolerami nie połączono z kontrolerem na liście poniżej.

![Przykładowa hierarchia kontrolerów z Container Instancesymi identyfikatorami na liście](./media/container-insights-analyze/controllers-view-aci.png)

Wybierz wartość w kolumnie **węzeł** dla określonego kontrolera.

![Przykład przechodzenia do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

Informacje wyświetlane podczas przeglądania kontrolerów są opisane w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Nazwa | Nazwa kontrolera.|
| Stan | Stan zbiorczy kontenerów po zakończeniu działania z stanem takim jak *OK*, *przerwany*, *Niepowodzenie*, *zatrzymano*lub *wstrzymano*. Jeśli kontener jest uruchomiony, ale stan nie był prawidłowo wyświetlany lub nie został pobrany przez agenta i nie odpowiedział przez dłużej niż 30 minut, stan jest *nieznany*. Dodatkowe szczegóły ikony stanu znajdują się w poniższej tabeli.|
| Minimalna &nbsp;%, średnia &nbsp;%, pięćdziesiąt &nbsp;%, &nbsp; 90%, używany 95. &nbsp;%, maks. &nbsp; %| Średnia Zbiorcza średniej wartości procentowej każdej jednostki dla wybranej metryki i percentylu. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max  | Rzutowanie średniej millicore procesora CPU lub wydajności pamięci kontenera dla wybranego percentylu. Średnia wartość jest mierzona na podstawie limitu procesora CPU/pamięci ustawionego dla elementu. |
| Containers | Łączna liczba kontenerów dla kontrolera lub pod. |
| Uruchamiania | Zestawienie liczby ponownych uruchomień z kontenerów. |
| Czas | Przedstawia czas od momentu rozpoczęcia kontenera. |
| Węzeł | Tylko dla kontenerów i zasobników. Pokazuje, który kontroler znajduje się w. | 
| Minimum trendu &nbsp;%, średnia &nbsp;%, pięćdziesiąt &nbsp;%, &nbsp; 90%, używany 95. &nbsp;%, maks. &nbsp; % | Trend wykresu słupkowego przedstawia średnią metrykę percentylu kontrolera. |

Ikony w polu Stan wskazują stan online kontenerów.
 
| Ikona | Stan | 
|--------|-------------|
| ![Ikona stanu gotowości do uruchomienia](./media/container-insights-analyze/containers-ready-icon.png) | Uruchomione (gotowe)|
| ![Ikona stanu oczekiwania lub wstrzymania](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekiwanie lub wstrzymane|
| ![Ikona ostatnio zgłoszonego stanu uruchomienia](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnio zgłoszone uruchomione, ale nie udzielono odpowiedzi przez ponad 30 minut|
| ![Ikona stanu pomyślnego](./media/container-insights-analyze/containers-green-icon.png) | Zatrzymanie zostało zatrzymane lub zakończyło się niepowodzeniem|

Ikona stanu wyświetla liczbę w zależności od tego, co zapewnia. Pokazuje najgorsze dwa stany, a po umieszczeniu wskaźnika myszy na stanie zostanie wyświetlony stan zestawienia ze wszystkich zasobników w kontenerze. Jeśli nie ma stanu gotowości, zostanie wyświetlona wartość stanu **(0)** .

W selektorze wybierz pozycję **Containers (kontenery**).

![Wybierz widok kontenerów](./media/container-insights-analyze/containers-containers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontenerów platformy Azure Kubernetes i Azure Container Instances. 

![Widok wydajności kontenerów > \<Name](./media/container-insights-analyze/containers-containers-view.png)

Z kontenera można przejść do szczegółów na węzeł lub, aby wyświetlić dane wydajności odfiltrowane dla tego obiektu. Wybierz wartość w kolumnie **pod** lub **węzeł** dla określonego kontenera.

![Przykład przechodzenia do szczegółów z węzła do kontenerów w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

Informacje wyświetlane podczas wyświetlania kontenerów są opisane w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Nazwa | Nazwa kontrolera.|
| Stan | Stan kontenerów (jeśli istnieją). Dodatkowe szczegóły ikony stanu znajdują się w następnej tabeli.|
| Minimalna &nbsp;%, średnia &nbsp;%, pięćdziesiąt &nbsp;%, &nbsp; 90%, używany 95. &nbsp;%, maks. &nbsp; % | Pakiet zbiorczy średniej wartości procentowej każdej jednostki dla wybranej metryki i percentylu. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max | Rzutowanie średniej millicore procesora CPU lub wydajności pamięci kontenera dla wybranego percentylu. Średnia wartość jest mierzona na podstawie limitu procesora CPU/pamięci ustawionego dla elementu. |
| Pod | Kontener, w którym znajduje się pod.| 
| Węzeł |  Węzeł, w którym znajduje się kontener. | 
| Uruchamiania | Przedstawia czas od momentu rozpoczęcia kontenera. |
| Czas | Reprezentuje godzinę uruchomienia lub ponownego uruchomienia kontenera. |
| Minimum trendu &nbsp;%, średnia &nbsp;%, pięćdziesiąt &nbsp;%, &nbsp; 90%, używany 95. &nbsp;%, maks. &nbsp; % | Trend wykresu słupkowego reprezentuje procentową metrykę percentylu kontenera. |

Ikony w polu Stan wskazują stan online, zgodnie z opisem w poniższej tabeli.
 
| Ikona | Stan |  
|--------|-------------|  
| ![Ikona stanu gotowości do uruchomienia](./media/container-insights-analyze/containers-ready-icon.png) | Uruchomione (gotowe)|  
| ![Ikona stanu oczekiwania lub wstrzymania](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekiwanie lub wstrzymane|  
| ![Ikona ostatnio zgłoszonego stanu uruchomienia](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnio zgłoszone uruchomione, ale nie udzielono odpowiedzi w ciągu ponad 30 minut|  
| ![Ikona stanu przerwania](./media/container-insights-analyze/containers-terminated-icon.png) | Zatrzymanie zostało zatrzymane lub zakończyło się niepowodzeniem|  
| ![Ikona stanu niepowodzenia](./media/container-insights-analyze/containers-failed-icon.png) | Stan niepowodzenia |  

## <a name="workbooks"></a>Skoroszyty

Skoroszyty łączą tekst, [kwerendy dzienników](../log-query/query-language.md), [metryki](../platform/data-platform-metrics.md)i parametry w rozbudowanych raportach interaktywnych. Skoroszyty są edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Azure Monitor kontenerów zawiera cztery skoroszyty umożliwiające rozpoczęcie pracy:

- **Pojemność dysku**: przedstawia wykresy interaktywnego użycia dysku dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

    - Procent użycia dysku dla wszystkich dysków.
    - Wolne miejsce na dysku dla wszystkich dysków.
    - Siatka pokazująca dysk każdego węzła, jego procent zajętego miejsca, Trend procentu zajętego miejsca, wolne miejsce na dysku (GiB) i trend wolnego miejsca na dysku (GiB). Gdy wiersz jest zaznaczony w tabeli, procent zajętego miejsca i wolnego miejsca na dysku (GiB) jest pokazywany poniżej wiersza. 

- **We/wy dysku**: przedstawia wykresy interaktywnego wykorzystania dysku dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

    - Dyskowe operacje we/wy są sumowane na wszystkich dyskach przez odczyt bajtów/s, liczba bajtów/s oraz trendy odczytu i zapisu bajtów/s.
    - Osiem wykresów wydajnościowych przedstawia kluczowe wskaźniki wydajności, które pomagają mierzyć i identyfikować wąskie gardła dyskowych operacji we/wy.

- **Kubelet**: obejmuje dwie siatki pokazujące statystyki operacyjne węzła klucza:

    - Przegląd według siatki węzła podsumowuje całkowitą operację, łączną liczbę błędów i operacji zakończonych powodzeniem według procentu i trendu dla każdego węzła.
    - Przegląd według typu operacji podsumowuje dla każdej operacji łączną operację, łączną liczbę błędów i operacji zakończonych powodzeniem według procentu i trendu.

- **Sieć**: przedstawia wykresy interaktywnego wykorzystania sieci dla każdej karty sieciowej każdego węzła, a siatka przedstawia kluczowe wskaźniki wydajności, które ułatwiają mierzenie wydajności kart sieciowych.

Możesz uzyskać dostęp do tych skoroszytów, wybierając je z listy rozwijanej **Wyświetl skoroszyty** .

![Lista rozwijana Wyświetl skoroszyty](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [tworzenie alertów wydajności za pomocą Azure monitor dla kontenerów](container-insights-alerts.md) , aby dowiedzieć się, jak tworzyć alerty dotyczące wysokiego użycia procesora i pamięci w celu obsługi procesów i procedur operacyjnych DevOps.
- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby wyświetlić wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do alertów, wizualizacji lub analizowania klastrów.
