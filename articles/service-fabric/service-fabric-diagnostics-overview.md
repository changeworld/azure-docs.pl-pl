---
title: Omówienie diagnostyki i monitorowanie sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat monitorowania i diagnostyki dla klastrów, aplikacje i usługi sieć szkieletowa usług Azure.
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
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: f784576547f0d85a825ad9dd107c6c84cd261092
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorowania i diagnostyki dla sieci szkieletowej usług Azure

Ten artykuł zawiera omówienie konfigurowania monitorowania i diagnostyki dla aplikacji uruchomionych w klastrów sieci szkieletowej usług. Monitorowania i diagnostyki są krytyczne dla programowania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Monitorowanie umożliwia śledzenie korzystania z aplikacji, z wykorzystania zasobów i ogólną kondycję klastra. Zdiagnozować i rozwiązać wszelkie problemy w klastrze, a także pomagające zapobiec problemy w przyszłości, można użyć tych informacji. 

Główne cele monitorowania i diagnostyki są:
* Wykrywanie i diagnozowanie problemów infrastruktury
* Wykrywaj problemy związane z aplikacją
* Zrozumienie zużycie zasobów
* Śledzenie wydajności aplikacji, usług i infrastruktury

W klastrze usługi sieć szkieletowa, monitorowania i diagnostyki dane pochodzą z trzech poziomów: aplikacja platformy (klaster) i infrastruktury. 
* [Poziomie aplikacji](service-fabric-diagnostics-event-generation-app.md) obejmuje dane dotyczące wydajności aplikacji i dodatkowe rejestrowania niestandardowego, który został dodany. Dane monitorowania aplikacji powinna kończyć w Application Insights (AI) z samej aplikacji. Są dostępne za pomocą zestawu SDK AI, EventFlow lub innym potoku wybranych przez użytkownika.
* [Poziom platformy](service-fabric-diagnostics-event-generation-infra.md) obejmuje zdarzenia z czynności w klastrze, związane z zarządzaniem klastra i aplikacji działających na nim. Dane monitorowania platformy powinny być wysyłane do OMS Log Analytics, rozwiązania analizy sieci szkieletowej usług, aby zwizualizować zdarzenia przychodzące. Te dane są zwykle wysyłane z kontem magazynu za pośrednictwem rozszerzenia systemu Windows lub Linux Azure Diagnostics, z którym jest on dostępny OMS Log Analytics. 
* Poziom infrastruktury koncentruje się na [monitorowania wydajności](service-fabric-diagnostics-event-generation-perf.md), wyglądającej kluczowych metryk i liczniki wydajności, aby określić wykorzystania zasobów i obciążenia. Monitorowanie wydajności można osiągnąć za pomocą agenta — zaleca się używanie Agent pakietu OMS (Microsoft Monitoring) tak, aby dane wydajności kończy się w tym samym miejscu jako zdarzeń platformy, który pozwala na lepsze środowisko diagnostyki jako użytkownik skorelowania zmiany w klastrze. 

