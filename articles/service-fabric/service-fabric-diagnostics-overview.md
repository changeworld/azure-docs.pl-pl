---
title: Omówienie diagnostyki i monitorowania sieci szkieletowej usługi platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat monitorowania i diagnostyki dla klastrów, aplikacji i usług Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: srrengar
ms.openlocfilehash: a607f560b5e74071f5ee15d03e615138f25a3aef
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406810"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorowanie i Diagnostyka Azure Service Fabric

Ten artykuł zawiera omówienie monitorowania i diagnostyki dla usługi Azure Service Fabric. Monitorowanie i Diagnostyka są kluczowe do programowania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Monitorowanie umożliwia śledzenie sposobu korzystania z aplikacji, wykorzystanie zasobów oraz ogólnej kondycji klastra. Dzięki tym informacjom można zdiagnozować i rozwiązać wszelkie problemy i zapobiec problemy w przyszłości. 

## <a name="application-monitoring"></a>Monitorowanie aplikacji
Monitorowanie aplikacji śledzi sposób używania funkcji i składników aplikacji. Chcesz monitorować Twoje aplikacje, aby upewnić się, że problemy z tego wpływu, jaki użytkownicy są przechwytywane. Monitorowanie aplikacji może być przydatne w następujących scenariuszach:
* Określanie ładowanie aplikacji i użytkownika traffic — należy skalować usługi w taki sposób, aby spełniały potrzeby użytkowników lub rozwiązać potencjalne wąskie gardło w aplikacji?
* Identyfikowanie problemów za pomocą komunikacji usług i komunikacji zdalnej w klastrze
* Ustalenie, co użytkownicy robią z aplikacją — zbierania danych telemetrycznych w swoich aplikacjach mogą pomóc w rozwoju nowymi funkcjami przewodnik i lepszą diagnostykę dla błędów aplikacji
* Monitorowanie, co dzieje się wewnątrz uruchomionych kontenerów

