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
ms.date: 05/06/2019
ms.author: magoedte
ms.openlocfilehash: ed387f7038c5dee1a1685c918abcae49942cd55d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148846"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Zrozumienie wydajności klastra AKS przy użyciu usługi Azure Monitor dla kontenerów 
Dzięki usłudze Azure Monitor dla kontenerów umożliwia wykresy wydajności oraz stan kondycji monitorowania obciążenia klastry usługi Azure Kubernetes Service (AKS) z dwóch perspektyw bezpośrednio z klastra usługi AKS lub we wszystkich klastrach usługi AKS w ramach subskrypcji platformy Azure Monitor. Wyświetlanie usługi Azure Container Instances (ACI) możliwe jest również w przypadku monitorowania określonych klastra AKS.

Ten artykuł pomoże zrozumieć środowisko między dwóch perspektyw, oraz jak pomaga szybko ocenić, zbadać i rozwiązać wykryte problemy.

Aby uzyskać informacje na temat włączania usługi Azure Monitor dla kontenerów, zobacz [dołączanie usługi Azure Monitor dla kontenerów](container-insights-onboard.md).

> [!IMPORTANT]
> Usługa Azure Monitor do obsługi kontenerów do monitorowania klastra usługi AKS z systemem Windows Server 2019 jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Monitor udostępnia widok wielu klastrów pokazujący stan kondycji wszystkich monitorowanych klastry AKS z systemem Linux i Windows Server 2019 wdrażane w grupach zasobów w subskrypcji.  Pokazuje klastrów AKS odnalezione nie są monitorowane przez rozwiązanie. Rozumieją kondycji klastra, a w tym miejscu możesz przejść do strony wydajność węzła i kontroler lub przejdź do zobaczyć wykresy wydajności dla klastra.  W przypadku klastrów usługi AKS wykryte i identyfikowane jako niemonitorowane można włączyć monitorowania dla tego klastra w dowolnym momencie.  

Główne różnice monitorowania klastra systemu Windows Server za pomocą usługi Azure Monitor dla kontenerów w porównaniu do klastra z systemem Linux są następujące:

- Metryka RSS pamięci nie jest dostępna dla węzła Windows i kontenerów 
- Informacje dotyczące pojemności magazynu dysku nie jest dostępna dla węzłów Windows
- Dzienniki na żywo pomoc techniczna jest dostępna z wyjątkiem dzienniki kontenerów Windows.
- Tylko pod, które mają być monitorowane w środowiskach, nie w środowiskach platformy Docker.
- Z wersji zapoznawczej maksymalnie 30 kontenery systemu Windows Server są obsługiwane. To ograniczenie nie ma zastosowania do kontenerów systemu Linux.  

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Wyświetlanie wielu klastrów z usługi Azure Monitor 
Zaznacz, aby wyświetlić stan kondycji wszystkich klastrach usługi AKS wdrożonych **Monitor** w okienku po lewej stronie w witrynie Azure portal.  W obszarze **Insights** zaznacz **kontenery**.  

