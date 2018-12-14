---
title: Zrozumienie, jak metryki, alerty pracy w usłudze Azure Monitor.
description: Zapoznaj się z omówieniem, co można zrobić za pomocą alertów dotyczących metryk i jak działają w usłudze Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: cf315718377210e13a6634c0eaf8caab158bcc4d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342225"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Zrozumienie, jak metryki, alerty pracy w usłudze Azure Monitor

Alerty metryk w usłudze Azure Monitor działać na podstawie metryk wielowymiarowych. Metryki te mogą być metryk platformy [metryki niestandardowe](../../azure-monitor/platform/metrics-custom-overview.md), [popularnych dzienników z usługi Log Analytics jest konwertowana na metryki](../../azure-monitor/platform/alerts-metric-logs.md), standardowych metryk usługi Application Insights. Alerty metryki oceny w regularnych odstępach czasu, aby sprawdzić, czy warunki na co najmniej jeden metryki szeregów czasowych są spełnione i otrzymywać powiadomienia, gdy są spełnione ocen. Alerty metryki są stanowe, czyli one tylko wysyłacie powiadomienia po zmianie stanu.

## <a name="how-do-metric-alerts-work"></a>Jak działają alerty metryki

Można zdefiniować regułę alertu metryki, określając zasób docelowy, nazwę monitorowanej, metryki i warunek (operator i wartość progową) i grupę akcji do wyzwalane, gdy zostanie wyzwolony reguły alertu.
Załóżmy, że utworzono prostą regułę alertu metryki w następujący sposób:

- (Do monitorowania zasobów platformy Azure) zasób docelowy: myVM
- Metryka: Procentowe użycie procesora CPU
- Agregacja czasu (statystyki, który jest uruchamiany za pośrednictwem pierwotne wartości metryk. Obsługiwane razem, gdy wszelkie operacje agregacji są Min, Max, Avg, łącznie): Średnia
- Okres (wygląd wsteczny okna za pośrednictwem metryki, które są sprawdzane w wartości):      W ciągu ostatnich 5 minut.
- Frequency (częstotliwość, z którą alert dotyczący metryki sprawdza, jeśli warunki są spełnione): 1 min
- Operator:     Większe niż
- Próg:      70

Od chwili, gdy zostanie utworzona reguła alertu monitor jest uruchamiane co 1 min i szuka w wartości metryk z ostatnich 5 minut i sprawdza się, jeśli średnia tych wartości przekracza 70. Jeśli warunek jest spełniony, oznacza to, średnie użycie procesora CPU procent dla ostatnich 5 minut przekracza 70, reguła alertu wyzwala powiadomienie aktywowane. Jeśli wiadomość e-mail lub Akcja punktu zaczepienia sieci web skonfigurowano grupy akcji skojarzonych z regułą alertu, otrzymasz powiadomienie aktywowanego zarówno.

Tego konkretnego wystąpienia uruchomieniem reguły alertu można również wyświetlać w witrynie Azure portal w bloku wszystkie alerty.

Załóżmy, że jest powyżej wartości progowej przez użycie na "myVM" odbywa się w kolejnych kontroli, reguły alertu nie zostanie uruchomiony ponownie, dopóki warunek nie zostanie rozwiązany.

Po za jakiś czas, gdy użycie na "myVM" sprowadza się powrót do normalnego oznacza to, że nie spadnie poniżej określonego progu. Reguła alertu monitoruje warunku dwa razy, aby Wyślij powiadomienie o usunięciu. Reguły alertu wysyła komunikat o usunięciu dezaktywowane podczas warunek alertu nie został spełniony dla trzech następujących po sobie kropek redukcji szumu w przypadku niestabilny warunków.

Jak rozpoznać powiadomienie jest wysyłane za pomocą elementów web hook lub wiadomości e-mail, stan wystąpienia alertu (nazywane stan monitora) w witrynie Azure portal jest również ustawiona na rozwiązany.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorowanie na dużą skalę za pomocą alertów dotyczących metryk w usłudze Azure Monitor

### <a name="using-dimensions"></a>Przy użyciu wymiarów

Alerty metryk w usłudze Azure Monitor również obsługiwać monitorowanie wielu kombinacje wartości wymiarów z jedną regułą. Przyjrzyjmy się, warto używać wielu kombinacji za pomocą przykładu.

Załóżmy, że masz plan usługi App Service dla witryny sieci Web. Chcesz monitorować użycie procesora CPU na wiele wystąpień witryny sieci web/aplikacji. Możesz to zrobić przy użyciu następujący regułę alertu metryki

- Zasób docelowy: myAppServicePlan
- Metryka: Procentowe użycie procesora CPU
- Wymiary
  - Wystąpienia = InstanceName1, InstanceName2
- Agregacja czasu: Średnia
- Okres: W ciągu ostatnich 5 minut.
- Częstotliwość: 1 min
- Operator: GreaterThan
- Próg: 70

Podobnie jak wcześniej, ta reguła umożliwia monitorowanie Jeśli średniego użycia procesora CPU dla ostatnich 5 minut przekracza 70%. Jednak ta zasada umożliwia monitorowanie dwóch wystąpień z witryną sieci Web. Każde wystąpienie będzie są monitorowane indywidualnie i będą wyświetlane powiadomienia indywidualnie.

Załóżmy, że, masz aplikację sieci web, która ma do czynienia z ogromną żądanie i będzie konieczne dodanie większej liczby wystąpień. Reguła powyżej nadal monitoruje tylko dwa wystąpienia. Można jednak utworzyć regułę w następujący sposób.

