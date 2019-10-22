---
title: Azure telemetria usługi Application Insights Data Model — Telemetria wyjątku | Microsoft Docs
description: Application Insights model danych dla telemetrii wyjątku
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0ba1c94ee8dc78b937d650cff32e1518a7ca5a12
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677420"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Dane telemetryczne wyjątku: Application Insights model danych

W [Application Insights](../../azure-monitor/app/app-insights-overview.md)wystąpienie wyjątku reprezentuje obsłużony lub nieobsługiwany wyjątek, który wystąpił podczas wykonywania monitorowanej aplikacji.

## <a name="problem-id"></a>Identyfikator problemu

Identyfikator, pod którym został zgłoszony wyjątek w kodzie. Używany do grupowania wyjątków. Zwykle kombinacja typu wyjątku i funkcji ze stosu wywołań.

Maksymalna długość: 1024 znaków

## <a name="severity-level"></a>Poziom ważności

Poziom ważności śledzenia. Wartość może być `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Szczegóły wyjątku

(Do rozszerzenia)

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Dowiedz się, jak [zdiagnozować wyjątki w aplikacjach sieci Web za pomocą Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
