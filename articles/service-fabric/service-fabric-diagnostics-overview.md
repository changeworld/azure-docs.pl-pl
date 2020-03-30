---
title: Omówienie monitorowania i diagnostyki sieci szkieletowej usług Azure
description: Dowiedz się więcej o monitorowaniu i diagnostyce klastrów, aplikacji i usług usługi Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: ef77810adfab213845c7824740effc3416d85407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282486"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorowanie i diagnostyka sieci szkieletowej usług Azure

Ten artykuł zawiera omówienie monitorowania i diagnostyki dla sieci szkieletowej usług Azure. Monitorowanie i diagnostyka mają kluczowe znaczenie dla tworzenia, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Na przykład można śledzić, jak aplikacje są używane, akcje podejmowane przez platformę sieci szkieletowej usług, wykorzystanie zasobów z licznikami wydajności i ogólną kondycję klastra. Te informacje można używać do diagnozowania i rozwiązywania problemów oraz zapobiegania ich występowaniu w przyszłości. W następnych kilku sekcjach pokrótce wyjaśnimy każdy obszar monitorowania sieci szkieletowej usług, który należy wziąć pod uwagę w przypadku obciążeń produkcyjnych. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitorowanie aplikacji
Monitorowanie aplikacji śledzi, jak funkcje i składniki aplikacji są używane. Chcesz monitorować aplikacje, aby upewnić się, że problemy, które wpływają na użytkowników są przechwytywać. Odpowiedzialność monitorowania aplikacji spoczywa na użytkownikach rozwijających aplikację i jej usługi, ponieważ jest unikatowa dla logiki biznesowej aplikacji. Monitorowanie aplikacji może być przydatne w następujących scenariuszach:
* Ile ruchu ma moja aplikacja? - Czy musisz skalować swoje usługi, aby sprostać wymaganiom użytkowników lub rozwiązać potencjalne wąskie gardło w aplikacji?
* Czy moja usługa obsługi połączeń jest skuteczna i śledzona?
* Jakie działania są podejmowane przez użytkowników mojej aplikacji? - Zbieranie danych telemetrycznych może prowadzić do przyszłego rozwoju funkcji i lepszej diagnostyki błędów aplikacji
* Czy moja aplikacja zgłasza nieobsługiwanie wyjątków? 
* Co dzieje się w usługach działających wewnątrz moich kontenerów?

Wspaniałą rzeczą w monitorowaniu aplikacji jest to, że deweloperzy mogą korzystać z dowolnych narzędzi i ram, które chcieliby, ponieważ żyje w kontekście aplikacji! Możesz dowiedzieć się więcej o rozwiązaniu platformy Azure do monitorowania aplikacji za pomocą usługi Azure Monitor — usługa Application Insights w [analizie zdarzeń za pomocą usługi Application Insights.](service-fabric-diagnostics-event-analysis-appinsights.md)
Mamy również tutorial z jak [skonfigurować to dla aplikacji .NET](service-fabric-tutorial-monitoring-aspnet.md). W tym samouczku opisano sposób instalowania odpowiednich narzędzi, przykład do pisania niestandardowych danych telemetrycznych w aplikacji i wyświetlania diagnostyki aplikacji i telemetrii w witrynie Azure portal. 


## <a name="platform-cluster-monitoring"></a>Monitorowanie platformy (klastra)
Użytkownik ma kontrolę nad tym, jakie dane telemetryczne pochodzą z ich aplikacji, ponieważ użytkownik zapisuje sam kod, ale co z diagnostyki z platformy sieci szkieletowej usług? Jednym z celów sieci szkieletowej usług jest utrzymanie aplikacji odporne na awarie sprzętu. Ten cel jest osiągany za pomocą możliwości usług systemowych platformy do wykrywania problemów z infrastrukturą i szybko pracy awaryjnej obciążeń do innych węzłów w klastrze. Ale w tym konkretnym przypadku, co zrobić, jeśli same usługi systemowe mają problemy? Lub jeśli podczas próby wdrożenia lub przeniesienia obciążenia, zasady umieszczania usług są naruszane? Sieci szkieletowej usług zapewnia diagnostyki dla tych i więcej, aby upewnić się, że są informowani o aktywności odbywających się w klastrze. Niektóre przykładowe scenariusze monitorowania klastra obejmują:

