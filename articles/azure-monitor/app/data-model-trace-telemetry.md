---
title: Model danych usługi Azure Application Insights — dane śledzenia danych
description: Model danych usługi Application Insights dla telemetrii śledzenia
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671957"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Śledzenie danych telemetrycznych: model danych usługi Application Insights

Śledzenie danych telemetrycznych (w [usłudze Application Insights)](../../azure-monitor/app/app-insights-overview.md)reprezentuje `printf` instrukcje śledzenia stylu, które są przeszukiwane tekstem. `Log4Net`, `NLog`a inne tekstowe wpisy plików dziennika są tłumaczone na wystąpienia tego typu. Ślad nie ma pomiarów jako rozszerzalność.

## <a name="message"></a>Komunikat

Śledzenie wiadomości.

Maksymalna długość: 32768 znaków

## <a name="severity-level"></a>Poziom ważności

Śledzenie poziomu ważności. Wartość może `Verbose` `Information`być `Warning` `Error`, `Critical`, , , .

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Następne kroki

- [Eksploruj dzienniki śledzenia .NET w usłudze Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Eksploruj dzienniki śledzenia języka Java w usłudze Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Zobacz [model danych](data-model.md) dla typów usługi Application Insights i modelu danych.
- [Pisanie niestandardowych danych telemetrycznych śledzenia](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Zapoznaj się z [platformami](../../azure-monitor/app/platforms.md) obsługiwanymi przez usługa Application Insights.
