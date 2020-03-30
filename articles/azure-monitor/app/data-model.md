---
title: Model danych telemetrycznych usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Omówienie modelu danych usługi Application Insights
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 94013a12e1cf48a8007fce2547c200d82a657b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671838"
---
# <a name="application-insights-telemetry-data-model"></a>Model danych telemetrycznych usługi Application Insights

[Usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) wysyła dane telemetryczne z aplikacji sieci web do witryny Azure portal, dzięki czemu można analizować wydajność i użycie aplikacji. Model telemetrii jest standaryzowany, dzięki czemu można utworzyć monitorowanie niezależne od platformy i języka. 

Dane zbierane przez usługi Application Insights modeluje ten typowy wzorzec wykonywania aplikacji:

![Application Insights Application Model](./media/data-model/application-insights-data-model.png)

Następujące typy danych telemetrycznych są używane do monitorowania wykonywania aplikacji. Następujące trzy typy są zazwyczaj automatycznie zbierane przez SDK usługi Application Insights z struktury aplikacji sieci web:

* [**Żądanie**](data-model-request-telemetry.md) — wygenerowane w celu zarejestrowania żądania odebranego przez aplikację. Na przykład sdk sieci Web usługi Application Insights automatycznie generuje element telemetrii żądania dla każdego żądania HTTP, które otrzymuje aplikacja sieci web. 

    **Operacja** jest wątki wykonywania, który przetwarza żądanie. Można również [napisać kod](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) do monitorowania innych typów operacji, takich jak "wybudzenie" w zadaniu sieci web lub funkcji, która okresowo przetwarza dane.  Każda operacja ma identyfikator. Ten identyfikator, który może służyć do [grupowanie](../../azure-monitor/app/correlation.md) wszystkich danych telemetrycznych generowanych podczas przetwarzania żądania przez aplikację. Każda operacja zakończy się powodzeniem lub niepowodzeniem i ma czas trwania.
* [**Wyjątek**](data-model-exception-telemetry.md) — zazwyczaj reprezentuje wyjątek, który powoduje, że operacja zakończy się niepowodzeniem.
* [**Zależność**](data-model-dependency-telemetry.md) — reprezentuje wywołanie z aplikacji do usługi zewnętrznej lub magazynu, takich jak interfejs API REST lub SQL. W ASP.NET wywołania zależności do języka SQL `System.Data`są definiowane przez program . Wywołania punktów końcowych HTTP `System.Net`są definiowane przez program . 

Usługa Application Insights udostępnia trzy dodatkowe typy danych dla niestandardowych danych telemetrycznych:

* [Śledzenie](data-model-trace-telemetry.md) — używane bezpośrednio lub za pośrednictwem karty do implementowania rejestrowania diagnostyki przy użyciu `Log4Net` struktury `System.Diagnostics`instrumentacji, która jest ci znana, na przykład lub .
* [Zdarzenie](data-model-event-telemetry.md) — zwykle używane do przechwytywania interakcji użytkownika z usługą, do analizowania wzorców użycia.
* [Metryka](data-model-metric-telemetry.md) — służy do raportowania okresowych pomiarów skalarnych.

Każdy element telemetrii można zdefiniować [informacje kontekstowe,](data-model-context.md) takie jak wersja aplikacji lub identyfikator sesji użytkownika. Kontekst to zestaw silnie wpisanych pól, które odblokowuje niektóre scenariusze. Gdy wersja aplikacji jest poprawnie zainicjowana, usługa Application Insights może wykryć nowe wzorce w zachowaniu aplikacji skorelowane z ponownemiejscem. Identyfikator sesji może służyć do obliczania awarii lub wpływu problemu na użytkowników. Obliczanie odrębnej liczby wartości identyfikatora sesji dla niektórych zależności nie powiodło się, śledzenia błędów lub wyjątku krytycznego daje dobre zrozumienie wpływu.

Model telemetrii usługi Application Insights definiuje sposób [skorelowania](../../azure-monitor/app/correlation.md) danych telemetrycznych z operacją, której jest częścią. Na przykład żądanie może nawiązać wywołania bazy danych SQL i zarejestrowane informacje diagnostyczne. Można ustawić kontekst korelacji dla tych elementów telemetrii, które wiążą go z powrotem do telemetrii żądania.

## <a name="schema-improvements"></a>Ulepszenia schematu

Model danych usługi Application Insights to prosty i podstawowy, ale zaawansowany sposób modelowania danych telemetrycznych aplikacji. Staramy się, aby model był prosty i smukły, aby obsługiwać podstawowe scenariusze i umożliwiał rozszerzenie schematu do użytku zaawansowanego.

Aby zgłosić problemy z modelem danych lub schematem i sugestie, użyj repozytorium [GitHub ApplicationInsights-Home.](https://github.com/Microsoft/ApplicationInsights-Home/issues)

## <a name="next-steps"></a>Następne kroki

- [Pisanie niestandardowych danych telemetrycznych](../../azure-monitor/app/api-custom-events-metrics.md)
- Dowiedz się, jak [rozszerzyć i filtrować dane telemetryczne](../../azure-monitor/app/api-filtering-sampling.md).
- [Próbkowanie](../../azure-monitor/app/sampling.md) służy do minimalizowania ilości danych telemetrycznych na podstawie modelu danych.
- Zapoznaj się z [platformami](../../azure-monitor/app/platforms.md) obsługiwanymi przez usługa Application Insights.
