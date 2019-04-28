---
title: Monitorowanie i Diagnostyka w aplikacjach usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Więcej informacji na temat monitorowania i diagnozowania aplikacji usługi Service Fabric siatki na platformie Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 6166808c85bbee9465a8fa12332afe2163027982
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810619"
---
# <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka
Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Monitorowanie i diagnostyka usługi Service Fabric siatki dzieli się na trzy główne typy danych diagnostycznych:

- Dzienniki aplikacji — są one zdefiniowane jako dzienniki z konteneryzowanych aplikacji, w jaki sposób ma instrumentacji aplikacji (np. dzienniki platformy docker)
- Zdarzenia platformy — zdarzeń z odpowiednich operacji usługi kontenera platformy siatki aktualnie w tym kontenerze aktywacji, dezaktywacji i kończenie działania.
- Metryki kontenera — metryki użycia i wydajności zasobów dla kontenerów (statystyk platformy docker)

W tym artykule opisano opcje monitorowania i diagnostyki dla najnowszej wersji zapoznawczej dostępne.

## <a name="application-logs"></a>Dzienniki aplikacji

Możesz wyświetlić dzienniki platformy docker z od wdrożonych kontenerów na podstawie poszczególnych kontenerów. W modelu aplikacji usługi Service Fabric siatki każdy kontener jest pakietu kodu w aplikacji. Aby wyświetlić skojarzone dzienniki przy użyciu pakietu kodu, użyj następującego polecenia:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Można uzyskać nazwy replik, można użyć polecenia "az siatki replik usługi". Nazwy repliki są zwiększenie liczby z 0.*

Poniżej przedstawiono, jak to wygląda przy przeglądaniu dzienniki z kontenerów VotingWeb.Code z aplikacji do głosowania:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md)