- Zasób docelowy: myAppServicePlan
- Metryka: Procentowe użycie procesora CPU
- Wymiary
  - Wystąpienia = *
- Agregacja czasu: Średnia
- Okres: W ciągu ostatnich 5 minut.
- Częstotliwość: 1 min
- Operator: GreaterThan
- Próg: 70

Ta reguła będzie automatycznie monitorować wszystkie wartości tj wystąpienia można monitorować wystąpień, jak pojawiają się bez konieczności modyfikowania usługi reguła alertu metryki ponownie.

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Monitorowanie wielu zasobów przy użyciu alertów dotyczących metryk

Ponieważ jak już wspomniano w poprzedniej sekcji, istnieje możliwość mają jedną regułę alertu metryki monitorującego każda kombinacja poszczególnych wymiaru (tj.) Seria metryki czasu). Jednak wcześniej było nadal ograniczona do zrobienia tego jednego zasobu naraz. Usługa Azure Monitor obsługuje także monitorowanie wielu zasobów przy użyciu jednej reguły alertu metryki. Ta funkcja jest obecnie w wersji zapoznawczej i obsługiwana tylko dla maszyn wirtualnych. Ponadto pojedynczy alert dotyczący metryki można monitorować zasoby w jednym regionie platformy Azure.

Można określić zakres monitorowania, pojedynczy alert dotyczący metryki w jednym z trzech sposobów:

- jako listę maszyn wirtualnych w jednym regionie platformy Azure w ramach subskrypcji
- Wszystkie maszyny wirtualne (w jednym regionie platformy Azure) w co najmniej jedną grupę zasobów w ramach subskrypcji
- Wszystkie maszyny wirtualne (w jednym regionie platformy Azure) w jednej subskrypcji

Tworzenie reguł alertów dotyczących metryk, które monitorują wiele zasobów nie jest obecnie obsługiwane za pośrednictwem witryny Azure portal. Możesz utworzyć te reguły za pomocą [szablonów usługi Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#resource-manager-template-for-metric-alert-that-monitors-multiple-resources). Otrzymasz poszczególnych powiadomień dla każdej maszyny wirtualnej. 

## <a name="typical-latency"></a>Typowy czas oczekiwania

Dla alertów dotyczących metryk zwykle otrzymasz powiadomienie w niecałe 5 minut po ustawieniu częstotliwości reguły alertu jako 1 min. W przypadku dużego obciążenia systemów powiadomień można napotkać dłuższy czas oczekiwania.

## <a name="supported-resource-types-for-metric-alerts"></a>Obsługiwane typy zasobów dla alertów dotyczących metryk

Pełną listę obsługiwane typy zasobów można znaleźć w tym [artykułu](../../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).

Jeśli są już dzisiaj za pomocą klasycznego alertów dotyczących metryk i chcesz dowiedzieć się, jeśli alertów dotyczących metryk obsługują wszystkie typy zasobów są używane, w poniższej tabeli przedstawiono zasobu typy obsługiwane przez klasyczny alertów dotyczących metryk i jeśli są one obsługiwane przez alertów dotyczących metryk, już dziś, czy nie.

|Typ zasobu obsługiwane przez klasyczny alertów dotyczących metryk | Obsługiwane przez alertów dotyczących metryk |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Yes |
| Microsoft.Batch/batchAccounts| Yes|
|Microsoft.Cache/redis| Yes
|Microsoft.ClassicCompute/virtualMachines | Nie |
|Microsoft.ClassicCompute/domainNames/slots/roles | Nie|
|Microsoft.CognitiveServices/accounts | Nie |
|Microsoft.Compute/virtualMachines | Yes|
|Microsoft.Compute/virtualMachineScaleSets| Yes|
|Microsoft.ClassicStorage/storageAccounts| Nie |
|Microsoft.DataFactory/datafactories | Yes|
|Microsoft.DBforMySQL/servers| Yes|
|Microsoft.DBforPostgreSQL/servers| Yes|
|Microsoft.Devices/IotHubs | Nie|
|Microsoft.DocumentDB/databaseAccounts| Nie|
|Microsoft.EventHub/namespaces | Yes|
|Microsoft.Logic/workflows | Yes|
|Microsoft.Network/loadBalancers |Yes|
|Microsoft.Network/publicIPAddresses| Yes|
|Microsoft.Network/applicationGateways| Yes|
|Microsoft.Network/expressRouteCircuits| Yes|
|Microsoft.Network/trafficManagerProfiles | Yes|
|Microsoft.Search/searchServices | Nie|
|Microsoft.ServiceBus/namespaces| Nie|
|Microsoft.Storage/storageAccounts | Yes|
|Microsoft.StreamAnalytics/streamingjobs| Yes|
|Microsoft.TimeSeriesInsights/environments | Yes|
|Firmy Microsoft. W sieci Web/farm serwerów | Yes |
|Firmy Microsoft. / Witryn (z wyjątkiem funkcji) | Yes|
|Firmy Microsoft. HostingEnvironments/Web/multiRolePools | Nie|
|Firmy Microsoft. HostingEnvironments/Web/workerPools| Nie
|Microsoft.SQL/Servers | Nie|

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się, jak tworzyć, wyświetlać i zarządzać alertów dotyczących metryk na platformie Azure](alerts-metric.md)
- [Dowiedz się, jak wdrożyć za pomocą szablonów usługi Azure Resource Manager alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Dowiedz się więcej o grupach akcji](action-groups.md)
