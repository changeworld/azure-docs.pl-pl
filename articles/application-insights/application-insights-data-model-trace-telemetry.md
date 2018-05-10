---
title: Model danych Telemetrii Insights aplikacji Azure — dane telemetryczne śledzenia | Dokumentacja firmy Microsoft
description: Model danych usługi Insights aplikacji dla dane telemetryczne śledzenia
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
ms.openlocfilehash: d93ed9f292b6c05d0a3fb3202567f4024f62e35e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="trace-telemetry-application-insights-data-model"></a>Dane telemetryczne śledzenia: model danych usługi Application Insights

Dane telemetryczne śledzenia (w [usługi Application Insights](app-insights-overview.md)) reprezentuje `printf` styl instrukcji śledzenia, które są przeszukiwane tekstu. `Log4Net`, `NLog`, i inne wpisy w pliku tekstowym dziennika są przetłumaczyć wystąpień tego typu. Śledzenie nie ma pomiarów jako rozszerzalności.

## <a name="message"></a>Komunikat

Komunikat śledzenia.

Maksymalna długość: 32768 znaków

## <a name="severity-level"></a>Poziom ważności

Poziom ważności śledzenia. Wartość może być `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Eksploruj dzienniki śledzenia platformy .NET w usłudze Application Insights](app-insights-asp-net-trace-logs.md).
- [Eksploruj dzienniki śledzenia w usłudze Application Insights Java](app-insights-java-trace-logs.md).
- Zobacz [modelu danych](application-insights-data-model.md) dla modelu danych i typów usługi Application Insights.
- [Zapisać dane telemetryczne śledzenia niestandardowych](app-insights-api-custom-events-metrics.md#tracktrace)
- Zapoznaj się z [platform](app-insights-platforms.md) obsługiwane przez usługę Application Insights.
