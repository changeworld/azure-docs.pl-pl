---
title: Usługa Azure Service Fabric aplikacji poziom monitorowania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat i zdarzeń na poziomie usługi Dzienniki aplikacji i umożliwia monitorowanie i diagnozowanie klastrów usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 613faf5bbc9498b82bc04460d30b2e94c30340db
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661058"
---
# <a name="application-logging"></a>Rejestrowanie aplikacji

Kod Instrumentacji jest nie tylko sposób w celu uzyskania szczegółowych informacji o użytkownikach, ale również jedynym sposobem, w których możesz sprawdzać, czy coś jest nie tak, w aplikacji i w celu zdiagnozowania, co musi mieć stałą. Chociaż z technicznego punktu widzenia można połączyć debugera usługę produkcyjną, nie jest powszechną praktyką. Dlatego po szczegółowe dane Instrumentacji jest ważne.

Niektóre produkty automatycznie instrumentować swój kod. Mimo że te rozwiązania mogą działać prawidłowo, ręcznej Instrumentacji prawie zawsze musi być specyficzne dla logiki biznesowej. W końcu musi mieć wystarczającą ilość informacji do całkowitego debugowania aplikacji. Aplikacje usługi Service Fabric można wyposażyć w dowolnej struktury rejestrowania. W tym dokumencie opisano kilka różne metody Instrumentacji kodu i kiedy należy wybrać jedno z podejść zamiast innego. 

Przykłady dotyczące używania tych sugestii, zobacz [Dodawanie rejestrowania aplikacji usługi Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Zestaw SDK usługi Application Insights

Usługa Application Insights ma rozbudowanej integracji z usługą Service Fabric gotowe. Użytkownicy mogą dodawać pakiety nuget sztucznej Inteligencji usługi Service Fabric i odbierania danych i dzienników i zebranych można wyświetlić w witrynie Azure portal. Ponadto zaleca się dodanie własnych danych telemetrycznych w celu diagnozowanie i debugowanie swoich aplikacji i śledzenie, które usługi i części swoich aplikacji są używane najczęściej. [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) klasy w zestawie SDK udostępnia wiele sposobów, aby śledzić telemetrii w aplikacji. Zapoznaj się z przykładem sposobu Instrumentacja i Dodaj usługę application insights do aplikacji w naszym samouczku dla [monitorowanie i diagnozowanie aplikacji platformy .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Podczas tworzenia rozwiązań usługi Service Fabric z szablonu w programie Visual Studio, **EventSource**-klasy pochodnej (**ServiceEventSource** lub **ActorEventSource**) jest generowany . Szablon jest tworzony, w którym można dodać zdarzenia dla aplikacji lub usługi. **EventSource** nazwa **musi** być unikatowa i powinny zostać zmienione z ciągu szablon domyślny plik -&lt;rozwiązania&gt;-&lt;projektu &gt;. Posiadanie wielu **EventSource** definicje, które używają tej samej nazwie powoduje problem w czasie wykonywania. Każde zdarzenie zdefiniowanych musi mieć unikatowy identyfikator. Jeśli identyfikator nie jest unikatowa, wystąpi błąd czasu wykonywania. W niektórych organizacjach preassign zakresów wartości identyfikatorów w celu uniknięcia konfliktów między oddzielne zespoły deweloperów. Aby uzyskać więcej informacji, zobacz [blog firmy zaliczko](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) lub [dokumentacji MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>Platforma ASP.NET Core rejestrowania

Należy dokładnie zaplanować, jak będzie instrumentować swój kod. Plan prawym Instrumentacji mogą pomóc w uniknięciu potencjalnie destabilizing bazy kodu, a następnie konieczności reinstrument kodu. Aby zmniejszyć ryzyko, możesz wybrać Biblioteka instrumentacji, takie jak [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), który jest częścią programu Microsoft ASP.NET Core. ASP.NET Core jest [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) interfejs, który za pomocą dostawcy wybranych przez użytkownika, jednocześnie minimalizując wpływ na istniejący kod. Można użyć kodu w programie ASP.NET Core w systemach Windows i Linux, a w pełny program .NET Framework, więc kod instrumentacji ze standardami.

## <a name="next-steps"></a>Kolejne kroki

Po wybraniu opcji rejestrowania dostawcy Instrumentacji aplikacji i usług, swoje dzienniki i zdarzenia należy zagregować, zanim będzie można wysłać do dowolnej platformy analizy. Przeczytaj o [usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) i [użyciu struktury EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) aby lepiej zrozumieć niektóre usługi Azure Monitor zalecane opcje.
