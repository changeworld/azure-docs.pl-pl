---
title: Jak skalować środowiska usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób skalowania środowiska Azure Time Series Insights. Aby zwiększyć lub zmniejszyć pojemność w ramach cen jednostki SKU, należy użyć witryny Azure portal.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: be06fd5a6f05d750e6ca9801a6004f7180a12d5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238991"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Jak skalować środowiska usługi Time Series Insights

W tym artykule opisano, jak w celu zmiany pojemności środowiska środowiska usługi Time Series Insights przy użyciu witryny Azure portal. Pojemność jest mnożnik stosowane do szybkości transferu danych przychodzących, pojemność i koszt związany z wybranej jednostki SKU.

Aby zwiększyć lub zmniejszyć pojemność w ramach danej jednostki SKU cen, można użyć witryny Azure portal.

Jednak zmiana warstwy cenowej jednostki SKU jest niedozwolona. Na przykład w środowisku zawierającym S1, w jednostce SKU wyceny nie można przekonwertować w wersji S2 lub na odwrót.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Stawki transferu danych przychodzących S1 SKU usługi i pojemności

| S1 Pojemność jednostki SKU | Szybkość transferu danych przychodzących | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 1 GB (1 milion zdarzeń) | 30 GB (30 mln zdarzeń) na miesiąc |
| 10 | 10 GB (10 milionów zdarzeń) | 300 GB (300 milionów zdarzeń) na miesiąc |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Stawki transferu danych przychodzących s2 SKU i pojemności

| S2 Pojemność jednostki SKU | Szybkość transferu danych przychodzących | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 10 GB (10 milionów zdarzeń) | 300 GB (300 milionów zdarzeń) na miesiąc |
| 10 | 100 GB (100 milionów zdarzeń) | 3 TB (3 MLD zdarzeń) na miesiąc |

Możliwości jest skalowane liniowo, więc S1 SKU usługi o pojemności 2 obsługuje 2 GB (2 mln) zdarzeń na szybkość transferu danych przychodzących dnia i 60 GB (60 milionów zdarzeń) na miesiąc.

## <a name="change-the-capacity-of-your-environment"></a>Zmienianie pojemności środowiska

1. W witrynie Azure portal zlokalizuj i wybierz środowisko usługi Time Series Insights.

1. Wybierz z menu dla danego środowiska usługi Time Series Insights **Konfiguruj**.

   [![configure.png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Dostosuj **pojemności** suwak, aby wybrać pojemność, który spełnia wymagania ceny transferu danych przychodzących i pojemność magazynu. Zwróć uwagę **szybkość transferu danych przychodzących**, **pojemność magazynu**, i **szacowany koszt** aktualizacji dynamicznej, aby pokazać wpływ zmiany.

   [![Suwak](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternatywnie możesz wpisać numer mnożnik pojemności w polu tekstowym z prawej strony suwaka.

1. Wybierz **Zapisz** Skalowanie środowiska. Wskaźnik postępu jest wyświetlany, dopóki zmiana zostaje zatwierdzona, chwilowo.

## <a name="next-steps"></a>Kolejne kroki

- Sprawdź, czy jest nową pojemność [wystarczające, aby zapobiec ograniczania](time-series-insights-diagnose-and-solve-problems.md).