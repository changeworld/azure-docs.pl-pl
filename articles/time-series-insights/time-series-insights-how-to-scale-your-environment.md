---
title: Jak skalować środowiska Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób skalowania środowiska Azure czas serii Insights. Użyj portalu Azure, aby zwiększyć lub zmniejszyć pojemność w cenową jednostki SKU.
ms.service: time-series-insights
services: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7e603cc9c130de6b65ae1935ac974557f5e74737
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651995"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Jak skalować środowiska Insights serii czasu

W tym artykule opisano sposób zmiany możliwości środowiska środowiska Insights serii czasu przy użyciu portalu Azure. Pojemność jest mnożnik stosowane do prędkości wejściowej, pojemności, kosztów związanych z Twojej wybranej jednostki SKU. 

Aby zwiększyć lub zmniejszyć pojemność w danym cenową jednostki SKU, można użyć portalu Azure. 

Jednak zmiana warstwy cenowej jednostka SKU nie jest dozwolona. Na przykład środowisko z S1 cennik SKU nie można przekonwertować na S2 lub na odwrót. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>Szybkość wejściowych S1 SKU i pojemności

| S1 Pojemność jednostki SKU | Szybkość transferu | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 1 GB (1 mln zdarzeń) | 30 GB (30 milionów zdarzeń) na miesiąc |
| 10 | 10 GB (10 milionów zdarzeń) | 300 GB (300 milionów zdarzeń) na miesiąc |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Szybkość wejściowych s2 jednostki SKU i pojemności

| S2 Pojemność jednostki SKU | Szybkość transferu | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 10 GB (10 milionów zdarzeń) | 300 GB (300 milionów zdarzeń) na miesiąc |
| 10 | 100 GB (100 milionów zdarzeń) | 3 TB (zdarzenia miliard 3) na miesiąc |

Możliwości skalowania liniowo, więc SKU S1 o pojemności 2 obsługuje 2 GB (2 milionów) zdarzenia według dnia służąca szybkość i 60 GB (60 miliona zdarzeń) na miesiąc.

## <a name="change-the-capacity-of-your-environment"></a>Zmiana wydajności środowiska
1. W portalu Azure Znajdź i zaznacz środowiska Insights serii czasu. 

2. Wybierz z menu w danym środowisku czasu serii Insighs **Konfiguruj**.

   ![Configure.PNG](media/scale-your-environment/configure.png)

3. Dostosuj **pojemności** suwak, aby wybrać wydajności, który spełnia wymagania ceny wejściowych i pojemności magazynu. Powiadomienie **prędkości wejściowej**, **pojemności**, i **szacowany koszt** aktualizacji dynamicznie, aby wyświetlić wpływu zmiany. 

   ![Suwak](media/scale-your-environment/slider.png)

   Alternatywnie możesz wpisać numer mnożnik pojemności w polu tekstowym po prawej stronie suwaka. 

4. Wybierz **zapisać** skalowania środowiska. Wskaźnik postępu jest wyświetlany, dopóki zmiana zostaje zatwierdzona, na chwilę. 

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Sprawdź, czy nowe pojemności jest wystarczające, aby zapobiec ograniczania](time-series-insights-diagnose-and-solve-problems.md).
