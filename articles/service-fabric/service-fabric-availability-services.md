---
title: Dostępność usługi Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano wykrywania usterek, pracy awaryjnej i recovery services
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: dd10af0d3c8a57168a27a039286ea0ec4c1dad02
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662754"
---
# <a name="availability-of-service-fabric-services"></a>Dostępność usługi Service Fabric
Ten artykuł zawiera omówienie sposobu usługi Azure Service Fabric obsługuje dostępności usługi.

## <a name="availability-of-service-fabric-stateless-services"></a>Dostępność usługi bezstanowej usługi Service Fabric
Usługi Service Fabric może być bezstanowa lub stanowa. Usługa bezstanowa jest usługą aplikacji, która nie ma [stan lokalnego](service-fabric-concepts-state.md) , które musi być wysoce niedostępne lub niepewne.

Tworzenie bezstanowej usługi wymaga zdefiniowania `InstanceCount`. Liczba wystąpień definiuje liczbę wystąpień usługi bezstanowej logikę aplikacji, która powinna być uruchomiona w klastrze. Zwiększenie liczby wystąpień jest zalecanym sposobem skalowania usługi bezstanowej.

W przypadku awarii instancji o nazwie — usługę bezstanową nowe wystąpienie jest tworzona w uprawnionych węzła w klastrze. Na przykład wystąpienie usługi bezstanowej może zakończyć się niepowodzeniem w węźle Node1 i utworzona ponownie na komputerze Węzeł5.

## <a name="availability-of-service-fabric-stateful-services"></a>Dostępność usług stanowych usługi Service Fabric
Usługa stanowa ma stan skojarzonych z nim. W usłudze Service Fabric usługa stanowa ma formę zestawu replik. Każdej repliki jest uruchomione wystąpienie kodu usługi. Replika ma również kopię stan dla tej usługi. Odczyt i zapis, operacje są wykonywane w jednej replice, o nazwie *głównej*. Zmiany stanu z zapisu operacji *replikowane* do innych replik w zestawie, o nazwie *aktywne pomocnicze bazy danych*i stosowane. 

Może istnieć tylko jedna replika podstawowa, ale może istnieć wiele replik aktywnej pomocniczej. Liczba aktywnych repliki pomocnicze można skonfigurować, większej liczby replik może tolerować większą liczbę jednoczesnych oprogramowania i awarii sprzętu.

Jeśli replika podstawowa ulegnie awarii, Usługa Service Fabric zapewnia jedną z aktywnej pomocniczej repliki nową podstawową replikę. Ta replika pomocnicza Active ma już zaktualizowaną wersję stanu, za pośrednictwem *replikacji*, i może kontynuować przetwarzanie dalszych operacji odczytu/zapisu. Ten proces jest nazywany *ponownej konfiguracji* i dokładniejszym opisem zawartym w [ponownej konfiguracji](service-fabric-concepts-reconfiguration.md) artykułu.

Koncepcja jest podstawowej maszyny wirtualnej lub aktywnej pomocniczej repliki jest znany jako *Rola repliki*. Te repliki są dokładniejszym opisem zawartym w [repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md) artykułu. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pojęć usługi Service Fabric zobacz następujące artykuły:

- [Skalowanie usługi Service Fabric](service-fabric-concepts-scalability.md)
- [Partycjonowanie usługi Service Fabric](service-fabric-concepts-partitioning.md)
- [Definiowanie stanu i zarządzanie nim](service-fabric-concepts-state.md)
- [Usługi Reliable Services](service-fabric-reliable-services-introduction.md)

