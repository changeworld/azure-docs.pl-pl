---
title: Model danych Telemetrii aplikacji platformy Azure w szczegółowych informacji | Dokumentacja firmy Microsoft
description: Omówienie modelu danych usługi Application Insights
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
ms.openlocfilehash: aaff60e847e0e9908a4cd9c07cb6cd47630c5e3a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647709"
---
# <a name="application-insights-telemetry-data-model"></a>Model danych telemetrii Insights aplikacji

[Usługa Azure Application Insights](app-insights-overview.md) wysyła dane telemetryczne z aplikacji sieci web w witrynie Azure Portal, dzięki czemu można analizować wydajność i użycie aplikacji. Model danych telemetrycznych są standaryzowane tak, aby można utworzyć platformy i monitorowania niezależny od języka. 

Dane zbierane przez usługę Application Insights modele tego wzorca do wykonywania typowych aplikacji:

![Model Application Insights aplikacji](./media/application-insights-data-model/application-insights-data-model.png)

Następujące rodzaje danych telemetrycznych są używane do monitorowania wykonywania aplikacji. Następujące trzy typy są zwykle automatycznie gromadzone przez zestaw SDK usługi Application Insights z platforma aplikacji sieci web:

* [**Żądanie** ](application-insights-data-model-request-telemetry.md) — wygenerowanego się żądanie odebrane przez aplikację. Na przykład w sieci web usługi Application Insights SDK automatycznie generuje elementu telemetrii żądania dla każdego żądania HTTP, która aplikacja internetowa odbierze. 

    **Operacji** jest wątków wykonawczych, która przetwarza żądania. Możesz również [pisanie kodu](app-insights-api-custom-events-metrics.md#trackrequest) monitorować inne rodzaje operacji, takich jak "wznawiania" w sieci web zadania lub funkcja, która okresowo przetwarza dane.  Każda operacja ma identyfikator. Ten identyfikator, który może być używany do [grupy](application-insights-correlation.md) wszystkie dane telemetryczne generowane podczas przetwarzania żądania przez aplikację. Każda operacja albo zakończy się pomyślnie lub nie powiedzie się i zawiera dany okres czasu.
* [**Wyjątek** ](application-insights-data-model-exception-telemetry.md) — zazwyczaj reprezentuje wyjątek, który powoduje, że operacja nie powiodła się.
* [**Zależność** ](application-insights-data-model-dependency-telemetry.md) — reprezentuje wywołanie z aplikacji do usługi zewnętrznej lub magazynu, takich jak interfejs API REST lub SQL. W programie ASP.NET: wywołania zależności do bazy danych SQL są definiowane przez `System.Data`. Wywołania do punktów końcowych HTTP są definiowane przez `System.Net`. 

Usługa Application Insights udostępnia trzy typy dodatkowe dane dla telemetrii niestandardowej:

* [Śledzenie](application-insights-data-model-trace-telemetry.md) — używać bezpośrednio lub za pośrednictwem karty, implementacji rejestrowania diagnostycznego przy użyciu framework instrumentacji, który jest znany, takich jak `Log4Net` lub `System.Diagnostics`.
* [Zdarzenie](application-insights-data-model-event-telemetry.md) — zwykle używane do przechwytywania interakcji użytkownika z usługi, aby analizować wzorce użycia.
* [Metryka](application-insights-data-model-metric-telemetry.md) — używane do pomiarów skalarne okresowych raportu.

Można zdefiniować każdego elementu telemetrii [informacje o kontekście](application-insights-data-model-context.md) takich jak identyfikator sesji aplikacji w wersji lub użytkownika. Kontekst jest zestaw pól silnie typizowaną odblokowuje niektórych scenariuszy. Wersja aplikacji jest prawidłowo zainicjowana, Application Insights może sprawdzić nowych wzorców w zachowaniu aplikacji skorelowane z ponownego wdrażania. Identyfikator sesji mogą być używane do obliczania awarii lub wpływu problemu na użytkowników. Obliczanie liczność unikatowych wartości wartości identyfikatora sesji dla niektórych nieudana zależność, śledzenia błąd lub wyjątek krytyczny zapewnia dobre zrozumienie wpływu na działalność.

Model danych telemetrycznych Insights aplikacji definiuje sposób [skorelować](application-insights-correlation.md) telemetrię, aby wykonać operację, który jest częścią. Na przykład żądanie może wykonywać wywołania bazy danych SQL i rejestrowane informacje diagnostyczne. Można ustawić kontekstu korelacji dla tych elementów danych telemetrycznych, które powiązanie ich dane telemetryczne żądania.

## <a name="schema-improvements"></a>Ulepszenia schematu

Model danych szczegółowych informacji w aplikacji jest proste i podstawowe zarazem zaawansowany sposób modelować dane telemetryczne aplikacji. Dążymy do zachowania modelu proste i obsługiwane na potrzeby podstawowych scenariuszy i umożliwiają rozszerzanie schematu dla zaawansowanych.

Aby zgłosić problemy schematu lub model danych i sugestie korzystania z serwisu GitHub [ApplicationInsights głównej](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) repozytorium.

## <a name="next-steps"></a>Kolejne kroki

- [Zapisywanie niestandardowych danych telemetrycznych](app-insights-api-custom-events-metrics.md)
- Dowiedz się, jak [rozszerzyć i filtrowanie danych telemetrycznych](app-insights-api-filtering-sampling.md).
- Użyj [próbkowania](app-insights-sampling.md) zminimalizować ilość danych telemetrycznych na podstawie modelu danych.
- Zapoznaj się z [platform](app-insights-platforms.md) obsługiwane przez usługę Application Insights.
