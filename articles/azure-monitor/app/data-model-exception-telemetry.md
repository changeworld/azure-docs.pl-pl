---
title: Modelu danych usługi Azure Application Insights Telemetrii — dane telemetryczne dotyczące wyjątków | Dokumentacja firmy Microsoft
description: Application Insights modelu danych dane telemetryczne dotyczące wyjątków
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
ms.openlocfilehash: efd7ad43ee9a2206f474621612eca7dfe5079f99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60908069"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Dane telemetryczne dotyczące wyjątków: Model danych usługi Application Insights

W [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md), wystąpienia wyjątku reprezentuje obsłużonych i nieobsłużonych wyjątków, który wystąpił podczas wykonywania monitorowanej aplikacji.

## <a name="problem-id"></a>Identyfikator problemu

Identyfikator, gdy wyjątek został zgłoszony w kodzie. Używane do obsługi wyjątków grupowania. Zazwyczaj kombinacja typ wyjątku i funkcję ze stosu wywołań.

Maksymalna długość: 1024 znaki

## <a name="severity-level"></a>Poziom ważności

Śledzenie poziomu ważności. Wartość może być `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Szczegóły wyjątku

(Aby rozszerzony)

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [modelu danych](data-model.md) dla usługi Application Insights typów i danych modelu.
- Dowiedz się, jak [diagnozowanie wyjątków w aplikacjach sieci web za pomocą usługi Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Zapoznaj się z [platform](../../azure-monitor/app/platforms.md) obsługiwane przez usługę Application Insights.
