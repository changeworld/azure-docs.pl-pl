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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: dc55e4999a09c45463ae75b05d610b290f5ff526
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248318"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Zrozumienie wydajności klastra AKS przy użyciu usługi Azure Monitor dla kontenerów 
Dzięki usłudze Azure Monitor dla kontenerów umożliwia wykresy wydajności oraz stan kondycji monitorowania obciążenia klastry usługi Azure Kubernetes Service (AKS) z dwóch perspektyw bezpośrednio z klastra usługi AKS lub we wszystkich klastrach usługi AKS w ramach subskrypcji platformy Azure Monitor. Wyświetlanie usługi Azure Container Instances (ACI) możliwe jest również w przypadku monitorowania określonych klastra AKS.

Ten artykuł pomoże zrozumieć środowisko między dwóch perspektyw, oraz jak pomaga szybko ocenić, zbadać i rozwiązać wykryte problemy.

Aby uzyskać informacje na temat włączania usługi Azure Monitor dla kontenerów, zobacz [dołączanie usługi Azure Monitor dla kontenerów](container-insights-onboard.md).

Azure Monitor udostępnia widok wielu klastrów przedstawiający stan kondycji wszystkich monitorowanych klastrów AKS z systemem Linux i Windows Server 2019 wdrożonych w grupach zasobów w Twoich subskrypcjach.  Pokazuje klastrów AKS odnalezione nie są monitorowane przez rozwiązanie. Rozumieją kondycji klastra, a w tym miejscu możesz przejść do strony wydajność węzła i kontroler lub przejdź do zobaczyć wykresy wydajności dla klastra.  W przypadku klastrów usługi AKS wykryte i identyfikowane jako niemonitorowane można włączyć monitorowania dla tego klastra w dowolnym momencie.  

Główne różnice monitorujące klaster systemu Windows Server z Azure Monitor kontenerów w porównaniu z klastrem z systemem Linux są następujące:

- Metryka RSS pamięci nie jest dostępna dla węzła i kontenerów systemu Windows.
- Informacje o pojemności magazynu dyskowego nie są dostępne dla węzłów systemu Windows.
- Obsługa dzienników na żywo jest dostępna z wyjątkiem dzienników kontenera systemu Windows.
- Monitorowane są tylko środowiska pod środowiskiem, a nie środowiska Docker.
- W wersji zapoznawczej obsługiwane są maksymalnie 30 kontenerów systemu Windows Server. To ograniczenie nie dotyczy kontenerów systemu Linux.  

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Wyświetlanie wielu klastrów z usługi Azure Monitor 
Zaznacz, aby wyświetlić stan kondycji wszystkich klastrach usługi AKS wdrożonych **Monitor** w okienku po lewej stronie w witrynie Azure portal.  W obszarze **Insights** zaznacz **kontenery**.  

