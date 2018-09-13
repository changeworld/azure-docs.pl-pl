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
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 777c52276ca767ee1fb4d05666850350c02c1e00
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644989"
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
