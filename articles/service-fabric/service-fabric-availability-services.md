---
title: Dostępność usług sieci szkieletowej usług
description: W tym artykule opisano wykrywanie błędów, tryb failover i odzyskiwanie usługi w aplikacji sieci szkieletowej usługi Azure.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551866"
---
# <a name="availability-of-service-fabric-services"></a>Dostępność usług sieci szkieletowej usług
W tym artykule przedstawiono omówienie sposobu, w jaki usługa Azure Service Fabric zachowuje dostępność usługi.

## <a name="availability-of-service-fabric-stateless-services"></a>Dostępność usług bezstanowych sieci szkieletowej usług
Usługi sieci szkieletowej usług może być stanowe lub bezstanowe. Usługa bezstanowa jest usługą aplikacji, która nie ma [stanu lokalnego,](service-fabric-concepts-state.md) który musi być wysoce dostępne lub niezawodne.

Tworzenie usługi bezstanowej wymaga `InstanceCount`zdefiniowania pliku . Liczba wystąpień definiuje liczbę wystąpień logiki aplikacji usługi bezstanowej, która powinna być uruchomiona w klastrze. Zwiększenie liczby wystąpień jest zalecanym sposobem skalowania w poziomie usługi bezstanowej.

Gdy wystąpienie bezstanowej usługi z named nie powiedzie się, nowe wystąpienie jest tworzone w kwalifikującym się węźle w klastrze. Na przykład wystąpienie usługi bezstanowej może zakończyć się niepowodzeniem w węźle1 i zostać ponownie utworzone w węźle5.

## <a name="availability-of-service-fabric-stateful-services"></a>Dostępność usług stanowych sieci szkieletowej usług
Usługa stanowa ma stan skojarzony z nim. W sieci szkieletowej usług usługa stanowa jest modelowana jako zestaw replik. Każda replika jest uruchomionym wystąpieniem kodu usługi. Replika ma również kopię stanu dla tej usługi. Operacje odczytu i zapisu są wykonywane w jednej replice, zwanej *Podstawową*. Zmiany stanu z operacji zapisu są *replikowane* do innych replik w zestawie replik, o nazwie *Active Secondaries*, i stosowane. 

Może istnieć tylko jedna replika podstawowa, ale może istnieć wiele aktywnych replik pomocniczych. Liczba aktywnych replik pomocniczych jest konfigurowalna, a większa liczba replik może tolerować większą liczbę równoczesnych awarii oprogramowania i sprzętu.

Jeśli replika podstawowa uginie się, sieci szkieletowej usług sprawia, że jeden z aktywnych replik pomocniczych nowej repliki podstawowej. Ta aktywna replika pomocnicza ma już zaktualizowaną wersję stanu za pośrednictwem *replikacji*i może kontynuować przetwarzanie dalszych operacji odczytu/zapisu. Ten proces jest nazywany *rekonfiguracji* i jest opisany dalej w [rekonfiguracji](service-fabric-concepts-reconfiguration.md) artykułu.

Pojęcie repliki jest podstawowy lub aktywny pomocniczy, jest znany jako *roli repliki*. Repliki te są opisane dalej w [repliki i wystąpień](service-fabric-concepts-replica-lifecycle.md) artykułu. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat koncepcji sieci szkieletowej usług, zobacz następujące artykuły:

- [Skalowanie usług sieci szkieletowej usług](service-fabric-concepts-scalability.md)
- [Usługi partycjonowania usługi sieci szkieletowej](service-fabric-concepts-partitioning.md)
- [Definiowanie stanu i zarządzanie nim](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

