---
title: Zrozumienie, jak metryki, alerty pracy w usłudze Azure Monitor.
description: Zapoznaj się z omówieniem, co można zrobić za pomocą alertów dotyczących metryk i jak działają w usłudze Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 1ec47ddf5769dd8ed624277a86db57f449581b90
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948693"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Zrozumienie, jak metryki, alerty pracy w usłudze Azure Monitor

Alerty metryk w usłudze Azure Monitor działać na podstawie metryk wielowymiarowych. Metryki te mogą być metryk platformy, metryki niestandardowe (wersja zapoznawcza), popularnych dzienników z usługi Log Analytics na metrykach standardowych metryk usługi Application Insights. Alerty metryki oceny w regularnych odstępach czasu, aby sprawdzić, czy warunki w jednym węźle lub metryki szeregów czasowych są spełnione i otrzymywać powiadomienia, gdy są spełnione ocen. Alerty metryki są stanowe oznacza to, że ich tylko wysyłacie powiadomienia po zmianie stanu.

## <a name="how-do-metric-alerts-work"></a>Jak działają alerty metryki

Można zdefiniować regułę alertu metryki, określając zasób docelowy, nazwę monitorowanej, metryki i warunek (operator i wartość progową) i grupę akcji do wyzwalane, gdy zostanie wyzwolony reguły alertu.
Załóżmy, że utworzono prostą regułę alertu metryki w następujący sposób:

- (Do monitorowania zasobów platformy Azure) zasób docelowy: myVM
- Metryka: Procentowe użycie procesora CPU
- Agregacja czasu (statystyki, który jest uruchamiany za pośrednictwem pierwotne wartości metryk. Obsługiwany czas wszelkie operacje agregacji są Min, Max, Avg, łącznie): średni
- Okres (wygląd wsteczny okna za pośrednictwem metryki, które są sprawdzane w wartości): w ciągu ostatnich 5 minut.
- Frequency (częstotliwość, z którą alert dotyczący metryki sprawdza, jeśli warunki są spełnione): 1 min
- Operator: Większa niż
- Próg: 70

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
- Agregacja czasu: średni
- Okres: w ciągu ostatnich 5 minut.
- Częstotliwość: 1 min
- Operator: większe
- Próg: 70

Podobnie jak wcześniej, ta reguła umożliwia monitorowanie Jeśli średniego użycia procesora CPU dla ostatnich 5 minut przekracza 70%. Jednak ta zasada umożliwia monitorowanie dwóch wystąpień z witryną sieci Web. Każde wystąpienie będzie są monitorowane indywidualnie i będą wyświetlane powiadomienia indywidualnie.

Załóżmy, że, masz aplikację sieci web, która ma do czynienia z ogromną żądanie i będzie konieczne dodanie większej liczby wystąpień. Reguła powyżej nadal monitoruje tylko dwa wystąpienia. Można jednak utworzyć regułę w następujący sposób.

- Zasób docelowy: myAppServicePlan
- Metryka: Procentowe użycie procesora CPU
- Wymiary
  - Wystąpienia = *
- Agregacja czasu: średni
- Okres: w ciągu ostatnich 5 minut.
- Częstotliwość: 1 min
- Operator: większe
- Próg: 70

Ta reguła będzie automatycznie monitorować wszystkie wartości tj wystąpienia można monitorować wystąpień, jak pojawiają się bez konieczności modyfikowania usługi reguła alertu metryki ponownie.

### <a name="monitoring-multiple-resource-using-metric-alerts"></a>Monitorowanie wielu zasobów przy użyciu alertów dotyczących metryk

Ponieważ jak już wspomniano w poprzedniej sekcji, istnieje możliwość mają jedną regułę alertu metryki monitorującego każda kombinacja poszczególnych wymiaru (tj.) Seria metryki czasu). Jest jednak nadal ograniczona do zrobienia tego jednego zasobu naraz. Alerty metryki teraz obsługują także monitorowanie wielu zasobów z jedną regułą w wersji zapoznawczej. Jeśli masz 100 maszyn wirtualnych w ramach subskrypcji, ta nowa funkcja pomaga szybko skonfigurować monitorowanie dla nich. 

Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Tworzenie reguł alertów dotyczących metryk, które monitorują wiele zasobów nie jest obecnie obsługiwane za pośrednictwem witryny Azure portal. Możesz utworzyć te reguły za pomocą szablonów usługi Azure Resource Manager.

## <a name="typical-latency"></a>Typowy czas oczekiwania

Dla alertów dotyczących metryk zwykle otrzymasz powiadomienie w niecałe 5 minut po ustawieniu częstotliwości reguły alertu jako 1 min. W przypadku dużego obciążenia systemów powiadomień można napotkać dłuższy czas oczekiwania.

## <a name="supported-resource-types-for-metric-alerts"></a>Obsługiwane typy zasobów dla alertów dotyczących metryk

Pełną listę obsługiwane typy zasobów można znaleźć w tym [artykułu](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się, jak tworzyć, wyświetlać i zarządzać alertów dotyczących metryk na platformie Azure](alert-metric.md)
- [Dowiedz się, jak wdrożyć za pomocą szablonów usługi Azure Resource Manager alertów dotyczących metryk](monitoring-create-metric-alerts-with-templates.md)
- [Dowiedz się więcej o grupach akcji](monitoring-action-groups.md)
