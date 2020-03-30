---
title: Model danych zależności usługi Azure Monitor application insights
description: Model danych usługi Application Insights dla telemetrii zależności
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671923"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria zależności: model danych usługi Application Insights

Telemetria zależności (w [usłudze Application Insights)](../../azure-monitor/app/app-insights-overview.md)reprezentuje interakcję monitorowanego składnika ze składnikiem zdalnym, takim jak SQL lub punkt końcowy HTTP.

## <a name="name"></a>Nazwa

Nazwa polecenia zainicjowanego za pomocą tego wywołania zależności. Niska wartość kardynalność. Przykładami są nazwa procedury składowanej i szablon ścieżki adresu URL.

## <a name="id"></a>ID

Identyfikator wystąpienia wywołania zależności. Używane do korelacji z elementem telemetrii żądania odpowiadającym temu wywołaniu zależności. Aby uzyskać więcej informacji, zobacz stronę [korelacji.](../../azure-monitor/app/correlation.md)

## <a name="data"></a>Dane

Polecenie zainicjowane przez to wywołanie zależności. Przykładami są instrukcja SQL i adres URL HTTP ze wszystkimi parametrami zapytania.

## <a name="type"></a>Typ

Nazwa typu zależności. Niska wartość kardynalność dla logicznego grupowania zależności i interpretacji innych pól, takich jak commandName i resultCode. Przykładami są SQL, tabela platformy Azure i HTTP.

## <a name="target"></a>Środowisko docelowe

Witryna docelowa wywołania zależności. Przykładami są nazwa serwera, adres hosta. Aby uzyskać więcej informacji, zobacz stronę [korelacji.](../../azure-monitor/app/correlation.md)

## <a name="duration"></a>Czas trwania

Czas trwania żądania `DD.HH:MM:SS.MMMMMM`w formacie: . Musi być `1000` mniejsza niż dni.

## <a name="result-code"></a>Kod wyniku

Kod wyniku wywołania zależności. Przykładami są kod błędu SQL i kod stanu HTTP.

## <a name="success"></a>Powodzenie

Wskazanie udanego lub nieudanego połączenia.

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Następne kroki

- Skonfiguruj śledzenie zależności dla [platformy .NET](../../azure-monitor/app/asp-net-dependencies.md).
- Skonfiguruj śledzenie zależności dla [języka Java](../../azure-monitor/app/java-agent.md).
- [Zapisywanie danych telemetrycznych zależności niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobacz [model danych](data-model.md) dla typów usługi Application Insights i modelu danych.
- Zapoznaj się z [platformami](../../azure-monitor/app/platforms.md) obsługiwanymi przez usługa Application Insights.
