---
title: Jak skalować środowisko Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób skalowania środowiska Azure Time Series Insightsowego. Użyj Azure Portal, aby dodać lub odjąć pojemność w ramach jednostki SKU cenowej.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2019
ms.custom: seodec18
ms.openlocfilehash: 13fa2b892013cf4a3fb96220c901030c6b0aee0b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129110"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Jak skalować środowisko Time Series Insights

W tym artykule opisano sposób zmiany pojemności środowiska Time Series Insights środowiska przy użyciu Azure Portal. Pojemność to mnożnik stosowany do szybkości transferu danych przychodzących, pojemności magazynu i kosztu związanego z wybraną jednostką SKU.

Za pomocą Azure Portal można zwiększyć lub zmniejszyć pojemność w ramach danej jednostki SKU cenowej.

Jednak zmiana jednostki SKU warstwy cenowej jest niedozwolona. Na przykład środowisko z jednostką SKU cen S1 nie może być konwertowane na S2 lub odwrotnie.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Szybkość i pojemność jednostki SKU w warstwie S1

| Pojemność jednostki SKU S1 | Szybkość transferu danych przychodzących | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 1 GB (1 000 000 zdarzeń) | 30 GB (30 000 000 zdarzeń) miesięcznie |
| 10 | 10 GB (10 000 000 zdarzeń) | 300 GB (zdarzenia 300 000 000) miesięcznie |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Szybkość i pojemność ruchu przychodzącego jednostki SKU

| Pojemność jednostki SKU S2 | Szybkość transferu danych przychodzących | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 10 GB (10 000 000 zdarzeń) | 300 GB (zdarzenia 300 000 000) miesięcznie |
| 10 | 100 GB (zdarzenia 100 000 000) | 3 TB (3 000 000 000 zdarzeń) miesięcznie |

Pojemności są skalowane liniowie, więc jednostka SKU S1 z pojemnością 2 obsługuje 2 GB (2 000 000) zdarzeń na dzień i 60 GB (60 000 000 zdarzeń) miesięcznie.

## <a name="change-the-capacity-of-your-environment"></a>Zmień pojemność środowiska

1. W Azure Portal Znajdź i wybierz środowisko Time Series Insights.

1. W menu środowiska Time Series Insights wybierz pozycję **Konfiguruj**.

   [![Konfiguruj plik PNG](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Dostosuj suwak **pojemności** , aby wybrać pojemność, która spełnia wymagania dotyczące stawek za transfer danych przychodzących i pojemności magazynu. Zwróć uwagę, że **szybkość**transferu danych przychodzących, **pojemność magazynu**i **Szacowana** aktualizacja kosztu są dynamiczne, aby pokazać wpływ zmiany.

   [![Skakując](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternatywnie można wpisać liczbę mnożnika pojemności do pola tekstowego po prawej stronie suwaka.

1. Wybierz pozycję **Zapisz** , aby skalować środowisko. Wskaźnik postępu jest wyświetlany do momentu, aż zmiana zostanie zatwierdzona.

## <a name="next-steps"></a>Następne kroki

- Sprawdź, czy nowa pojemność jest [wystarczająca, aby zapobiec ograniczaniu wydajności](time-series-insights-diagnose-and-solve-problems.md).