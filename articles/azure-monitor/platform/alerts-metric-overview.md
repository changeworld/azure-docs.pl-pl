---
title: Dowiedz się, jak działają alerty metryk w usłudze Azure Monitor.
description: Zapoznaj się z omówieniem tego, co możesz zrobić z alertami metryk i jak działają w usłudze Azure Monitor.
ms.date: 03/17/2020
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: a6860cad077b597df923274f8971f5652d4ba9e3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397971"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Omówienie działania alertów metryk w usłudze Azure Monitor

Alerty metryki w usłudze Azure Monitor działają na podstawie metryk wielowymiarowych. Te metryki mogą być [metryki platformy,](alerts-metric-near-real-time.md#metrics-and-dimensions-supported) [metryki niestandardowe,](../../azure-monitor/platform/metrics-custom-overview.md) [popularne dzienniki z usługi Azure Monitor konwertowane na metryki](../../azure-monitor/platform/alerts-metric-logs.md) i metryki usługi Application Insights. Alerty metryki oceniają w regularnych odstępach czasu, aby sprawdzić, czy warunki w jednej lub więcej metryki szeregów czasowych są spełnione i powiadamiać o spełnieniu oceny. Alerty metryki są stanowe, oznacza to, że wysyłają powiadomienia tylko wtedy, gdy stan się zmieni.

## <a name="how-do-metric-alerts-work"></a>Jak działają alerty metryk?

Regułę alertu metryki można zdefiniować, określając zasób docelowy, który ma być monitorowany, nazwę metryki, typ warunku (statyczny lub dynamiczny) oraz warunek (operator i próg/czułość) oraz grupę akcji, która ma zostać wyzwolona podczas uruchamiania reguły alertu. Typy warunku mają wpływ na sposób określania wartości progowych. [Dowiedz się więcej o typie warunku Progi dynamiczne i opcjach czułości](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Reguła alertu z statycznym typem warunku

Załóżmy, że utworzono prostą statyczną regułę alertu metryki progowej w następujący sposób:

- Zasób docelowy (zasób platformy Azure, który chcesz monitorować): myVM
- Metryka: Procent procesora CPU
- Typ warunku: statyczny
- Agregacja czasu (statystyka, która jest uruchamiana zaobiegają się z nieprzetworzonymi wartościami metryk. Obsługiwane agregacje czasu to Min, Max, Avg, Total, Count): Średnia
- Okres (okno spojrzenia wstecz, nad którym są sprawdzane wartości metryki): w ciągu ostatnich 5 minut
- Częstotliwość (częstotliwość, z jaką alert metryczny sprawdza, czy warunki są spełnione): 1 min
- Operator: Większy niż
- Próg: 70

Od momentu utworzenia reguły alertu monitor jest uruchamiany co 1 minut i analizuje wartości metryki z ostatnich 5 minut i sprawdza, czy średnia tych wartości przekracza 70. Jeśli warunek jest spełniony, czyli średni procent procesora CPU w ciągu ostatnich 5 minut przekracza 70, reguła alertu uruchamia aktywowane powiadomienie. Jeśli w grupie akcji skojarzonej z regułą alertu skonfigurowano akcję wiadomości e-mail lub haka sieci Web, otrzymasz aktywowane powiadomienie.

Gdy używasz wielu warunków w jednej regule, reguła "ands" warunki razem.  Oznacza to, że alert jest uruchamiany, gdy wszystkie warunki w alertu ocenić jako true i rozwiązać, gdy jeden z warunków nie jest już prawdziwe. Przykładem tego typu alertu będzie alert, gdy "CPU wyższy niż 90%" i "długość kolejki wynosi ponad 300 elementów". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Reguła alertu z dynamicznym typem warunku

Załóżmy, że utworzono prostą regułę alertu alertu metryki dynamiczne progi w następujący sposób:

- Zasób docelowy (zasób platformy Azure, który chcesz monitorować): myVM
- Metryka: Procent procesora CPU
- Typ warunku: Dynamiczny
- Agregacja czasu (statystyka, która jest uruchamiana zaobiegają się z nieprzetworzonymi wartościami metryk. Obsługiwane agregacje czasu to Min, Max, Avg, Total, Count): Średnia
- Okres (okno spojrzenia wstecz, nad którym są sprawdzane wartości metryki): w ciągu ostatnich 5 minut
- Częstotliwość (częstotliwość, z jaką alert metryczny sprawdza, czy warunki są spełnione): 1 min
- Operator: Większy niż
- Czułość: Średnia
- Okresy wstecz: 4
- Liczba naruszeń: 4

Po utworzeniu reguły alertu algorytm uczenia maszynowego progów dynamicznych uzyska dostępne dane historyczne, obliczy próg, który najlepiej pasuje do wzorca zachowania serii metryk i będzie stale uczyć się na podstawie nowych danych, aby próg był dokładniejszy.

Od momentu utworzenia reguły alertu monitor jest uruchamiany co 1 minut i analizuje wartości metryki w ciągu ostatnich 20 minut pogrupowanych w 5-minutowe okresy i sprawdza, czy średnia wartości okresu w każdym z 4 okresów przekracza oczekiwany próg. Jeśli warunek jest spełniony, czyli średni procent procesora CPU w ciągu ostatnich 20 minut (cztery 5 minut okresy) odbiega od oczekiwanego zachowania cztery razy, reguła alertu uruchamia aktywowane powiadomienie. Jeśli w grupie akcji skojarzonej z regułą alertu skonfigurowano akcję wiadomości e-mail lub haka sieci Web, otrzymasz aktywowane powiadomienie.

### <a name="view-and-resolution-of-fired-alerts"></a>Wyświetlanie i rozpoznawanie odpalanych alertów

Powyższe przykłady alertów rozpalania reguł można również wyświetlić w witrynie Azure portal w bloku **Wszystkie alerty.**

Załóżmy, że użycie na "myVM" nadal jest powyżej progu w kolejnych kontroli, reguła alertu nie będzie uruchamiany ponownie, dopóki warunki nie zostaną rozwiązane.

Po pewnym czasie użycie na "myVM" wraca do normy (spada poniżej progu). Reguła alertu monitoruje warunek jeszcze dwa razy, aby wysłać rozpoznane powiadomienie. Reguła alertu wysyła komunikat rozwiązany/dezaktywowany, gdy warunek alertu nie jest spełniony przez trzy kolejne okresy w celu zmniejszenia hałasu w przypadku warunków trzepotania.

Ponieważ rozpoznane powiadomienie jest wysyłane za pośrednictwem haków sieci web lub wiadomości e-mail, stan wystąpienia alertu (o nazwie stan monitora) w witrynie Azure portal jest również ustawiony na rozwiązany.

### <a name="using-dimensions"></a>Korzystanie z wymiarów

Alerty metryki w usłudze Azure Monitor obsługują również monitorowanie wielu kombinacji wartości wymiarów za pomocą jednej reguły. Zrozummy, dlaczego można użyć wielu kombinacji wymiarów za pomocą przykładu.

Załóżmy, że masz plan usługi app service dla swojej witryny. Chcesz monitorować użycie procesora CPU w wielu wystąpieniach z uruchomieniem witryny sieci Web/aplikacji. Można to zrobić za pomocą reguły alertu metryki w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: Procent procesora CPU
- Typ warunku: statyczny
- Wymiary
  - Wystąpienie = Nazwa wystąpienia1, Nazwa wystąpienia2
- Agregacja czasu: średnia
- Okres: W ciągu ostatnich 5 minut
- Częstotliwość: 1 min
- Operator: GreaterThan
- Próg: 70

Podobnie jak poprzednio, ta reguła monitoruje, czy średnie użycie procesora CPU w ciągu ostatnich 5 minut przekracza 70%. Jednak za pomocą tej samej reguły można monitorować dwa wystąpienia z uruchomieniem witryny sieci Web. Każde wystąpienie będzie monitorowane indywidualnie, a powiadomienia otrzymasz indywidualnie.

Załóżmy, że masz aplikację internetową, która widzi ogromne zapotrzebowanie i musisz dodać więcej wystąpień. Powyższa reguła nadal monitoruje tylko dwa wystąpienia. Można jednak utworzyć regułę w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: Procent procesora CPU
- Typ warunku: statyczny
- Wymiary
  - Wystąpienie = *
- Agregacja czasu: średnia
- Okres: W ciągu ostatnich 5 minut
- Częstotliwość: 1 min
- Operator: GreaterThan
- Próg: 70

Ta reguła będzie automatycznie monitorować wszystkie wartości dla wystąpienia, tj. można monitorować wystąpienia, jak pojawiają się bez konieczności modyfikowania reguły alertu metryki ponownie.

Podczas monitorowania wielu wymiarów reguła alertów dynamicznych progów może tworzyć dostosowane progi dla setek serii metryk naraz. Dynamiczne progi skutkują mniejszą liczbą reguł alertów do zarządzania i znaczną oszczędność czasu na zarządzaniu i tworzeniu reguł alertów.

Załóżmy, że masz aplikację internetową z wieloma wystąpieniami i nie wiesz, jaki jest najbardziej odpowiedni próg. Powyższe zasady będą zawsze stosować próg 70%. Można jednak utworzyć regułę w następujący sposób:

- Zasób docelowy: myAppServicePlan
- Metryka: Procent procesora CPU
- Typ warunku: Dynamiczny
- Wymiary
  - Wystąpienie = *
- Agregacja czasu: średnia
- Okres: W ciągu ostatnich 5 minut
- Częstotliwość: 1 min
- Operator: GreaterThan
- Czułość: Średnia
- Okresy wstecz: 1
- Liczba naruszeń: 1

Ta reguła monitoruje, czy średnie użycie procesora CPU w ciągu ostatnich 5 minut przekracza oczekiwane zachowanie dla każdego wystąpienia. Ta sama reguła, którą można monitorować wystąpienia, ponieważ pojawiają się one bez konieczności modyfikowania reguły alertu metryki ponownie. Każde wystąpienie otrzyma próg, który pasuje do wzorca zachowania serii metryk i będzie stale zmieniać na podstawie nowych danych, aby próg bardziej dokładne. Podobnie jak poprzednio, każde wystąpienie będzie monitorowane indywidualnie i otrzymasz powiadomienia indywidualnie.

Zwiększenie okresów spojnu i liczby naruszeń może również umożliwić filtrowanie alertów tylko alertów na definicji znacznego odchylenia. [Dowiedz się więcej o opcjach zaawansowanych Progów dynamicznych](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorowanie na dużą skalę przy użyciu alertów metryk w usłudze Azure Monitor

Do tej pory można było zobaczyć, jak pojedynczy alert metryki może służyć do monitorowania jednej lub wielu szeregów czasowych metryki związanych z jednym zasobem platformy Azure. Wiele razy może być ta sama reguła alertu stosowane do wielu zasobów. Usługa Azure Monitor obsługuje również monitorowanie wielu zasobów (tego samego typu) za pomocą jednej reguły alertu metryki dla zasobów, które istnieją w tym samym regionie platformy Azure. 

Ta funkcja jest obecnie obsługiwana dla metryk platformy (nie niestandardowych metryk) dla następujących usług w następujących chmurach platformy Azure:

| Usługa | Publiczna platforma Azure | Instytucje rządowe | Chiny |
|:--------|:--------|:--------|:--------|
| Maszyny wirtualne  | **Tak** | Nie | Nie |
| Bazy danych serwerów SQL | **Tak** | **Tak** | Nie |
| Pule elastyczne serwera SQL | **Tak** | **Tak** | Nie |
| Urządzenia brzegowe skrzynki danych | **Tak** | **Tak** | Nie |

Zakres monitorowania można określić za pomocą jednej reguły alertu metryki na jeden z trzech sposobów. Na przykład za pomocą maszyn wirtualnych można określić zakres jako:  

- lista maszyn wirtualnych w jednym regionie platformy Azure w ramach subskrypcji
- wszystkie maszyny wirtualne (w jednym regionie platformy Azure) w jednej lub kilku grupach zasobów w ramach subskrypcji
- wszystkie maszyny wirtualne (w jednym regionie platformy Azure) w jednej subskrypcji

Tworzenie reguł alertów metryki, które monitorują wiele zasobów jest jak [tworzenie innych alertów metryki,](alerts-metric.md) który monitoruje pojedynczy zasób. Jedyną różnicą jest to, że można wybrać wszystkie zasoby, które chcesz monitorować. Reguły te można również tworzyć za pomocą [szablonów usługi Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Otrzymasz indywidualne powiadomienia dla każdego monitorowatego zasobu.

> [!NOTE]
>
> W regule alertu metryki, która monitoruje wiele zasobów, tylko jeden warunek jest dozwolony.

## <a name="typical-latency"></a>Typowe opóźnienie

W przypadku alertów metrycznych zazwyczaj otrzymasz powiadomienie w mniej niż 5 minut, jeśli ustawisz częstotliwość reguły alertu na 1 min. W przypadku dużego obciążenia dla systemów powiadomień może wystąpić dłuższe opóźnienie.

## <a name="supported-resource-types-for-metric-alerts"></a>Obsługiwane typy zasobów dla alertów metryk

Pełną listę obsługiwanych typów zasobów można znaleźć w tym [artykule](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak tworzyć, wyświetlać alerty metryk i zarządzać nimi na platformie Azure](alerts-metric.md)
- [Dowiedz się, jak wdrażać alerty metryk przy użyciu szablonów usługi Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Dowiedz się więcej o grupach akcji](action-groups.md)
- [Dowiedz się więcej o typie warunku Progi dynamiczne](alerts-dynamic-thresholds.md)
