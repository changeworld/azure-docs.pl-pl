---
title: Jak skalować środowiska usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób skalowania środowiska Azure Time Series Insights. Aby zwiększyć lub zmniejszyć pojemność w ramach cen jednostki SKU, należy użyć witryny Azure portal.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: fcdfc78a62d901bdaf50c18e9286e6f2d30c5b13
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714133"
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

2. Wybierz z menu dla danego środowiska usługi Time Series Insights **Konfiguruj**.

   ![configure.png](media/scale-your-environment/configure.png)

3. Dostosuj **pojemności** suwak, aby wybrać pojemność, który spełnia wymagania ceny transferu danych przychodzących i pojemność magazynu. Zwróć uwagę **szybkość transferu danych przychodzących**, **pojemność magazynu**, i **szacowany koszt** aktualizacji dynamicznej, aby pokazać wpływ zmiany. 

   ![Suwak](media/scale-your-environment/slider.png)

   Alternatywnie możesz wpisać numer mnożnik pojemności w polu tekstowym z prawej strony suwaka. 

4. Wybierz **Zapisz** Skalowanie środowiska. Wskaźnik postępu jest wyświetlany, dopóki zmiana zostaje zatwierdzona, chwilowo. 

## <a name="next-steps"></a>Kolejne kroki

- Sprawdź, czy jest nową pojemność [wystarczające, aby zapobiec ograniczania](time-series-insights-diagnose-and-solve-problems.md).