---
title: Zrozumienie, jak metryki, alerty pracy w usłudze Azure Monitor.
description: Zapoznaj się z omówieniem, co można zrobić za pomocą alertów dotyczących metryk i jak działają w usłudze Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 6138a9ff6bb6d34b09c49fa7b5dbb67cbf5eb1b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244907"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Zrozumienie, jak metryki, alerty pracy w usłudze Azure Monitor

Alerty metryk w usłudze Azure Monitor działać na podstawie metryk wielowymiarowych. Metryki te mogą być [metryk platformy](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [metryki niestandardowe](../../azure-monitor/platform/metrics-custom-overview.md), [popularnych dzienników z usługi Azure Monitor przekonwertowane na metryki](../../azure-monitor/platform/alerts-metric-logs.md) i metryk usługi Application Insights. Alerty metryki oceny w regularnych odstępach czasu, aby sprawdzić, czy warunki na co najmniej jeden metryki szeregów czasowych są spełnione i otrzymywać powiadomienia, gdy są spełnione ocen. Alerty metryki są stanowe, czyli one tylko wysyłacie powiadomienia po zmianie stanu.

## <a name="how-do-metric-alerts-work"></a>Jak działają alerty metryki

Można zdefiniować regułę alertu metryki, określając zasób docelowy, który ma być monitorowany, Nazwa metryki, typ warunku (statycznej lub dynamicznej) i warunek (operator i wartość progową/czułości) i grupę akcji do wyzwalane, gdy zostanie wyzwolony reguły alertu. Typy warunków wpływa na sposób, w jaki są określane wartości progowe. [Dowiedz się więcej o opcjach Typ i ważność warunek dynamicznymi progami](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Reguła alertu o typie warunek statyczny

Załóżmy, że utworzono regułę alertu metryki prosty próg statyczne w następujący sposób:

- (Do monitorowania zasobów platformy Azure) zasób docelowy: myVM
- Metryka: Procentowe użycie procesora CPU
- Typ warunku: Static
- Agregacja czasu (statystyki, który jest uruchamiany za pośrednictwem pierwotne wartości metryk. Czasu obsługiwane operacje agregacji są minimalny, maksymalny, Avg, łącznie i Count): Średnia
- Okres (wygląd wsteczny okna za pośrednictwem metryki, które są sprawdzane w wartości): W ciągu ostatnich 5 minut.
- Frequency (częstotliwość, z którą alert dotyczący metryki sprawdza, jeśli warunki są spełnione): 1 min
- Operator: Większe niż
- Wartość progowa: 70

Od chwili, gdy zostanie utworzona reguła alertu monitor jest uruchamiane co 1 min i szuka w wartości metryk z ostatnich 5 minut i sprawdza się, jeśli średnia tych wartości przekracza 70. Jeśli warunek jest spełniony, oznacza to, średnie użycie procesora CPU procent dla ostatnich 5 minut przekracza 70, reguła alertu wyzwala powiadomienie aktywowane. Jeśli wiadomość e-mail lub Akcja punktu zaczepienia sieci web skonfigurowano grupy akcji skojarzonych z regułą alertu, otrzymasz powiadomienie aktywowanego zarówno.

### <a name="alert-rule-with-dynamic-condition-type"></a>Reguła alertu o typie warunek dynamiczny

Załóżmy, że utworzono proste reguła alertu metryki dynamicznymi progami w następujący sposób:

- (Do monitorowania zasobów platformy Azure) zasób docelowy: myVM
- Metryka: Procentowe użycie procesora CPU
- Typ warunku: Dynamiczne
- Agregacja czasu (statystyki, który jest uruchamiany za pośrednictwem pierwotne wartości metryk. Czasu obsługiwane operacje agregacji są minimalny, maksymalny, Avg, łącznie i Count): Średnia
- Okres (wygląd wsteczny okna za pośrednictwem metryki, które są sprawdzane w wartości): W ciągu ostatnich 5 minut.
- Frequency (częstotliwość, z którą alert dotyczący metryki sprawdza, jeśli warunki są spełnione): 1 min
- Operator: Większe niż
- Czułość: Średni
- Wygląd wstecz okresy: 4
- Liczba naruszeń: 4

Po utworzeniu reguły alertu dynamicznymi progami algorytmu uczenia maszynowego uzyskania danych historycznych, który jest dostępny, obliczy wartości progowej, która najlepiej pasuje do wzorca zachowanie metryki serii, a będzie stale Dowiedz się, na podstawie nowych danych Próg dokładniejsze.

Od chwili, gdy zostanie utworzona reguła alertu monitor jest uruchamiane co 1 min i analizuje wartości metryk w ciągu ostatnich 20 minut, pogrupowane według okresy 5 minut i sprawdza, czy gdy średnią z okresu wartości w każdym z 4 okresów przekracza próg oczekiwane. Jeśli warunek jest spełniony, oznacza to, średnia procentowe użycie procesora CPU w ciągu ostatnich 20 minut (cztery okresy 5 minut) odpowiadają regułom z cztery razy zachowanie jest oczekiwane, reguła alertu wyzwala powiadomienie aktywowane. Jeśli wiadomość e-mail lub Akcja punktu zaczepienia sieci web skonfigurowano grupy akcji skojarzonych z regułą alertu, otrzymasz powiadomienie aktywowanego zarówno.

### <a name="view-and-resolution-of-fired-alerts"></a>Wyświetlanie i rozpoznawanie wyzwolone alerty

Powyższe przykłady reguł alertów wyzwalania można również wyświetlać w witrynie Azure portal w **wszystkie alerty** bloku.

Załóżmy, że użycie na "myVM" nadal jest powyżej wartości progowej przez w kolejnych kontroli, reguły alertu nie zostanie uruchomiony ponownie, dopóki warunki nie są rozwiązane.

Po pewnym czasie wykorzystanie na "myVM" wróci do normalnego oznacza to, nie spadnie poniżej wartości progowej. Reguła alertu monitoruje warunku dwa razy, aby Wyślij powiadomienie o usunięciu. Reguły alertu wysyła komunikat o usunięciu dezaktywowane podczas warunek alertu nie został spełniony dla trzech następujących po sobie kropek redukcji szumu w przypadku niestabilny warunków.

Jak rozpoznać powiadomienie jest wysyłane za pomocą elementów web hook lub wiadomości e-mail, stan wystąpienia alertu (nazywane stan monitora) w witrynie Azure portal jest również ustawiona na rozwiązany.

### <a name="using-dimensions"></a>Przy użyciu wymiarów

Alerty metryk w usłudze Azure Monitor obsługują także monitorowanie wielu kombinacje wartości wymiarów z jedną regułą. Przyjrzyjmy się, warto używać wielu kombinacji za pomocą przykładu.

Załóżmy, że masz plan usługi App Service dla witryny sieci Web. Chcesz monitorować użycie procesora CPU na wiele wystąpień witryny sieci web/aplikacji. Możesz to zrobić przy użyciu regułę alertu metryki w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: Procentowe użycie procesora CPU
- Typ warunku: Static
- Wymiary
  - Wystąpienia = InstanceName1, InstanceName2
- Agregacja czasu: Średnia
- Okres: W ciągu ostatnich 5 minut.
- Częstotliwość: 1 min
- Operator: GreaterThan
- Wartość progowa: 70

Podobnie jak wcześniej, ta reguła umożliwia monitorowanie Jeśli średniego użycia procesora CPU dla ostatnich 5 minut przekracza 70%. Jednak ta zasada umożliwia monitorowanie dwóch wystąpień z witryną sieci Web. Każde wystąpienie będzie są monitorowane indywidualnie i będą wyświetlane powiadomienia indywidualnie.

Załóżmy, że masz aplikację sieci web, która ma do czynienia z ogromną żądanie i będzie konieczne dodanie większej liczby wystąpień. Reguła powyżej nadal monitoruje tylko dwa wystąpienia. Można jednak utworzyć regułę w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: Procentowe użycie procesora CPU
- Typ warunku: Static
- Wymiary
  - Wystąpienia = *
- Agregacja czasu: Średnia
- Okres: W ciągu ostatnich 5 minut.
- Częstotliwość: 1 min
- Operator: GreaterThan
- Wartość progowa: 70

Ta reguła będzie automatycznie monitorować wszystkie wartości tj wystąpienia można monitorować wystąpień, jak pojawiają się bez konieczności modyfikowania usługi reguła alertu metryki ponownie.

Podczas monitorowania wielu wymiarach, dynamicznymi progami regułę alertów można tworzyć dostosowane progi setek serii metryki naraz. Dynamicznymi progami powoduje mniejszej liczby reguł alertów do zarządzania i dużo czasu na redukcję zarządzania i tworzenia reguł alertów.

Załóżmy, że masz aplikację sieci web za pomocą wielu wystąpień i nie wiesz, co to jest najbardziej odpowiedni próg. Powyższe zasady będą zawsze używać próg wynoszący 70%. Można jednak utworzyć regułę w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: Procentowe użycie procesora CPU
- Typ warunku: Dynamiczne
- Wymiary
  - Wystąpienia = *
- Agregacja czasu: Średnia
- Okres: W ciągu ostatnich 5 minut.
- Częstotliwość: 1 min
- Operator: GreaterThan
- Czułość: Średni
- Wygląd wstecz okresy: 1
- Liczba naruszeń: 1

Ta reguła umożliwia monitorowanie Jeśli średniego użycia procesora CPU dla ostatnich 5 minut przekroczy oczekiwane zachowanie dla każdego wystąpienia. Ta zasada może monitorować wystąpień, jak pojawiają się bez konieczności modyfikowania usługi reguła alertu metryki ponownie. Każde wystąpienie będzie pobrać wartości progowej, która pasuje do wzorca zachowanie metryki serii i będzie stale zmianom w zależności od nowe dane, które umożliwiają bardziej precyzyjne wartość progową. Podobnie jak wcześniej, każde wystąpienie będzie monitorowany indywidualnie i będą wyświetlane powiadomienia indywidualnie.

Zwiększenie okresy ponownie spojrzenie i liczbę naruszeń można również zezwolić filtrowanie alertów tylko alert na danej definicji odchylenie znaczące. [Dowiedz się więcej o dynamicznymi progami zaawansowane opcje](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorowanie na dużą skalę za pomocą alertów dotyczących metryk w usłudze Azure Monitor

Do tej pory wiesz, jak pojedynczy alert dotyczący metryki może służyć do monitorowania jednej lub wielu metryk Szeregi czasowe związane z jednego zasobu platformy Azure. Wiele razy możesz zechcieć tę samą regułę alertu stosowane do wielu zasobów. Usługa Azure Monitor obsługuje także monitorowanie wielu zasobów przy użyciu jednej reguły alertu metryki. Ta funkcja jest obecnie obsługiwana tylko na maszynach wirtualnych. Ponadto pojedynczy alert dotyczący metryki można monitorować zasoby w jednym regionie platformy Azure.

Można określić zakres monitorowania, pojedynczy alert dotyczący metryki w jednym z trzech sposobów:

- jako listę maszyn wirtualnych w jednym regionie platformy Azure w ramach subskrypcji
- Wszystkie maszyny wirtualne (w jednym regionie platformy Azure) w co najmniej jedną grupę zasobów w ramach subskrypcji
- Wszystkie maszyny wirtualne (w jednym regionie platformy Azure) w jednej subskrypcji

Tworzenie reguł alertów dotyczących metryk, które monitorują wiele zasobów przypomina [tworzenie innych alertu metryki](alerts-metric.md) monitorująca pojedynczy zasób. Tylko różnica polega na tym, że będzie wybierz wszystkie zasoby, które chcesz monitorować. Możesz również utworzyć tych reguł za pomocą [szablonów usługi Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources). Otrzymasz poszczególnych powiadomień dla każdej maszyny wirtualnej.

## <a name="typical-latency"></a>Typowy czas oczekiwania

Dla alertów dotyczących metryk zwykle otrzymasz powiadomienie w niecałe 5 minut po ustawieniu częstotliwości reguły alertu jako 1 min. W przypadku dużego obciążenia systemów powiadomień można napotkać dłuższy czas oczekiwania.

## <a name="supported-resource-types-for-metric-alerts"></a>Obsługiwane typy zasobów dla alertów dotyczących metryk

Pełną listę obsługiwane typy zasobów można znaleźć w tym [artykułu](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Jeśli są już dzisiaj za pomocą klasycznego alertów dotyczących metryk i chcesz dowiedzieć się, jeśli alertów dotyczących metryk obsługują wszystkie typy zasobów są używane, w poniższej tabeli przedstawiono zasobu typy obsługiwane przez klasyczny alertów dotyczących metryk i jeśli są one obsługiwane przez alertów dotyczących metryk, już dziś, czy nie.

|Typ zasobu obsługiwane przez klasyczny alertów dotyczących metryk | Obsługiwane przez alertów dotyczących metryk |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Tak |
| Microsoft.Batch/batchAccounts| Tak|
|Microsoft.Cache/redis| Tak |
|Microsoft.ClassicCompute/virtualMachines | Nie |
|Microsoft.ClassicCompute/domainNames/slots/roles | Nie|
|Microsoft.CognitiveServices/accounts | Nie |
|Microsoft.Compute/virtualMachines | Tak|
|Microsoft.Compute/virtualMachineScaleSets| Tak|
|Microsoft.ClassicStorage/storageAccounts| Nie |
|Microsoft.DataFactory/datafactories | Tak|
|Microsoft.DBforMySQL/servers| Tak|
|Microsoft.DBforPostgreSQL/servers| Tak|
|Microsoft.Devices/IotHubs | Nie|
|Microsoft.DocumentDB/databaseAccounts| Tak|
|Microsoft.EventHub/namespaces | Tak|
|Microsoft.Logic/workflows | Tak|
|Microsoft.Network/loadBalancers |Tak|
|Microsoft.Network/publicIPAddresses| Yes|
|Microsoft.Network/applicationGateways| Yes|
|Microsoft.Network/expressRouteCircuits| Yes|
|Microsoft.Network/trafficManagerProfiles | Yes|
|Microsoft.Search/searchServices | Tak|
|Microsoft.ServiceBus/namespaces| Tak |
|Microsoft.Storage/storageAccounts | Tak|
|Microsoft.StreamAnalytics/streamingjobs| Yes|
|Microsoft.TimeSeriesInsights/environments | Yes|
|Microsoft W sieci Web/farm serwerów | Tak |
|Microsoft / Witryn (z wyjątkiem funkcji) | Yes|
|Microsoft HostingEnvironments/Web/multiRolePools | Nie|
|Microsoft HostingEnvironments/Web/workerPools| Nie |
|Microsoft.SQL/Servers | Nie |

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się, jak tworzenie, wyświetlanie i zarządzanie alertów dotyczących metryk na platformie Azure](alerts-metric.md)
- [Dowiedz się, jak wdrożyć za pomocą szablonów usługi Azure Resource Manager alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Dowiedz się więcej o grupach akcji](action-groups.md)
- [Dowiedz się więcej na temat typu warunku dynamicznymi progami](alerts-dynamic-thresholds.md)
