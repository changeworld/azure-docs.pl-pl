---
title: Rozproszone śledzenie w ramach usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zawiera informacje o pomocy technicznej firmy Microsoft do rozproszonego śledzenia za pośrednictwem naszych lokalnej usługi przesyłania dalej i współpracy w projekcie OpenCensus
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7bc04748f2a5b8caa8f589140dd46f0650b7b390
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898852"
---
# <a name="what-is-distributed-tracing"></a>Co to jest rozproszonego śledzenia?

Pojawienie się nowoczesnym rozwiązaniom w chmurze i [mikrousług](https://azure.com/microservices) architektury spowodowało proste, niezależnie do wdrożenia usług, które mogą pomóc w obniżeniu kosztów przy jednoczesnym zwiększeniu dostępność i przepływność. Ale podczas gdy przesunięcia te mają poszczególnych usług ułatwia zrozumienie jako całości, ich wprowadzone ogólną systemów trudniejsze poprawić i debugowania.

W architekturami monolitycznymi uzyskane używaliśmy do debugowania za pomocą stosów wywołań. Stosy wywołań to doskonały narzędzia do wyświetlania przepływem wykonania (metoda wywoływana metoda B, który wywołuje metodę C), oraz szczegóły i parametry o każdej z tych wywołań. Jest to doskonałe rozwiązanie dla monolitycznych projektów lub usług działających w ramach jednego procesu, ale w jaki sposób możemy debugować kiedy wywołanie jest granicę procesu nie po prostu odwołania na stosie lokalnego? 

To, gdzie rozproszonego śledzenia jest oferowana w.  

Rozproszonego śledzenia jest odpowiednikiem stosy wywołań dla nowoczesnych architektur mikrousług i w chmurze dodając profilera wydajności uproszczony zgłaszane w. W usłudze Azure Monitor firma Microsoft zapewnia obydwoma środowiskami, co umożliwia korzystanie z danych rozproszonego śledzenia. Pierwsza to naszym [Diagnostyka transakcji](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) widoku, który działa jak stos wywołań z wymiarem czasu dodany do programu. Wyświetl diagnostykę transakcji zapewnia wgląd w jednego transakcji/żądania i jest przydatny do znajdowania przyczyny niezawodnością i wydajnością na podstawie danego żądania.

Usługa Azure Monitor zapewnia również [mapy aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) widoku, która agreguje wiele transakcji, aby wyświetlić widok topologiczny sposobu interakcji systemów i jakie średniej wydajności i współczynniki błędów. 

## <a name="how-to-enable-distributed-tracing"></a>Jak włączyć rozproszonego śledzenia

Włączenie rozproszonego śledzenia dla usług w aplikacji jest proste i polega na dodaniu prawidłowego zestawu SDK lub bibliotekę do każdej usługi oparte na języku, który usługa została wdrożona w.

## <a name="enabling-via-application-insights-sdks"></a>Włączanie za pomocą zestawów SDK Application Insights

Zestawy Application Insights SDK dla platformy .NET, .NET Core, Java, Node.js i JavaScript wszystkie obsługują natywnie rozproszonego śledzenia Instrukcje dotyczące instalowania i konfigurowania każdego zestawu SDK usługi Application Insights są dostępne poniżej:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

Przy użyciu prawidłowego zestawu SDK Application Insights zainstalowany i skonfigurowany informacje śledzenia są automatycznie zbierane dla popularnych platform, bibliotek i technologii przez zestaw SDK zależności auto-modułów zbierających dzienniki. Pełna lista obsługiwanych technologii jest dostępna w [dokumentacja automatycznego zbierania zależności](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Ponadto, dowolna Technologia można śledzić ręcznie przy użyciu wywołania do [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) na [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Włącz za pośrednictwem OpenCensus

Oprócz zestawy Application Insights SDK usługi Application Insights obsługuje również rozproszonego śledzenia za pośrednictwem [OpenCensus](https://opencensus.io/). OpenCensus jest typu open source, niezwiązane z żadnym dostawcą, jednym dystrybucję biblioteki umożliwia zbieranie metryk i rozproszonego śledzenia dla usług. Umożliwia ona także społeczności "open source", aby włączyć śledzenie rozproszonego przy użyciu popularnych technologii, takich jak Redis Memcached lub bazy danych MongoDB. [Microsoft współpracuje w OpenCensus z kilku innych monitorowania i chmurze partnerów](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Aby dodać do aplikacji za pomocą OpenCensus, możliwości rozproszonego śledzenia najpierw [zainstalować i skonfigurować usługę przesyłania lokalnego dalej w programie Application Insights](./../../azure-monitor/app/opencensus-local-forwarder.md). W tym miejscu należy skonfigurować OpenCensus do kierowania danych rozproszonego śledzenia za pomocą lokalnej usługi przesyłania dalej. Zarówno [Python](./../../azure-monitor/app/opencensus-python.md) i [Przejdź](./../../azure-monitor/app/opencensus-go.md) są obsługiwane.

Witryna OpenCensus zachowuje Dokumentacja referencyjna interfejsu API [Python](https://opencensus.io/api/python/trace/usage.html) i [Przejdź](https://godoc.org/go.opencensus.io), jak również jako różne różne przewodniki dotyczące korzystania z OpenCensus. 

## <a name="next-steps"></a>Kolejne kroki

* [Podręcznik użycia OpenCensus języka Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Monitorowanie wydajności end-to-end](./../../azure-monitor/learn/tutorial-performance.md)
