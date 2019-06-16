---
title: Omówienie diagnostyki i monitorowania sieci szkieletowej usługi platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat monitorowania i diagnostyki dla klastrów, aplikacji i usług Azure Service Fabric.
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
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: a6c32058c68adbfd11a4cede6332b42076bea015
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60952087"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorowanie i Diagnostyka Azure Service Fabric

Ten artykuł zawiera omówienie monitorowania i diagnostyki dla usługi Azure Service Fabric. Monitorowanie i Diagnostyka są kluczowe do programowania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Na przykład można śledzić, jak Twoje aplikacje są używane, akcje wykonywane przez platformy usługi Service Fabric, wykorzystanie zasobów przy użyciu liczników wydajności i ogólnej kondycji klastra. Aby zdiagnozować i rozwiązać problemy i zapobiec ich w przyszłości, można użyć tych informacji. Kilka następnych sekcji będzie krótko opisano każdego obszaru usługi monitorowania sieci szkieletowej pod uwagę dla obciążeń produkcyjnych. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitorowanie aplikacji
Monitorowanie aplikacji śledzi sposób używania funkcji i składników aplikacji. Chcesz monitorować Twoje aplikacje, aby upewnić się, że problemy z tego wpływu, jaki użytkownicy są przechwytywane. Odpowiedzialność za monitorowanie aplikacji znajduje się na użytkowników, tworzenie aplikacji i usług, ponieważ jest on unikatowy dla logiki biznesowej aplikacji. Monitorowanie aplikacji może być przydatne w następujących scenariuszach:
* Ilość ruchu dotyczy mojej aplikacji? — Nie musisz skalować swoje usługi w celu spełnienia wymagań użytkownika lub adres potencjalnych wąskich gardeł w aplikacji?
* Czy Moje wywołania usług udane, jak i śledzone?
* Jakie działania są podejmowane przez użytkowników w mojej aplikacji? — Zbierania danych telemetrycznych mogą ukierunkować rozwój funkcji przyszłych oraz lepszą diagnostykę dla błędów aplikacji
* Moja aplikacja zgłasza nieobsługiwane wyjątki? 
* Co się dzieje w ramach usług działających w mojej kontenery?

