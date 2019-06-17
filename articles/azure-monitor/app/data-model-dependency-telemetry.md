---
title: Model danych aplikacji systemu Azure szczegółowe informacje Telemetrii — Telemetrii zależności | Dokumentacja firmy Microsoft
description: Model danych szczegółowych informacji aplikacji dla telemetrii zależności
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 3e3d6b8fdc9ac8dd28f73fecd6231e97a5645407
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60901029"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria zależności: Model danych usługi Application Insights

Telemetria zależności (w [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md)) reprezentuje interakcji monitorowanego składnika za pomocą zdalnego składnika, takiego jak SQL lub punkt końcowy HTTP.

## <a name="name"></a>Name (Nazwa)

Nazwa polecenia inicjowane za pomocą tego wywołania zależności. Kardynalność niska wartość. Przykłady to nazwa procedury składowanej i szablon ścieżki adresu URL.

## <a name="id"></a>ID

Identyfikator wystąpienia wywołań zależności. Używane na potrzeby korelacji z elementu telemetrii żądania odpowiadający wywołanie zależności. Aby uzyskać więcej informacji, zobacz [korelacji](../../azure-monitor/app/correlation.md) strony.

## <a name="data"></a>Dane

Polecenia inicjowane przez wywołanie zależności. Przykładami są instrukcji SQL i adresem URL protokołu HTTP z wszystkie parametry zapytania.

## <a name="type"></a>Typ

Nazwa typu zależności. Niski Kardynalność wartości powodują ustawienie logicznego grupowania zależności i interpretacji inne pola, takie jak commandName i kod wyniku. Przykłady to SQL, tabela platformy Azure i HTTP.

## <a name="target"></a>Cel

Witryna docelowa wywołania zależności. Przykłady to nazwa serwera, adres hosta. Aby uzyskać więcej informacji, zobacz [korelacji](../../azure-monitor/app/correlation.md) strony.

## <a name="duration"></a>Czas trwania

Czas trwania w formacie żądania: `DD.HH:MM:SS.MMMMMM`. Musi być mniejsza niż `1000` dni.

## <a name="result-code"></a>Kod wyniku

Kod wyniku wywołania zależności. Przykłady to kod błędu SQL i kod stanu HTTP.

## <a name="success"></a>Powodzenie

Wskazanie wywołanie powodzeniem lub niepowodzeniem.

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Kolejne kroki

- Konfigurowanie śledzenia dla zależności [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Konfigurowanie śledzenia dla zależności [Java](../../azure-monitor/app/java-agent.md).
- [Zapisywanie danych telemetrycznych zależność niestandardową](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobacz [modelu danych](data-model.md) dla usługi Application Insights typów i danych modelu.
- Zapoznaj się z [platform](../../azure-monitor/app/platforms.md) obsługiwane przez usługę Application Insights.
