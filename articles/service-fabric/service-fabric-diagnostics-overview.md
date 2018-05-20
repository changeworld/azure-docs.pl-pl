---
title: Omówienie diagnostyki i monitorowanie sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat monitorowania i diagnostyki dla klastrów, aplikacje i usługi sieć szkieletowa usług Azure.
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
ms.openlocfilehash: 4fcf8c514cb785dbb0a149e5b3073fc72937b68a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorowania i diagnostyki dla sieci szkieletowej usług Azure

Ten artykuł zawiera omówienie monitorowania i diagnostyki dla sieci szkieletowej usług Azure. Monitorowania i diagnostyki są krytyczne dla programowania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Monitorowanie umożliwia śledzenie korzystania z aplikacji, z wykorzystania zasobów i ogólną kondycję klastra. Dzięki tym informacjom można zdiagnozować i rozwiązać wszystkie problemy i zapobiec problemy w przyszłości. 

## <a name="application-monitoring"></a>Monitorowanie aplikacji
Monitorowanie aplikacji śledzi, jak są używane funkcje i składniki aplikacji. Chcesz monitorować Twoje aplikacje, aby upewnić się, że problemy tego wpływu, na który użytkownicy są przechwytywane. Monitorowanie aplikacji może być przydatne w następujących scenariuszach:
* Określanie użytkownika i aplikacji obciążenia ruchu — potrzebuje skalowania usługi, aby spełniały potrzeby użytkowników lub rozwiązać potencjalne wąskie gardło w aplikacji?
* identyfikowanie problemów za pomocą komunikacji usługi i komunikacji zdalnej w klastrze
* Ustaleniem, co robią użytkownicy z aplikacją — zbieranie danych telemetrii w aplikacji może pomóc w przewodniku przyszłych funkcji programowanie i lepszą diagnostykę błędów aplikacji
* Monitorowanie, co dzieje się wewnątrz uruchomionych kontenerów

