---
title: Zdarzenia sieci szkieletowej usług Azure
description: Dowiedz się więcej o zdarzeniach sieci szkieletowej usług dostarczonych po wyjęciu z pudełka, aby ułatwić monitorowanie klastra sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451727"
---
# <a name="service-fabric-events"></a>Zdarzenia usługi Service Fabric 

Platforma sieci szkieletowej usług zapisuje kilka zdarzeń strukturalnych dla kluczowych działań operacyjnych w klastrze. Obejmują one od uaktualnień klastra do decyzji o umieszczeniu repliki. Każde zdarzenie udostępniane przez sieci szkieletowe usługi mapuje jedną z następujących jednostek w klastrze:
* Klaster
* Aplikacja
* Usługa
* Partycja
* Replika 
* Kontener

Aby wyświetlić pełną listę zdarzeń ujawnionych przez platformę — [lista zdarzeń sieci szkieletowej usług](service-fabric-diagnostics-event-generation-operational.md).

Oto kilka przykładów scenariuszy, dla których powinny być widoczne zdarzenia w klastrze. 
* Zdarzenia cyklu życia węzła: jak węzły pojawiają się, zejdź w dół, skalowanie w/wyjęcie, uruchom ponownie i są aktywowane / dezaktywowane, zdarzenia te będą widoczne pokazując, co się stało i pomóc zidentyfikować, czy coś jest nie tak z samego komputera lub jeśli nie było interfejsu API, który został wywołany za pośrednictwem SF, aby zmodyfikować stan węzła.
* Uaktualnienie klastra: w miarę uaktualniania klastra (zmiana wersji SF lub zmiany konfiguracji) zostanie wyświetlone zainicjowanie uaktualnienia, przejście przez każdą domenę uaktualnienia i ukończenie (lub wycofanie). 
* Uaktualnienia aplikacji: podobnie jak uaktualnienia klastra, istnieje kompleksowy zestaw zdarzeń, jak rolkach uaktualnienia przez. Zdarzenia te mogą być przydatne do zrozumienia, kiedy uaktualnienie zostało zaplanowane, bieżący stan uaktualnienia i ogólną sekwencję zdarzeń. Jest to przydatne do spojrzenia wstecz, aby zobaczyć, jakie uaktualnienia zostały wdrożone pomyślnie lub czy wycofywanie zostało wyzwolone.
* Wdrażanie/usuwanie aplikacji/usługi: występują zdarzenia dla każdej aplikacji, usługi i kontenera, tworzone lub usuwane i przydatne podczas skalowania lub wysuwania np.
* Przenoszenie partycji (ponowna konfiguracja): za każdym razem, gdy partycja stanowa przechodzi przez ponowną konfigurację (zmiana w zestawie replik), zdarzenie jest rejestrowane. Jest to przydatne, jeśli próbujesz zrozumieć, jak często zestaw replik partycji zmienia się lub przechodzi w błąd, lub śledzić, który węzeł był uruchomiony repliki podstawowej w dowolnym momencie w czasie.
* Zdarzenia chaosu: podczas korzystania z usługi [Chaos](service-fabric-controlled-chaos.md) sieci szkieletowej usług, zobaczysz zdarzenia za każdym razem, gdy usługa jest uruchomiona lub zatrzymana lub gdy wstrzykuje błąd w systemie.
* Zdarzenia kondycji: Sieci szkieletowej usług udostępnia zdarzenia kondycji za każdym razem, gdy zostanie utworzony raport o kondycji ostrzeżenie lub błąd lub jednostka wraca do stanu kondycji OK lub wygasa raport kondycji. Te zdarzenia są bardzo pomocne do śledzenia historycznych statystyk kondycji dla jednostki. 

## <a name="how-to-access-events"></a>Jak uzyskać dostęp do wydarzeń

Istnieje kilka różnych sposobów, dzięki którym można uzyskać dostęp do zdarzeń sieci szkieletowej usług:
* Zdarzenia są rejestrowane za pośrednictwem standardowych kanałów, takich jak dzienniki zdarzeń ETW/Windows i mogą być wizualizowane przez dowolne narzędzie do monitorowania, które obsługuje te, takie jak dzienniki usługi Azure Monitor. Domyślnie klastry utworzone w portalu mają włączona diagnostyka i agent diagnostyki systemu Windows Azure wysyłający zdarzenia do magazynu tabel platformy Azure, ale nadal musisz zintegrować to z zasobem analizy dzienników. Dowiedz się więcej o konfigurowaniu [agenta diagnostyki platformy Azure](service-fabric-diagnostics-event-aggregation-wad.md) w celu modyfikowania konfiguracji diagnostyki klastra w celu pobrania większej liczby dzienników lub liczników wydajności oraz [integracji dzienników usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* Interfejsy API odpoczynku usługi EventStore, które umożliwiają bezpośrednie wykonywanie zapytań do klastra lub za pośrednictwem biblioteki klienta sieci szkieletowej usług. Zobacz [Query EventStore interfejsów API dla zdarzeń klastra](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat monitorowania klastra — [monitorowanie klastra i platformy](service-fabric-diagnostics-event-generation-infra.md).
* Dowiedz się więcej o usłudze EventStore — [eventstore — omówienie usługi](service-fabric-diagnostics-eventstore.md)
