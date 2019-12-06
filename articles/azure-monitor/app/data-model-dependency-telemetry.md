---
title: Model danych zależności Application Insights Azure Monitor
description: Application Insights model danych dla telemetrii zależności
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 5021d3b34816159fc78590a5947ddd3a790303ee
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872642"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria zależności: Application Insights model danych

Telemetria zależności (w [Application Insights](../../azure-monitor/app/app-insights-overview.md)) reprezentuje interakcję monitorowanego składnika ze składnikiem zdalnym, takim jak SQL lub punkt końcowy HTTP.

## <a name="name"></a>Nazwa

Nazwa polecenia zainicjowanego z tym wywołaniem zależności. Niska wartość kardynalności. Przykładami są nazwa procedury przechowywanej i szablon ścieżki URL.

## <a name="id"></a>ID

Identyfikator wystąpienia wywołania zależności. Używany do korelacji z elementem żądania telemetrii, odpowiadającym temu wywołaniu zależności. Aby uzyskać więcej informacji, zobacz stronę [korelacji](../../azure-monitor/app/correlation.md) .

## <a name="data"></a>Dane

Polecenie zainicjowane przez to wywołanie zależności. Przykłady to instrukcja SQL i adres URL protokołu HTTP ze wszystkimi parametrami zapytania.

## <a name="type"></a>Typ

Nazwa typu zależności. Niska wartość kardynalności logicznego grupowania zależności i interpretacji innych pól, takich jak CommandName i resultCode. Przykłady to SQL, Azure Table i HTTP.

## <a name="target"></a>Cel

Lokacja docelowa wywołania zależności. Przykłady to nazwa serwera, adres hosta. Aby uzyskać więcej informacji, zobacz stronę [korelacji](../../azure-monitor/app/correlation.md) .

## <a name="duration"></a>Czas trwania

Czas trwania żądania w formacie: `DD.HH:MM:SS.MMMMMM`. Musi być krótszy niż `1000` dni.

## <a name="result-code"></a>Kod wyniku

Kod wyniku wywołania zależności. Przykłady to kod błędu SQL i kod stanu HTTP.

## <a name="success"></a>Powodzenie

Wskazanie powodzenia lub nieudanych wywołań.

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Następne kroki

- Skonfiguruj Śledzenie zależności dla [platformy .NET](../../azure-monitor/app/asp-net-dependencies.md).
- Skonfiguruj Śledzenie zależności dla [środowiska Java](../../azure-monitor/app/java-agent.md).
- [Napisz niestandardową telemetrię zależności](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
