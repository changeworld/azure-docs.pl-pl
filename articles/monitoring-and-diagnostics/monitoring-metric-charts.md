---
title: Azure Eksploratora metryk monitora
description: Więcej informacji na temat nowych funkcji w Eksploratorze metryk Monitor Azure
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.component: metrics
ms.openlocfilehash: a2611f89d9eef1ec6bac34389fa4db833aecc087
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264092"
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor Eksploratora metryk

Porada tego opisano dalej generowania metryki Monitor Azure wykresów środowisko znajdującego się w publicznej wersji zapoznawczej. Nowe środowisko obsługuje renderowanie wykresy metryki wielowymiarowej i podstawowe metryki z nie wymiarów. Można wykreślić wykresy nakładki metryki z różnych typów zasobów, wiele grup zasobów i subskrypcje. Wykresy metryki wielowymiarowej można dostosować przy zastosowaniu filtrów wymiarów, a także grupowanie. Dowolnego wykresu, w tym dostosowane wykresy można przypiąć do pulpitów nawigacyjnych.

Jeśli szukasz informacji na temat starego środowisko, który obsługuje tylko podstawowe metryki o wymiarach nie można znaleźć części pod tytułem "Dostęp do metryk za pośrednictwem portalu" w [przewodnik Przegląd metryki usługi Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="what-is-azure-monitor-metrics-explorer"></a>Co to jest Eksploratora metryk Monitor Azure?

Azure Eksploratora metryk Monitor wchodzi w skład portalu Microsoft Azure, który umożliwia kreślenia wykresy, wizualnie korelowanie trendów i badania wzrostów i DIP w wartości metryk. Eksploratora metryk jest niezbędny punkt początkowy badania różnych wydajności i dostępności problemów z aplikacjami i infrastruktury hostowana na platformie Azure lub monitorowane przez usługi Azure monitora. 

## <a name="what-are-metrics-in-azure"></a>Co to są metryki na platformie Azure?

Metryki na platformie Microsoft Azure są serii mierzonych i liczb, które są zbierane i są przechowywane wraz z upływem czasu. Brak metryki standard (lub "platformy") i metryki niestandardowe. Standardowa metryki są dostarczane przez platformę Azure, sama. Standardowa metryki odzwierciedlają statystyki kondycji i użycia zasobów platformy Azure. Podczas gdy metryki niestandardowe są wysyłane na platformie Azure przez aplikacje przy użyciu [aplikacji interfejsu API Insights dla niestandardowych zdarzeń](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Metryki niestandardowe są przechowywane w zasobów usługi Application Insights wraz z innych aplikacji określonych metryk.

## <a name="what-are-multi-dimensional-metrics"></a>Co to są metryki wielowymiarowej?

Udostępnianie wielu zasobów platformy Azure teraz metryki wielowymiarowej. Te metryki śledzić wielu serii wartości dla jednego lub wielu wymiarów nazwanego. Na przykład metryka "dostępnego miejsca na dysku" może mieć wymiar o nazwie "Dysk" o wartości "C:", "D:", który umożliwi wyświetlanie albo dostępnego miejsca na dysku na wszystkich dyskach lub dla każdego dysku pojedynczo. 

W poniższym przykładzie przedstawiono dwa zestawy danych dla metryki hipotetyczny o nazwie "Przepływność sieci". Pierwszego zestawu danych nie ma żadnych wymiarów. Drugi zestaw danych zawiera wartości z dwóch wymiarów "Adres IP" i "Direction":

### <a name="network-throughput"></a>Przepustowość sieci
(Ta metryka nie ma żadnych wymiarów)

 |Sygnatura czasowa        | Wartość metryki | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 Kb/s | 
   | 8/9/2017 8:15 | 1,141.4 Kb/s |
   | 8/9/2017 8:16 | 1,110.2 Kb/s |

Ta metryka-wymiarowej może jedynie odpowiedzi podstawowe pytanie, takich jak "jaki był Moje przepływność sieci w danym momencie?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Przepustowość sieci + dwóch wymiarów ("Adres IP" i "Direction")

| Sygnatura czasowa          | Wymiar "Adres IP" | Wymiar "Direction" | Wartość metryki| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Kierunek = "Wyślij"    | 646.5 kb/s |
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Kierunek = "Otrzymywać" | 420.1 KB/s |
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Kierunek = "Wyślij"    | 150.0 kb/s | 
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Kierunek = "Otrzymywać" | 115.2 Kb/s |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Kierunek = "Wyślij"    | 515.2 Kb/s |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Kierunek = "Otrzymywać" | 371.1 KB/s |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Kierunek = "Wyślij"    | 155.0 kb/s |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Kierunek = "Otrzymywać" | 100.1 KB/s |

Ta metryka pozwala uzyskać odpowiedzi na pytania, takie jak "jaki był przepływności sieci dla każdego adresu IP?" i "ile dane zostały wysłane i odebrane?" Metryki wielowymiarowej przenoszenia dodatkowe wartości analityczne i diagnostyki w porównaniu-wymiarowej metryki. 

## <a name="how-do-i-create-a-new-chart"></a>Jak utworzyć nowy wykres?

   > [!NOTE]
   > Niektóre funkcje środowisko metryki starego nie są jeszcze dostępne w nowych Eksploratora metryk. Nowe środowisko jest w wersji zapoznawczej, możesz kontynuować korzystanie z stary (z systemem innym niż wielowymiarowych) widok metryki monitora Azure. 

1. Otwórz Azure portal
2. Przejdź do nowego **Monitor** , a następnie wybierz **metryki (wersja zapoznawcza)**.

   ![Metryki podglądu](./media/monitoring-metric-charts/001.png)

3. **Metryki selektora** zostanie automatycznie otwarte dla Ciebie. Wybierz zasób z listy, aby wyświetlić jego skojarzony metryki. Na liście są wyświetlane tylko zasoby z metryki.

   ![Metryki podglądu](./media/monitoring-metric-charts/002.png)

   > [!NOTE]
   >Jeśli masz więcej niż jedną subskrypcją platformy Azure, Eksploratora metryk ściąga limit zasobów między wszystkie subskrypcje, które są wybrane w ustawieniach portalu -> filtru przez listę subskrypcji. Aby go zmienić, kliknij ikonę Koło zębate ustawień portalu u góry ekranu i wybierz subskrypcje, które chcesz użyć.

4. W przypadku niektórych typów zasobów (np. konta magazynu i maszyn wirtualnych), przed wybraniem metrykę musisz wybrać **usługi Sub**. Każda usługa sub niesie własny zestaw miar, które mają zastosowanie do tej usługi sub, a nie do innych usług sub.

   Na przykład każdy magazyn Azure ma metryki dla subservices "Blob", "Files", "Kolejki" i "Tabele", które są wszystkie części konta magazynu. Jednak metryki "liczba wiadomości w kolejce" dotyczy naturalnie subservice "Kolejki", a nie inne subservices konta magazynu.

   ![Metryki podglądu](./media/monitoring-metric-charts/003.png)

5. Wybierz metrykę, z listy. Jeśli znasz częściowa nazwa metryki, które mają można uruchomić wpisywać go, aby wyświetlić listę filtrowane dostępne metryki:

   ![Metryki podglądu](./media/monitoring-metric-charts/004.png)

6. Po wybraniu metrykę, spowoduje, że wykres z agregacją domyślny dla wybranych metryki. W tym momencie wystarczy kliknąć od **selektora metryki** go zamknąć. Wykres można również przełączyć się do różnych agregacji. Dla niektórych metryki przełączania agregacji można wybrać wartości, które mają być wyświetlane na wykresie. Na przykład można przełączać się między wartość średnią, minimalną i maksymalną. 

7. Klikając ikonę dodać metrykę ![Ikona metryki](./media/monitoring-metric-charts/icon001.png) i powtórzyć kroki 3 – 6 można dodać więcej metryki na tym samym wykresie.

   > [!NOTE]
   > Zwykle nie mają metryki różne jednostki miary (tj. "MS" i "kilobajtów") lub różnią się w skali na jeden wykres. Zamiast tego należy wziąć pod uwagę przy użyciu wielu wykresów. Kliknij przycisk Dodaj wykresu, aby utworzyć wiele wykresów w Eksploratorze metryk.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Jak zastosować filtry wykresy?

Filtry można stosować do wykresy pokazujące metryki z wymiarów. Na przykład, jeśli Metryka "Liczba transakcji" ma wymiar, "Typ odpowiedzi", który wskazuje, czy odpowiedź z transakcji powodzeniem lub niepowodzeniem, następnie filtrowanie, w tym wymiarze czy wykreślenia wykresu wiersz dla tylko pomyślnie (lub tylko nie powiodła się) transakcji. 

### <a name="to-add-a-filter"></a>Aby dodać filtr:

1. Kliknij ikonę Dodaj filtr ![ikona filtru](./media/monitoring-metric-charts/icon002.png) powyżej wykresu

2. Wybierz, który wymiar (właściwości), do których chcesz filtrować

   ![metryki obrazu](./media/monitoring-metric-charts/006.png)

3. Wybierz wartości wymiaru, które mają zostać uwzględnione podczas kreślenia wykresu (w tym przykładzie przedstawiono filtrowanie transakcji magazynu powiodło się):

   ![metryki obrazu](./media/monitoring-metric-charts/007.png)

4. Po wybraniu wartości filtru, kliknij poza selektor filtrów, aby je zamknąć. Teraz wykres pokazuje liczbę transakcji magazynowych nie powiodło się:

   ![metryki obrazu](./media/monitoring-metric-charts/008.png)

5. Można Powtórz kroki 1 – 4 do zastosowanie wielu filtrów do tej samej schematów.

## <a name="how-do-i-segment-a-chart"></a>Jak segment wykresu?

Podział metrykę według wymiaru do wizualizacji segmentów jak różne metryki porównania względem siebie i zidentyfikować odległe mniejsze segmenty wymiaru. 

### <a name="to-segment-a-chart"></a>Segment wykresu:

1. Kliknij ikonę Dodaj grupowania  ![metryki obrazu](./media/monitoring-metric-charts/icon003.png) powyżej wykresu.
 
   > [!NOTE]
   > Może mieć wielu filtrów, ale tylko jedno grupowanie na pojedynczego wykresu.

2. Wybierz wymiaru, na którym chcesz podzielić wykresu: 

   ![metryki obrazu](./media/monitoring-metric-charts/010.png)

   Teraz wykres zawiera teraz wiele wierszy, po jednym dla każdego segmentu wymiaru:

   ![metryki obrazu](./media/monitoring-metric-charts/012.png)

3. Kliknij przycisk od **selektora grupowania** go zamknąć.

   > [!NOTE]
   > Używać filtrowanie i grupowania dotyczących tego samego wymiaru, aby ukryć segmentów, które nie mają znaczenia dla danego scenariusza i ułatwić wykresy.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Jak Przypnij wykresy, aby pulpity nawigacyjne?

Po skonfigurowaniu wykresy, można dodać go do pulpitów nawigacyjnych, dzięki czemu można go ponownie wyświetlić, prawdopodobnie w kontekście innych monitorowania telemetrii lub udostępnienia zespołowi. 

Aby przypiąć wykres skonfigurowanych do pulpitu nawigacyjnego:

Po skonfigurowaniu wykresu, kliknij **akcje wykresu** menu w prawym rogu wykresu z góry, a następnie kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

   ![metryki obrazu](./media/monitoring-metric-charts/013.png)

## <a name="next-steps"></a>Następne kroki

  Odczyt [Tworzenie niestandardowych pulpitów nawigacyjnych KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) Aby dowiedzieć się więcej o najlepszych rozwiązaniach do tworzenia pulpitów nawigacyjnych można wykonać z metryki.