---
title: Monitorowanie poziomu platformy sieci szkieletowej usług Azure
description: Dowiedz się więcej o zdarzeniach i dziennikach na poziomie platformy używanych do monitorowania i diagnozowania klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 720cc157111293146b796f8567f94a4f1f4830c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376940"
---
# <a name="monitoring-the-cluster"></a>Monitorowanie klastra

Należy monitorować na poziomie klastra, aby ustalić, czy sprzęt i klaster zachowują się zgodnie z oczekiwaniami. Chociaż sieci szkieletowej usług można zachować aplikacje uruchomione podczas awarii sprzętu, ale nadal trzeba zdiagnozować, czy błąd występuje w aplikacji lub w podstawowej infrastruktury. Należy również monitorować klastry, aby lepiej planować pojemność, pomagając w podejmowaniu decyzji dotyczących dodawania lub usuwania sprzętu.

Usługa Fabric udostępnia kilka zdarzeń platformy strukturalnej, jako [zdarzenia sieci szkieletowej usług,](service-fabric-diagnostics-events.md)za pośrednictwem EventStore i różnych kanałów dziennika out-of-the-box. 

W systemie Windows zdarzenia sieci szkieletowej usług są dostępne `logLevelKeywordFilters` od jednego dostawcy ETW z zestawem odpowiednich do pobrania między kanałami operacyjną i data & wiadomości — w ten sposób oddzielamy wychodzące zdarzenia sieci szkieletowej usług, które mają być filtrowane zgodnie z potrzebami.

* **Działalność operacyjna** Operacje wysokiego poziomu wykonywane przez sieci szkieletowej usług i klastra, w tym zdarzenia dla węzła zbliża się, nowa aplikacja jest wdrażany lub wycofywania uaktualnienia, itp. Zobacz pełną listę wydarzeń [tutaj](service-fabric-diagnostics-event-generation-operational.md).  

* **Operacyjne - szczegółowe**  
Raporty o kondycji i decyzje dotyczące równoważenia obciążenia.

Dostęp do kanału operacyjnego można uzyskać na wiele sposobów, w tym dzienniki zdarzeń ETW/Windows, [eventstore](service-fabric-diagnostics-eventstore.md) (dostępny w systemie Windows w wersji 6.2 i nowszych dla klastrów systemu Windows). EventStore zapewnia dostęp do zdarzeń klastra na podstawie jednostki (jednostek, w tym klastra, węzłów, aplikacji, usług, partycji, replik i kontenerów) i udostępnia je za pośrednictwem interfejsów API REST i biblioteki klienta sieci szkieletowej usług. Usługa EventStore służy do monitorowania klastrów deweloperskich/testowych oraz uzyskiwania zrozumienia stanu klastrów produkcyjnych w czasie.

* **Wiadomości & danych**  
Krytyczne dzienniki i zdarzenia generowane w wiadomości (obecnie tylko ReverseProxy) i ścieżka danych (modele niezawodnych usług).

* **Wiadomości & danych - szczegółowe**  
Pełny kanał, który zawiera wszystkie dzienniki niekrytyczne z danych i wiadomości w klastrze (ten kanał ma bardzo dużą liczbę zdarzeń).

Oprócz nich istnieją dwa ustrukturyzowane kanały EventSource pod warunkiem, a także dzienniki, które zbieramy do celów pomocy technicznej.