Największą zaletą monitorowania aplikacji to, że deweloperzy mogą używać dowolnych narzędzi i framework chciałby, ponieważ znajduje się w kontekście aplikacji! Dowiedz się więcej na temat platformy Azure umożliwiająca monitorowanie aplikacji za pomocą usługi Azure Monitor — usługa Application Insights w [analiza zdarzeń za pomocą usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Mamy także zapoznać się z samouczkiem jak [Ustaw tę wartość dla aplikacji .NET](service-fabric-tutorial-monitoring-aspnet.md). W tym samouczku pokazano, jak zainstalować odpowiednie narzędzia przykład do napisania niestandardowej telemetrii w aplikacji i wyświetlanie diagnostyki aplikacji i danych telemetrycznych w witrynie Azure portal. 


## <a name="platform-cluster-monitoring"></a>Monitorowanie platformy (klastrowy)
Użytkownik znajduje się w kontroli, jakie telemetrii pochodzi z aplikacji, ponieważ użytkownik pisze kod automatycznie, ale co o diagnostyce platformy usługi Service Fabric? Jednym z celów usługi Service Fabric jest zapewnienie aplikacji odpornych na awarie sprzętowe. W tym celu odbywa się za pośrednictwem usługi systemowe platformy możliwość wykrywania problemów z infrastrukturą i szybko obciążeń w tryb failover do innych węzłów w klastrze. Ale co zrobić, jeśli w tym konkretnym przypadku samych usług systemu występują problemy? Lub jeśli podczas próby wdrażania lub przenoszenia obciążeń, naruszenia reguły umieszczania usług? Usługa Service Fabric udostępnia diagnostykę dla tych i innych do upewnij się, że wyświetlana jest także informacja o działaniu odbywa się w klastrze. Niektóre przykładowe scenariusze dotyczące monitorowania klastra obejmują:

Usługa Service Fabric udostępnia rozbudowany zestaw zdarzeń poza pole. Te [zdarzenia usługi Service Fabric](service-fabric-diagnostics-events.md) jest możliwy za pośrednictwem bazy danych EventStore lub operational channel (kanał zdarzeń udostępnianych przez platformę). 

* Zdarzenia usługi Service Fabric kanały — Windows na zdarzenia usługi Service Fabric są dostępne od pojedynczego dostawcy funkcji ETW z zestawem odpowiednie `logLevelKeywordFilters` używane do pobierania kanał operacyjnej, danych i komunikatów — jest to sposób, w którym firma Microsoft oddzielenia wychodzące Usługa sieci szkieletowej zdarzeń ma zostać wykonane filtrowanie na zgodnie z potrzebami. W systemie Linux zdarzenia usługi Service Fabric pochodzić LTTng i są umieszczane w jednej tabeli magazynu, od których one mogą być filtrowane zgodnie z potrzebami. Te kanały zawierają wyselekcjonowanych, ze strukturą zdarzenia, które umożliwia lepsze zrozumienie stanu klastra. Diagnostyka są domyślnie włączone w czasie tworzenia klastra, które tworzą tabeli usługi Azure Storage, do którego wysyłane są zdarzenia z te kanały służących do wykonywania zapytań w przyszłości. 

* Bazy danych EventStore — bazy danych EventStore jest funkcji oferowanych przez platformę, która dostarcza zdarzenia platformy usługi Service Fabric jest dostępna w narzędziu Service Fabric Explorer i za pośrednictwem interfejsu API REST. Można wyświetlić widoku migawki, co się dzieje w klastrze dla każdej jednostki np. węzeł, usługi, aplikacji i zapytań w zależności od czasu wystąpienia zdarzenia. Możesz również Dowiedz się więcej o bazie danych EventStore na [omówienie bazy danych EventStore](service-fabric-diagnostics-eventstore.md).    

![Bazy danych EventStore](media/service-fabric-diagnostics-overview/eventstore.png)

Diagnostyka podane są w formie kompleksowy zestaw zdarzeń, gotowych. Te [zdarzenia usługi Service Fabric](service-fabric-diagnostics-events.md) ilustrują akcje wykonywane przez platformę, na różnych jednostkach, takich jak węzły, aplikacji, usług, partycji itp. W ostatnim przypadku powyżej były węzeł, aby przejść w dół, platformy czy emitować `NodeDown` zdarzeń i można wysyłać powiadomienia bezpośrednio przez narzędzie monitorowania wybranego. Inne typowe przykłady `ApplicationUpgradeRollbackStarted` lub `PartitionReconfigured` podczas przejścia w tryb failover. **Te same zdarzenia są dostępne w klastrach systemów Windows i Linux.**

Zdarzenia są wysyłane za pośrednictwem standardowych kanałów w systemach Windows i Linux oraz mogą zostać odczytane przez dowolnego narzędzia do monitorowania, które obsługuje. Rozwiązania usługi Azure Monitor to dzienniki usługi Azure Monitor. Swobodnie dowiedzieć się więcej o naszych [usługi Azure Monitor rejestruje integracji](service-fabric-diagnostics-event-analysis-oms.md) zawierający niestandardowy pulpit nawigacyjny operacyjne dla klastra i pewnych przykładowych zapytań, z których możesz utworzyć alerty. Więcej pojęcia dotyczące monitorowania klastra są dostępne pod adresem [poziomu generowania zdarzeń i dzienników platformy](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Monitorowanie kondycji
Platforma Service Fabric obejmuje modelu kondycji udostępnia rozszerzalną raportowania kondycji stanu jednostki w klastrze. Każdego węzła, aplikacji, usług, partycji, repliki lub wystąpienia, ma stan kondycji ciągle nadaje się do aktualizacji. Stan kondycji może być "OK", "Ostrzeżenie" lub "Error". Zdarzenia usługi Service Fabric można traktować jako zleceń, wykonywane przez klaster do różnych jednostek i kondycji jako przymiotnikiem dla każdej jednostki. Każdorazowo przejścia kondycję określonego obiektu, zdarzenie również będzie emitowane. W ten sposób można ustawić zapytań i generowanie alertów dla zdarzeń kondycji w narzędziu monitorowania wybranego, tak jak dowolne inne zdarzenie. 

Ponadto nawet umożliwialiśmy użytkownikom zastąpienia kondycji dla obiektów. Gdy aplikacja jest przechodzi uaktualnienie i testy weryfikacyjne kończy się niepowodzeniem, możesz zapisywać kondycji sieci szkieletowej usług przy użyciu interfejsu API kondycji oznacza aplikacji nie jest już jest w dobrej kondycji i usługi Service Fabric będzie automatycznie wycofać uaktualnienie! Aby uzyskać więcej informacji o modelu kondycji, zapoznaj się [wprowadzenie do monitorowania kondycji usługi Service Fabric](service-fabric-health-introduction.md)

![Pulpit nawigacyjny kondycji SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdogs
Ogólnie rzecz biorąc strażnika jest oddzielną usługą, którą można obejrzeć kondycji i obciążenia usługi, punkty końcowe polecenie ping i raportów kondycji dla wszystkich elementów w klastrze. Może to pomóc uniknąć błędów, które nie są wykrywane na podstawie widoku jednej usługi. Watchdogs jest również dobrym miejscem do śledzenia hostowanie kodu, który wykonuje czynności zaradczych bez interakcji z użytkownikiem (na przykład: czyszczenie plików dziennika w magazynie w określonych odstępach czasu). Można znaleźć przykład implementacji usługi strażnika [tutaj](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitorowanie infrastruktury (wydajność)
Teraz, gdy Omówiliśmy już diagnostyki w aplikacji oraz platformę, jak wiemy, że działa zgodnie z oczekiwaniami? Monitorowanie infrastruktury podstawowej jest kluczową częścią informacje o stanie klastra i wykorzystanie zasobów. Mierzenie wydajności systemu zależy od wielu czynników, które może być wartością subiektywną, w zależności od obciążenia. Czynniki te są zwykle mierzy się za pomocą liczników wydajności. Te liczniki wydajności mogą pochodzić z różnych źródeł, w tym systemu operacyjnego, programu .NET framework lub samą platformę usługi Service Fabric. Są sytuacje, w których były przydatne

* Am I efektywnie wykorzystując sprzęt? Czy chcesz użyć sprzętu w 90% procesora CPU lub 10% zasobów Procesora. Jeśli źródłem jest przydatna, kiedy skalowania klastra lub optymalizowanie procesów aplikacji.
* Czy mogę przewidzieć, problem z infrastrukturą aktywnie? -wiele problemów są poprzedzone nagłe zmiany (spadnie) wydajności, dzięki czemu można użyć liczników wydajności, takie jak wykorzystanie we/wy i procesora CPU do przewidywania i diagnozować problemy z wyprzedzeniem sieci.

Listę liczników wydajności, które powinny być zbierane na poziomie infrastruktury znajduje się w temacie [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md). 

Usługa Service Fabric udostępnia również zestaw liczników wydajności dla modeli programowania usługi Reliable Services i uczestników. Jeśli używasz jednej z tych modeli te liczniki wydajności mogą informacje, aby upewnić się, że Twoje aktorów są obrotowych i zmniejszana poprawnie lub czy żądań usługi reliable service są aktualnie obsługiwane wystarczająco szybko. Aby uzyskać więcej informacji, zobacz [niezawodne zdalna komunikacja usług monitorowania](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) i [monitorowanie wydajności struktury Reliable actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Rozwiązania usługi Azure Monitor do zbierania tych to dzienniki usługi Azure Monitor, podobnie jak poziom monitorowania platformy. Należy używać [agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) do zbierania liczników wydajności odpowiednie i wyświetlać je w dziennikach w usłudze Azure Monitor.

## <a name="recommended-setup"></a>Zalecane ustawienia
Teraz, posunęliśmy się za pośrednictwem każdego obszaru monitorowanie i przykładowe scenariusze, w tym miejscu znajduje się podsumowanie platformy Azure, narzędzia do monitorowania i konfigurowania wymaganych do monitorowania wszystkich obszarów powyżej. 

* Monitorowanie aplikacji za pomocą [usługi Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitorowanie klastrów za pomocą [agenta funkcji Diagnostyka](service-fabric-diagnostics-event-aggregation-wad.md) i [dzienniki usługi Azure Monitor](service-fabric-diagnostics-oms-setup.md)
* Monitorowanie infrastruktury za pomocą [dzienniki usługi Azure Monitor](service-fabric-diagnostics-oms-agent.md)

Można również użyć i modyfikować przykładowy szablon ARM znajduje się [tutaj](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) do automatycznego wdrożenia wszystkich niezbędnych zasobów i agentów. 

## <a name="other-logging-solutions"></a>Inne rozwiązania rejestrowania

Mimo że te dwa rozwiązania zalecane jest, [dzienniki usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) i [usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) utworzone w integracji z usługą Service Fabric, wiele zdarzeń są zapisywane przy użyciu dostawcy ETW i są Rozszerzalne z innymi rozwiązaniami rejestrowania. Należy również zwrócić uwagę na [Elastic Stack](https://www.elastic.co/products) (zwłaszcza, jeśli rozważasz uruchomiony klaster w środowisku, w trybie offline), [Dynatrace](https://www.dynatrace.com/), lub jakiejkolwiek innej platformie swoje preferencje. Mamy listę zintegrowanych partnerów dostępne [tutaj](service-fabric-diagnostics-partners.md).

Kluczowe punkty, dla dowolnej platformy, które możesz wybrać powinien zawierać, jak dobrze się przy użyciu interfejsu użytkownika, funkcji podczas badania, niestandardowe wizualizacje i pulpity nawigacyjne dostępne, a dodatkowe narzędzia zapewniają rozszerzanie środowiska monitorowania. 

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać wprowadzenie do instrumentacji aplikacji, zobacz [poziomu generowania zdarzeń i dzienników aplikacji](service-fabric-diagnostics-event-generation-app.md).
* Zapoznaj się z artykułem instrukcje konfigurowania usługi Application Insights dla aplikacji za pomocą [monitorowanie i diagnozowanie aplikacji platformy ASP.NET Core w usłudze Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Dowiedz się więcej o funkcji monitorowania i zdarzeń, Usługa Service Fabric udostępnia w miejscu platformy [poziomu generowania zdarzeń i dzienników platformy](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurowanie integracji dzienników usługi Azure Monitor za pomocą usługi Service Fabric w [Konfigurowanie dzienników usługi Azure Monitor dla klastra](service-fabric-diagnostics-oms-setup.md)
* Informacje o sposobie konfigurowania dzienników usługi Azure Monitor do monitorowania kontenerów — [monitorowania i diagnostyki dla Windows kontenery w usłudze Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Zobacz przykład diagnostyki problemy i rozwiązania z usługą Service Fabric w [diagnozowanie typowe scenariusze](service-fabric-diagnostics-common-scenarios.md)
* Zapoznaj się z innych produktów diagnostyki, które integrują się z usługą Service Fabric w [diagnostycznych partnerów usługi Service Fabric](service-fabric-diagnostics-partners.md)
* Dowiedz się więcej o ogólnych zaleceń dotyczących monitorowania zasobów platformy Azure — [najlepsze rozwiązania — monitorowanie i Diagnostyka](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 