Usługa Fabric zapewnia kompleksowy zestaw zdarzeń po wyjęciu z pudełka. Te [zdarzenia sieci szkieletowej usług](service-fabric-diagnostics-events.md) są dostępne za pośrednictwem EventStore lub kanału operacyjnego (kanał zdarzeń udostępniane przez platformę). 

* Kanały zdarzeń sieci szkieletowej usług — w systemie Windows zdarzenia sieci `logLevelKeywordFilters` szkieletowej usług są dostępne od jednego dostawcy ETW z zestawem odpowiednich do pobrania między kanałami operacyjne i & wiadomości — w ten sposób oddzielamy wychodzące zdarzenia sieci szkieletowej usług, które mają być filtrowane zgodnie z potrzebami. W systemie Linux zdarzenia sieci szkieletowej usług są za pośrednictwem lttng i są umieszczane w jednej tabeli magazynu, z której można filtrować w razie potrzeby. Te kanały zawierają wyselekcjonowane, ustrukturyzowane zdarzenia, które mogą służyć do lepszego zrozumienia stanu klastra. Diagnostyka jest domyślnie włączona w czasie tworzenia klastra, które tworzą tabelę usługi Azure Storage, w której zdarzenia z tych kanałów są wysyłane do kwerendy w przyszłości. 

* EventStore — EventStore jest funkcją oferowaną przez platformę, która udostępnia zdarzenia platformy sieci szkieletowej usług dostępne w Eksploratorze sieci szkieletowej usług i za pośrednictwem interfejsu API REST. Możesz zobaczyć widok migawki tego, co dzieje się w klastrze dla każdej jednostki, na przykład węzeł, usługa, aplikacja i kwerenda na podstawie czasu zdarzenia. Możesz również przeczytać więcej o EventStore w [Przeglądzie EventStore](service-fabric-diagnostics-eventstore.md).    

![Sklep eventowy](media/service-fabric-diagnostics-overview/eventstore.png)

Dostarczone diagnostyki mają formę kompleksowego zestawu zdarzeń po wyjęciu z pudełka. Te [zdarzenia sieci szkieletowej usług](service-fabric-diagnostics-events.md) ilustrują akcje wykonywane przez platformę na różnych jednostkach, takich jak węzły, aplikacje, usługi, partycje itp. W ostatnim scenariuszu powyżej, jeśli węzeł miałby przejść w `NodeDown` dół, platforma będzie emitować zdarzenie i może zostać natychmiast powiadomiony przez narzędzie monitorowania wyboru. Inne typowe przykłady obejmują `ApplicationUpgradeRollbackStarted` lub `PartitionReconfigured` podczas pracy awaryjnej. **Te same zdarzenia są dostępne zarówno w klastrach systemu Windows, jak i Linux.**

Zdarzenia są wysyłane za pośrednictwem standardowych kanałów w systemie Windows i Linux i mogą być odczytywane przez dowolne narzędzie do monitorowania, które je obsługuje. Rozwiązaniem usługi Azure Monitor są dzienniki usługi Azure Monitor. Możesz dowiedzieć się więcej o naszej [integracji dzienników usługi Azure Monitor,](service-fabric-diagnostics-event-analysis-oms.md) która zawiera niestandardowy pulpit nawigacyjny operacyjny dla klastra i kilka przykładowych zapytań, z których można tworzyć alerty. Więcej koncepcji monitorowania klastra są dostępne na [poziomie platformy zdarzenia i generowania dziennika.](service-fabric-diagnostics-event-generation-infra.md)

