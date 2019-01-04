---
title: Usługi Azure Application Insights Telemetrii Data Model — danych Telemetrycznych zdarzeń | Dokumentacja firmy Microsoft
description: Model danych szczegółowych informacji aplikacji dla zdarzenia telemetrii
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 7e69fb74226aaaad13b0a5410e0a46a152d73533
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812339"
---
# <a name="event-telemetry-application-insights-data-model"></a>Dane telemetryczne zdarzeń: Model danych usługi Application Insights

Elementy można tworzyć zdarzenia telemetrii (w [usługi Application Insights](../../application-insights/app-insights-overview.md)) do reprezentowania zdarzenie, które wystąpiły w aplikacji. Zazwyczaj jest interakcji z użytkownikiem takich jak przycisk kliknij przycisk lub kolejność wyewidencjonowania. Można także zdarzeń cyklu życia aplikacji, takich jak inicjowanie lub konfiguracji aktualizacji. 

Semantycznie zdarzenia mogą być lub nie może zostać skorelowane z żądania. Jeśli jednak poprawnie użyty danych telemetrycznych zdarzeń jest ważniejsza niż żądania lub śledzenia. Zdarzenia reprezentują dane telemetryczne, firm i temat do oddzielnych, powinny być łagodniej [próbkowania](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Name (Nazwa)

Nazwa zdarzenia. Aby umożliwić odpowiednie grupowania i przydatne metryki, ograniczania aplikacji, tak, aby generuje niewielką liczbę nazw oddzielne zdarzenie. Na przykład nie używaj odrębną nazwę dla każdego wystąpienia wygenerowane zdarzenia.

Maksymalna długość: 512 znaków

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [modelu danych](data-model.md) dla usługi Application Insights typów i danych modelu.
- [Zapisywanie danych telemetrycznych zdarzeń niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Zapoznaj się z [platform](../../application-insights/app-insights-platforms.md) obsługiwane przez usługę Application Insights.
