---
title: Model danych Telemetrii aplikacji Azure Insights - dane telemetryczne zdarzenia | Dokumentacja firmy Microsoft
description: Model danych usługi Insights aplikacji dla dane telemetryczne zdarzenia
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 4f6b5c35b65b4aff2dbe8dafbb2eb07d75c2382a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="event-telemetry-application-insights-data-model"></a>Dane telemetryczne zdarzenia: model danych usługi Application Insights

Można utworzyć zdarzenia telemetrii elementów (w [usługi Application Insights](app-insights-overview.md)) do reprezentowania zdarzenie w aplikacji. Zwykle jest on interakcji z użytkownikiem, takich jak przycisk kliknij lub kolejność wyewidencjonowania. Można także zdarzeń cyklu życia aplikacji, takich jak inicjowanie lub konfiguracji aktualizacji. 

Semantycznie zdarzenia może lub nie może zostać skorelowane z żądania. Jednak jeśli prawidłowo używana, dane telemetryczne zdarzenia jest ważniejsze niż żądań i śladów. Zdarzenia należy tematu, aby rozdzielić od siebie, mniej aktywnego i reprezentują firm telemetrii [próbkowania](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Name (Nazwa)

Nazwa zdarzenia. Umożliwia grupowanie prawidłowego i przydatne metryki, należy ograniczyć aplikacji, tak aby generuje niewielką liczbę nazw oddzielne zdarzenie. Na przykład nie używaj osobnej nazwy dla każdego wystąpienia wygenerowane zdarzenia.

Maksymalna długość: 512 znaków

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Miar niestandardowych

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [modelu danych](application-insights-data-model.md) dla modelu danych i typów usługi Application Insights.
- [Pisanie niestandardowych zdarzeń telemetrii](app-insights-api-custom-events-metrics.md#trackevent)
- Zapoznaj się z [platform](app-insights-platforms.md) obsługiwane przez usługę Application Insights.
