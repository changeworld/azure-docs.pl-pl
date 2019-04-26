---
title: Usługa Azure Service Fabric platformy poziom monitorowania | Dokumentacja firmy Microsoft
description: Informacje na temat zdarzeń na poziomie platformy i dzienniki umożliwia monitorowanie i diagnozowanie klastrów usługi Azure Service Fabric.
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
ms.openlocfilehash: cbdbedf32e8a3dad85262f287b27a03df780d95a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393065"
---
# <a name="monitoring-the-cluster"></a>Monitorowanie klastra

Należy monitorować na poziomie klastra, aby ustalić, czy sprzęt i klastra zachowują się zgodnie z oczekiwaniami. Chociaż usługa Service Fabric można zachować aplikacji działających w czasie awarii sprzętu, ale nadal musisz zdiagnozować, czy błąd występuje w aplikacji lub podstawowej infrastruktury. Należy również monitorować klastrów lepiej planowania pojemności, pomagając w podejmowaniu decyzji o dodaniu lub usunięciu sprzętu.

Usługa Service Fabric udostępnia kilka zdarzeń ze strukturą platformy, jako [zdarzenia usługi Service Fabric](service-fabric-diagnostics-events.md), za pośrednictwem bazy danych EventStore i różnych dziennika kanały out-of--box. 

W Windows, są dostępne z pojedynczego dostawcy funkcji ETW z zestawem istotne zdarzenia usługi Service Fabric `logLevelKeywordFilters` używane do pobierania kanał operacyjnej, danych i komunikatów — jest to sposób możemy oddzielić wychodzących zdarzeń usługi Service Fabric ma zostać wykonane filtrowanie w razie potrzeby.

* **Operational** wysokiego poziomu operacji wykonywanych przez usługi Service Fabric i klastra, w tym zdarzenia dla węzła pojawi się, wdrażania nowej aplikacji lub uaktualnienia wycofywania itp. Zobacz pełną listę zdarzeń [tutaj](service-fabric-diagnostics-event-generation-operational.md).  

* **Operacyjne — szczegółowe**  
Raportów o kondycji i decyzje dotyczące równoważenia obciążenia.

Kanał operacji jest możliwy za pośrednictwem różnych sposobów, w tym dzienniki zdarzeń ETW/Windows, [bazy danych EventStore](service-fabric-diagnostics-eventstore.md) (dostępne w Windows w wersji 6.2 i nowszych dla klastrów Windows). Bazy danych EventStore daje dostęp do zdarzenia klastra na podstawie poszczególnych jednostek (łącznie z klastra, węzłów, aplikacji, usług, partycji, repliki i kontenerów jednostek) i udostępnia je za pośrednictwem interfejsów API REST i biblioteki klienckiej usługi Service Fabric. Użyj bazy danych EventStore do monitorowania klastrów i testowania aplikacji i pobrania w momencie znajomości stan klastrów produkcyjnych.

* **Komunikaty & danych**  
Dzienniki krytyczne i zdarzenia generowane w wiadomości (obecnie tylko elementu ReverseProxy) i ścieżki danych (modeli usług reliable services).

* **Obsługa wiadomości — szczegółowe & danych**  
Pełne kanał, który zawiera wszystkie niekrytyczne dzienniki danych i komunikatów w klastrze (ten kanał ma bardzo dużej liczby zdarzeń).

Oprócz wspomnianych istnieją dwa ze strukturą kanały źródła zdarzeń, a także dzienniki, które zbieramy celów pomocy technicznej.

