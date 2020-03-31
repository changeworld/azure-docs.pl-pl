---
title: Monitorowanie poziomu aplikacji sieci szkieletowej usługi Azure
description: Dowiedz się więcej o zdarzeniach i dziennikach na poziomie aplikacji i usług używanych do monitorowania i diagnozowania klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464721"
---
# <a name="application-logging"></a>Rejestrowanie aplikacji

Instrumentowanie kodu jest nie tylko sposobem uzyskania szczegółowych informacji o użytkownikach, ale także jedynym sposobem, w jaki można wiedzieć, czy coś jest nie tak w aplikacji i zdiagnozować, co należy naprawić. Chociaż technicznie jest możliwe połączenie debugera z usługą produkcyjną, nie jest to powszechna praktyka. Tak więc, posiadanie szczegółowych danych oprzyrządowania jest ważne.

Niektóre produkty automatycznie instrumentować kod. Chociaż te rozwiązania mogą działać dobrze, ręczne instrumentowanie jest prawie zawsze wymagane, aby być specyficzne dla logiki biznesowej. W końcu musi mieć wystarczającą ilość informacji do forensically debugowania aplikacji. Aplikacje sieci szkieletowej usług mogą być instrumentowane z dowolnej struktury rejestrowania. W tym dokumencie opisano kilka różnych podejść do instrumentacji kodu i kiedy wybrać jedno podejście nad innym. 

Aby zapoznać się z przykładami dotyczącymi używania tych sugestii, zobacz [Dodawanie rejestrowania do aplikacji sieci szkieletowej usług](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Application Insights SDK

Usługa Application Insights ma bogatą integrację z sieci szkieletową usług po wyjęciu z pudełka. Użytkownicy mogą dodawać pakiety nuget sieci szkieletowej usług AI i odbierać dane i dzienniki utworzone i zebrane widoczne w witrynie Azure portal. Ponadto użytkownicy są zachęcani do dodawania własnych danych telemetrycznych w celu diagnozowania i debugowania ich aplikacji i śledzenia, które usługi i części ich aplikacji są najczęściej używane. [Klasa TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) w SDK udostępnia wiele sposobów śledzenia danych telemetrycznych w aplikacjach. Zapoznaj się z przykładem, jak instrumentować i dodawać szczegółowe informacje o aplikacji w naszym samouczku do [monitorowania i diagnozowania aplikacji .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Podczas tworzenia rozwiązania sieci szkieletowej usług na podstawie szablonu w programie Visual Studio jest generowana klasa **eventsource****(ServiceEventSource** lub **ActorEventSource).** Tworzony jest szablon, w którym można dodać zdarzenia dla aplikacji lub usługi. Nazwa **EventSource** **musi** być unikatowa i powinna zostać zmieniona z&lt;&gt;-&lt;domyślnego ciągu szablonu Projektu&gt;rozwiązania MyCompany. Posiadanie wielu definicji **EventSource,** które używają tej samej nazwy powoduje problem w czasie wykonywania. Każde zdefiniowane zdarzenie musi mieć unikatowy identyfikator. Jeśli identyfikator nie jest unikatowy, występuje błąd środowiska uruchomieniowego. Niektóre organizacje preassign zakresy wartości dla identyfikatorów, aby uniknąć konfliktów między oddzielnymi zespołami programistycznymi. Aby uzyskać więcej informacji, zobacz [blog Vance'a](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) lub [dokumentację MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>rejestrowanie rdzenia ASP.NET

Ważne jest, aby dokładnie zaplanować, jak będzie instrument kodu. Plan instrumentacji prawo może pomóc uniknąć potencjalnie destabilizacji bazy kodu, a następnie konieczności ponownego powtnienia kodu. Aby zmniejszyć ryzyko, można wybrać bibliotekę instrumentacji, taką jak [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), która jest częścią programu Microsoft ASP.NET Core. ASP.NET Core ma interfejs [ILogger,](/dotnet/api/microsoft.extensions.logging.ilogger) którego można używać z wybranym dostawcą, minimalizując jednocześnie wpływ na istniejący kod. Można użyć kodu w ASP.NET Core w systemie Windows i Linux oraz w pełnym programie .NET Framework, dzięki czemu kod instrumentacji jest standaryzowany.

## <a name="next-steps"></a>Następne kroki

Po wybraniu dostawcy rejestrowania do instrumencie aplikacji i usług, dzienniki i zdarzenia muszą być agregowane, zanim będą mogły być wysyłane do dowolnej platformy analizy. Przeczytaj o [usłudze Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) i [EventFlow,](service-fabric-diagnostics-event-aggregation-eventflow.md) aby lepiej zrozumieć niektóre zalecane opcje usługi Azure Monitor.