![Przykład pulpitu nawigacyjnego wielu klastrów w usłudze Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Na **monitorowania klastrów** kartę, jesteś w stanie się następujące czynności:

1. Jak wiele klastrów znajdują się w stanie krytycznych lub w złej kondycji, a ile to dobrej kondycji i nie raportowania (nazywane stan nieznany)?
2. Czy wszystkie moje [aparatu Kubernetes usługi Azure (usługi AKS — aparat)](https://github.com/Azure/aks-engine) wdrożeniami Zdrowy?
3. Jak wiele węzłów, użytkownika i zasobników systemu są wdrażane w klastrze?
4. Ilość miejsca na dysku jest dostępny i czy istnieje problem pojemności?

Stany kondycji, uwzględnione są następujące: 

* **Dobra** — wykryte nie problemy dla maszyny Wirtualnej i czy działa on zgodnie z potrzebami. 
* **Krytyczne** — co najmniej jeden problem krytyczny, są wykrywane, które należy uwzględnić w celu przywrócenia normalnej stanu operacyjnego, zgodnie z oczekiwaniami.
* **Ostrzeżenie** — co najmniej jeden problem, są wykrywane, które muszą być kierowane lub stan kondycji może stać się krytyczna.
* **Nieznany** — Jeśli usługa nie był w stanie nawiązać połączenie z węzłem lub zasobników, jego stan zmieni się stan nieznany.
* **Nie można odnaleźć** — jedno obszaru roboczego, grupy zasobów lub subskrypcji zawierającej obszaru roboczego dla tego rozwiązania zostało usunięte.
* **Nieautoryzowany** — użytkownik nie ma wymaganych uprawnień do odczytu danych w obszarze roboczym.
* **Błąd** — wystąpił błąd podczas próby odczytu danych z obszaru roboczego.
* **MIS skonfigurowane** — usługa Azure Monitor dla kontenerów nie został prawidłowo skonfigurowany w określonym obszarze roboczym.
* **Brak danych** -danych nie jest zgłaszany do obszaru roboczego w ciągu ostatnich 30 minut.

Stan kondycji oblicza ogólny stan klastra jako *najgorszy* trzy stany z jednym wyjątkiem — Jeśli jeden z trzech stanów *nieznany*, będą wyświetlane ogólny stan klastra **nieznany**.  

Poniższa tabela zawiera podział obliczeń kontrolowanie stanów kondycji monitorowanego klastra w widoku wielu klastrów.

| |Stan |Dostępność |  
|-------|-------|-----------------|  
|**Zasobnik użytkownika**| | |  
| |W dobrej kondycji |100% |  
| |Ostrzeżenie |90 - 99% |  
| |Krytyczny |< 90% |  
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |  
|**System Pod**| | |  
| |W dobrej kondycji |100% |
| |Ostrzeżenie |ND |
| |Krytyczny |< 100% |
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |
|**Node** | | |
| |W dobrej kondycji |> 85% |
| |Ostrzeżenie |60 - 84% |
| |Krytyczny |< 60% |
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |

Z listy klastrów, możesz przejść do szczegółów do **klastra** strony, klikając nazwę klastra, do **węzłów** stronie wydajności, klikając zbiór węzłów w **węzłów** kolumny dla tego konkretnego klastra lub testowania odzyskiwania po awarii w dół do **kontrolerów** stronie wydajności, klikając zbiorcze z **zasobników użytkownika** lub **zasobników System**kolumny.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Wyświetl wydajność bezpośrednio z klastra usługi AKS
Dostęp do usługi Azure Monitor na kontenerów jest dostępne bezpośrednio z klastra usługi AKS, wybierając **Insights** z okienka po lewej stronie. Wyświetlanie informacji na temat klastra usługi AKS jest podzielony na cztery perspektywy:

- Klaster
- Węzły 
- Kontrolery  
- Containers

Domyślna strona otwarty po kliknięciu **Insights** jest **klastra**, i zawiera cztery wykresy wydajności wiersza, wyświetlanie kluczowe metryki wydajności klastra. 

![Przykładowe wykresy wydajności na karcie klastra](./media/container-insights-analyze/containers-cluster-perfview.png)

Wykres wydajności przedstawia cztery metryki wydajności:

- **Wykorzystanie procesora CPU węzła&nbsp;%**: Zagregowane perspektywy wykorzystanie procesora CPU dla całego klastra. Wyniki zakresu czasu można filtrować, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- **Wykorzystanie pamięci węzła&nbsp;%**: Zagregowane perspektywy wykorzystanie pamięci dla całego klastra. Wyniki zakresu czasu można filtrować, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- **Liczba węzłów**: Liczba węzłów i stan usługi Kubernetes. Są Stany węzłów klastra, reprezentowany *wszystkich*, *gotowe*, i *niegotowe* i może być filtrowana indywidualnie lub w połączeniu w selektorze powyżej wykresu. 
- **Liczba zasobników działań**: Liczba zasobników i stan usługi Kubernetes. Są Stany zasobników reprezentowane *wszystkich*, *oczekujące*, *systemem*, i *nieznany* i może być filtrowana indywidualnie lub w Selektor powyżej wykresu. 

Strzałka w lewo/w prawo umożliwia przechodzić przez każdy punkt danych na wykresie i Strzałka w górę/w dół klucze, aby przechodzić między wiersze percentyl.

Usługa Azure Monitor dla kontenerów obsługuje również usługi Azure Monitor [Eksploratora metryk](../platform/metrics-getting-started.md), gdzie można utworzyć własnego wykresów wykres, korelować i śledzenie trendów i przypinać do pulpitów nawigacyjnych. Z poziomu Eksploratora metryk umożliwia także kryteria zostało ustawione w celu wizualizacji metryk jako podstawa [metryki na podstawie reguły alertu](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Wyświetl metryki kontenera w Eksploratorze metryk
W Eksploratorze metryk można wyświetlić zagregowane węzła i zasobnik wykorzystania metryki z usługi Azure Monitor kontenerów. W poniższej tabeli przedstawiono szczegółowe informacje ułatwiające zrozumienie metod wykorzystania wykresy metryk w celu wizualizacji metryk kontenera.

|Przestrzeń nazw | Metryka |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| insights.container/pods | |
| | podCount |

Można zastosować [podział](../platform/metrics-charts.md#apply-splitting-to-a-chart) metryki, aby go wyświetlić, wymiarów i wizualizację różnych części porównać ze sobą. Dla węzła, możesz posegmentować wykres według *hosta* wymiar, a w zasobniku można podzielić ją przez poniższe wymiary:

* Kontroler
* Przestrzeni nazw Kubernetes
* Węzeł
* Etap

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizowanie węzłów, kontrolerów i kondycji kontenera

Po przełączeniu do **węzłów**, **kontrolerów**, i **kontenery** karta automatycznie wyświetlane w prawej części strony to okienko właściwości. Pokazuje właściwości elementów wybranych, tym etykiety, należy zdefiniować do organizowania obiekty usługi Kubernetes. Po wybraniu węzła systemu Linux zawiera również w sekcji **pojemność dysku lokalnego** dostępnego miejsca na dysku i procent użycia dla każdego dysku wyświetlone w węźle. Kliknij pozycję **>>** połączyć w okienku view\hide okienka. 

![W okienku właściwości perspektyw Kubernetes przykład](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Podczas rozwijania obiektów w hierarchii, na podstawie aktualizacji okienka właściwości w obiekcie, który został wybrany. W okienku można wyświetlić zdarzeń Kubernetes za pomocą wstępnie zdefiniowanych wyszukiwań w dziennikach, klikając **dzienniki zdarzeń Kubernetes widoku** widocznego u góry okienka. Aby uzyskać więcej informacji o wyświetlaniu danych dzienników platformy Kubernetes, zobacz [przeszukiwanie dzienników w celu analizowania danych](container-insights-log-search.md). Podczas przeglądania zasobów klastra, zobaczysz dzienniki kontenera i zdarzeń w czasie rzeczywistym. Aby uzyskać więcej informacji na temat tej funkcji i konfiguracji wymaganej do udzielić i sterowania dostępem, zobacz [sposób wyświetlania dzienników czasu rzeczywistego z usługą Azure Monitor dla kontenerów](container-insights-live-logs.md). 

Użyj **+ Dodaj filtr** opcję w górnej części strony, aby filtrować wyniki dla widoku przez **usługi**, **węzła**, **Namespace**, lub  **Pulę węzłów** i po wybraniu zakresu filtru, które następnie wybrać jedną z wartości podanych w **wybierz wartości** pola.  Po skonfigurowaniu filtru jest stosowana globalnie podczas wyświetlania dowolnego punktu widzenia klastra AKS.  Formuła obsługuje tylko znaku równości.  Możesz dodać dodatkowe filtry na podstawie pierwszy z nich w celu dalszego zawężenia wyników.  Na przykład, jeśli określono filtr według **węzła**, drugi filtr będzie tylko pozwalają na wybór **usługi** lub **Namespace**.  

![Przykład przy użyciu filtru, aby zawęzić wyniki](./media/container-insights-analyze/add-filter-option-01.png)

Określenie filtru w jednej karty w dalszym ciągu stosowane, gdy można wybrać inny, i zostanie usunięta po kliknięciu **x** symbol obok określonego filtru.   

Przełącz się do **węzłów** kartę i hierarchii wiersz poniżej model obiektów usługi Kubernetes, począwszy od węzła w klastrze. Rozwiń węzeł i można wyświetlić co najmniej jeden zasobników, uruchomione w węźle. Jeśli więcej niż jednego kontenera jest zgrupowany do zasobnik, są wyświetlane jako ostatni wiersz w hierarchii. Można również wyświetlić, jak wiele powiązanych obciążeniach-pod są uruchomione na hoście, jeśli host ma procesor lub dużego wykorzystania pamięci.

![Przykładowa hierarchia Kubernetes węzeł w widoku wydajności](./media/container-insights-analyze/containers-nodes-view.png)

Kontenery systemu Windows Server z systemem operacyjnym Windows Server 2019 r są wyświetlane po wszystkich węzłach opartych na systemie Linux, na liście. Po rozwinięciu węzła systemu Windows Server, można wyświetlić co najmniej jeden zasobników, jak i kontenery uruchomione w węźle. Po wybraniu węzła zawartość okienka właściwości wskazuje informacje o wersji, z wyjątkiem informacji o agencie, ponieważ węzły systemu Windows Server nie mają zainstalowanego agenta.  

![Przykładowa hierarchia węzłów z węzłami systemu Windows Server, na liście](./media/container-insights-analyze/nodes-view-windows.png) 

Kontener wystąpień wirtualnych węzły na platformie Azure z systemem operacyjnym Linux są wyświetlane po ostatnim węźle klastra AKS na liście.  Po rozwinięciu węzła wirtualnego usługi ACI, można wyświetlić co najmniej zasobników ACI, jak i kontenery uruchomione w węźle.  Metryki są nie zbieranych i zgłaszanych w przypadku węzłów tylko zasobników.

![Przykładowa hierarchia węzła Usługa Container Instances, na liście](./media/container-insights-analyze/nodes-view-aci.png)

Z rozwinięty węzeł można przejść do szczegółów z pod lub kontenerów uruchomione w węźle z kontrolerem, aby wyświetlić dane dotyczące wydajności przefiltrowana pod kątem tego kontrolera. Kliknij wartość, w obszarze **kontrolera** kolumny dla określonego węzła.   
![Przykład przechodzenie do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-node-controller.png)

Można wybrać kontrolery lub kontenerów w górnej części strony i sprawdź stan i użycia zasobów dla tych obiektów.  Jeśli zamiast tego chcesz przejrzeć wykorzystanie pamięci w **metryki** listy rozwijanej wybierz **RSS pamięci** lub **zestaw roboczy pamięci**. **Pamięć RSS** jest obsługiwany tylko w przypadku rozwiązania Kubernetes w wersji 1.8 i nowszych. W przeciwnym razie do wyświetlania wartości **Min&nbsp; %**  jako *NaN&nbsp;%*, który jest wartość typu danych liczbowych, który reprezentuje niezdefiniowany lub wartość wyniku. 

![Widok wydajności węzłów kontenerów](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Domyślnie dane dotyczące wydajności opiera się na ostatnie 6 godzin, ale okno można zmienić za pomocą **TimeRange** opcji w lewym górnym rogu. Można również filtrować wyniki do zakresu, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyl. 

![Wybór percentyl filtrowanie danych](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Gdy przesuniesz wskaźnik myszy nad wykres słupkowy w ramach **Trend** kolumny, Każdy słupek pokazuje użycie procesora CPU lub pamięci, w zależności od tego, który jest wybrana metryka, 15 minut przed upływem próbki. Po wybraniu wykresu trendu za pomocą klawiatury, umożliwia klawisze Alt + PageUp lub Alt + PageDown przechodzić przez każdy słupek indywidualnie i szczegółowe informacje ten sam sposób jak w mouseover.

![Trend wykres słupkowy, umieść kursor nad przykład](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

W następnym przykładzie, należy pamiętać, pierwszy na liście - węzeł *aks-nodepool1 -*, wartość **kontenery** jest 9, która stanowi pakiet zbiorczy całkowita liczba kontenerów wdrożonych.

![Pakiet zbiorczy kontenerów na przykład węzeł](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Ułatwia ona szybko ustalić, czy masz kompromisu kontenerów między węzłami w klastrze. 

W poniższej tabeli opisano informacje, które są prezentowane podczas wyświetlania węzłów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa hosta. |
| Stan | Kubernetes widok stanu węzła. |
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Średni procent węzła, w oparciu o procentowy podczas wybranego okresu. |
| Avg, Min, Max, 50., 90 | Węzły średnia wartość rzeczywista oparciu o procentowy podczas tego okresu czasu wybrany. Średnia wartość jest mierzony od limitu Procesora/pamięci dla węzła; dla zasobników i kontenerów to wartość średnia zgłaszaną przez hosta. |
| Containers | Liczba kontenerów. |
| Czas pracy | Reprezentuje czas, ponieważ węzeł uruchomiony lub został ponownie uruchomiony. |
| Kontrolery | Tylko w przypadku kontenerów i zasobników. Przedstawia on kontrolera, który jest we wszystkich. Nie wszystkie zasobników są w kontrolerze, więc niektóre może być wyświetlany **n/d**. | 
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Trend wykres słupkowy przedstawia procent metryki średni percentyl kontrolera. |

W selektorze, wybierz **kontrolerów**.

![Wybierz kontrolerów widoku](./media/container-insights-analyze/containers-controllers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontrolery i kontrolery ACI wirtualnego węzła lub zasobników wirtualny węzeł nie jest połączony z kontrolerem.

![Widok wydajności kontrolerów < nazwa >](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchia wiersz rozpoczyna się z kontrolerem i po rozwinięciu kontrolera, możesz wyświetlić co najmniej jeden zasobników.  Rozwiń zasobnik, a ostatni wiersz wyświetla kontenera grupowania zasobnik. Z kontrolera rozwinięty przechodzić do węzła, z którym jest uruchomiona na, aby wyświetlić dane dotyczące wydajności filtrowane dla tego węzła. Zasobników ACI, które nie są podłączone do kontrolera są ostatnio wymienione na liście.

![Przykładowa hierarchia kontrolerów z zasobników Container Instances, na liście](./media/container-insights-analyze/controllers-view-aci.png)

Kliknij wartość, w obszarze **węzła** kolumny dla określonego kontrolera.   

![Przykład przejść do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

W poniższej tabeli opisano informacje, które jest wyświetlane, gdy wyświetlanie kontrolerów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| Stan | Stan pakietu zbiorczego kontenery po ukończeniu uruchomione o stanie, takie jak *OK*, *zwolniony*, *niepowodzenie* *zatrzymane*, lub *Wstrzymana*. Jeśli kontener jest uruchomiony, ale stan był albo nie zostało prawidłowo wyświetlane lub nie została pobrana przez agenta i nie odpowiedział ponad 30 minut, stan jest *nieznany*. W poniższej tabeli znajdują się dodatkowe szczegóły ikonę stanu.|
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Przedstawia średnią średnią wartość procentową poszczególnych jednostek dla wybranej metryki i percentyl. |
| Avg, Min, Max, 50., 90  | Zbiorczy średni Procesora pamięci lub millicore wydajności kontenera dla wybranych percentyl. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Containers | Łączna liczba kontenerów dla kontrolera lub zasobników. |
| Uruchamia ponownie | Rzutowanie liczby ponowne uruchomienie z kontenerów. |
| Czas pracy | Reprezentuje czas od momentu uruchomienia kontenera. |
| Węzeł | Tylko w przypadku kontenerów i zasobników. Przedstawia on kontrolera, który jest znajdującej się. | 
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;%| Wykres słupkowy trend reprezentuje metryki średni percentyl kontrolera. |

Ikony w polu Stan wskazują stan online kontenerów:
 
| Ikona | Stan | 
|--------|-------------|
| ![Gotowe uruchomionej ikona stanu](./media/container-insights-analyze/containers-ready-icon.png) | Uruchamianie (gotowe)|
| ![Ikona stanu oczekuje lub wstrzymana](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekuje lub wstrzymana|
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|
| ![Ikona stanu powodzenia](./media/container-insights-analyze/containers-green-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|

Ikona stanu przedstawia liczbę oparte na zasobnik udostępnia. Pokazuje dwa najgorsze Stany i po umieszczeniu status Wyświetla stan pakietu zbiorczego z wszystkich zasobników w kontenerze. Jeśli nie ma stanu gotowości, wyświetlana jest wartość stanu **(0)**. 

W selektorze, wybierz **kontenery**.

![Wybierz kontenery widoku](./media/container-insights-analyze/containers-containers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontenerów usługi Kubernetes na platformie Azure i usługi Azure Container Instances.  

![Widok wydajności kontrolerów < nazwa >](./media/container-insights-analyze/containers-containers-view.png)

Kontener można przejść pod lub węzeł, aby wyświetlić dane dotyczące wydajności przefiltrowana pod kątem tego obiektu. Kliknij wartość, w obszarze **Pod** lub **węzła** kolumny dla określonego kontenera.   

![Przykład przejść do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

W poniższej tabeli opisano informacje, który jest wyświetlany podczas przeglądania kontenerów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| Stan | Stan kontenerów, jeśli istnieje. Dodatkowe szczegóły ikona stanu znajdują się w następnej tabeli.|
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Pakiet zbiorczy średnią wartość procentową poszczególnych jednostek dla wybranej metryki i percentyl. |
| Avg, Min, Max, 50., 90  | Pakiet zbiorczy średni wydajność użycia CPU millicore lub pamięci przez kontener dla wybranych percentyl. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Zasobnik | Kontener, w którym znajduje się pod.| 
| Węzeł |  Węzeł, w którym znajduje się kontener. | 
| Ponowne uruchomienie | Reprezentuje czas od momentu uruchomienia kontenera. |
| Czas pracy | Reprezentuje czas, ponieważ kontener został uruchomiony lub ponownie uruchomiony. |
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Trend wykres słupkowy przedstawia procent metryki średni percentyl kontenera. |

Ikony w polu Stan wskazują online stany zasobników, zgodnie z opisem w poniższej tabeli:
 
| Ikona | Stan |  
|--------|-------------|  
| ![Gotowe uruchomionej ikona stanu](./media/container-insights-analyze/containers-ready-icon.png) | Uruchamianie (gotowe)|  
| ![Ikona stanu oczekuje lub wstrzymana](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekuje lub wstrzymana|  
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|  
| ![Ikona stanu zakończone](./media/container-insights-analyze/containers-terminated-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|  
| ![Ikona stanu nie powiodło się](./media/container-insights-analyze/containers-failed-icon.png) | Stan niepowodzenia |  

## <a name="next-steps"></a>Kolejne kroki
- Przegląd [Tworzenie alertów wydajności dzięki usłudze Azure Monitor dla kontenerów](container-insights-alerts.md) dowiesz się, jak tworzyć alerty dla wysokie wykorzystanie procesora CPU i pamięci do obsługi infrastruktury DevOps lub procedur i procesy operacyjne. 
- Widok [dziennika przykłady zapytań](container-insights-log-search.md#search-logs-to-analyze-data) wstępnie zdefiniowane zapytania i przykłady do oceny lub Dostosuj alerty, wizualizacji i analizowanie klastry usługi.
