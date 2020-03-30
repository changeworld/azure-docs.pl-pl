---
title: Monitorowanie kubernetów za pomocą usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak można wyświetlać i analizować wydajność klastra Kubernetes za pomocą usługi Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 227fe70512536790d179797394b6fba22e7eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298371"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Monitorowanie wydajności klastra usługi Kubernetes za pomocą usługi Azure Monitor dla kontenerów

Za pomocą usługi Azure Monitor dla kontenerów można użyć wykresów wydajności i stanu kondycji do monitorowania obciążenia klastrów kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS), usłudze Azure Stack lub innym środowisku z dwóch perspektyw. Można monitorować bezpośrednio z klastra lub można wyświetlić wszystkie klastry w ramach subskrypcji z usługi Azure Monitor. Wyświetlanie wystąpień kontenera platformy Azure jest również możliwe podczas monitorowania określonego klastra AKS.

Ten artykuł ułatwia zrozumienie dwóch perspektyw i jak usługa Azure Monitor pomaga szybko ocenić, zbadać i rozwiązać wykryte problemy.

Aby uzyskać informacje dotyczące włączania usługi Azure Monitor dla kontenerów, zobacz [Wbudowany monitor Azure Dla kontenerów.](container-insights-onboard.md)

Usługa Azure Monitor udostępnia widok wieloas klastrowy, który pokazuje stan kondycji wszystkich monitorowanych klastrów kubernetes z systemem Linux i Windows Server 2019 wdrożonych w grupach zasobów w ramach subskrypcji. Pokazuje klastry wykryte we wszystkich środowiskach, które nie są monitorowane przez rozwiązanie. Można natychmiast zrozumieć kondycję klastra, a w tym miejscu można przejść do szczegółów do węzła i kontrolera strony wydajności lub przejdź do, aby wyświetlić wykresy wydajności klastra. W przypadku klastrów usługi AKS, które zostały wykryte i zidentyfikowane jako niemonitorowane, można włączyć monitorowanie ich w dowolnym momencie. 

Główne różnice w monitorowaniu klastra systemu Windows Server z usługą Azure Monitor dla kontenerów w porównaniu z klastrem systemu Linux są opisane w [tym](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) artykule.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Widok z wieloma klastrami z usługi Azure Monitor

Aby wyświetlić stan kondycji wszystkich wdrożonych klastrów kubernetes, wybierz **monitor** z lewego okienka w witrynie Azure portal. W sekcji **Insights** wybierz pozycję **Kontenery**. 

![Przykład pulpitu nawigacyjnego usługi Azure Monitor z wieloma klastrami](./media/container-insights-analyze/azmon-containers-multiview.png)

Można zakres wyniki przedstawione w siatce, aby pokazać klastry, które są:

* **Platforma Azure** — klastry AKS i AKS-Engine hostowane w usłudze Azure Kubernetes
* **Azure Stack (wersja zapoznawcza)** — klastry AKS-Engine hostowane w usłudze Azure Stack
* Klastry platformy Kubernetes hostowane lokalnie w usłudze **Inne niż Azure —wersja zapoznawcza**
* **Wszystkie** — umożliwia wyświetlanie wszystkich klastrów usługi Kubernetes hostowanych na platformie Azure, usłudze Azure Stack i środowiskach lokalnych, które są dołączane do usługi Azure Monitor dla kontenerów

Aby wyświetlić klastry z określonego środowiska, wybierz go z pigułki **Środowiska** w lewym górnym rogu strony.

