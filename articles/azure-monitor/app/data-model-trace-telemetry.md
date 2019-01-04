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
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: a80c96891f3d91a920519db2915932742bd84d72
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002301"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria śledzenia usługi: Model danych usługi Application Insights

Śledzenie danych telemetrycznych (w [usługi Application Insights](../../application-insights/app-insights-overview.md)) reprezentuje `printf` stylu instrukcji śledzenia, które są przeszukiwane tekstu. `Log4Net`, `NLog`, i innych wpisy w pliku tekstowym dziennika są tłumaczone na wystąpień tego typu. Śledzenie nie ma miar jako rozszerzalności.

## <a name="message"></a>Komunikat

Komunikat śledzenia.

Maksymalna długość: wynosić 32 768 znaków

## <a name="severity-level"></a>Poziom ważności

Śledzenie poziomu ważności. Wartość może być `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Eksplorowanie dzienników śledzenia .NET w usłudze Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Zapoznaj się z języka Java, dzienniki śledzenia w usłudze Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Zobacz [modelu danych](data-model.md) dla usługi Application Insights typów i danych modelu.
- [Zapisywanie śledzenia niestandardowych danych telemetrycznych](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Zapoznaj się z [platform](../../azure-monitor/app/platforms.md) obsługiwane przez usługę Application Insights.