Sieć szkieletowa usług obsługuje wiele opcji Instrumentacja kod aplikacji przy użyciu odpowiednich śladów i telemetrii. Zaleca się, że używasz Application Insights (AI). AI przez integrację z sieci szkieletowej usług zawiera narzędzia związane z portalu Azure i Visual Studio, a także określonych metryk usługi Service Fabric, zapewnienie obsługi kompleksowego out box rejestrowania. Chociaż wiele dzienniki są automatycznie tworzone i zbierane automatycznie z AI, firma Microsoft zaleca dodanie więcej niestandardowego rejestrowania dla poszczególnych aplikacji, aby utworzyć rozbudowanej diagnostyki. Zobacz więcej na temat rozpoczynania pracy z usługą Application Insights z sieci szkieletowej usług w [analiza zdarzeń z usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Monitorowanie platformy (klaster)
Monitorowanie klastra usługi sieć szkieletowa jest szczególnie ważne w celu zapewnienia, że platforma i wszystkich obciążeń działają zgodnie z oczekiwaniami. Jednym z celów usługi sieć szkieletowa jest do zachowania aplikacji odporność na awarie sprzętowe. W tym celu odbywa się za pośrednictwem usługi systemowe platformy możliwość wykrywania problemów z infrastrukturą i szybko obciążeń trybu failover do innych węzłów w klastrze. Ale co zrobić, jeśli w tym przypadku uwierzytelnienia usługi system ma problemy? Lub jeśli podczas próby przeniesienia obciążenia, naruszenia reguły umieszczania usług? Monitorowanie klastra służy do uzyskiwania informacji o działaniu odbywa się w klastrze, co ułatwia diagnozowanie problemów i ich efektywne rozwiązanie. Niektóre elementy klucza, który chcesz szukać są:
* Sieć szkieletowa usług zachowuje się oczekiwaniami, wprowadzenie do aplikacji i obejść klastra równoważenia? 
* Akcje użytkownika są podejmowane w klastrze potwierdzone i wykonywane na zgodnie z oczekiwaniami? Jest to szczególnie istotne w przypadku, gdy skalowanie klastra.
* Sieć szkieletowa usług obsługuje dane i usługa usługa komunikacji wewnątrz klastra poprawnie?

Sieć szkieletowa usług zapewnia rozbudowany zestaw zdarzeń z pola. Te [zdarzeń usługi sieć szkieletowa](service-fabric-diagnostics-events.md) jest możliwy za pośrednictwem interfejsów API EventStore lub kanału operacyjne (udostępniany przez platformę kanał zdarzeń). 
* EventStore - EventStore (dostępnej w systemie Windows w wersji 6.2 i nowszych, Linux w toku lub nowszym Data ostatniej aktualizacji w tym artykule), udostępnia te zdarzenia za pomocą zestawu interfejsów API (dostępne za pośrednictwem punkty końcowe REST lub za pomocą biblioteki klienta). Dowiedz się więcej o EventStore w [omówienie EventStore](service-fabric-diagnostics-eventstore.md).
* Sieć szkieletowa usług zdarzeń kanałów — w systemie Windows sieci szkieletowej usług zdarzenia są dostępne z jednego dostawcy ETW z zestawem odpowiednich `logLevelKeywordFilters` umożliwia pobranie między & operacyjnego i danych wiadomości kanałem — jest sposób odseparować limit wychodzące Usługi sieci szkieletowej zdarzeń ma zostać przefiltrowana w razie potrzeby. W systemie Linux zdarzenia platformy Service Fabric przepuszczane LTTng i są umieszczane w jednej tabeli magazynu, z których one mogą być filtrowane zgodnie z potrzebami. Te kanały zawierają wyselekcjonowanych, strukturalnych zdarzenia, które umożliwiają lepsze zrozumienie stanu klastra. Diagnostyka są domyślnie włączone w czasie tworzenia klastra, co spowoduje utworzenie tabeli magazynu Azure, gdy zdarzenia te kanały są wysyłane można zbadać w przyszłości. 

Firma Microsoft zaleca się używanie EventStore do szybkiego analizy, a także aby uzyskać pomysłem migawki działania klastra, a jeśli czynności są wykonywane jako. Do zbierania dzienników i zdarzenia generowane przez klaster, ogólnie zalecamy użycie [rozszerzenia diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md). To jest zintegrowany z usługi sieć szkieletowa Analytics, analizy dzienników OMS sieci szkieletowej usług określonego rozwiązania, które zawiera niestandardowy pulpit nawigacyjny do monitorowania klastrów sieci szkieletowej usług i umożliwia zapytań dotyczących zdarzeń, klastra i skonfigurować alerty. Dowiedz się więcej o tym w [analiza zdarzeń z OMS](service-fabric-diagnostics-event-analysis-oms.md). 

 Więcej o monitorowaniu klastra na [platformy poziomu generowania zdarzeń i dziennika](service-fabric-diagnostics-event-generation-infra.md).


 ![Rozwiązanie rz OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

## <a name="performance-monitoring"></a>Monitorowanie wydajności
Monitorowanie infrastruktury podstawowej jest kluczowym elementem opis stanu klastra i z wykorzystania zasobów. Pomiaru wydajności systemu zależy od wielu czynników, z których każdy jest zwykle mierzy się za pomocą kluczowych wskaźników wydajności (KPI). Kluczowe wskaźniki wydajności odpowiednich sieci szkieletowej usług mogą być mapowane na metryki, które mogą być zbierane z węzłów w klastrze, jako liczniki wydajności.
Tych wskaźników KPI może ułatwić:
* Opis wykorzystania zasobów i obciążenia — na potrzeby skalowanie klastra lub optymalizacji procesów usług.
* Przewidywanie problemów z infrastrukturą — wiele problemów są poprzedzone nagłych zmian (porzucania) wydajności, dzięki czemu można używać kluczowych wskaźników wydajności takich jak użycie sieci We/Wy i procesora CPU prognozowania i diagnozowanie problemów infrastrukturalne.

Lista liczników wydajności, które powinny być zebrane na poziomie infrastruktury można znaleźć w folderze [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md). 

Sieć szkieletowa usług zawiera zestaw liczników wydajności dla modeli programowania Reliable Services i uczestników. Jeśli korzystasz z jednej z tych modeli, te liczniki wydajności zapewniają wskaźników KPI, które pozwalają zagwarantować, że Twoje podmioty są wirowania górę i w dół poprawnie lub czy Twoich żądań obsługi niezawodnych są obsługiwane tyle szybko. Aby uzyskać więcej informacji, zobacz [monitorowania niezawodnej Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) i [monitorowanie wydajności dla elementów Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Oprócz tego usługi Application Insights ma również zestaw metryki wydajności, który odbierze, jeśli skonfigurowano aplikację.

Użyj [agent pakietu OMS](service-fabric-diagnostics-oms-agent.md) do zbierania liczników wydajności odpowiednie i wyświetlić te wskaźniki KPI w OMS analizy dzienników.

![Wykres Omówienie diagnostyki](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Monitorowanie kondycji
Platformy Service Fabric zawiera model kondycji, co zapewnia rozszerzonego raportowania kondycji stanu jednostek w klastrze. Każdego węzła, aplikacji, usług, partycji, repliki lub wystąpienia, ma stan kondycji stale nadaje się do aktualizacji. Stan kondycji może być "OK", "Ostrzeżenie" lub "Error". Zmiany stanu kondycji za pomocą raportów kondycji, które są emitowane dla każdej jednostki oparte na problemy w klastrze. Stan kondycji jednostki można sprawdzić w dowolnym momencie w Service Fabric Explorer (SFX), jak pokazano poniżej, lub można zbadać za pomocą interfejsu API kondycji platformy. Można również dostosować raportów o kondycji i modyfikować stanu kondycji jednostki przez dodanie własnych raportów kondycji lub przy użyciu interfejsu API kondycji. Więcej informacji na temat modelu kondycji można znaleźć w folderze [wprowadzenie do monitorowania kondycji sieci szkieletowej usług](service-fabric-health-introduction.md).

![Pulpit nawigacyjny kondycji SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Oprócz wyświetlać najnowsze raportów o kondycji w SFX, każdy raport jest również dostępny jako zdarzenie. Zdarzenia kondycji mogą być zbierane przez kanał operacyjne (zobacz [agregacji zdarzenia z diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) i przechowywane w analizy dzienników dla alertów i wykonywaniu zapytań w przyszłości. Dzięki temu można wykrywać problemy, które mogą mieć wpływ na jego dostępność aplikacji, tak więc zaleca się Konfigurowanie alertów dla scenariuszy awarii odpowiednie (niestandardowe alerty za pomocą analizy dzienników).

## <a name="other-logging-solutions"></a>Inne rozwiązania rejestrowania

Mimo że te dwa rozwiązania firma Microsoft zaleca, [OMS](service-fabric-diagnostics-event-analysis-oms.md) i [usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) utworzone w integracji z usługi Service Fabric, wiele zdarzeń są zapisywane za pośrednictwem dostawcy etw i są rozszerzalne z innymi Rejestrowanie rozwiązania. Należy również zwrócić uwagę na [stosu elastycznych](https://www.elastic.co/products) (szczególnie, gdy rozważane jest uruchomiony klaster w środowisku w trybie offline), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/), lub innych Platforma swoich preferencji. 

Klucz wskazuje na dowolnej platformie wybierz powinna zawierać jak doświadczenia są z interfejsem użytkownika i badania opcje możliwość wizualizacji danych i tworzenie czytelny pulpitów nawigacyjnych i dodatkowe narzędzia zapewniają do rozszerzania monitorowania, automatyczne takich jak alerty.

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do instrumentacji aplikacji, dla [poziomu generowania zdarzeń i dziennika aplikacji](service-fabric-diagnostics-event-generation-app.md).
* Dowiedz się więcej na temat monitorowania platformy i zdarzenia platformy Service Fabric zawiera w miejscu [platformy poziomu generowania zdarzeń i dziennika](service-fabric-diagnostics-event-generation-infra.md).
* Przejdź przez proces konfigurowania AI aplikacji z [monitorowania i diagnostyki dla aplikacji platformy ASP.NET Core sieci szkieletowej usług](service-fabric-tutorial-monitoring-aspnet.md).
* Dowiedz się, jak skonfigurować OMS analizy dzienników dla monitorowania kontenery- [monitorowania i diagnostyki dla systemu Windows kontenerów w sieci szkieletowej usług Azure](service-fabric-tutorial-monitoring-wincontainers.md).
* Więcej informacji na temat ogólnych monitorowania zalecenia dotyczące zasobów Azure - [najlepsze rozwiązania w zakresie - monitorowania i diagnostyki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
