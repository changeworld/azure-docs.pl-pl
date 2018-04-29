---
title: Zdarzenia sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zdarzeń usługi sieć szkieletowa fabrycznej ułatwiają monitorowanie klastra usługi sieć szkieletowa usług Azure.
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
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 6379f463d5c2e9027e1d8d4dd90db61cb72cdd44
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="service-fabric-events"></a>Zdarzenia sieci szkieletowej usług 

Platformy Service Fabric zapisuje kilka zdarzeń strukturalnych dla klucza operacyjne czynności wykonywane w klastrze. Te należeć do zakresu od uaktualnienia klastra na decyzje dotyczące umieszczenia repliki. Każde zdarzenie, że sieć szkieletowa usług przedstawia mapy do jednej z następujących jednostek w klastrze:
* Klaster
* Aplikacja
* Usługa
* Partycja
* Replika 
* Kontener

Aby wyświetlić pełną listę zdarzeń udostępniany przez platformę - [zdarzenia listy usługi sieć szkieletowa](service-fabric-diagnostics-event-generation-operational.md).

Poniżej przedstawiono niektóre przykładowe scenariusze ważne, czy powinien zostać wyświetlony zdarzenia w klastrze. 
1. Zdarzenia cyklu życia węzła: jako węzły znaleziona, przestaną działać, są aktywowane dezaktywowane lub ponownego uruchomienia, zdarzenia mają być widoczne, wyświetlanie, co się stało i zidentyfikować jeśli coś się stało z samej maszyny lub wystąpił interfejs API, który został wywołany za pomocą rz do Zmodyfikuj stan węzła.
1. Uaktualnianie klastra: uaktualnienie klastra (rz wersji lub konfiguracji zmian), zostanie wyświetlony uaktualnienia inicjowania, Przywróć przez każdego użytkownika UDs i ukończyć (lub wycofywania). 
1. Uaktualnienia aplikacji: podobnie do klastra uaktualnia, brak rozbudowany zestaw zdarzeń uaktualnienia przedstawia za pośrednictwem. Zdarzenia te mogą być przydatne zrozumienie, kiedy uaktualnienie zostało zaplanowane, bieżący stan uaktualnienia, a ogólną sekwencję zdarzeń. Jest to przydatne w przypadku wyszukiwania wstecz zobaczyć, jakie uaktualnienia zostały wycofane pomyślnie.
1. Wdrażanie aplikacji/usługi / usuwania: występują zdarzenia dla każdej aplikacji, usług i kontener, tworzona lub usuwana.
1. Partycja przenosi (ponownej konfiguracji): gdy stanowe partycji odbywa się przez rekonfiguracji (zmiany w zestawie replik), zdarzenie jest rejestrowane. Jest to przydatne, jeśli chcesz zrozumieć, jak często zestawie replik partycji jest zmieniany lub śledzenia, który węzeł został uruchomiony z repliki podstawowej w dowolnym momencie w czasie.
1. Zdarzenia chaos: korzystając z usługi Service Fabric [Chaos](service-fabric-controlled-chaos.md) service, zobaczysz zdarzenia za każdym razem, gdy usługa jest uruchomiona lub zatrzymana lub jej injects błąd w systemie.
1. Zdarzenia kondycji: Sieć szkieletowa usług przedstawia zdarzenia kondycji pojawi się ostrzeżenie lub błąd raport o kondycji jest tworzony, lub jednostki powraca do stanu kondycji OK lub raport o kondycji wygasa. Te zdarzenia są bardzo przydatne śledzić statystyki kondycji historycznych jednostki. 

## <a name="how-to-access-events"></a>Jak uzyskać dostępu do zdarzeń

Istnieją na kilka różnych sposobów, za pomocą których mogą uzyskiwać zdarzenia sieci szkieletowej usług:
* za pośrednictwem kanału operacyjną. Można je zbieranych za pośrednictwem rozszerzenia diagnostyki Azure i wysyłane do tabeli magazynu do użycia lub wprowadzanie do narzędzia, takiego jak OMS analizy dzienników. "Diagnostyki" jest włączona w klastrze, agenta diagnostyki Azure w klastrze jest wdrożony i jest domyślnie skonfigurowany tak, aby odczytać w dziennikach z operacyjnej kanału. Dowiedz się więcej o konfigurowaniu [agenta diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md) do modyfikacji konfiguracji diagnostyki klastra do pobrania więcej dzienników lub liczniki wydajności. 
* za pomocą interfejsów API Rest usługi EventStore umożliwiające zapytania klastra, bezpośrednio lub za pomocą biblioteki klienta usługi sieci szkieletowej. Zobacz [interfejsy API EventStore zapytania do zdarzenia klastra](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Kolejne kroki
* Więcej informacji na monitorowanie klastra - [monitorowania klastra i platforma](service-fabric-diagnostics-event-generation-infra.md).
* Dowiedz się więcej o usłudze EventStore - [EventStore usług — omówienie](service-fabric-diagnostics-eventstore.md)