* [Zdarzenia interfejsu Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Model programowania określonych zdarzeń.

* [Zdarzenia dotyczące elementów Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Liczniki wydajności i określonych zdarzeń modelu programowania.

* Dzienniki pomocy technicznej  
Dzienniki systemu, generowane przez usługę Service Fabric tylko po to, które będą używane przez firmę Microsoft, podczas świadczenie pomocy technicznej.

Te różne kanały pokrywał większą część platformy poziomu rejestrowania, jest zalecane. Aby poprawić rejestrowanie na poziomie platformy, należy wziąć pod uwagę inwestowanie w lepsze zrozumienie modelu kondycji i dodawanie niestandardowych raportów o kondycji i dodawanie niestandardowych **liczniki wydajności** do kompilacji w czasie rzeczywistym zrozumienia wpływu usługi usługi i aplikacji w klastrze.

Aby można było korzystać z tych dzienników, wysoce zalecane jest pozostawienie "Włączono usługę Diagnostyka" podczas tworzenia klastra w witrynie Azure Portal. Włączając diagnostykę po wdrożeniu klastra systemu Windows Azure Diagnostics jest w stanie potwierdzić operacyjnej usług Reliable Services i kanałów w elementach Reliable actors w celu przechowywania danych, jak wyjaśniono dalej w [agregowanie zdarzeń przy użyciu platformy Azure Diagnostyka](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Usługa Azure kondycji usługi Service Fabric i raportowanie obciążenia

Usługa Service Fabric ma swój własny model kondycji, który opisano szczegółowo w następujących artykułach:

- [Wprowadzenie do monitorowania kondycji usługi Service Fabric](service-fabric-health-introduction.md)
- [Tworzenie raportów i sprawdzanie kondycji usług](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Dodawanie niestandardowych raportów o kondycji usługi Service Fabric](service-fabric-report-health.md)
- [Wyświetlanie raportów o kondycji usługi Service Fabric](service-fabric-view-entities-aggregated-health.md)

Monitorowanie kondycji ma kluczowe znaczenie dla wielu płaszczyznach systemu operacyjnego to usługa, szczególnie podczas uaktualniania aplikacji. Po uaktualnieniu każdej z domen usługi domeny uaktualnień musi upłynąć kontrole kondycji wdrożenia przechodzi do następnej domeny uaktualnienia. Jeśli nie można osiągnąć stan kondycji OK, wdrożenie zostanie wycofana, tak, aby aplikacja pozostaje w znanym stanie OK. Chociaż niektórzy klienci mogą mieć wpływ, zanim usługi zostaną wycofane, większość klientów nie będą występować problem. Ponadto występuje rozpoznawania stosunkowo szybko bez konieczności oczekiwania dla akcji z osoby pełniącej rolę operatora. Więcej kontroli kondycji, które są zawarte w kodzie, bardziej odporne usługi ma problemy z wdrażaniem.

Innym aspektem kondycja usługi zgłasza metryki z usługi. Metryki są ważne w usłudze Service Fabric, ponieważ są one używane do równoważyć obciążenie zasobów. Metryki można także wskaźnik określający kondycji systemu. Na przykład Niewykluczone, że aplikacja, która ma wiele usług, a każde wystąpienie zgłasza żądania na drugim metrykę (jednostek Uzależnionych). Jeśli jedna usługa używa więcej zasobów niż inna usługa, usługi Service Fabric przenosi wystąpień usługi wokół klastra próby wykorzystania zasobów nawet zachowania. Aby uzyskać bardziej szczegółowy opis sposobu działania wykorzystanie zasobów, zobacz [Zarządzanie zużycia zasobów i obciążenia w usłudze Service Fabric za pomocą metryk](service-fabric-cluster-resource-manager-metrics.md).

Metryki również mogą pomóc zapewniają wgląd w sposób działa usługa. Wraz z upływem czasu można użyć metryki, aby sprawdzić, czy usługa działa w ramach oczekiwanych parametrów. Na przykład jeśli o 9: 00 w poniedziałek rano średnia RPS wynosi 1000 trendów, następnie może skonfigurowaniu Raport kondycji, który generuje alert w przypadku RPS znajduje się poniżej 500 lub ponad 1500. Wszystko, co może być doskonale dobrym rozwiązaniem, ale może być zapoznać należy upewnić się, że klientom najwyższą wspaniałego środowiska. Usługi można zdefiniować zestaw metryk, które mogą zostać zgłoszone do celów sprawdzania kondycji, ale które nie mają wpływu na równoważenia zasobów klastra. Aby to zrobić, należy ustawić waga metryki na zero. Zalecamy rozpocząć wszystkie metryki o wadze wynoszącej zero i nie zwiększa wagi, dopóki nie masz pewności, zrozumieć wpływ waga metryki zasobu równoważenia dla klastra.

> [!TIP]
> Nie używaj zbyt wiele metryk ważonych. Może być trudne do zrozumienia, dlaczego wystąpień usługi są przenoszone równoważenia. Kilka metryk mogą długą drogę!

Wszystkie informacje, które można wskazać kondycję i wydajność aplikacji jest kandydatem do raportów dotyczących metryk i kondycji. **Licznik wydajności Procesora może informujące, jaki jest wykorzystywana w węźle, ale go nie informujące, czy określona usługa jest w dobrej kondycji, ponieważ wiele usług może działać na jednym węźle.** Jednak RPS, przetwarzanie, elementy, takie jak metryki i wszelkie opóźnienia żądania może wskazywać kondycji określonej usługi.

## <a name="service-fabric-support-logs"></a>Dzienniki pomocy technicznej usługi Service Fabric

Jeśli zachodzi potrzeba skontaktuj się z działem pomocy technicznej firmy Microsoft, aby uzyskać pomoc dotyczącą klastra usługi Azure Service Fabric, dzienniki pomocy technicznej prawie zawsze są wymagane. Jeśli klaster jest hostowana na platformie Azure, dzienniki pomocy technicznej są automatycznie konfigurowane i zebrane w ramach tworzenia klastra. Dzienniki są przechowywane na koncie dedykowanych dla magazynu w grupie zasobów klastra. Konto magazynu nie ma stałej nazwie, ale w ramach konta zostanie wyświetlony kontenerów obiektów blob i tabel o nazwach zaczynających *sieci szkieletowej*. Uzyskać informacji o konfigurowaniu kolekcji dzienników na potrzeby klastra autonomicznego, zobacz [tworzenie samodzielnego klastra usługi Azure Service Fabric i zarządzanie](service-fabric-cluster-creation-for-windows-server.md) i [ustawienia konfiguracji dla autonomicznego Windows cluster](service-fabric-cluster-manifest.md). Dla wystąpień usługi Service Fabric autonomicznych dzienniki powinny być przesyłane do udziału pliku lokalnego. Jesteś **wymagane** ma następujące dzienniki pomocy technicznej, ale nie są przeznaczone do można używać przez nikogo poza zespół obsługi klienta firmy Microsoft.

## <a name="measuring-performance"></a>Mierzenie wydajności

Zmierzyć wydajność klastra pomoże zrozumieć, jak jest w stanie obsłużyć obciążenia i sterować ich decyzji związanych z skalowania klastra (Zobacz więcej informacji na temat skalowania klastra [na platformie Azure](service-fabric-cluster-scale-up-down.md), lub [lokalnych](service-fabric-cluster-windows-server-add-remove-nodes.md)). Dane dotyczące wydajności jest również przydatne, gdy w porównaniu do akcji, które można swoje aplikacje i usługi oraz trwało, podczas analizowania dzienników w przyszłości. 

Aby uzyskać listę liczników wydajności zbierających dane, korzystając z usługi Service Fabric, zobacz [liczników wydajności w usłudze Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Poniżej przedstawiono dwie typowe sposoby, w których możesz skonfigurować zbieranie danych o wydajności dla klastra:

* **Przy użyciu agenta**  
Jest to preferowany sposób zbierania wydajności na komputerze, ponieważ agenci mają zwykle listę metryk maksymalną wydajność, które mogą być zbierane i jest stosunkowo łatwe procesowi wybrać metryki, które chcesz zbierać lub zmienić. Przeczytaj na temat usługi Azure Monitor oferty usługi Azure Monitor dzienników w usłudze Service Fabric [usługi Azure Monitor rejestruje integracji](service-fabric-diagnostics-event-analysis-oms.md) i [konfigurowania agenta usługi Log Analytics](../log-analytics/log-analytics-windows-agent.md) Aby dowiedzieć się więcej na temat agenta usługi Log Analytics, która jest jeden taki agent monitorowania, który jest w stanie wczytać dane wydajności dla maszyn wirtualnych klastra i wdrożone kontenery.

* **Liczniki wydajności do usługi Azure Table Storage**  
Można również wysłać metryki wydajności, tym samym magazynem tabeli jako zdarzeń. Wymaga to zmianę konfiguracji diagnostyki platformy Azure w celu zbierania liczników wydajności odpowiednich maszyn wirtualnych w klastrze i włączanie, aby wczytać statystyk platformy docker, jeśli będziesz wdrażać żadnych kontenerów. Przeczytaj o konfigurowaniu [liczników wydajności w WAD](service-fabric-diagnostics-event-aggregation-wad.md) w usłudze Service Fabric, aby skonfigurować zbieranie danych licznika wydajności.

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj na temat usługi Service Fabric [usługi Azure Monitor rejestruje integracji](service-fabric-diagnostics-event-analysis-oms.md) zbieranie danych diagnostycznych z klastra i utworzenia niestandardowych kwerend i alerty
* Dowiedz się więcej o usłudze Service Fabric w skompilowanych doświadczenie diagnostyczne [bazy danych EventStore](service-fabric-diagnostics-eventstore.md)
* Opisano niektóre [typowych scenariuszach diagnostycznych](service-fabric-diagnostics-common-scenarios.md) w usłudze Service Fabric
