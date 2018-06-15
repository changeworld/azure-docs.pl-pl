---
title: Ponowna konfiguracja w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Zrozumienie ponowna konfiguracja partycji w sieci szkieletowej usług
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
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212810"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Ponowna konfiguracja w sieci szkieletowej usług Azure
A *konfiguracji* jest zdefiniowany jako repliki i ich role w partycji usługi stanowej.

A *ponowna konfiguracja* jest proces przechodzenia do innej konfiguracji jedną konfigurację. Dobrym zmiany do zestawu replik dla partycji usługi stanowej. Stary konfiguracja jest nazywana *poprzedniej konfiguracji (komputer)*, i nosi nazwę nowej konfiguracji *bieżącej konfiguracji (DW)*. Protokół ponownej konfiguracji w sieci szkieletowej usług Azure umożliwia zachowanie spójności i obsługuje dostępności podczas zmiany do zestawu replik.

Menedżer trybu failover inicjuje reconfigurations w odpowiedzi na różne zdarzenia w systemie. Na przykład jeśli podstawowy nie powiedzie się następnie zmiana konfiguracji jest inicjowane wspieranie aktywne pomocnicze podstawowego. Innym przykładem jest w odpowiedzi na uaktualnień aplikacji, gdy mogą być konieczność przeniesienia serwera podstawowego do innego węzła, aby uaktualnić węzła.

## <a name="reconfiguration-types"></a>Typy ponownej konfiguracji
Reconfigurations można podzielić na dwa typy:

- Reconfigurations, gdy zmienia się serwerem podstawowym:
    - **Tryb failover**: tryb failover są reconfigurations w odpowiedzi na awarię podstawowej uruchomione.
    - **SwapPrimary**: zamiany są reconfigurations, gdy sieć szkieletowa usług musi przenieść podstawowe działa z jednego węzła do innego, zazwyczaj w odpowiedzi na Równoważenie obciążenia lub uaktualnienia.

- Reconfigurations, w którym nie zmienia się serwerem podstawowym.

## <a name="reconfiguration-phases"></a>Ponowna konfiguracja fazy
Zmiana konfiguracji przebiega w kilku etapach:

- **Phase0**: w tej fazie odbywa się w podstawowej wymiany reconfigurations gdzie bieżącej podstawowej przenosi jego stan na nową podstawową i przejść do aktywne pomocnicze.

- **Fazy 1.**: w tej fazie odbywa się podczas reconfigurations, gdy zmienia się serwerem podstawowym. W tej fazie sieci szkieletowej usług identyfikuje prawidłowe podstawowej między bieżącym replik. Ta faza nie jest wymagana podczas wymiany podstawowej reconfigurations, ponieważ nową podstawową został już wybrany. 

- **Fazy 2**: w tej fazie sieci szkieletowej usług gwarantuje, że wszystkie dane są dostępne w większości replik bieżącej konfiguracji.

Istnieje kilka faz, które są tylko do użytku wewnętrznego.

## <a name="stuck-reconfigurations"></a>Reconfigurations zablokowane
Można uzyskać reconfigurations *zablokował* z różnych przyczyn. Typowe przyczyny są między innymi:

- **Repliki w dół**: niektóre fazy ponowna konfiguracja wymaga większości replik w konfiguracji należy się.
- **Problemy z siecią lub komunikacji**: Reconfigurations wymagają połączenia z siecią między węzłami w różnych.
- **Błędy interfejsu API**: protokół ponowna konfiguracja wymaga, aby implementacji usługi Zakończ niektórych interfejsów API. Na przykład nie ramach token anulowania w niezawodnej usługi powoduje, że reconfigurations SwapPrimary do zatrzymywane.

Użyj raportów o kondycji z składników systemu, takich jak System.FM, System.RA i System.RAP, aby zdiagnozować w przypadku, gdy zmiana konfiguracji utkwiła. [Strony raportu kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) opisano te raporty kondycji.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących pojęć sieci szkieletowej usług zobacz następujące artykuły:

- [Cykl życia usług Reliable Services — C#](service-fabric-reliable-services-lifecycle.md)
- [Raportów o kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md)
