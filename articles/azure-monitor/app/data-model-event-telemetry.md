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
ms.openlocfilehash: 8e519e5a5e5fe6b893fa5663e1426630f46f885f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900854"
---
# <a name="event-telemetry-application-insights-data-model"></a>Dane telemetryczne zdarzeń: Model danych usługi Application Insights

Elementy można tworzyć zdarzenia telemetrii (w [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md)) do reprezentowania zdarzenie, które wystąpiły w aplikacji. Zazwyczaj jest interakcji z użytkownikiem takich jak przycisk kliknij przycisk lub kolejność wyewidencjonowania. Można także zdarzeń cyklu życia aplikacji, takich jak inicjowanie lub konfiguracji aktualizacji. 

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
- Zapoznaj się z [platform](../../azure-monitor/app/platforms.md) obsługiwane przez usługę Application Insights.
