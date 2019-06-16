---
title: Ponowna konfiguracja w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Zrozumienie ponowna konfiguracja partycji w usłudze Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60882201"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Ponowna konfiguracja w usłudze Azure Service Fabric
A *konfiguracji* jest zdefiniowany jako repliki i ich ról dla partycji usługi stanowej.

A *ponownej konfiguracji* to proces przenoszenia jednej konfiguracji do innej konfiguracji. Wprowadza zmiany do zestawu replik dla partycji usługi stanowej. Starej konfiguracji jest nazywany *poprzedniej konfiguracji (PC)* , a nowa konfiguracja jest nazywana *bieżącej konfiguracji (DW)* . Protokół ponownej konfiguracji w usłudze Azure Service Fabric zachowuje spójności i utrzymuje dostępności podczas zmiany do zestawu replik.

Menedżer trybu failover inicjuje reconfigurations w odpowiedzi na różne zdarzenia w systemie. Na przykład jeśli podstawowy nie powiedzie się następnie ponownej konfiguracji przez jest inicjowane podwyższenie poziomu aktywnej pomocniczej do podstawowego. Innym przykładem jest w odpowiedzi na uaktualnień aplikacji, gdy może być konieczne przeniesienie podstawowego do innego węzła w celu uaktualniania węzła.

## <a name="reconfiguration-types"></a>Typy ponowna konfiguracja
Reconfigurations można podzielić na dwa typy:

- Gdy zmienia się podstawowym reconfigurations:
    - **Tryb failover**: Przejścia w tryb failover są reconfigurations w odpowiedzi na błąd działającego podstawowego.
    - **SwapPrimary**: Wymiany są reconfigurations, w którym usługi Service Fabric musi przenieść wykonywanie głównej z jednego węzła do innego, zazwyczaj w odpowiedzi na Równoważenie obciążenia lub uaktualnienia.

- Reconfigurations gdzie podstawowej nie ulega zmianie.

## <a name="reconfiguration-phases"></a>Ponowna konfiguracja fazy
Zmiana konfiguracji będzie kontynuowane w kilku fazach:

- **Phase0**: Ta faza odbywa się w reconfigurations wymiany primary, gdzie bieżący podstawowe przesyła jego stan nową podstawową i przejść do aktywnej pomocniczej.

- **Phase1**: Ta faza odbywa się podczas reconfigurations, gdy podstawowy ulegnie zmianie. W tej fazie usługi Service Fabric identyfikuje prawidłowy podstawowy między bieżącym replik. Ta faza nie jest wymagana podczas wymiany primary reconfigurations, ponieważ nową podstawową został już wybrany. 

- **Fazy 2**: W tej fazie usługi Service Fabric zapewnia, że wszystkie dane będą dostępne w większości replik bieżącej konfiguracji.

Istnieje kilka faz, które są tylko do użytku wewnętrznego.

## <a name="stuck-reconfigurations"></a>Zablokowane reconfigurations
Można uzyskać reconfigurations *została zablokowana* różnych powodów. Typowe przyczyny są między innymi:

- **Repliki w dół**: Niektóre etapy ponownej konfiguracji wymagają większość replik w konfiguracji rozpocząć.
- **Problemy z siecią lub komunikacji**: Reconfigurations wymagają łączności sieciowej między różnych węzłach.
- **Błędy interfejsu API**: Protokół ponownej konfiguracji wymaga, że implementacji usługi Zakończ niektórych interfejsów API. Na przykład nie zapewniane token anulowania, która w niezawodnej usługi powoduje, że reconfigurations SwapPrimary utknięcie.

Używanie raportów kondycji ze składników systemu, takie jak System.FM System.RA i System.RAP, aby zdiagnozować, w przypadku, gdy zmiana konfiguracji jest zablokowany. [Strony raportu kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) w tym artykule opisano te raporty kondycji.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pojęć usługi Service Fabric zobacz następujące artykuły:

- [Cykl życia usług Reliable Services — C#](service-fabric-reliable-services-lifecycle.md)
- [Raportów o kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md)
