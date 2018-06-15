---
title: Dostępność usługi sieć szkieletowa usług | Dokumentacja firmy Microsoft
description: W tym artykule opisano wykrywania błędów, trybu failover i odzyskiwania usługi
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0794c0e190ecbc4cce808e94f98bb0ac63d1075a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206253"
---
# <a name="availability-of-service-fabric-services"></a>Dostępność usług sieci szkieletowej usług
Ten artykuł zawiera omówienie zachowywanie dostępności usługi sieć szkieletowa usług Azure.

## <a name="availability-of-service-fabric-stateless-services"></a>Dostępność usług bezstanowych sieci szkieletowej usług
Usługi sieci szkieletowej usług mogą być stanowego lub bezstanowego. Usługi bezstanowej jest usługą aplikacji, która nie ma [stanu lokalnego](service-fabric-concepts-state.md) który musi być wysoce niedostępne lub niepewne.

Tworzenie usługi bezstanowej wymaga zdefiniowania `InstanceCount`. Liczba wystąpień definiuje liczbę wystąpień usługi bezstanowej logiki aplikacji, która powinna być uruchomiona w klastrze. Zwiększenie liczby wystąpień jest zalecanym sposobem skalowania usługi bezstanowej.

W przypadku awarii wystąpienia o nazwie — usługę bezstanową nowe wystąpienie jest tworzony na kwalifikujące się węzła w klastrze. Na przykład wystąpienie usługi bezstanowej może zakończyć się niepowodzeniem w węźle Node1 i zostać ponownie utworzone na komputerze Węzeł5.

## <a name="availability-of-service-fabric-stateful-services"></a>Dostępność usług stanowych sieci szkieletowej usług
Usługa stanowa ma stan skojarzony z nim. W sieci szkieletowej usług usługi stanowej ma formę zestawu replik. Każda replika jest uruchomione wystąpienie kodu usługi. Replika ma również kopię stan dla tej usługi. Odczyt i zapis operacje są wykonywane na jednej repliki, nazywany *głównej*. Zapisać zmiany stanu z działania są *replikowane* do innych replik w zestawie replik, nazywany *aktywne pomocnicze bazy danych*i stosowane. 

Może istnieć tylko jedna replika podstawowa, ale może istnieć wiele replik aktywnej pomocniczej. Liczba aktywnych replik pomocniczych można skonfigurować, większej liczby replik może tolerować większą liczbę jednoczesnych oprogramowania i awarie sprzętowe.

Jeśli replika podstawowa ulegnie awarii, Service Fabric sprawia, że jedną z aktywnej pomocniczej repliki nową podstawową replikę. Ta replika pomocnicza Active ma już zaktualizowaną wersję stanu, za pomocą *replikacji*, i będzie możliwe kontynuowanie, dalsze przetwarzanie operacji odczytu/zapisu. Ten proces jest nazywany *ponowna konfiguracja* i dalsze opisanych w [ponowna konfiguracja](service-fabric-concepts-reconfiguration.md) artykułu.

Pojęcie repliki podstawowej lub dodatkowej Active, jest nazywany *roli repliki*. Te repliki są opisane dalej w [repliki i wystąpień](service-fabric-concepts-replica-lifecycle.md) artykułu. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących pojęć sieci szkieletowej usług zobacz następujące artykuły:

- [Skalowanie usługi w sieci szkieletowej usług](service-fabric-concepts-scalability.md)
- [Partycjonowanie usług sieci szkieletowej usług](service-fabric-concepts-partitioning.md)
- [Definiowanie i stan zarządzania](service-fabric-concepts-state.md)
- [Usługi Reliable Services](service-fabric-reliable-services-introduction.md)