![Wykres Omówienie diagnostyki](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>Scenariusze monitorowania

W tej sekcji opisano najważniejsze scenariusze monitorowania klastra sieci szkieletowej usług - aplikacji, klastra, wydajności i monitorowanie kondycji. Dla każdego scenariusza omówiono konwersji i ogólną podejście do monitorowania. Więcej informacji na temat tych i innych ogólne zalecenia monitorowania dotyczące zasobów platformy Azure można znaleźć w folderze [najlepsze rozwiązania w zakresie - monitorowania i diagnostyki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 

Te scenariusze również słabo są mapowane na trzy poziomy danych monitorowania i diagnostyki, zgodnie z powyższym opisem, tj. dla każdego scenariusza odpowiednio będą obsługiwane w klastrze, monitorowania i diagnostyki danych przesyłanych poziomie odpowiedniego powinny mieć . Monitorowania scenariusz kondycji jest wyjątek, ponieważ obejmuje klaster i wszystkie uruchomione w nim.

## <a name="application-monitoring"></a>Monitorowanie aplikacji
Monitorowanie aplikacji śledzi, jak są używane funkcje i składniki aplikacji, która zostanie utworzone. Chcesz monitorować Twoje aplikacje, aby upewnić się, że problemy tego wpływu, na który użytkownicy są przechwytywane. Monitorowanie aplikacji może być przydatne w:
* Określanie użytkownika i aplikacji obciążenia ruchu — potrzebuje skalowania usługi, aby spełniały potrzeby użytkowników lub rozwiązać potencjalne wąskie gardło w aplikacji?
* identyfikowanie problemów za pomocą komunikacji usługi i komunikacji zdalnej w klastrze
* ustaleniem, co robią użytkownicy z aplikacją — instrumentacji aplikacji mogą pomóc w przewodniku przyszłych funkcji programowanie i lepszą diagnostykę błędów aplikacji

Do monitorowania na poziomie aplikacji w sieci szkieletowej usług, firma Microsoft zaleca użycie Application Insights (AI). AI przez integrację z usługi Service Fabric zawiera narzędzia środowiska Visual Studio i portalu Azure i zrozumienia kontekstu usługi Service Fabric i komunikacji zdalnej pulpitu nawigacyjnego AI i mapowanie aplikacji, co może prowadzić do kompleksowe rejestrowania poza pole środowisko. Chociaż wiele dzienniki są automatycznie tworzone i zbierane automatycznie, gdy przy użyciu AI, zaleca się dodać dalsze niestandardowego rejestrowania dla poszczególnych aplikacji i mieć który wyświetlani AI obok jaka jest dostępna do utworzenia rozbudowanej diagnostyki obsługi problemów w przyszłości. Zobacz więcej na temat korzystania z usługi Service Fabric w AI [analiza zdarzeń z usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md). 

Aby rozpocząć pracę z Instrumentacji kodu do monitorowania aplikacji, przejdź do [poziomu generowania zdarzeń i dziennika aplikacji](service-fabric-diagnostics-event-generation-app.md). 

### <a name="monitoring-application-availability"></a>Monitorowanie dostępności aplikacji
Istnieje możliwość, że wszystko w klastrze wydaje się działać zgodnie z oczekiwaniami i raportowania jest w dobrej kondycji, ale prawdopodobnie aplikacji można niedostępne lub jest niedostępny. Chociaż nie są wielu przypadkach, w których ta sytuacja może mieć miejsce, należy znać w takim przypadku można ograniczyć go jak najszybciej. Monitorowanie dostępności szeroko dotyczy śledzenia o dostępności składników systemu, aby zrozumieć ogólny czas "działania" systemu. W klastrze możemy skupić się na dostępność z punktu widzenia aplikacji — działa platformy, aby upewnić się, że cyklem życia aplikacji scenariusze zarządzania nie powodują przestoju. Niemniej jednak różne problemy w klastrze może spowodować wpływających na czas działania aplikacji, a w tych przypadkach jako właściciel aplikacji zwykle chcesz od razu wiedzieć. Zaleca się równolegle z wszystkich innych aplikacji, wdrożenie programu alarmowego w klastrze. Celem takiego programu alarmowego byłoby Sprawdź odpowiednie punkty końcowe w odstępach czasu zestawu aplikacji i wysyłać raporty, że są one dostępne. Można to także zrobić za pomocą zewnętrznej usługi wysyła pakiet usługi ping punktu końcowego, który zwraca raportu w danym przedziale czasu.

## <a name="cluster-monitoring"></a>Monitorowanie klastra
Monitorowanie klastra usługi sieć szkieletowa jest szczególnie ważne w celu zapewnienia, że platforma i wszystkich obciążeń na nim uruchomione, działają zgodnie z założeniami. Jednym z celów usługi sieć szkieletowa jest zachowanie aplikacji działających przy użyciu awarie sprzętowe. Jest to osiągane przez usługi systemowe platformy możliwość wykrywania problemów z infrastrukturą i szybko obciążeń trybu failover do innych węzłów w klastrze. Ale co zrobić, jeśli w tym przypadku uwierzytelnienia usługi system ma problemy? Lub jeśli podczas próby przeniesienia obciążenia, naruszenia reguły umieszczania usług? Monitorowanie klastra służy do uzyskiwania informacji o działaniu odbywa się w klastrze, co ułatwia diagnozowanie problemów i ich efektywne rozwiązanie. Niektóre elementy klucza, który chcesz szukać są:
* Sieć szkieletowa usług zachowuje się oczekiwaniami, wprowadzenie do aplikacji i obejść klastra równoważenia? 
* Akcje podejmowane są do modyfikacji konfiguracji klastra są potwierdzone i przetwarzane zgodnie z oczekiwaniami? Jest to szczególnie istotne w przypadku, gdy skalowanie klastra.
* Sieć szkieletowa usług obsługuje dane i usługa usługa komunikacji wewnątrz klastra poprawnie?

Sieć szkieletowa usług zapewnia rozbudowany zestaw zdarzeń fabrycznej kanałami Operational danych i komunikatów. W systemie Windows, są w postaci jednego dostawcy ETW z zestawem odpowiednich `logLevelKeywordFilters` umożliwia pobranie między różnych kanałów. W systemie Linux wszystkich zdarzeń platformy przepuszczane LTTng i są umieszczane w jednej tabeli, od których one mogą być filtrowane zgodnie z potrzebami. 

Te kanały zawierają wyselekcjonowanych, strukturalnych zdarzenia, które umożliwiają lepsze zrozumienie stanu klastra. "Diagnostyki" jest domyślnie włączona w czasie tworzenia klastra, aby skonfigurować możesz z tabeli magazynu Azure wysyłania zdarzeń z tych kanałów można zbadać w przyszłości. Więcej o monitorowaniu klastra na [platformy poziomu generowania zdarzeń i dziennika](service-fabric-diagnostics-event-generation-infra.md). Zalecane jest użycie pakietu OMS analizy dzienników do monitorowania sieci klastra. Analizy dzienników OMS oferuje rozwiązanie określonych sieci szkieletowej usług analizy sieci szkieletowej usługi, który zawiera niestandardowy pulpit nawigacyjny do monitorowania klastrów sieci szkieletowej usług i umożliwia zapytań dotyczących zdarzeń, klastra i skonfigurować alerty. Dowiedz się więcej o tym w [analiza zdarzeń z OMS](service-fabric-diagnostics-event-analysis-oms.md). 

### <a name="watchdogs"></a>Watchdogs
Ogólnie rzecz biorąc programu alarmowego jest oddzielny usługa można obejrzeć kondycji i obciążenie usługi ping punktów końcowych i raportu kondycji dla wszystkich elementów w klastrze. Może to pomóc zapobiec wystąpieniu błędów, które nie można wykryć na podstawie widoku jednej usługi. Watchdogs są również dobrym miejscem do hosta kod, który wykonuje czynności zaradczych bez interakcji z użytkownikiem (na przykład czyszczenie plików dziennika w magazynie, co pewien czas). Można znaleźć implementacji usługi programu alarmowego próbki [tutaj](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="performance-monitoring"></a>Monitorowanie wydajności
Monitorowanie infrastruktury podstawowej jest kluczowym elementem opis stanu klastra i z wykorzystania zasobów. Pomiaru wydajności systemu zależy od wielu czynników, z których każdy jest zwykle mierzy się za pomocą kluczowych wskaźników wydajności (KPI). Kluczowe wskaźniki wydajności odpowiednich sieci szkieletowej usług mogą być mapowane na metryki, które mogą być zbierane z węzłów w klastrze, jako liczniki wydajności.
Tych wskaźników KPI może ułatwić:
* Opis wykorzystania zasobów i obciążenia — na potrzeby skalowania klastra lub optymalizacji procesów usługi
* przewidywanie problemów z infrastrukturą — wiele problemów są poprzedzone nagłych zmian (porzucania) wydajności, dzięki czemu można używać kluczowych wskaźników wydajności takie jak sieci wykorzystanie we/wy i procesora CPU, przewidywanie i diagnozowania problemów infrastrukturalne

Lista liczników wydajności, które powinny być zebrane na poziomie infrastruktury można znaleźć w folderze [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md). 

W przypadku monitorowania poziomu wydajności aplikacji platformy Service Fabric zawiera zestaw liczników wydajności modele programowania Reliable Services i uczestników. Jeśli korzystasz z jednej z tych modeli, te liczniki wydajności zapewniają wskaźników KPI, które pozwalają zagwarantować, że Twoje podmioty są wirowania górę i w dół poprawnie lub czy Twoich żądań obsługi niezawodnych są obsługiwane tyle szybko. Zobacz [monitorowania niezawodnej Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) i [monitorowanie wydajności dla elementów Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters) Aby uzyskać więcej informacji o tych. Oprócz tego usługi Application Insights ma również zestaw metryki wydajności, który odbierze, jeśli skonfigurowano aplikację.

Użyj agenta pakietu OMS można zebrać liczników wydajności odpowiednie i wyświetlanie tych wskaźników KPI w OMS Log Analytics. Można również użyć rozszerzenie agenta diagnostyki Azure (lub inne podobne agenta), aby zebrać i zapisać metryki dla analizy. 

## <a name="health-monitoring"></a>Monitorowanie kondycji
Platformy Service Fabric zawiera model kondycji, co zapewnia rozszerzonego raportowania kondycji stanu jednostek w klastrze. Każdego węzła, aplikacji, usług, partycji, repliki lub wystąpienia, ma stan kondycji stale nadaje się do aktualizacji. Stan kondycji może być "OK", "Ostrzeżenie" lub "Error". Zmiany stanu kondycji za pomocą raportów kondycji, które są emitowane dla każdej jednostki oparte na problemy w klastrze. Stan kondycji jednostki można sprawdzić w dowolnym momencie w Service Fabric Explorer (SFX), jak pokazano poniżej, lub można zbadać za pomocą interfejsu API kondycji platformy. Można również dostosować raportów o kondycji i modyfikować stanu kondycji jednostki przez dodanie własnych raportów kondycji lub przy użyciu interfejsu API kondycji. Więcej informacji na temat modelu kondycji można znaleźć w folderze [wprowadzenie do monitorowania kondycji sieci szkieletowej usług](service-fabric-health-introduction.md).

![Pulpit nawigacyjny kondycji SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Oprócz wyświetlać najnowsze raportów o kondycji w SFX, każdy raport jest również dostępny jako zdarzenie. Zdarzenia kondycji mogą być zbierane przez kanał operacyjne (zobacz [agregacji zdarzenia z diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) i przechowywane w OMS analizy dzienników dla alertów i wykonywaniu zapytań w przyszłości. Dzięki temu można wykrywać problemy, które mogą mieć wpływ na jego dostępność aplikacji, tak więc zaleca się Konfigurowanie alertów dla scenariuszy awarii odpowiednie (niestandardowe alerty za pomocą pakietu OMS).

## <a name="monitoring-workflow"></a>Monitorowanie przepływu pracy 

Ogólny przepływ pracy monitorowania i diagnostyki obejmuje trzy kroki:

1. **Generowanie zdarzeń**: dotyczy to zdarzeń (dzienników, śledzenie zdarzeń niestandardowych), aby na poziomie infrastruktury, platformy (klastra) i aplikacji
2. **Zdarzenie agregacji**: tego etapu jest potoku dla zdarzeń na końcu w narzędziu gdzie można je analizować za, w tym rozszerzenia diagnostyki Azure lub EventFlow
3. **Analiza**: zdarzeń muszą być dostępne w narzędziu, aby umożliwić analiza - wizualizacji zapytań, alerty, itp.

Wiele produktów są dostępne, który obejmuje te trzy obszary i wolnych do wyboru różnych technologii dla każdego. Należy się upewnić, czy różnych elementów współdziałać, aby dostarczać rozwiązanie monitorowania end-to-end dla klastra.

## <a name="event-generation"></a>Generowanie zdarzeń

Pierwszym krokiem w przepływie pracy monitorowania i diagnostyki jest konfigurowanie tworzenia i generowania dane zdarzeń i dziennika. Te zdarzenia, dzienników i liczniki wydajności są emitowane na wszystkich trzech poziomach: poziom aplikacji (wszelkie Instrumentacji dodany do aplikacji i usługi wdrożone do klastra), platformy (zdarzenia wysyłanego z klastra, w zależności od operacji usługi sieć szkieletowa) i poziom infrastruktury (metryki wydajności z każdego węzła). Danych diagnostycznych, zbieranych na każdym z tych poziomów jest można dostosowywać, chociaż usługi sieć szkieletowa włączyć Instrumentacji niektóre domyślne. 

Przeczytaj więcej na temat [zdarzenia na poziomie platformy](service-fabric-diagnostics-event-generation-infra.md) i [zdarzenia na poziomie aplikacji](service-fabric-diagnostics-event-generation-app.md) zrozumieć, jaka jest dostępna oraz sposób zwiększenia instrumentacji. Głównego decyzji, które należy wprowadzić w tym miejscu jest sposób Instrumentacja aplikacji. Dla aplikacji .NET zaleca się używanie ILogger, ale można też eksplorować EventSource, Serilog i inne podobne biblioteki. Dla języka Java firma Microsoft zaleca używanie narzędzia Log4j. Poza, dostępnych jest kilka innych opcji dostępnych które mogą być używane w oparciu o rodzaju aplikacji. Możesz zająć się Poznaj, co będzie najlepiej dla tej sprawy konkretnego lub wybierz jeden czy potrafisz najbardziej przy użyciu. 

Po podejmowania decyzji w sprawie dostawcy logowania chcesz użyć, należy się upewnić, że dzienniki są agregowane i przechowywane poprawnie.

## <a name="event-aggregation"></a>Agregacja zdarzeń

Do zbierania dzienników i zdarzenia generowane przez aplikacje i klastra, ogólnie zalecamy użycie [rozszerzenia diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md) (więcej podobnie do zbierania dzienników opartej o agentów) lub [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (zbieranie danych dziennika w trakcie). Jeśli używasz usługi Application Insights i opracowywania w .NET lub Java, przy użyciu zestawu SDK usługi Application Insights zamiast EventFlow jest zalecane.

Podczas gdy można pobrać zadania podobne, odbywa się za pomocą przy użyciu tylko jednego z nich, w większości sytuacji należy połączenie agenta rozszerzeń diagnostyki Azure z potoku w procesie kolekcji (AI zestawu SDK lub EventFlow) prowadzi do bardziej niezawodne, kompleksowy, monitorowania przepływu pracy . Agent rozszerzeń diagnostyki Azure będzie ścieżki dla zdarzenia na poziomie platformy, podczas korzystania z zestawu SDK AI lub EventFlow (w trakcie kolekcji) dla poziomu dzienników aplikacji. 

W przypadku, gdy ma być używany tylko jeden z nich poniżej przedstawiono niektóre najważniejszych należy wziąć pod uwagę.
* Zbieranie dzienników aplikacji przy użyciu rozszerzenia diagnostyki Azure jest dobra opcja w przypadku usługi Service Fabric, jeśli zestaw źródeł dziennika oraz miejsc docelowych nie zmieniają się często, istnieje bezpośrednie mapowanie między źródeł oraz ich miejsc docelowych. Powód dla diagnostyki Azure to konfiguruje odbywa się w warstwie Menedżera zasobów dzięki wprowadzeniu istotnych zmian w konfiguracji wymaga zaktualizowania całego klastra. Oznacza to, że często kończy się są mniej wydajne niż korzystanie z zestawu SDK AI lub EventFlow.
* Użycie EventFlow umożliwia wysyłanie ich dzienników bezpośrednio do analizy i wizualizacji platformy i/lub do magazynu usług. Inne biblioteki (ILogger, Serilog itp.) mogą być także wykorzystywane do tego celu, ale EventFlow przynosi korzyści związane z został zaprojektowany specjalnie z myślą o zbierania dzienników w trakcie i do obsługi usługi sieć szkieletowa usług. Zwykle ma kilka zalet w postaci łatwość konfiguracji i wdrożenia i zapewnia większą elastyczność do obsługi rejestrowania różnych biblioteki i narzędzia do analizy. 

## <a name="event-analysis"></a>Analiza zdarzeń

Istnieje kilka dużą platformy, które istnieją w rynku, jeśli chodzi o analizy i wizualizacji danych monitorowania i diagnostyki. Są dwa, które są zalecane [OMS](service-fabric-diagnostics-event-analysis-oms.md) i [usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) z powodu ich integracji z sieci szkieletowej usług. Należy również zwrócić uwagę na [stosu elastycznych](https://www.elastic.co/products) (szczególnie, gdy rozważane jest uruchomiony klaster w środowisku w trybie offline), [Splunk](https://www.splunk.com/), lub inne platformy swoich preferencji. 

Klucz wskazuje na dowolnej platformie wybierz powinna zawierać jak doświadczenia są z interfejsem użytkownika i badania opcje możliwość wizualizacji danych i tworzenie czytelny pulpitów nawigacyjnych i dodatkowe narzędzia zapewniają do rozszerzania monitorowania, automatyczne takich jak alerty.

Oprócz platformy, należy wybrać podczas konfigurowania klastra sieci szkieletowej usług jako zasobów platformy Azure, również uzyskać dostęp do platformy Azure poza pole monitorowanie wydajności dla maszyny.

### <a name="azure-monitor"></a>Azure Monitor

Można użyć [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) do monitorowania wielu zasobów platformy Azure, na których jest zbudowany klastra sieci szkieletowej usług. Zestaw metryki dla [zestaw skali maszyny wirtualnej](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) i indywidualnych [maszyn wirtualnych](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) automatycznie są zbierane i wyświetlane w portalu Azure. Aby wyświetlić zebrane informacje w portalu Azure, wybierz grupę zasobów, zawierającą klaster sieci szkieletowej usług. Wybierz zestaw skali maszyny wirtualnej, który chcesz wyświetlić. W **monitorowanie** sekcji (na lewym nav), wybierz opcję **metryki** do wyświetlania wykresu wartości.

![Widok portalu Azure zebranych informacji metryki](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Aby dostosować wykresy, postępuj zgodnie z instrukcjami [metryk w Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Można również tworzyć alerty oparte na tych metryk, zgodnie z opisem w [w monitorze Azure tworzyć alerty dla usług Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat monitorowania platformy i zdarzenia platformy Service Fabric zawiera w miejscu [platformy poziomu generowania zdarzeń i dzienników](service-fabric-diagnostics-event-generation-infra.md)
* Wprowadzenie do instrumentacji aplikacji, dla [poziomu generowania zdarzeń i dziennika aplikacji](service-fabric-diagnostics-event-generation-app.md)
* Przejdź przez proces konfigurowania AI aplikacji z [monitorowania i diagnostyki dla aplikacji platformy ASP.NET Core sieci szkieletowej usług](service-fabric-tutorial-monitoring-aspnet.md)
* Dowiedz się, jak skonfigurować OMS analizy dzienników dla monitorowania kontenery- [monitorowania i diagnostyki dla systemu Windows kontenerów w sieci szkieletowej usług Azure](service-fabric-tutorial-monitoring-wincontainers.md)

