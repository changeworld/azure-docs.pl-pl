---
title: Model danych aplikacji systemu Azure szczegółowe informacje Telemetrii — Telemetria śledzenia usługi | Dokumentacja firmy Microsoft
description: Model danych szczegółowych informacji aplikacji dla telemetrii śledzenia
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
ms.openlocfilehash: 79d012ddb454842e53fe296d9b618438ee7b3e91
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093829"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Śledzenie danych telemetrycznych: model danych usługi Application Insights

Śledzenie danych telemetrycznych (w [usługi Application Insights](app-insights-overview.md)) reprezentuje `printf` stylu instrukcji śledzenia, które są przeszukiwane tekstu. `Log4Net`, `NLog`, i innych wpisy w pliku tekstowym dziennika są tłumaczone na wystąpień tego typu. Śledzenie nie ma miar jako rozszerzalności.

## <a name="message"></a>Komunikat

Komunikat śledzenia.

Maksymalna długość: wynosić 32 768 znaków

## <a name="severity-level"></a>Poziom ważności

Śledzenie poziomu ważności. Wartość może być `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Eksplorowanie dzienników śledzenia .NET w usłudze Application Insights](app-insights-asp-net-trace-logs.md).
- [Zapoznaj się z języka Java, dzienniki śledzenia w usłudze Application Insights](app-insights-java-trace-logs.md).
- Zobacz [modelu danych](application-insights-data-model.md) dla usługi Application Insights typów i danych modelu.
- [Zapisywanie śledzenia niestandardowych danych telemetrycznych](app-insights-api-custom-events-metrics.md#tracktrace)
- Zapoznaj się z [platform](app-insights-platforms.md) obsługiwane przez usługę Application Insights.
