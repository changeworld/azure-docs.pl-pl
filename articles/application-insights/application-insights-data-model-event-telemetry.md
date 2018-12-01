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
ms.openlocfilehash: 5883a8eb4a550c61251fa20331dbd433328c5d59
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724047"
---
# <a name="event-telemetry-application-insights-data-model"></a>Dane telemetryczne zdarzeń: model danych usługi Application Insights

Elementy można tworzyć zdarzenia telemetrii (w [usługi Application Insights](app-insights-overview.md)) do reprezentowania zdarzenie, które wystąpiły w aplikacji. Zazwyczaj jest interakcji z użytkownikiem takich jak przycisk kliknij przycisk lub kolejność wyewidencjonowania. Można także zdarzeń cyklu życia aplikacji, takich jak inicjowanie lub konfiguracji aktualizacji. 

Semantycznie zdarzenia mogą być lub nie może zostać skorelowane z żądania. Jeśli jednak poprawnie użyty danych telemetrycznych zdarzeń jest ważniejsza niż żądania lub śledzenia. Zdarzenia reprezentują dane telemetryczne, firm i temat do oddzielnych, powinny być łagodniej [próbkowania](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Name (Nazwa)

Nazwa zdarzenia. Aby umożliwić odpowiednie grupowania i przydatne metryki, ograniczania aplikacji, tak, aby generuje niewielką liczbę nazw oddzielne zdarzenie. Na przykład nie używaj odrębną nazwę dla każdego wystąpienia wygenerowane zdarzenia.

Maksymalna długość: 512 znaków

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [modelu danych](application-insights-data-model.md) dla usługi Application Insights typów i danych modelu.
- [Zapisywanie danych telemetrycznych zdarzeń niestandardowych](app-insights-api-custom-events-metrics.md#trackevent)
- Zapoznaj się z [platform](app-insights-platforms.md) obsługiwane przez usługę Application Insights.
