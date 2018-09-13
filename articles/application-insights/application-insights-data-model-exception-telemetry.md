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
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 88e521a04d69d4ca169e33a80ac15620568c5282
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644673"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Dane telemetryczne dotyczące wyjątków: model danych usługi Application Insights

W [usługi Application Insights](app-insights-overview.md), wystąpienia wyjątku reprezentuje obsłużonych i nieobsłużonych wyjątków, który wystąpił podczas wykonywania monitorowanej aplikacji.

## <a name="problem-id"></a>Identyfikator problemu

Identyfikator, gdy wyjątek został zgłoszony w kodzie. Używane do obsługi wyjątków grupowania. Zazwyczaj kombinacja typ wyjątku i funkcję ze stosu wywołań.

Maksymalna długość: 1024 znaki

## <a name="severity-level"></a>Poziom ważności

Śledzenie poziomu ważności. Wartość może być `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Szczegóły wyjątku

(Aby rozszerzony)

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [modelu danych](application-insights-data-model.md) dla usługi Application Insights typów i danych modelu.
- Dowiedz się, jak [diagnozowanie wyjątków w aplikacjach sieci web za pomocą usługi Application Insights](app-insights-asp-net-exceptions.md).
- Zapoznaj się z [platform](app-insights-platforms.md) obsługiwane przez usługę Application Insights.