### <a name="health-monitoring"></a>Monitorowanie kondycji
Platforma sieci szkieletowej usług zawiera model kondycji, który zapewnia rozszerzalne raportowanie kondycji dla stanu jednostek w klastrze. Każdy węzeł, aplikacja, usługa, partycja, replika lub wystąpienie, ma stale aktualizowany stan kondycji. Stan kondycji może być "OK", "Ostrzeżenie" lub "Błąd". Pomyśl o zdarzeniach sieci szkieletowej usług jako zleceń wykonanych przez klaster do różnych jednostek i kondycji jako przymiotnik dla każdej jednostki. Za każdym razem, gdy kondycja poszczególnych przejść jednostki, zdarzenie będzie również emitowane. W ten sposób można skonfigurować kwerendy i alerty dla zdarzeń kondycji w wybranym narzędziu monitorowania, podobnie jak każde inne zdarzenie. 

Ponadto możemy nawet umożliwić użytkownikom zastąpienie kondycji dla jednostek. Jeśli aplikacja przechodzi uaktualnienie i masz testy sprawdzania poprawności nie, można zapisać do kondycji sieci szkieletowej usługi przy użyciu interfejsu API kondycji, aby wskazać, że aplikacja nie jest już w dobrej kondycji, a sieci szkieletowej usług automatycznie wycofa uaktualnienia! Aby uzyskać więcej informacji na temat modelu kondycji, zapoznaj się z [wprowadzeniem do monitorowania kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)