* [Zdarzenia interfejsu Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Programowanie zdarzeń specyficznych dla modelu.

* [Zdarzenia dotyczące elementów Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Programowanie zdarzeń specyficznych dla modelu i liczników wydajności.

* Dzienniki pomocy technicznej  
Dzienniki systemowe generowane przez usługę Fabric tylko do wykorzystania przez nas podczas świadczenia pomocy technicznej.

Te różne kanały obejmują większość rejestrowania na poziomie platformy, które jest zalecane. Aby poprawić rejestrowanie na poziomie platformy, należy rozważyć inwestowanie w lepsze zrozumienie modelu kondycji i dodawanie niestandardowych raportów kondycji i dodawanie niestandardowych **liczników wydajności** w celu utworzenia w czasie rzeczywistym zrozumienia wpływu usług i aplikacji na klaster.

Aby skorzystać z tych dzienników, zdecydowanie zaleca się pozostawienie włączonej funkcji "Diagnostyka" podczas tworzenia klastra w witrynie Azure Portal. Włączając diagnostykę, po wdrożeniu klastra diagnostyka systemu Windows Azure może potwierdzić kanały operacyjne, niezawodne usługi i niezawodne podmioty oraz przechowywać dane zgodnie [z dalszymi wyjaśnieniami w obszarze Zdarzenia zbiorcze za pomocą usługi Azure Diagnostics.](service-fabric-diagnostics-event-aggregation-wad.md)

## <a name="azure-service-fabric-health-and-load-reporting"></a>Raportowanie kondycji i obciążenia sieci szkieletowej usługi Azure

Usługa Service Fabric ma swój własny model kondycji, który jest szczegółowo opisany w tych artykułach:

- [Wprowadzenie do monitorowania kondycji usługi Service Fabric](service-fabric-health-introduction.md)
- [Tworzenie raportów i sprawdzanie kondycji usług](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Dodawanie niestandardowych raportów kondycji sieci szkieletowej usług](service-fabric-report-health.md)
- [Wyświetlanie raportów kondycji sieci szkieletowej usług](service-fabric-view-entities-aggregated-health.md)

Monitorowanie kondycji ma kluczowe znaczenie dla wielu aspektów obsługi usługi, szczególnie podczas uaktualniania aplikacji. Po uaktualnieniu każdej domeny uaktualnienia usługi domena uaktualnienia musi przejść testy kondycji, zanim wdrożenie zostanie przeniesiony do następnej domeny uaktualnienia. Jeśli stan kondycji OK nie można osiągnąć, wdrożenie jest przywracane, tak aby aplikacja pozostała w znanym stanie OK. Chociaż może to mieć wpływ na niektórych klientów, zanim usługi zostaną wycofane, większość klientów nie wystąpi problem. Ponadto rozdzielczość występuje stosunkowo szybko bez konieczności oczekiwania na działanie od ludzkiego operatora. Im więcej kontroli kondycji, które są włączone do kodu, tym bardziej odporne usługi jest do problemów z wdrażaniem.

Innym aspektem kondycji usługi jest raportowanie metryk z usługi. Metryki są ważne w sieci szkieletowej usług, ponieważ są one używane do równoważenia użycia zasobów. Metryki mogą być również wskaźnikiem kondycji systemu. Na przykład może mieć aplikację, która ma wiele usług, a każde wystąpienie zgłasza żądania na sekundę (RPS) metryki. Jeśli jedna usługa używa więcej zasobów niż inna usługa, sieć szkieletowa usług przenosi wystąpienia usług wokół klastra, aby spróbować utrzymać równomierne wykorzystanie zasobów. Aby uzyskać bardziej szczegółowe wyjaśnienie, jak działa wykorzystanie zasobów, zobacz [Zarządzanie zużyciem zasobów i ładowaniem w sieci szkieletowej usług za pomocą metryk](service-fabric-cluster-resource-manager-metrics.md).

Metryki mogą również pomóc w wglądie w wydajność usługi. Z biegiem czasu można użyć metryki, aby sprawdzić, czy usługa działa w ramach oczekiwanych parametrów. Na przykład jeśli trendy pokazują, że o godzinie 9:00 w poniedziałek rano średnia rps wynosi 1000, a następnie można skonfigurować raport kondycji, który ostrzega, jeśli RPS jest poniżej 500 lub powyżej 1500. Wszystko może być idealnie w porządku, ale może warto spojrzeć, aby upewnić się, że twoi klienci mają wspaniałe doświadczenie. Usługa może zdefiniować zestaw metryk, które mogą być zgłaszane do celów sprawdzania kondycji, ale nie mają wpływu na równoważenie zasobów klastra. Aby to zrobić, ustaw wagę metryki na zero. Zaleca się, aby rozpocząć wszystkie metryki z wagą zero i nie zwiększać wagi, dopóki nie upewnisz się, że rozumiesz, jak ważenie metryki wpływa na równoważenie zasobów dla klastra.

> [!TIP]
> Nie używaj zbyt wielu ważonych danych. Może być trudne do zrozumienia, dlaczego wystąpienia usługi są przenoszone wokół równoważenia. Kilka wskaźników może przejść długą drogę!

Wszelkie informacje, które mogą wskazywać na kondycję i wydajność aplikacji jest kandydatem do metryk i raportów kondycji. **Licznik wydajności procesora CPU może powiedzieć, jak węzeł jest używany, ale nie informuje, czy określona usługa jest w dobrej kondycji, ponieważ wiele usług może być uruchomiony w jednym węźle.** Ale metryki, takie jak RPS, elementy przetworzone i opóźnienie żądania wszystkie mogą wskazywać kondycję określonej usługi.

## <a name="service-fabric-support-logs"></a>Dzienniki pomocy technicznej sieci szkieletowej usług

Jeśli musisz skontaktować się z pomocą techniczną firmy Microsoft, aby uzyskać pomoc dotyczącą klastra sieci szkieletowej usług Azure, dzienniki pomocy technicznej są prawie zawsze wymagane. Jeśli klaster jest hostowany na platformie Azure, dzienniki pomocy technicznej są automatycznie konfigurowane i zbierane w ramach tworzenia klastra. Dzienniki są przechowywane na dedykowanym koncie magazynu w grupie zasobów klastra. Konto magazynu nie ma stałej nazwy, ale na koncie są widoczne kontenery obiektów blob i tabele z nazwami zaczynających się od *sieci szkieletowej*. Aby uzyskać informacje dotyczące konfigurowania kolekcji dzienników dla klastra autonomicznego, zobacz [Tworzenie autonomicznego klastra](service-fabric-cluster-creation-for-windows-server.md) sieci szkieletowej usługi Azure i ustawienia konfiguracji [autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md)oraz zarządzanie nimi . W przypadku autonomicznych wystąpień sieci szkieletowej usług dzienniki powinny być wysyłane do lokalnego udziału plików. Wymagane **jest,** aby te dzienniki były przeznaczone do obsługi technicznej, ale nie są one przeznaczone do użytku przez osoby spoza zespołu obsługi klienta firmy Microsoft.

## <a name="measuring-performance"></a>Pomiar wydajności

Pomiar wydajności klastra pomoże Ci zrozumieć, w jaki sposób jest w stanie obsługiwać decyzje dotyczące obciążenia i napędu wokół skalowania klastra (zobacz więcej informacji na temat skalowania klastra [na platformie Azure](service-fabric-cluster-scale-up-down.md)lub [lokalnie).](service-fabric-cluster-windows-server-add-remove-nodes.md) Dane dotyczące wydajności są również przydatne w porównaniu z działaniami, które użytkownik lub aplikacje i usługi mogły zostać podjęte podczas analizowania dzienników w przyszłości. 

Aby uzyskać listę liczników wydajności do zebrania podczas korzystania z sieci szkieletowej usług, zobacz [Liczniki wydajności w sieci szkieletowej usług](service-fabric-diagnostics-event-generation-perf.md)

Oto dwa typowe sposoby konfigurowania zbierania danych o wydajności dla klastra:

* **Korzystanie z agenta**  
Jest to preferowany sposób zbierania wydajności z komputera, ponieważ agenci zwykle mają listę możliwych metryk wydajności, które mogą być zbierane i jest to stosunkowo łatwy proces, aby wybrać metryki, które chcesz zebrać lub zmienić. Przeczytaj o usłudze Azure Monitor oferującej dzienniki usługi Azure Monitor w usłudze [Azure Monitor loguje integrację](service-fabric-diagnostics-event-analysis-oms.md) i [konfigurowanie agenta analizy dzienników,](../log-analytics/log-analytics-windows-agent.md) aby dowiedzieć się więcej o agencie analizy dzienników, który jest jednym z takich agentów monitorowania, który jest w stanie odebrać dane dotyczące wydajności dla maszyn wirtualnych klastra i wdrożonych kontenerów.

* **Liczniki wydajności usługi Azure Table Storage**  
Można również wysłać metryki wydajności do tego samego magazynu tabeli, co zdarzenia. Wymaga to zmiany konfiguracji diagnostyki platformy Azure, aby odebrać odpowiednie liczniki wydajności z maszyn wirtualnych w klastrze i włączenie go do pobrania statystyk docker, jeśli będzie wdrażanie kontenerów. Przeczytaj o konfigurowaniu [liczników wydajności w wad](service-fabric-diagnostics-event-aggregation-wad.md) w sieci szkieletowej usług, aby skonfigurować kolekcję liczników wydajności.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [integracji dzienników usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) w sieci szkieletowej usług w celu zbierania diagnostyki klastra i tworzenia niestandardowych zapytań i alertów
* Dowiedz się więcej o sieci szkieletowej usług w wbudowanym doświadczeniu diagnostycznym, [EventStore](service-fabric-diagnostics-eventstore.md)
* Przechodzenie przez niektóre [typowe scenariusze diagnostyczne](service-fabric-diagnostics-common-scenarios.md) w sieci szkieletowej usług
