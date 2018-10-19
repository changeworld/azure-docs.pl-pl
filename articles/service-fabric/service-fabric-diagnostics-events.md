---
title: Usługa Azure Service Fabric zdarzeń | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zdarzenia usługi Service Fabric udostępniane poza pole, które ułatwiają monitorowanie klastra usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: ca63d67f6d7c19b4ca6928c4cc0f9ccb06eace2b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402985"
---
# <a name="service-fabric-events"></a>Zdarzenia usługi Service Fabric 

Platformy usługi Service Fabric zapisuje kilka zdarzeń ze strukturą dla klucza operacyjne czynności wykonywane w klastrze. Te w zakresie od Uaktualnianie klastra na decyzje dotyczące umieszczenia repliki. Każde zdarzenie, czy usługi Service Fabric udostępnia mapuje do jednej z następujących jednostek w klastrze:
* Klaster
* Aplikacja
* Usługa
* Partycja
* Replika 
* Kontener

Aby wyświetlić pełną listę zdarzeń udostępnianych przez platformę - [zdarzenia listy usługi Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Poniżej przedstawiono kilka przykładów ważnych scenariuszy, że powinny zostać wyświetlone zdarzenia dla klastra. 
1. Zdarzenia cyklu życia węzłów: węzły pojawiają się, przestaną działać, są aktywowane dezaktywowane lub ponownego uruchomienia, zdarzenia zostaną ujawnione pokazujący, co się stało i pomóc w zidentyfikowaniu, jeśli coś się stało z samej maszyny lub wystąpił interfejsu API, która została wywołana przy użyciu SF do Zmodyfikuj stan węzła.
1. Uaktualnienie klastra: uaktualnienie klastra (SF wersji zmiany lub konfiguracji), zostanie wyświetlony uaktualnienia zainicjować, wdrażać przy użyciu każdego z domenami aktualizacji i wykonaj (lub wycofywania). 
1. Uaktualnienia aplikacji: Podobnie klastra uaktualnienie, jak uaktualnienie jest wprowadzane jest rozbudowany zestaw zdarzeń. Te zdarzenia mogą być przydatne do zrozumienia, jeśli uaktualnienie zostało zaplanowane, bieżący stan uaktualnienia oraz ogólną sekwencję zdarzeń. Jest to przydatne do wyszukiwania wstecz zobaczyć, jakie uaktualnienia zostały wycofane pomyślnie.
1. Wdrażanie aplikacji/usługi / usuwania: dostępne są zdarzenia, dla każdej aplikacji, usług i kontenerów, tworzona lub usuwana.
1. Partycja przenosi (ponownej konfiguracji): zawsze, gdy stanowych partycji odbywa się przez rekonfiguracji (zmiany w zestawie), zdarzenie jest rejestrowane. Jest to przydatne, jeśli chcesz zrozumieć, jak często zestaw replik partycji ulegnie zmianie lub śledzenia, który węzeł była uruchomiona Twoja repliki podstawowej w dowolnym momencie w czasie.
1. Zdarzenia chaos: korzystając z usługi Service Fabric [Chaos](service-fabric-controlled-chaos.md) usługi, zobaczysz zdarzenia, za każdym razem, gdy usługa jest uruchomiona lub zatrzymana lub w przypadku, gdy jej wprowadza błędów w systemie.
1. Zdarzenia dotyczące kondycji: Usługi Service Fabric udostępnia zdarzenia dotyczące kondycji za każdym razem, gdy tworzony jest ostrzeżenie lub błąd raport o kondycji jednostki powraca do stanu kondycji OK lub raport o kondycji wygasa. Te zdarzenia są bardzo przydatne do śledzenia kondycji historyczne statystyki dla jednostki. 

## <a name="how-to-access-events"></a>Jak uzyskać dostęp do zdarzeń

Istnieje kilka różnych sposobów, za pomocą których zdarzenia usługi Service Fabric można uzyskać dostęp:
* za pośrednictwem kanału operacyjnej. Można je zbieranych za pośrednictwem rozszerzenia diagnostyki Azure i wysyłane do tabeli magazynu w celu użycia lub przyjęciu przez narzędzia, takiego jak Azure Log Analytics. Po włączeniu "diagnostyki" dla klastra agenta funkcji Diagnostyka Azure jest wdrażana w klastrze i jest domyślnie skonfigurowana do odczytu w dziennikach operacyjnych kanału. Dowiedz się więcej o konfigurowaniu [agenta funkcji Diagnostyka Azure](service-fabric-diagnostics-event-aggregation-wad.md) do modyfikowania konfiguracji diagnostyki do klastra, aby wczytać więcej dzienników lub liczników wydajności. 
* za pomocą interfejsów API Rest usługi bazy danych EventStore umożliwiające zapytania klastra bezpośrednio lub za pomocą biblioteki klienckiej usługi Service Fabric. Zobacz [interfejsów API bazy danych EventStore zapytań dla zdarzenia klastra](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Kolejne kroki
* Więcej informacji na monitorowanie klastra — [monitorowanie klastra i platformy](service-fabric-diagnostics-event-generation-infra.md).
* Dowiedz się więcej o usłudze bazy danych EventStore — [Omówienie usługi bazy danych EventStore](service-fabric-diagnostics-eventstore.md)
