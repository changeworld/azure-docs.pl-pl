---
title: Śledzenie rozproszone w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Zawiera informacje o pomocy technicznej firmy Microsoft dla śledzenia rozproszonego za pośrednictwem naszej współpracy w projekcie OpenCensus
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 83575aa7f9b63615f453e00bd06b00a5540b9a9e
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892261"
---
# <a name="what-is-distributed-tracing"></a>Co to jest śledzenie rozproszone?

Pojawienie się nowoczesnych architektur chmury i [mikrousług](https://azure.com/microservices) doprowadziło do prostych, niezależnie wdrażanych usług, które mogą pomóc obniżyć koszty przy jednoczesnym zwiększeniu dostępności i przepływności. Ale chociaż te ruchy ułatwiły zrozumienie poszczególnych usług jako całości, sprawiły, że ogólne systemy stały się trudniejsze do uzasadnienia i debugowania.

W architekturach monolitycznych przyzwyczailiśmy się do debugowania za pomocą stosów wywołań. Stosy wywołań są genialne narzędzia do wyświetlania przepływu wykonania (metoda A o nazwie metoda B, która nazywa się metoda C), wraz ze szczegółami i parametrami o każdym z tych wywołań. Jest to idealne rozwiązanie dla monolitów lub usług uruchomionych w jednym procesie, ale jak debugować, gdy wywołanie znajduje się w granicach procesu, a nie tylko odwołanie na lokalnym stosie? 

W tym miejscu dochodzi do rozproszonego śledzenia.  

Śledzenie rozproszone jest odpowiednikiem stosów wywołań dla nowoczesnych architektur chmury i mikrousług, z dodatkiem uproszczonego profilera wydajności. W usłudze Azure Monitor udostępniamy dwa środowiska do korzystania z rozproszonych danych śledzenia. Pierwszym z nich jest nasz widok [diagnostyki transakcji,](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) który jest jak stos wywołań z wymiarem czasu dodanym. Widok diagnostyki transakcji zapewnia wgląd w jedną transakcję/żądanie i jest przydatny do znajdowania głównej przyczyny problemów z niezawodnością i wąskich gardeł wydajności na podstawie każdego żądania.

Usługa Azure Monitor oferuje również widok [mapy aplikacji,](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) który agreguje wiele transakcji, aby pokazać widok topologiczny, w jaki sposób systemy współdziałają i jakie są średnie wskaźniki wydajności i błędów. 

## <a name="how-to-enable-distributed-tracing"></a>Jak włączyć śledzenie rozproszone

Włączanie śledzenia rozproszonego w usługach w aplikacji jest tak proste, jak dodanie odpowiedniego agenta, SDK lub biblioteki do każdej usługi, na podstawie języka, w jakim usługa została zaimplementowana.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Włączanie za pośrednictwem usługi Application Insights za pomocą automatycznego instrumentacji lub zestawów SDK

Agenci usługi Application Insights i/lub SDK dla platformy .NET, .NET Core, Java, Node.js i JavaScript obsługują śledzenie rozproszone natywnie. Instrukcje dotyczące instalowania i konfigurowania każdego zestawu SDK usługi Application Insights są dostępne poniżej:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

Po zainstalowaniu i skonfigurowaniu odpowiedniego sdk usługi Application Insights, informacje o śledzeniu są automatycznie zbierane dla popularnych struktur, bibliotek i technologii przez auto-modułów zbierających zależności SDK. Pełna lista obsługiwanych technologii jest dostępna w [dokumentacji automatycznego zbierania zależności.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)

 Ponadto dowolną technologię można śledzić ręcznie za pomocą wywołania [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) na [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Włącz za pośrednictwem OpenCensus

Oprócz zestawów SDK usługi Application Insights usługa Application Insights obsługuje również śledzenie rozproszone za pośrednictwem [usługi OpenCensus.](https://opencensus.io/) OpenCensus jest open source, niezależny od dostawcy, pojedynczej dystrybucji bibliotek w celu zapewnienia kolekcji metryk i śledzenia rozproszonego dla usług. Umożliwia również społeczności open source, aby umożliwić śledzenie rozproszone z popularnych technologii, takich jak Redis, Memcached lub MongoDB. [Microsoft współpracuje nad OpenCensus z kilkoma innymi partnerami monitoruj i w chmurze.](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)

[Python](opencensus-python.md) 

Witryna OpenCensus przechowuje dokumentację referencyjną API dla [Pythona](https://opencensus.io/api/python/trace/usage.html) i [Go,](https://godoc.org/go.opencensus.io)a także różne przewodniki dotyczące korzystania z OpenCensus. 

## <a name="next-steps"></a>Następne kroki

* [Przewodnik użycia języka OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Kompleksowe monitorowanie wydajności](./../../azure-monitor/learn/tutorial-performance.md)
