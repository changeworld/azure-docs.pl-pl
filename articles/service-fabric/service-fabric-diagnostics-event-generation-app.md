---
title: Poziom aplikacji sieci szkieletowej usług Azure monitorowania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat i zdarzenia na poziomie usługi Dzienniki aplikacji i umożliwia monitorowanie i diagnozowanie klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 7af0dd37b5c16e48ce4e504211e68a29cf8bce77
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="application-and-service-level-logging"></a>Rejestrowanie poziomu usług i aplikacji

Kod Instrumentacji stanowi podstawę do większości innych aspektów monitorowania usług. Instrumentacja jest jedynym sposobem wiesz, czy jest coś niewłaściwego i diagnozowania, co wymaga naprawy. Chociaż jest technicznie można połączyć z usługą produkcji debugera, nie jest typowym rozwiązaniem. Tak o szczegółowe dane Instrumentacji jest ważna.

Niektóre produkty automatyczną Instrumentację kodu. Mimo że te rozwiązania mogą również działać, ręczne Instrumentacja prawie zawsze jest wymagana. W końcu musi mieć wystarczającej ilości informacji do całkowitego debugowania aplikacji. W tym dokumencie opisano różne podejścia do Instrumentacji kodu i kiedy należy wybrać jeden z nich zamiast innego.

Przykłady dotyczące używania tych sugestii, zobacz [Dodaj logowanie do aplikacji sieci szkieletowej usług](service-fabric-how-to-diagnostics-log.md).

## <a name="eventsource"></a>EventSource

Po utworzeniu rozwiązania sieci szkieletowej usług z szablonu w programie Visual Studio, **EventSource**-klasy (**ServiceEventSource** lub **ActorEventSource**) jest generowany. Utworzyć szablonu, w którym można dodać zdarzeń dla usługi lub aplikacji. **EventSource** nazwa **musi** być unikatowa i powinny zostać zmienione z domyślnego szablonu ciągu Moja firma -&lt;rozwiązania&gt;-&lt;projektu&gt;. Istnieniem wielu **EventSource** definicje, które korzystają z tej samej nazwy powoduje problem w czasie wykonywania. Każdy zdefiniowane zdarzenie musi mieć unikatowy identyfikator. Jeśli identyfikator nie jest unikatowa, wystąpi błąd czasu wykonywania. Niektóre organizacje preassign zakresów wartości identyfikatorów uniknąć konfliktów między oddzielne zespoły deweloperów. Aby uzyskać więcej informacji, zobacz [zaliczko w blogu](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) lub [dokumentacji MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>Platformy ASP.NET Core rejestrowania

Należy dokładnie zaplanować, jak będzie Instrumentacja kodu. Plan prawym Instrumentacji może pomóc w uniknięciu potencjalnie destabilizing kodzie, a następnie konieczności reinstrument kod. Aby zmniejszyć ryzyko, można wybrać Biblioteka instrumentacji, takie jak [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), który jest częścią pakietu Microsoft ASP.NET Core. Ma platformy ASP.NET Core [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) interfejs, który można używać z dostawcy wybranych przez użytkownika, przy jednoczesnym zmniejszeniu wpływu na istniejący kod. Kod w ASP.NET Core w systemach Windows i Linux, a pełna platformy .NET, więc kodu Instrumentacji jest standardowym.

## <a name="application-insights-sdk"></a>Zestaw SDK usługi Application Insights

Usługa Application Insights ma sformatowanego integracji z sieci szkieletowej usług fabrycznej. Użytkownicy mogą Dodawanie pakietów nuget AI usługi Service Fabric i odbierania danych i dzienniki utworzone i zebranych można wyświetlić w portalu Azure. Ponadto zaleca się dodanie własnych telemetrii, aby zdiagnozować i debugowania aplikacji i śledzenie, które usługi i części aplikacji są używane najczęściej. [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) klasa w zestawie SDK udostępnia wiele sposobów, aby śledzić telemetrii w aplikacji. Zapoznaj się z przykładem Instrumentacja i dodawania usługi application insights do aplikacji w naszym samouczku dla [monitorowanie i diagnozowanie aplikacji .NET](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="next-steps"></a>Kolejne kroki

Po wybraniu dostawcy rejestrowania Instrumentacja aplikacji i usług z dzienników i zdarzeń muszą zagregowane, zanim będzie można wysłać do dowolnej platformie analizy. Przeczytaj informacje o [usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md), i [WAD](service-fabric-diagnostics-event-aggregation-wad.md) aby lepiej zrozumieć niektóre zalecane opcje.