Usługa Service Fabric obsługuje wiele opcji do Instrumentacji kodu aplikacji przy użyciu odpowiednich danych śledzenia i danych telemetrycznych. Zaleca się, że używasz Application Insights (SI). Sztucznej Inteligencji firmy integracji z usługą Service Fabric zawiera narzędzia środowiska dla programu Visual Studio i platforma Azure portal, a także określonych metryk usługi Service Fabric, zapewniają środowisko kompleksowe rejestrowania poza pole. Chociaż wiele dzienników są automatycznie tworzone i zebrane dla Ciebie ze sztuczną Inteligencją, zaleca się dodanie dalsze niestandardowego rejestrowania aplikacji, aby utworzyć bardziej zaawansowane środowisko diagnostyki. Zobacz więcej na temat rozpoczynania pracy z usługą Application Insights z usługą Service Fabric w [analiza zdarzeń za pomocą usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Monitorowanie platformy (klastrowy)
Monitorowanie klastra usługi Service Fabric jest krytyczne w celu zapewnienia, że wszystkie obciążenia i platformy działają zgodnie z oczekiwaniami. Jednym z celów usługi Service Fabric jest zapewnienie aplikacji odpornych na awarie sprzętowe. W tym celu odbywa się za pośrednictwem usługi systemowe platformy możliwość wykrywania problemów z infrastrukturą i szybko obciążeń w tryb failover do innych węzłów w klastrze. Ale co zrobić, jeśli w tym konkretnym przypadku samych usług systemu występują problemy? Lub jeśli podczas próby Przenieś obciążenia, naruszenia reguły umieszczania usług? Monitorowanie klastra umożliwia aktualne informacje na temat działań, które odbywa się w klastrze, która pomaga w diagnozowaniu problemów i skutecznie ich rozwiązywania. Ważne informacje, które wydaje się patrzeć są następujące:
* Usługa Service Fabric zachowuje się sposób, w których oczekujesz, wprowadzenie do aplikacji i obejść klastra równoważenia? 
* Akcje użytkownika przechodzą w klastrze potwierdzone i wykonywane na zgodnie z oczekiwaniami? Jest to szczególnie istotne podczas skalowania klastra.
* Usługa Service Fabric obsługuje danymi i usługami komunikacji wewnątrz klastra prawidłowo?

Usługa Service Fabric udostępnia rozbudowany zestaw zdarzeń poza pole. Te [zdarzenia usługi Service Fabric](service-fabric-diagnostics-events.md) jest możliwy za pośrednictwem interfejsów API bazy danych EventStore lub kanał operacyjny (zdarzenie kanału udostępnianych przez platformę). 
* Bazy danych EventStore — bazy danych EventStore, (dostępne w Windows w wersji 6.2 i nowszych wersjach systemu Linux w toku, począwszy od Data ostatniej aktualizacji w tym artykule), udostępnia te zdarzenia za pomocą zestawu interfejsów API (dostępnym za pośrednictwem punkty końcowe REST lub za pomocą biblioteki klienta). Dowiedz się więcej o bazie danych EventStore na [omówienie bazy danych EventStore](service-fabric-diagnostics-eventstore.md).
* Zdarzenia usługi Service Fabric kanały — Windows na zdarzenia usługi Service Fabric są dostępne od pojedynczego dostawcy funkcji ETW z zestawem odpowiednie `logLevelKeywordFilters` używane do pobierania kanał operacyjnej, danych i komunikatów — jest to sposób, w którym firma Microsoft oddzielenia wychodzące Usługa sieci szkieletowej zdarzeń ma zostać wykonane filtrowanie na zgodnie z potrzebami. W systemie Linux zdarzenia usługi Service Fabric pochodzić LTTng i są umieszczane w jednej tabeli magazynu, od których one mogą być filtrowane zgodnie z potrzebami. Te kanały zawierają wyselekcjonowanych, ze strukturą zdarzenia, które umożliwia lepsze zrozumienie stanu klastra. Diagnostyka są domyślnie włączone w czasie tworzenia klastra, które tworzą tabeli usługi Azure Storage, do którego wysyłane są zdarzenia z te kanały służących do wykonywania zapytań w przyszłości. 

Firma Microsoft zaleca się korzystanie z bazy danych EventStore do szybkiej analizy i mieć migawki pogląd na sposób działania klastra, a jeśli rzeczy dzieją się jako. Do zbierania dzienników i zdarzeń generowanych przez Twój klaster, ogólnie zaleca się używanie [rozszerzenia diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md). To dobrze integruje się z analiza usługi Service Fabric, usługa Log Analytics usługi Service Fabric określonego rozwiązania, które zawiera niestandardowy pulpit nawigacyjny do monitorowania klastrów usługi Service Fabric i umożliwia zapytania zdarzenia klastra i konfigurowania alertów. Dowiedz się więcej o [analiza zdarzeń za pomocą usługi Log Analytics](service-fabric-diagnostics-event-analysis-oms.md). 

 Możesz dowiedzieć się więcej o monitorowaniu klastrze w lokalizacji [poziomu generowania zdarzeń i dzienników platformy](service-fabric-diagnostics-event-generation-infra.md).

## <a name="performance-monitoring"></a>Monitorowanie wydajności
Monitorowanie infrastruktury podstawowej jest kluczową częścią informacje o stanie klastra i wykorzystanie zasobów. Mierzenie wydajności systemu zależy od wielu czynników, z których każdy jest zwykle mierzy się za pomocą kluczowych wskaźników wydajności (KPI). Usługa Service Fabric odpowiedniego kluczowe wskaźniki wydajności mogą być mapowane na metryki, które mogą być zbierane z węzłów w klastrze, jako liczników wydajności.
Tych wskaźników KPI może ułatwić:
* Zrozumienie wykorzystania zasobów i obciążenie — na potrzeby skalowania klastra lub optymalizowanie procesów usługi.
* Prognozowanie problemów z infrastruktury — wiele problemów są poprzedzone nagłe zmiany (spadnie) wydajności, aby można było używać kluczowych wskaźników wydajności takie jak wykorzystanie sieci We/Wy i procesora CPU, przewidywanie i diagnozować problemy infrastrukturalne.

Listę liczników wydajności, które powinny być zbierane na poziomie infrastruktury znajduje się w temacie [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md). 

Usługa Service Fabric udostępnia zestaw liczników wydajności dla modeli programowania usługi Reliable Services i uczestników. Jeśli używasz jednej z tych modeli tych liczników wydajności może zapewnić wskaźników KPI, które pomagają zapewnić, że Twoje aktorów są obrotowych i zmniejszana poprawnie lub czy żądań usługi reliable service są aktualnie obsługiwane wystarczająco szybko. Aby uzyskać więcej informacji, zobacz [niezawodne zdalna komunikacja usług monitorowania](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) i [monitorowanie wydajności struktury Reliable actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Oprócz tego usługa Application Insights ma również zestaw metryk wydajności, które będą zbierane, jeśli skonfigurowane przy użyciu aplikacji.

Użyj [agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) zebrać liczników wydajności odpowiednie w celu wyświetlenia tych wskaźników KPI w usłudze Azure Log Analytics.

![Wykres — Omówienie diagnostyki](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Monitorowanie kondycji
Platforma Service Fabric obejmuje modelu kondycji udostępnia rozszerzalną raportowania kondycji stanu jednostki w klastrze. Każdego węzła, aplikacji, usług, partycji, repliki lub wystąpienia, ma stan kondycji ciągle nadaje się do aktualizacji. Stan kondycji może być "OK", "Ostrzeżenie" lub "Error". Stan kondycji jest zmieniany przy użyciu raportów kondycji, które są emitowane dla każdej jednostki, w oparciu o problemy w klastrze. Kondycja jednostki można sprawdzić w dowolnym momencie w usługi Service Fabric Explorer (SFX), jak pokazano poniżej, lub może być odpytywany za pomocą interfejsu API kondycji platformy. Można również dostosować raportów o kondycji i modyfikować stanu kondycji jednostki przez dodanie własnych raportów kondycji lub przy użyciu interfejsu API kondycji. Szczegółowe informacje na temat modelu kondycji znajduje się w temacie [wprowadzenie do monitorowania kondycji usługi Service Fabric](service-fabric-health-introduction.md).

![Pulpit nawigacyjny kondycji SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Oprócz możliwości wyświetlania najnowszych raportów kondycji w SFX, każdy raport jest również dostępny jako zdarzenie. Zdarzenia kondycji mogą być zbierane przez kanał operacyjnej (zobacz [agregacji zdarzeń za pomocą diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) i przechowywane w usłudze Log Analytics dla alertów i wykonywanie zapytań w przyszłości. Dzięki temu można wykrywać problemy, które mogą mieć wpływ na dostępność Twojej aplikacji, tak więc zaleca się Konfigurowanie alertów dla scenariuszy awarii odpowiednie (niestandardowe alerty przy użyciu usługi Log Analytics).

## <a name="other-logging-solutions"></a>Inne rozwiązania rejestrowania

Mimo że te dwa rozwiązania zalecane jest, [usługi Azure Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) i [usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) utworzone w integracji z usługą Service Fabric, wiele zdarzeń są zapisywane przy użyciu dostawcy etw i są Rozszerzalne z innymi rozwiązaniami rejestrowania. Należy również zwrócić uwagę na [Elastic Stack](https://www.elastic.co/products) (zwłaszcza, jeśli rozważasz uruchomiony klaster w środowisku, w trybie offline), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/), lub innych Platforma z preferencjami. 

Kluczowe punkty, dla dowolnej platformy, które możesz wybrać powinien zawierać, jak dobrze są przy użyciu interfejsu użytkownika i wykonywanie zapytań opcje możliwości wizualizacji danych i tworzenie czytelny pulpitów nawigacyjnych i dodatkowe narzędzia, które zapewniają ulepszenia monitorowania, takie jak automatyczne alerty.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać wprowadzenie do instrumentacji aplikacji, zobacz [poziomu generowania zdarzeń i dzienników aplikacji](service-fabric-diagnostics-event-generation-app.md).
* Dowiedz się więcej o funkcji monitorowania i zdarzeń, Usługa Service Fabric udostępnia w miejscu platformy [poziomu generowania zdarzeń i dzienników platformy](service-fabric-diagnostics-event-generation-infra.md).
* Zapoznaj się z artykułem kroki, aby skonfigurować sztucznej Inteligencji do aplikacji za pomocą [monitorowanie i diagnozowanie aplikacji platformy ASP.NET Core w usłudze Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Dowiedz się, jak skonfigurować usługi Azure Log Analytics w celu monitorowania kontenerów — [monitorowania i diagnostyki dla Windows kontenery w usłudze Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Dowiedz się więcej o ogólnych zaleceń dotyczących monitorowania zasobów platformy Azure — [najlepsze rozwiązania — monitorowanie i Diagnostyka](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