![Przykład pulpitu nawigacyjnego wielu klastrów w usłudze Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Na **monitorowania klastrów** kartę, jesteś w stanie się następujące czynności:

1. Jak wiele klastrów znajdują się w stanie krytycznych lub w złej kondycji, a ile to dobrej kondycji i nie raportowania (nazywane stan nieznany)?
2. Czy wszystkie moje [aparatu Kubernetes usługi Azure (usługi AKS — aparat)](https://github.com/Azure/aks-engine) wdrożeniami Zdrowy?
3. Ile węzłów, użytkowników i systemowych zasobników są wdrożone na klaster?
4. Ile miejsca na dysku jest dostępne i czy występuje problem z pojemnością?

Stany kondycji, uwzględnione są następujące: 

* **Dobra** — wykryte nie problemy dla maszyny Wirtualnej i czy działa on zgodnie z potrzebami. 
* **Krytyczne** — co najmniej jeden problem krytyczny, są wykrywane, które należy uwzględnić w celu przywrócenia normalnej stanu operacyjnego, zgodnie z oczekiwaniami.
* **Ostrzeżenie** — co najmniej jeden problem, są wykrywane, które muszą być kierowane lub stan kondycji może stać się krytyczna.
* **Nieznany** — Jeśli usługa nie był w stanie nawiązać połączenie z węzłem lub zasobników, jego stan zmieni się stan nieznany.
* **Nie można odnaleźć** — jedno obszaru roboczego, grupy zasobów lub subskrypcji zawierającej obszaru roboczego dla tego rozwiązania zostało usunięte.
* **Nieautoryzowany** — użytkownik nie ma wymaganych uprawnień do odczytu danych w obszarze roboczym.
* **Błąd** — wystąpił błąd podczas próby odczytu danych z obszaru roboczego.
* Nieprawidłowo **skonfigurowana** — Azure monitor dla kontenerów nie została poprawnie skonfigurowana w określonym obszarze roboczym.
* **Brak danych** -danych nie jest zgłaszany do obszaru roboczego w ciągu ostatnich 30 minut.

Stan kondycji oblicza ogólny stan klastra jako *najgorszy z* trzech stanów z jednym wyjątkiem — Jeśli którykolwiek z trzech stanów jest *nieznany*, ogólny stan klastra zostanie wyświetlony jako **nieznany**.  

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

- Użycie procesora CPU przez węzeł: **&nbsp;%** Zagregowana perspektywa użycia procesora CPU dla całego klastra. Wyniki zakresu czasu można filtrować, wybierając **Avg**, **Min**, **Max**, **50.** , **90**, i **95** w selektorze percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- Użycie pamięci przez węzeł: **&nbsp;%** Zagregowana perspektywa wykorzystania pamięci dla całego klastra. Wyniki zakresu czasu można filtrować, wybierając **Avg**, **Min**, **Max**, **50.** , **90**, i **95** w selektorze percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- **Liczba węzłów**: Liczba węzłów i stan z Kubernetes. Są Stany węzłów klastra, reprezentowany *wszystkich*, *gotowe*, i *niegotowe* i może być filtrowana indywidualnie lub w połączeniu w selektorze powyżej wykresu. 
- **Liczba działań pod**: Liczba pod i stan z Kubernetes. Są Stany zasobników reprezentowane *wszystkich*, *oczekujące*, *systemem*, i *nieznany* i może być filtrowana indywidualnie lub w Selektor powyżej wykresu. 

Możesz użyć klawiszy strzałek w lewo/w prawo, aby przechodzić przez każdy punkt danych na wykresie, a klawiszy strzałek w górę/w dół, aby przechodzić przez wiersze percentylu. Kliknięcie ikony pinezki w prawym górnym rogu dowolnego z wykresów spowoduje Przypinanie wybranego wykresu do ostatnio oglądanego pulpitu nawigacyjnego platformy Azure. Z poziomu pulpitu nawigacyjnego można zmieniać rozmiar wykresu i zmienić jego położenie. Wybranie wykresu z pulpitu nawigacyjnego spowoduje przekierowanie do Azure Monitor kontenerów i załadowanie poprawnego zakresu i widoku.

Azure Monitor for Containers obsługują również [Eksploratora metryk](../platform/metrics-getting-started.md)Azure monitor, w którym można tworzyć własne wykresy wykresów, skorelować i badać trendy oraz przypinać do pulpitów nawigacyjnych. W Eksploratorze metryk można również użyć kryteriów ustawionych do wizualizacji metryk jako podstawy [reguły alertu opartej](../platform/alerts-metric.md)na metrykach.  

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

Można zastosować [podział](../platform/metrics-charts.md#apply-splitting-to-a-chart) metryki w celu wyświetlenia jej według wymiarów i wizualizowania, jak różne segmenty są porównywane ze sobą. W przypadku węzła można podzielić wykres na wymiar *hosta* , a na przykład można podzielić go na segmenty według następujących wymiarów:

* Kontroler
* Kubernetes przestrzeń nazw
* Węzeł
* Etap

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizowanie węzłów, kontrolerów i kondycji kontenera

Po przełączeniu do **węzłów**, **kontrolerów**, i **kontenery** karta automatycznie wyświetlane w prawej części strony to okienko właściwości. Wyświetla właściwości wybranego elementu, w tym etykiety zdefiniowane do organizowania obiektów Kubernetes. W przypadku wybrania węzła systemu Linux jest on również widoczny w sekcji **pojemność dysku lokalnego** dostępna przestrzeń dyskowa i procent użyty dla każdego dysku prezentowanego w węźle. Kliknij pozycję **>>** połączyć w okienku view\hide okienka. 

![W okienku właściwości perspektyw Kubernetes przykład](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Podczas rozwijania obiektów w hierarchii, na podstawie aktualizacji okienka właściwości w obiekcie, który został wybrany. W okienku można wyświetlić zdarzeń Kubernetes za pomocą wstępnie zdefiniowanych wyszukiwań w dziennikach, klikając **dzienniki zdarzeń Kubernetes widoku** widocznego u góry okienka. Aby uzyskać więcej informacji o wyświetlaniu danych dzienników platformy Kubernetes, zobacz [przeszukiwanie dzienników w celu analizowania danych](container-insights-log-search.md). Podczas przeglądania zasobów klastra można zobaczyć dzienniki kontenerów i zdarzenia w czasie rzeczywistym. Aby uzyskać więcej informacji na temat tej funkcji oraz konfiguracji wymaganej do udzielenia i kontroli dostępu, zobacz [jak wyświetlać dzienniki w czasie rzeczywistym za pomocą Azure monitor dla kontenerów](container-insights-live-logs.md). 

Użyj opcji **+ Dodaj filtr** w górnej części strony, aby przefiltrować wyniki dla widoku według **usługi**, **węzła**, **obszaru nazw**lub **puli węzłów** , a po wybraniu zakresu filtru, wybierz jedną z wartości wyświetlanych w **Wybierz pole wartości** .  Po skonfigurowaniu filtru jest stosowana globalnie podczas wyświetlania dowolnego punktu widzenia klastra AKS.  Formuła obsługuje tylko znaku równości.  Możesz dodać dodatkowe filtry na podstawie pierwszy z nich w celu dalszego zawężenia wyników.  Na przykład, jeśli określono filtr według **węzła**, drugi filtr będzie tylko pozwalają na wybór **usługi** lub **Namespace**.  

![Przykład przy użyciu filtru, aby zawęzić wyniki](./media/container-insights-analyze/add-filter-option-01.png)

Określenie filtru w jednej karty w dalszym ciągu stosowane, gdy można wybrać inny, i zostanie usunięta po kliknięciu **x** symbol obok określonego filtru.   

Przełącz się do **węzłów** kartę i hierarchii wiersz poniżej model obiektów usługi Kubernetes, począwszy od węzła w klastrze. Rozwiń węzeł i można wyświetlić co najmniej jeden zasobników, uruchomione w węźle. Jeśli więcej niż jednego kontenera jest zgrupowany do zasobnik, są wyświetlane jako ostatni wiersz w hierarchii. Można również wyświetlić, jak wiele powiązanych obciążeniach-pod są uruchomione na hoście, jeśli host ma procesor lub dużego wykorzystania pamięci.

![Przykładowa hierarchia Kubernetes węzeł w widoku wydajności](./media/container-insights-analyze/containers-nodes-view.png)

Kontenery systemu Windows Server z systemem operacyjnym Windows Server 2019 są wyświetlane po wszystkich węzłach na liście z systemem Linux. Po rozwinięciu węzła systemu Windows Server można wyświetlić jeden lub więcej zasobników i kontenerów uruchomionych w węźle. Po wybraniu węzła w okienku właściwości są wyświetlane informacje o wersji, z wyłączeniem informacji o agencie, ponieważ na węzłach systemu Windows Server nie zainstalowano agenta.  

![Przykładowa hierarchia węzłów z węzłami systemu Windows Server na liście](./media/container-insights-analyze/nodes-view-windows.png) 

Kontener wystąpień wirtualnych węzły na platformie Azure z systemem operacyjnym Linux są wyświetlane po ostatnim węźle klastra AKS na liście.  Po rozwinięciu węzła wirtualnego usługi ACI, można wyświetlić co najmniej zasobników ACI, jak i kontenery uruchomione w węźle.  Metryki są nie zbieranych i zgłaszanych w przypadku węzłów tylko zasobników.

![Przykładowa hierarchia węzła Usługa Container Instances, na liście](./media/container-insights-analyze/nodes-view-aci.png)

Z rozwinięty węzeł można przejść do szczegółów z pod lub kontenerów uruchomione w węźle z kontrolerem, aby wyświetlić dane dotyczące wydajności przefiltrowana pod kątem tego kontrolera. Kliknij wartość, w obszarze **kontrolera** kolumny dla określonego węzła.   
![Przykład przechodzenie do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-node-controller.png)

Można wybrać kontrolery lub kontenerów w górnej części strony i sprawdź stan i użycia zasobów dla tych obiektów.  Jeśli zamiast tego chcesz przejrzeć wykorzystanie pamięci w **metryki** listy rozwijanej wybierz **RSS pamięci** lub **zestaw roboczy pamięci**. **Pamięć RSS** jest obsługiwany tylko w przypadku rozwiązania Kubernetes w wersji 1.8 i nowszych. W przeciwnym razie do wyświetlania wartości **Min&nbsp; %**  jako *NaN&nbsp;%* , który jest wartość typu danych liczbowych, który reprezentuje niezdefiniowany lub wartość wyniku.

W obszarze zestaw roboczy pamięci widoczne są zarówno pamięć rezydentną, jak i pamięć wirtualna (pamięć podręczna) oraz łączna ilość używanej aplikacji. W obszarze RSS pamięci jest wyświetlana tylko pamięć główna, która jest pamięcią rezydentną. Ta Metryka przedstawia rzeczywistą pojemność dostępnej pamięci.

![Widok wydajności węzłów kontenerów](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Domyślnie dane dotyczące wydajności opiera się na ostatnie 6 godzin, ale okno można zmienić za pomocą **TimeRange** opcji w lewym górnym rogu. Można również filtrować wyniki do zakresu, wybierając **Avg**, **Min**, **Max**, **50.** , **90**, i **95** w selektorze percentyl. 

![Wybór percentyl filtrowanie danych](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Gdy przesuniesz wskaźnik myszy nad wykres słupkowy w ramach **Trend** kolumny, Każdy słupek pokazuje użycie procesora CPU lub pamięci, w zależności od tego, który jest wybrana metryka, 15 minut przed upływem próbki. Po wybraniu wykresu trendu za pomocą klawiatury możesz użyć klawiszy Alt + PageUp lub Alt + PageDown, aby przetworzyć poszczególne paski osobno i uzyskać te same szczegóły, jak w przypadku MouseOver.

![Słupek wykresu słupkowego trendu na przykład](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

W następnym przykładzie, należy pamiętać, pierwszy na liście - węzeł *aks-nodepool1 -* , wartość **kontenery** jest 9, która stanowi pakiet zbiorczy całkowita liczba kontenerów wdrożonych.

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

![\<Nazwa > kontroleruje widok wydajności](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchia wiersz rozpoczyna się z kontrolerem i po rozwinięciu kontrolera, możesz wyświetlić co najmniej jeden zasobników.  Rozwiń zasobnik, a ostatni wiersz wyświetla kontenera grupowania zasobnik. Z kontrolera rozwinięty przechodzić do węzła, z którym jest uruchomiona na, aby wyświetlić dane dotyczące wydajności filtrowane dla tego węzła. Zasobników ACI, które nie są podłączone do kontrolera są ostatnio wymienione na liście.

![Przykładowa hierarchia kontrolerów z zasobników Container Instances, na liście](./media/container-insights-analyze/controllers-view-aci.png)

Kliknij wartość, w obszarze **węzła** kolumny dla określonego kontrolera.   

![Przykład przejść do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

W poniższej tabeli opisano informacje, które jest wyświetlane, gdy wyświetlanie kontrolerów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| Stan | Stan pakietu zbiorczego kontenery po ukończeniu uruchomione o stanie, takie jak *OK*, *zwolniony*, *niepowodzenie* *zatrzymane*, lub *Wstrzymana*. Jeśli kontener jest uruchomiony, ale stan był albo nie zostało prawidłowo wyświetlane lub nie została pobrana przez agenta i nie odpowiedział ponad 30 minut, stan jest *nieznany*. W poniższej tabeli znajdują się dodatkowe szczegóły ikonę stanu.|
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Średnia średnia średniej wartości procentowej każdej jednostki dla wybranej metryki i percentylu. |
| Avg, Min, Max, 50., 90  | Rzutowanie średniej millicore procesora CPU lub wydajności pamięci kontenera dla wybranego percentylu. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Containers | Łączna liczba kontenerów dla kontrolera lub zasobników. |
| Ponowne uruchomienie | Rzutowanie liczby ponownych uruchomień z kontenerów. |
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

Ikona stanu przedstawia liczbę oparte na zasobnik udostępnia. Pokazuje dwa najgorsze Stany i po umieszczeniu status Wyświetla stan pakietu zbiorczego z wszystkich zasobników w kontenerze. Jeśli nie ma stanu gotowości, wyświetlana jest wartość stanu **(0)** . 

W selektorze, wybierz **kontenery**.

![Wybierz kontenery widoku](./media/container-insights-analyze/containers-containers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontenerów usługi Kubernetes na platformie Azure i usługi Azure Container Instances.  

![\<Nazwa > kontroleruje widok wydajności](./media/container-insights-analyze/containers-containers-view.png)

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

## <a name="disk-capacity-workbook"></a>Skoroszyt pojemności dysku
Skoroszyty łączą tekst, [kwerendy dzienników](../log-query/query-language.md), [metryki](../platform/data-platform-metrics.md)i parametry w rozbudowanych raportach interaktywnych. Skoroszyty są edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Azure Monitor dla kontenerów zawiera skoroszyt, aby rozpocząć pracę, **pojemność dysku**.  Ten skoroszyt przedstawia interaktywne wykresy użycia dysku dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

- Użycie dysku% dla wszystkich dysków
- Wolne miejsce na dysku dla wszystkich dysków
- Tabela pokazująca dla każdego dysku węzła, jego zajęte miejsce (%), Trend wykorzystanego miejsca (%), wolne miejsce na dysku (GiB) i trend wolnego miejsca na dysku (GiB). Gdy w tabeli zostanie wybrany wiersz, procent zajętego miejsca i wolnego miejsca na dysku (GiB) pokazano poniżej 

Możesz uzyskać dostęp do tego skoroszytu, wybierając pozycję **pojemność dysku** z listy rozwijanej **Wyświetl skoroszyty** .  

![Lista rozwijana Wyświetl skoroszyty](./media/container-insights-analyze/view-workbooks-dropdown-list.png)


## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z tematem [tworzenie alertów dotyczących wydajności za pomocą Azure monitor dla kontenerów](container-insights-alerts.md) , aby dowiedzieć się, jak tworzyć alerty dotyczące wysokiego użycia procesora i pamięci w celu obsługi procesów i procedur operacyjnych DevOps. 
- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.
