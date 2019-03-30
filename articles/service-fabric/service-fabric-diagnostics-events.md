---
title: Usługa Azure Service Fabric zdarzeń | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zdarzenia usługi Service Fabric udostępniane poza pole, które ułatwiają monitorowanie klastra usługi Azure Service Fabric.
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
ms.openlocfilehash: b4270b9438a397ec09537c9d6343515ebc21af98
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665342"
---
# <a name="service-fabric-events"></a>Zdarzenia usługi Service Fabric 

Platformy usługi Service Fabric zapisuje kilka zdarzeń ze strukturą kluczowych działań operacyjnych dzieje się w klastrze. Te w zakresie od Uaktualnianie klastra na decyzje dotyczące umieszczenia repliki. Każde zdarzenie, czy usługi Service Fabric udostępnia mapuje do jednej z następujących jednostek w klastrze:
* Klaster
* Aplikacja
* Usługa
* Partycja
* Replika 
* Kontener

Aby wyświetlić pełną listę zdarzeń udostępnianych przez platformę - [zdarzenia listy usługi Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Poniżej przedstawiono niektóre przykładowe scenariusze, że powinny zostać wyświetlone zdarzenia dla klastra. 
* Zdarzenia cyklu życia węzłów: węzły pojawiają się, przestaną działać, skalowanie w pionie i poziomie, uruchom ponownie i są aktywowane dezaktywowane, te zdarzenia zostaną ujawnione pokazujący, co się stało i pomóc w zidentyfikowaniu, jeśli coś się stało z samej maszyny lub wystąpił interfejsu API, który został wywoływane za pośrednictwem SF do modyfikowania stan węzła.
* Uaktualnienie klastra: uaktualnienie klastra (SF wersji zmiany lub konfiguracji), zostanie wyświetlony uaktualnienia zainicjować, wdrażać przy użyciu każdej z domen uaktualnienia, a następnie ukończ (lub wycofywania). 
* Uaktualnienia aplikacji: Podobnie jak Uaktualnianie klastra ma kompleksowy zestaw zdarzeń, jak uaktualnienie jest wprowadzane. Te zdarzenia mogą być przydatne do zrozumienia, jeśli uaktualnienie zostało zaplanowane, bieżący stan uaktualnienia oraz ogólną sekwencję zdarzeń. Jest to przydatne dla patrząc Wstecz, aby zobaczyć, co uaktualnienia zostały wycofane pomyślnie, lub czy został wyzwolony wycofywania.
* Wdrażanie aplikacji/usługi / usuwania: dostępne są zdarzenia dla każdej aplikacji, usług i kontenerów, są utworzone lub usunięte i przydatne podczas skalowania do wewnątrz lub na zewnątrz np. zwiększenie liczby replik
* Partycja przenosi (ponownej konfiguracji): zawsze, gdy stanowych partycji odbywa się przez rekonfiguracji (zmiany w zestawie), zdarzenie jest rejestrowane. Jest to przydatne, jeśli chcesz zrozumieć, jak często zmiany lub przejść w tryb failover zestawu replik partycji lub śledzenia, który węzeł była uruchomiona Twoja repliki podstawowej w dowolnym momencie w czasie.
* Zdarzenia chaos: korzystając z usługi Service Fabric [Chaos](service-fabric-controlled-chaos.md) usługi, zobaczysz zdarzenia, za każdym razem, gdy usługa jest uruchomiona lub zatrzymana lub w przypadku, gdy jej wprowadza błędów w systemie.
* Zdarzenia dotyczące kondycji: Usługa Service Fabric udostępnia zdarzenia dotyczące kondycji za każdym razem, gdy tworzony jest ostrzeżenie lub błąd raport o kondycji jednostki powraca do stanu kondycji OK lub raport o kondycji wygasa. Te zdarzenia są bardzo przydatne do śledzenia kondycji historyczne statystyki dla jednostki. 

## <a name="how-to-access-events"></a>Jak uzyskać dostęp do zdarzeń

Istnieje kilka różnych sposobów, za pomocą których zdarzenia usługi Service Fabric można uzyskać dostęp:
* Zdarzenia są rejestrowane za pomocą standardowych kanałów, takich jak dzienniki zdarzeń ETW/Windows i mogą być wizualizowane przez dowolnego narzędzia do monitorowania, który obsługuje takie jak dzienniki usługi Azure Monitor. Domyślnie klastry utworzone w portalu ma włączoną diagnostyką i agenta diagnostyki Windows Azure, wysyłanie zdarzeń do usługi Azure table storage, ale nadal konieczne zintegrowanie to z zasobem usługi log analytics. Dowiedz się więcej o konfigurowaniu [agenta funkcji Diagnostyka Azure](service-fabric-diagnostics-event-aggregation-wad.md) do modyfikowania konfiguracji diagnostyki do klastra, aby wczytać więcej dzienników lub liczniki wydajności i [usługi Azure Monitor rejestruje integracji](service-fabric-diagnostics-event-analysis-oms.md)
* Usługa bazy danych EventStore interfejsów API Rest, dzięki czemu możesz zbadać klastra bezpośrednio lub za pomocą biblioteki klienckiej usługi Service Fabric. Zobacz [interfejsów API bazy danych EventStore zapytań dla zdarzenia klastra](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Kolejne kroki
* Więcej informacji na monitorowanie klastra — [monitorowanie klastra i platformy](service-fabric-diagnostics-event-generation-infra.md).
* Dowiedz się więcej o usłudze bazy danych EventStore — [Omówienie usługi bazy danych EventStore](service-fabric-diagnostics-eventstore.md)
