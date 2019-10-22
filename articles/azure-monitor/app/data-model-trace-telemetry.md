---
title: Model danych telemetria usługi Application Insights platformy Azure — dane telemetryczne śledzenia | Microsoft Docs
description: Application Insights model danych na potrzeby telemetrii śledzenia
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 855a93d8a6350c625fe0820fae83644aec3459ed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678113"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Dane telemetryczne śledzenia: Application Insights model danych

Dane telemetryczne śledzenia (w [Application Insights](../../azure-monitor/app/app-insights-overview.md)) reprezentują `printf`e instrukcje dotyczące śledzenia stylów, które są przeszukiwane przez tekst. `Log4Net`, `NLog` i inne wpisy pliku dziennika na podstawie tekstu są tłumaczone na wystąpienia tego typu. Ślad nie ma pomiarów jako rozszerzalności.

## <a name="message"></a>Wiadomość

Komunikat śledzenia.

Maksymalna długość: 32768 znaków

## <a name="severity-level"></a>Poziom ważności

Poziom ważności śledzenia. Wartość może być `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Następne kroki

- [Poznaj dzienniki śledzenia .NET w Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Poznaj dzienniki śledzenia środowiska Java w Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- [Napisz niestandardowe dane telemetryczne śledzenia](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