![Pulpit nawigacyjny kondycji SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Strażnicy
Ogólnie rzecz biorąc watchdog jest oddzielną usługą, która może obserwować kondycję i ładować między usługami, ping punktów końcowych i raportować kondycję dla czegokolwiek w klastrze. Może to pomóc w zapobieganiu błędom, które nie zostaną wykryte na podstawie widoku pojedynczej usługi. Watchdogs są również dobrym miejscem do hostowania kodu, który wykonuje akcje naprawcze bez interakcji z użytkownikiem (na przykład czyszczenie plików dziennika w magazynie w określonych odstępach czasu). Przykładową implementację usługi watchdog można znaleźć [tutaj](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitorowanie infrastruktury (wydajności)
Teraz, gdy omówiliśmy diagnostykę w aplikacji i na platformie, skąd wiemy, że sprzęt działa zgodnie z oczekiwaniami? Monitorowanie podstawowej infrastruktury jest kluczowym elementem zrozumienia stanu klastra i wykorzystania zasobów. Pomiar wydajności systemu zależy od wielu czynników, które mogą być subiektywne w zależności od obciążeń. Czynniki te są zazwyczaj mierzone za pomocą liczników wydajności. Te liczniki wydajności mogą pochodzić z różnych źródeł, w tym systemu operacyjnego, platformy .NET lub samej platformy sieci szkieletowej usług. Niektóre scenariusze, w których byłyby one przydatne, są

* Czy efektywnie korzystam ze sprzętu? Czy chcesz używać sprzętu w 90% CPU lub 10% CPU. Jest to przydatne podczas skalowania klastra lub optymalizacji procesów aplikacji.
* Czy mogę aktywnie przewidywać problemy z infrastrukturą? - wiele problemów jest poprzedzonych nagłymi zmianami (spadki) wydajności, dzięki czemu można użyć liczników wydajności, takich jak wykorzystanie we/wy sieci i procesora CPU, aby aktywnie przewidywać i diagnozować problemy.

Listę liczników wydajności, które powinny być zbierane na poziomie infrastruktury można znaleźć w [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md). 

Usługa Service Fabric zawiera również zestaw liczników wydajności dla modeli programowania niezawodne usługi i aktorów. Jeśli używasz jednego z tych modeli, te liczniki wydajności można uzyskać informacje, aby upewnić się, że podmioty są przędzenia w górę iw dół poprawnie lub że żądania niezawodnej usługi są obsługiwane wystarczająco szybko. Aby uzyskać więcej informacji, zobacz [Monitorowanie niezawodnej komunikacji zdalnej](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) usługi i [monitorowania wydajności dla wiarygodnych aktorów.](service-fabric-reliable-actors-diagnostics.md#performance-counters) 

Rozwiązanie usługi Azure Monitor do zbierania tych jest dzienniki usługi Azure Monitor, podobnie jak monitorowanie na poziomie platformy. Agent usługi [Log Analytics](service-fabric-diagnostics-oms-agent.md) służy do zbierania odpowiednich liczników wydajności i wyświetlania ich w dziennikach usługi Azure Monitor.

## <a name="recommended-setup"></a>Zalecana konfiguracja
Teraz, gdy przeszliśmy przez każdy obszar monitorowania i przykładowe scenariusze, oto podsumowanie narzędzi do monitorowania platformy Azure i skonfigurować potrzebne do monitorowania wszystkich obszarów powyżej. 

* Monitorowanie aplikacji za pomocą [usługi Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitorowanie klastra za pomocą [agenta diagnostycznego](service-fabric-diagnostics-event-aggregation-wad.md) i [dzienników usługi Azure Monitor](service-fabric-diagnostics-oms-setup.md)
* Monitorowanie infrastruktury za pomocą [dzienników usługi Azure Monitor](service-fabric-diagnostics-oms-agent.md)

Można również użyć i zmodyfikować przykładowy szablon ARM znajdujący się [w tym miejscu,](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) aby zautomatyzować wdrażanie wszystkich niezbędnych zasobów i agentów. 

## <a name="other-logging-solutions"></a>Inne rozwiązania w zakresie rejestrowania

Mimo że dwa rozwiązania zalecane, [dzienniki usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) i usługa Application [Insights](service-fabric-diagnostics-event-analysis-appinsights.md) zostały wbudowane w integrację z siecią szkieletową usług, wiele zdarzeń jest wypisywane za pośrednictwem dostawców ETW i są rozszerzalne z innymi rozwiązaniami rejestrowania. Należy również przyjrzeć się [stosu elastycznego](https://www.elastic.co/products) (zwłaszcza jeśli rozważasz uruchomienie klastra w środowisku offline), [Dynatrace](https://www.dynatrace.com/)lub innej platformy preferencji. Mamy listę zintegrowanych partnerów dostępnych [tutaj.](service-fabric-diagnostics-partners.md)

Kluczowe punkty dla dowolnej platformy, którą wybierzesz, powinny obejmować komfort korzystania z interfejsu użytkownika, możliwości wykonywania zapytań, dostępne wizualizacje niestandardowe i pulpity nawigacyjne oraz dodatkowe narzędzia, które zapewniają w celu zwiększenia komfortu monitorowania. 

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z instrumentacji aplikacji, zobacz [Zdarzenia na poziomie aplikacji i generowania dziennika](service-fabric-diagnostics-event-generation-app.md).
* Przejdź przez kroki, aby skonfigurować usługę Application Insights dla aplikacji za pomocą [monitora i zdiagnozować aplikację ASP.NET Core w sieci szkieletowej usług.](service-fabric-tutorial-monitoring-aspnet.md)
* Dowiedz się więcej na temat monitorowania platformy i zdarzeń, które usługa Fabric zapewnia dla Ciebie na [poziomie platformy zdarzenia i generowania dziennika.](service-fabric-diagnostics-event-generation-infra.md)
* Konfigurowanie integracji dzienników usługi Azure Monitor z siecią szkieletową usług w [witrynie Konfigurowanie dzienników usługi Azure Monitor dla klastra](service-fabric-diagnostics-oms-setup.md)
* Dowiedz się, jak skonfigurować dzienniki usługi Azure Monitor do monitorowania kontenerów — [monitorowanie i diagnostyka kontenerów systemu Windows w sieci szkieletowej usług Azure.](service-fabric-tutorial-monitoring-wincontainers.md)
* Zobacz przykładowe problemy z diagnostyką i rozwiązania z siecią szkieletową usług w [diagnozowaniu typowych scenariuszy](service-fabric-diagnostics-common-scenarios.md)
* Zapoznaj się z innymi produktami diagnostycznymi, które integrują się z siecią szkieletową usług w [partnerach diagnostycznych sieci szkieletowej usług](service-fabric-diagnostics-partners.md)
* Dowiedz się więcej o ogólnych zaleceniach dotyczących monitorowania zasobów platformy Azure — [najważniejsze wskazówki — monitorowanie i diagnostyka](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 