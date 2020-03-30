---
title: Model danych telemetrycznych wyjątków usługi Azure Application Insights
description: Model danych usługi Application Insights dla telemetrii wyjątków
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671940"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Dane telemetryczne wyjątku: model danych usługi Application Insights

W [usłudze Application Insights](../../azure-monitor/app/app-insights-overview.md)wystąpienie wyjątku reprezentuje obsługiwany lub nieobsługiowany wyjątek, który wystąpił podczas wykonywania monitorowanej aplikacji.

## <a name="problem-id"></a>Identyfikator problemu

Identyfikator, gdzie wyjątek został zgłoszony w kodzie. Używane do grupowania wyjątków. Zazwyczaj kombinacja typu wyjątku i funkcji ze stosu wywołań.

Maksymalna długość: 1024 znaków

## <a name="severity-level"></a>Poziom ważności

Śledzenie poziomu ważności. Wartość może `Verbose` `Information`być `Warning` `Error`, `Critical`, , , .

## <a name="exception-details"></a>Szczegóły wyjątku

(Do rozszerzenia)

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- Zobacz [model danych](data-model.md) dla typów usługi Application Insights i modelu danych.
- Dowiedz się, jak [diagnozować wyjątki w aplikacjach internetowych za pomocą usługi Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Zapoznaj się z [platformami](../../azure-monitor/app/platforms.md) obsługiwanymi przez usługa Application Insights.
