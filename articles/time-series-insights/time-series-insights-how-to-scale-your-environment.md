---
title: Jak skalować środowisko Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób skalowania środowiska Azure Time Series Insightsowego. Użyj Azure Portal, aby dodać lub odjąć pojemność w ramach jednostki SKU cenowej.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3f03f5ed75c720c9b0daf30d721ef4d2aee9749c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991159"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Jak skalować środowisko Time Series Insights

W tym artykule opisano sposób zmiany pojemności środowiska Time Series Insights przy użyciu [Azure Portal](https://portal.azure.com). Pojemność to mnożnik stosowany do szybkości transferu danych przychodzących, pojemności magazynu i kosztu związanego z wybraną jednostką SKU.

Za pomocą Azure Portal można zwiększyć lub zmniejszyć pojemność w ramach danej jednostki SKU cenowej.

Jednak zmiana jednostki SKU warstwy cenowej jest niedozwolona. Na przykład środowisko z jednostką SKU cen S1 nie może być konwertowane na S2 lub odwrotnie.

## <a name="ga-limits"></a>Limity GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Zmień pojemność środowiska

1. W Azure Portal Znajdź i wybierz środowisko Time Series Insights.

1. W menu środowiska Time Series Insights wybierz pozycję **Konfiguruj**.

   [![Configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Dostosuj suwak **pojemności** , aby wybrać pojemność, która spełnia wymagania dotyczące stawek za transfer danych przychodzących i pojemności magazynu. Zwróć uwagę, że **szybkość**transferu danych przychodzących, **pojemność magazynu**i **Szacowana aktualizacja kosztu** są dynamiczne, aby pokazać wpływ zmiany.

   [Suwak![](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternatywnie można wpisać liczbę mnożnika pojemności do pola tekstowego po prawej stronie suwaka.

1. Wybierz pozycję **Zapisz** , aby skalować środowisko. Wskaźnik postępu jest wyświetlany do momentu, aż zmiana zostanie zatwierdzona.

1. Sprawdź, czy nowa pojemność jest [wystarczająca, aby zapobiec ograniczaniu wydajności](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zapoznaj się [z tematem przechowywanie w Time Series Insights](time-series-insights-concepts-retention.md).

- Dowiedz się więcej o [konfigurowaniu przechowywania danych w Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Dowiedz się więcej [na temat planowania środowiska](time-series-insights-environment-planning.md).