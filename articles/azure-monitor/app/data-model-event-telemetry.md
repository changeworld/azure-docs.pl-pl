---
title: Model danych telemetrycznych usługi Azure Application Insights — dane telemetryczne zdarzeń | Dokumenty firmy Microsoft
description: Model danych usługi Application Insights dla telemetrii zdarzeń
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671887"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria zdarzeń: model danych usługi Application Insights

Można utworzyć elementy telemetryczne zdarzeń (w [usłudze Application Insights),](../../azure-monitor/app/app-insights-overview.md)aby reprezentować zdarzenie, które wystąpiło w aplikacji. Zazwyczaj jest to interakcja użytkownika, takich jak kliknięcie przycisku lub zamówienie wyewidencjonowania. Może to być również zdarzenie cyklu życia aplikacji, takie jak inicjowanie lub aktualizacja konfiguracji. 

Semantycznie, zdarzenia mogą lub nie mogą być skorelowane z żądaniami. Jednak jeśli jest używana poprawnie, telemetria zdarzeń jest ważniejsza niż żądania lub ślady. Zdarzenia reprezentują dane telemetryczne biznesowe i powinny podlegać oddzielne, mniej agresywne [próbkowanie](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Nazwa

Nazwa zdarzenia. Aby umożliwić prawidłowe grupowanie i przydatne metryki, należy ograniczyć aplikację, tak aby generuje niewielką liczbę oddzielnych nazw zdarzeń. Na przykład nie należy używać oddzielnej nazwy dla każdego wygenerowanego wystąpienia zdarzenia.

Maksymalna długość: 512 znaków

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- Zobacz [model danych](data-model.md) dla typów usługi Application Insights i modelu danych.
- [Pisanie niestandardowych danych telemetrycznych zdarzeń](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Zapoznaj się z [platformami](../../azure-monitor/app/platforms.md) obsługiwanymi przez usługa Application Insights.