![Przykład selektora pigułki środowiskowej](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Na karcie **Monitorowane klastry** dowiesz się, co następuje:

- Ile klastrów jest w stanie krytycznym lub złej kondycji, w porównaniu z ile są w dobrej kondycji lub nie raportowania (dalej nieznany stan).
- Czy wszystkie wdrożenia [usługi Azure Kubernetes Engine (aparat AKS)](https://github.com/Azure/aks-engine) są w dobrej kondycji.
- Ile węzłów oraz zasobników użytkowników i systemów są wdrażane na klaster.
- Ile miejsca na dysku jest dostępne i czy występuje problem z pojemnością.

Stanami zdrowia są: 

* **Zdrowy:** Nie wykryto żadnych problemów dla maszyny Wirtualnej i działa zgodnie z wymaganiami. 
* **Krytyczne:** Wykryto co najmniej jeden krytyczny problem, który musi zostać rozwiązany, aby przywrócić normalny stan operacyjny zgodnie z oczekiwaniami.
* **Ostrzeżenie:** Wykryto co najmniej jeden problem, który musi zostać rozwiązany lub stan zdrowia może stać się krytyczny.
* **Nieznany:** Jeśli usługa nie może nawiązać połączenia z węzłem lub zasobnikiem, stan zmieni się w nieznany stan.
* **Nie znaleziono:** Obszar roboczy, grupa zasobów lub subskrypcja zawierająca obszar roboczy dla tego rozwiązania została usunięta.
* **Nieautoryzowane:** Użytkownik nie ma wymaganych uprawnień do odczytu danych w obszarze roboczym.
* **Błąd:** Wystąpił błąd podczas próby odczytu danych z obszaru roboczego.
* **Nieprawidłowo skonfigurowany:** usługa Azure Monitor dla kontenerów nie została poprawnie skonfigurowana w określonym obszarze roboczym.
* **Brak danych:** dane nie zostały zgłoszone do obszaru roboczego przez ostatnie 30 minut.

Stan kondycji oblicza ogólny stan klastra jako *najgorszy z* trzech stanów z jednym wyjątkiem. Jeśli którykolwiek z trzech stanów jest nieznany, ogólny stan klastra pokazuje **Nieznany**. 

Poniższa tabela zawiera podział obliczeń, które sterują stanami kondycji monitorowanego klastra w widoku wieloas klastrowym.

| |Stan |Dostępność |  
|-------|-------|-----------------|  
|**Zasobnik użytkownika**| | |  
| |W dobrej kondycji |100% |  
| |Ostrzeżenie |90 - 99% |  
| |Krytyczny |<90% |  
| |Nieznane |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |  
|**Kapsuła systemowa**| | |  
| |W dobrej kondycji |100% |
| |Ostrzeżenie |Nie dotyczy |
| |Krytyczny |<100% |
| |Nieznane |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |
|**Node** | | |
| |W dobrej kondycji |>85% |
| |Ostrzeżenie |60 - 84% |
| |Krytyczny |<60% |
| |Nieznane |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |

Z listy klastrów można przejść do szczegółów do strony **Klastra,** wybierając nazwę klastra. Następnie przejdź do strony wydajności **Węzły,** wybierając zestawienie węzłów w kolumnie **Węzły** dla tego określonego klastra. Można też przejść do szczegółów strony wydajności **Kontrolery,** wybierając zestawienie **zbiorcze kolumny Zasobniki użytkownika** lub **Zasobniki systemu.**

## <a name="view-performance-directly-from-a-cluster"></a>Wyświetlanie wydajności bezpośrednio z klastra

Dostęp do usługi Azure Monitor dla kontenerów jest dostępny bezpośrednio z klastra AKS, wybierając**klaster** **aplikacji Insights** > z lewego okienka lub po wybraniu klastra z widoku wieloaklerwersytowego. Informacje o klastrze są podzielone na cztery perspektywy:

- Klaster
- Węzły 
- Kontrolery 
- Kontenery

>[!NOTE]
>Środowisko opisane w dalszej części tego artykułu mają również zastosowanie do wyświetlania wydajności i stanu kondycji klastrów usługi Kubernetes hostowanych w usłudze Azure Stack lub innym środowisku, gdy jest wybrany z widoku wieloakleraterowego. 

Strona domyślna otwiera i wyświetla cztery wykresy wydajności linii, które pokazują kluczowe metryki wydajności klastra. 

![Przykładowe wykresy wydajności na karcie Klaster](./media/container-insights-analyze/containers-cluster-perfview.png)

Na wykresach wydajności wyświetlane są cztery wskaźniki wydajności:

- **Wykorzystanie procesora&nbsp;CPU węzła:** zagregowana perspektywa wykorzystania procesora CPU dla całego klastra. Aby filtrować wyniki dla zakresu czasu, wybierz **opcję Średnia**, **Min**, **50,** **90,** **95,** lub **Max** w selektorze percentyli nad wykresem. Filtry mogą być używane indywidualnie lub łączone. 
- **Wykorzystanie pamięci&nbsp;węzła:** zagregowana perspektywa wykorzystania pamięci dla całego klastra. Aby filtrować wyniki dla zakresu czasu, wybierz **opcję Średnia**, **Min**, **50,** **90,** **95,** lub **Max** w selektorze percentyli nad wykresem. Filtry mogą być używane indywidualnie lub łączone. 
- **Liczba węzłów**: Liczba węzłów i stan z kubernetes. Stany reprezentowanych węzłów klastra to Suma, Gotowe i Nieugotowane. Można je filtrować pojedynczo lub łączyć w selektorze nad wykresem. 
- **Liczba aktywnych zasobników**: Liczba zasobników i stan z kubernetes. Stany reprezentowanych zasobników to Suma, Oczekujące, Uruchomione, Nieznane, Powiodło się lub Nie powiodło się. Można je filtrować pojedynczo lub łączyć w selektorze nad wykresem. 

Za pomocą klawiszy strzałek w lewo i w prawo można przełączać się między każdym punktem danych na wykresie. Za pomocą klawiszy strzałek w górę i w dół można przechodzić przez linie percentyla. Wybierz ikonę pinezki w prawym górnym rogu dowolnego wykresu, aby przypiąć wybrany wykres do ostatniego wyświetlanego pulpitu nawigacyjnego platformy Azure. Na pulpicie nawigacyjnym można zmienić rozmiar i zmienić położenie wykresu. Wybranie wykresu z pulpitu nawigacyjnego przekierowuje do usługi Azure Monitor dla kontenerów i ładuje poprawny zakres i widok.

Usługa Azure Monitor dla kontenerów obsługuje również [Eksploratora metryk](../platform/metrics-getting-started.md)usługi Azure Monitor, w którym można tworzyć własne wykresy wykresów, korelować i badać trendy oraz przypinać do pulpitów nawigacyjnych. W Eksploratorze metryk można również użyć kryteriów ustawionych w celu wizualizacji metryk jako podstawy [reguły alertów opartej na danych metryk.](../platform/alerts-metric.md) 

## <a name="view-container-metrics-in-metrics-explorer"></a>Wyświetlanie metryk kontenerów w Eksploratorze metryk

W Eksploratorze metryk można wyświetlić zagregowane metryki wykorzystania węzła i zasobnika z usługi Azure Monitor dla kontenerów. W poniższej tabeli podsumowano szczegóły ułatwiające zrozumienie, jak używać wykresów metryk do wizualizacji metryk kontenera.

|Przestrzeń nazw | Metryka | Opis | 
|----------|--------|-------------|
| insights.container/nodes | |
| | cpuUsageMillicores | Zagregowany pomiar wykorzystania procesora CPU w klastrze. Jest to rdzeń procesora podzielony na 1000 jednostek (milli = 1000). Służy do określania użycia rdzeni w kontenerze, w którym wiele aplikacji może używać jednego rdzenia.| 
| | cpuUsagePercentage | Zagregowane średnie wykorzystanie procesora CPU mierzone w procentach w klastrze.|
| | pamięćZdjęty | Pamięć RSS kontenera używana w bajtach.| 
| | memoryRssPercentage | Pamięć RSS kontenera używana w procentach.|
| | memoryWorkingSetBytes | Używany kontenerowy zestaw roboczy pamięci.| 
| | memoryWorkingSetPercentage | Pamięć zestawu roboczego kontenera używana w procentach. | 
| | nodesCount (liczba węzłów) | Liczba węzłów z kubernetes.|
| insights.container/zasobniki | |
| | Liczba PodCount | Liczba zasobników z kubernetes.|

Można [podzielić](../platform/metrics-charts.md#apply-splitting-to-a-chart) metrykę, aby wyświetlić ją według wymiaru i wizualizować, jak różne segmenty są ze sobą porównywane. W przypadku węzła można segmentować wykres według wymiaru *hosta.* W zasobniku można segmentować go według następujących wymiarów:

* Kontrolera
* Obszar nazw kubernetes
* Węzeł
* Etap

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizowanie węzłów, kontrolerów i kondycji kontenera

Po przełączeniu się na karty **Węzły,** **Kontrolery**i **Kontenery** po prawej stronie strony zostanie automatycznie wyświetlone okienko właściwości. Pokazuje właściwości wybranego elementu, który zawiera etykiety zdefiniowane w celu organizowania obiektów Kubernetes. Po wybraniu węzła systemu Linux w sekcji **Lokalna pojemność dysku** jest również wyświetlana dostępna ilość miejsca na dysku i procent używany dla każdego dysku przedstawionego w węźle. Zaznacz **>>** łącze w okienku, aby wyświetlić lub ukryć okienko.

Podczas rozwijania obiektów w hierarchii okienko właściwości jest aktualizowane na podstawie wybranego obiektu. W okienku można również wyświetlać dzienniki kontenerów kubernetes (stdout/stderror), zdarzenia i metryki zasobników, wybierając łącze **Wyświetl dane na żywo (podgląd)** u góry okienka. Aby uzyskać więcej informacji na temat konfiguracji wymaganej do udzielenia i kontrolowania dostępu do wyświetlania tych danych, zobacz [Konfigurowanie danych na żywo (wersja zapoznawcza)](container-insights-livedata-setup.md). Podczas przeglądania zasobów klastra, można zobaczyć te dane z kontenera w czasie rzeczywistym. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Jak wyświetlić dzienniki, zdarzenia i dane zasobników kubernetes w czasie rzeczywistym](container-insights-livedata-overview.md). Aby wyświetlić dane dziennika kubernetes przechowywane w obszarze roboczym na podstawie wstępnie zdefiniowanych wyszukiwań w dzienniku, wybierz pozycję **Wyświetl dzienniki kontenerów** z listy rozwijanej **Wyświetl w analizie.** Aby uzyskać dodatkowe informacje na ten temat, zobacz [Dzienniki wyszukiwania do analizowania danych](container-insights-log-search.md#search-logs-to-analyze-data).

Użyj opcji **+ Dodaj filtr** u góry strony, aby filtrować wyniki dla widoku według **usługi,** **węzła,** **obszaru nazw**lub **puli węzłów**. Po wybraniu zakresu filtru wybierz jedną z wartości wyświetlanych w polu **Wybierz wartość.Pl.** Po skonfigurowaniu filtru jest stosowany globalnie podczas przeglądania dowolnej perspektywy klastra AKS. Formuła obsługuje tylko znak równości. Możesz dodać dodatkowe filtry na pierwszym, aby jeszcze bardziej zawęzić wyniki. Na przykład, jeśli określisz filtr według **węzła,** można wybrać tylko **usługę** lub **obszar nazw** dla drugiego filtru.

Określanie filtru na jednej karcie jest nadal stosowane po wybraniu innej. Jest usuwany po wybraniu symbolu **x** obok określonego filtru. 

Przełącz się do karty **Węzły,** a hierarchia wierszy jest zgodna z modelem obiektu Kubernetes, który rozpoczyna się od węzła w klastrze. Rozwiń węzeł, aby wyświetlić jeden lub więcej zasobników uruchomionych w węźle. Jeśli więcej niż jeden kontener jest zgrupowany do zasobnika, są one wyświetlane jako ostatni wiersz w hierarchii. Można również wyświetlić, ile obciążeń niezwiązanych z zasobami jest uruchomionych na hoście, jeśli host ma ciśnienie procesora lub pamięci.

![Przykład hierarchii węzłów Kubernetes w widoku wydajności](./media/container-insights-analyze/containers-nodes-view.png)

Kontenery systemu Windows Server z systemem operacyjnym Windows Server 2019 są wyświetlane po wszystkich węzłach opartych na systemie Linux na liście. Po rozwinięciu węzła systemu Windows Server można wyświetlić co najmniej jeden zasobników i kontenerów uruchamianych w węźle. Po wybraniu węzła w okienku właściwości są wyświetlane informacje o wersji. Informacje o agencie są wykluczone, ponieważ węzły systemu Windows Server nie mają zainstalowanego agenta. 

![Przykładowa hierarchia węzłów z wymienionymi węzłami systemu Windows Server](./media/container-insights-analyze/nodes-view-windows.png) 

Węzły wirtualne wystąpienia kontenerów platformy Azure, które uruchamiają system operacyjny Linux, są wyświetlane po ostatnim węźle klastra AKS na liście. Po rozwinięciu węzła wirtualnego wystąpień kontenera można wyświetlić co najmniej jeden kontener wystąpień zasobników i kontenerów, które są uruchamiane w węźle. Metryki nie są zbierane i zgłaszane dla węzłów, tylko dla zasobników.

![Przykładowa hierarchia węzłów z wymienionymi wystąpieniami kontenerów](./media/container-insights-analyze/nodes-view-aci.png)

Z rozwiniętego węzła można przejść do szczegółów z zasobnika lub kontenera, który działa w węźle do kontrolera, aby wyświetlić dane wydajności filtrowane dla tego kontrolera. Wybierz wartość w kolumnie **Kontroler** dla określonego węzła.
 
![Przykład przechodzenia do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-node-controller.png)

Wybierz kontrolery lub kontenery w górnej części strony, aby przejrzeć stan i wykorzystanie zasobów dla tych obiektów. Aby przejrzeć wykorzystanie pamięci, z listy rozwijanej **Metryka** wybierz pozycję **Pamięć RSS** lub **Zestaw roboczy pamięci**. **Pamięć RSS** jest obsługiwana tylko dla usługi Kubernetes w wersji 1.8 i nowszej. W przeciwnym razie można wyświetlić wartości **&nbsp; Min** jako *NaN&nbsp;*, która jest wartością typu danych liczbowych, która reprezentuje wartość niezdefiniowana lub niereprezentowalna.

![Widok wydajności węzłów kontenerów](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Zestaw roboczy pamięci** pokazuje zarówno pamięć rezydentną, jak i pamięć wirtualną (pamięć podręczna) i jest sumą tego, czego używa aplikacja. **Pamięć RSS** pokazuje tylko pamięć główną (która jest niczym innym jak pamięcią rezydentną innymi słowy). Ta metryka pokazuje rzeczywistą pojemność dostępnej pamięci. Jaka jest różnica między pamięcią rezydentną a pamięcią wirtualną?

- Pamięć rezydentna lub pamięć główna, jest rzeczywistą ilością pamięci komputera dostępne dla węzłów klastra.

- Pamięć wirtualna jest zarezerwowana przestrzeń dysku twardego (cache) używane przez system operacyjny do wymiany danych z pamięci na dysk, gdy pod ciśnieniem pamięci, a następnie pobrać go z powrotem do pamięci w razie potrzeby.

Domyślnie dane dotyczące wydajności są oparte na ostatnich sześciu godzinach, ale okno można zmienić za pomocą opcji **TimeRange** w lewym górnym rogu. Wyniki można również filtrować w zakresie czasu, wybierając **min,** **50,** **90,** **95 i** **Max** w selektorze percentyla. **Avg** 

![Wybór percentyla do filtrowania danych](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Po umieszczeniu wskaźnika myszy na wykresie słupkowym w kolumnie **Trend** na każdym pasku jest wyświetlane użycie procesora CPU lub pamięci, w zależności od wybranej metryki, w okresie próbkowania wynoszącym 15 minut. Po wybraniu wykresu trendu za pomocą klawisza Alt+Page up lub klawisza Alt+Page down można przełączać się między każdym paskiem indywidualnie. Otrzymasz te same szczegóły, które chcesz, jeśli najechał na pasku.

![Przykład na wykres słupkowy trendu](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

W następnym przykładzie dla pierwszego węzła na liście *aks-nodepool1-*, wartość **kontenerów** wynosi 9. Ta wartość jest zestawienia całkowitej liczby kontenerów wdrożonych.

![Przykład zestawienia kontenerów na węzeł](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Te informacje mogą pomóc szybko określić, czy masz właściwą równowagę kontenerów między węzłami w klastrze. 

Informacje, które są prezentowane podczas wyświetlania **węzłów** kartę jest opisany w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Nazwa | Nazwa hosta. |
| Stan | Widok kubernetes stanu węzła. |
| Min&nbsp;%,&nbsp;Średnia %, 50%,&nbsp;90%,&nbsp;95%,&nbsp;Maks.&nbsp;%  | Średni procent węzła na podstawie percentylu podczas wybranego czasu trwania. |
| Min, Avg, 50, 90, 95, Max | Średnia wartość rzeczywista węzłów na podstawie percentylu w wybranym czasie trwania. Średnia wartość jest mierzona z limitu procesora CPU/pamięci ustawionego dla węzła. W przypadku zasobników i kontenerów jest to średnia wartość raportowana przez hosta. |
| Kontenery | Liczba kontenerów. |
| Uptime | Reprezentuje czas od uruchomienia lub ponownego uruchomienia węzła. |
| Kontrolera | Tylko dla pojemników i strąków. Pokazuje, w którym kontrolerze się znajduje. Nie wszystkie zasobniki znajdują się w kontrolerze, więc niektóre mogą wyświetlać **n / a**. | 
| Trend&nbsp;Min %,&nbsp;Średnia %,&nbsp;50%,&nbsp;90%,&nbsp;95%, Maks.&nbsp;% | Trend wykresu słupkowego reprezentuje średni procent metryki percentylu kontrolera. |

Można zauważyć obciążenie po rozwinięciu węzła o nazwie **Inny proces**. Reprezentuje niekontralizowanych procesów, które są uruchamiane w węźle i zawiera:

* Samodzielnie zarządzane lub zarządzane procesy kubernetes nie konteneryzowane

* Procesy wykonywania kontenera  

* Kubelet  

* Procesy systemowe uruchomione w węźle

* Inne obciążenia inne niż Kubernetes uruchomione na sprzęcie węzła lub maszynie wirtualnej

Jest on obliczany przez: *Całkowite użycie z CAdvisor* - *Użycie z konteneryzowany proces*.  

W selektorze wybierz **pozycję Kontrolery**.

![Wybierz widok Kontrolery](./media/container-insights-analyze/containers-controllers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontrolerów i wystąpień kontenerów kontrolerów węzłów wirtualnych lub zasobników węzłów wirtualnych niepodłączony do kontrolera.

![\<Nazwa> widoku wydajności kontrolerów](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchia wierszy rozpoczyna się od kontrolera. Po rozwinięciu kontrolera można wyświetlić jeden lub więcej zasobników. Rozwiń zasobnik, a ostatni wiersz wyświetla kontener zgrupowany z zasobnikiem. Z rozszerzonego kontrolera można przejść do węzła, na który jest uruchomiony, aby wyświetlić dane o wydajności filtrowane dla tego węzła. Zasobników wystąpień kontenera niepodłączony do kontrolera są wymienione jako ostatnie na liście.

![Przykładowa hierarchia kontrolerów z wymienionymi zasobnikami wystąpień kontenerów](./media/container-insights-analyze/controllers-view-aci.png)

Wybierz wartość w kolumnie **Węzeł** dla określonego kontrolera.

![Przykład przechodzenia do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

Informacje wyświetlane podczas wyświetlania kontrolerów są opisane w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Nazwa | Nazwa kontrolera.|
| Stan | Stan zestawienia kontenerów po jego zakończeniu działa ze stanem, takim jak *OK*, *Zakończone*, *Nie powiodło się,* *Zatrzymano*lub *Wstrzymano*. Jeśli kontener jest uruchomiony, ale stan nie został poprawnie wyświetlony lub nie został odebrany przez agenta i nie odpowiedział przez więcej niż 30 minut, stan jest *nieznany.* Dodatkowe szczegóły dotyczące ikony stanu znajdują się w poniższej tabeli.|
| Min&nbsp;%,&nbsp;Średnia %, 50%,&nbsp;90%,&nbsp;95%,&nbsp;Maks.&nbsp;%| Średnia zbiorcza średniego procentu każdej encji dla wybranej metryki i percentylu. |
| Min, Avg, 50, 90, 95, Max  | Zestawienie średniej wydajności procesora CPU lub pamięci kontenera dla wybranego percentyla. Średnia wartość jest mierzona z limitu CPU/memory ustawionego dla zasobnika. |
| Kontenery | Całkowita liczba kontenerów dla kontrolera lub zasobnika. |
| Ponownym uruchomieniu | Zestawienie liczby ponownych uruchomień z kontenerów. |
| Uptime | Reprezentuje czas od momentu rozpoczęcia kontenera. |
| Węzeł | Tylko dla pojemników i strąków. Pokazuje, w którym kontrolerze się znajduje. | 
| Trend&nbsp;Min %,&nbsp;Średnia %,&nbsp;50%,&nbsp;90%,&nbsp;95%, Maks.&nbsp;% | Trend wykresu słupkowego reprezentuje średnią metrykę percentyla kontrolera. |

Ikony w polu stanu wskazują stan online kontenerów.
 
| Ikona | Stan | 
|--------|-------------|
| ![Ikona stanu gotowego biegu](./media/container-insights-analyze/containers-ready-icon.png) | Działa (gotowy)|
| ![Ikona stanu oczekiwania lub wstrzymane](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekiwanie lub wstrzymane|
| ![Ostatnio zgłoszona ikona stanu bieżącego](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnio zgłoszone uruchomione, ale nie odpowiedział przez więcej niż 30 minut|
| ![Ikona stanu Pomyślny](./media/container-insights-analyze/containers-green-icon.png) | Zatrzymano lub nie udało się zatrzymać|

Ikona stanu wyświetla liczbę na podstawie tego, co zapewnia zasobnik. Pokazuje najgorsze dwa stany, a po najechaniu kursorem na stan wyświetla stan zestawienia ze wszystkich zasobników w kontenerze. Jeśli stan nie jest gotowy, wyświetlana jest wartość stanu **(0).**

W selektorze wybierz pozycję **Kontenery**.

![Wybierz widok Kontenery](./media/container-insights-analyze/containers-containers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontenerów azure kubernetes i azure container instances. 

![\<Nazwa> widoku wydajności kontenerów](./media/container-insights-analyze/containers-containers-view.png)

Z kontenera można przejść do zasobnika lub węzła, aby wyświetlić dane wydajności filtrowane dla tego obiektu. Wybierz wartość w kolumnie **Zasob lub** **Węzeł** dla określonego kontenera.

![Przykład przechodzenia do szczegółów z węzła na kontenery w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

Informacje wyświetlane podczas wyświetlania kontenerów opisano w poniższej tabeli.

| Kolumna | Opis | 
|--------|-------------|
| Nazwa | Nazwa kontrolera.|
| Stan | Stan kontenerów, jeśli istnieje. Dodatkowe szczegóły ikony stanu znajdują się w następnej tabeli.|
| Min&nbsp;%,&nbsp;Średnia %, 50%,&nbsp;90%,&nbsp;95%,&nbsp;Maks.&nbsp;% | Zestawienie średniego procentu każdej encji dla wybranej metryki i percentyla. |
| Min, Avg, 50, 90, 95, Max | Zestawienie średniej millicore cpu lub wydajność pamięci kontenera dla wybranego percentyla. Średnia wartość jest mierzona z limitu CPU/memory ustawionego dla zasobnika. |
| Pod | Kontener, w którym znajduje się zasobnik.| 
| Węzeł |  Węzeł, w którym znajduje się kontener. | 
| Ponownym uruchomieniu | Reprezentuje czas od momentu rozpoczęcia kontenera. |
| Uptime | Reprezentuje czas od uruchomienia lub ponownego uruchomienia kontenera. |
| Trend&nbsp;Min %,&nbsp;Średnia %,&nbsp;50%,&nbsp;90%,&nbsp;95%, Maks.&nbsp;% | Trend wykresu słupkowego reprezentuje średni procent metryki percentylu kontenera. |

Ikony w polu stanu wskazują stany online zasobników, zgodnie z opisem w poniższej tabeli.
 
| Ikona | Stan |  
|--------|-------------|  
| ![Ikona stanu gotowego biegu](./media/container-insights-analyze/containers-ready-icon.png) | Działa (gotowy)|  
| ![Ikona stanu oczekiwania lub wstrzymane](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekiwanie lub wstrzymane|  
| ![Ostatnio zgłoszona ikona stanu bieżącego](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnio zgłoszone uruchomione, ale nie odpowiedział w więcej niż 30 minut|  
| ![Ikona stanu zakończonego](./media/container-insights-analyze/containers-terminated-icon.png) | Zatrzymano lub nie udało się zatrzymać|  
| ![Ikona stanu niepowodzenie](./media/container-insights-analyze/containers-failed-icon.png) | Stan nie powiodło się |  

## <a name="workbooks"></a>Skoroszyty

Skoroszyty łączą tekst, [zapytania dziennika,](../log-query/query-language.md) [metryki](../platform/data-platform-metrics.md)i parametry w zaawansowane raporty interaktywne. Skoroszyty mogą być edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Usługa Azure Monitor dla kontenerów zawiera cztery skoroszyty na początek:

- **Pojemność dysku**: Przedstawia interaktywne wykresy użycia dysku dla każdego dysku przedstawionego do węzła w kontenerze według następujących perspektyw:

    - Procent użycia dysku dla wszystkich dysków.
    - Wolne miejsce na dysku dla wszystkich dysków.
    - Siatka, która pokazuje dysk każdego węzła, jego procent używanego miejsca, trend procentu używanego miejsca, wolne miejsce na dysku (GiB) i trend wolnego miejsca na dysku (GiB). Gdy wiersz jest zaznaczony w tabeli, procent używanego miejsca i wolnego miejsca na dysku (GiB) jest wyświetlany pod wierszem. 

- **We/Wy dysku**: Przedstawia interaktywne wykresy wykorzystania dysku dla każdego dysku przedstawionego do węzła w kontenerze według następujących perspektyw:

    - We/Wy dysku podsumowane na wszystkich dyskach przez odczyt bajtów/s, zapisy bajtów/s oraz odczytywanie i zapis według trendów bajtów/s.
    - Na ośmiu wykresach wydajności wyświetlane są kluczowe wskaźniki wydajności ułatwiające pomiar i identyfikowanie wąskich gardeł we/wy dysku.

- **Kubelet**: Zawiera dwie siatki, które pokazują statystyki operacyjne węzła kluczowego:

    - Omówienie przez siatkę węzłów podsumowuje całkowitą operację, całkowita liczba błędów i pomyślne operacje według procentu i trendu dla każdego węzła.
    - Przegląd według typu operacji podsumowuje dla każdej operacji całkowitą operację, całkowita liczba błędów i pomyślne operacje według procentu i trendu.

- **Sieć:** Przedstawia interaktywne wykresy wykorzystania sieci dla karty sieciowej każdego węzła, a siatka przedstawia kluczowe wskaźniki wydajności, aby pomóc zmierzyć wydajność kart sieciowych.

Dostęp do tych skoroszytów można uzyskać, zaznaczając każdy z nich z listy rozwijanej **Widok skoroszytów.**

![Lista rozwijana Wyświetlanie skoroszytów](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [Tworzenie alertów wydajności za pomocą usługi Azure Monitor dla kontenerów,](container-insights-alerts.md) aby dowiedzieć się, jak tworzyć alerty dotyczące wysokiego wykorzystania procesora CPU i pamięci w celu obsługi procesów i procedur operacyjnych.

- Wyświetlanie [przykładów zapytań dziennika,](container-insights-log-search.md#search-logs-to-analyze-data) aby wyświetlić wstępnie zdefiniowane kwerendy i przykłady do oceny lub dostosowania do alertów, wizualizacji lub analizowania klastrów.

- Wyświetl [kondycję klastra monitora,](container-insights-health.md) aby dowiedzieć się więcej o wyświetlaniu stanu kondycji klastra usługi Kubernetes.
