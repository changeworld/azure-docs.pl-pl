---
title: Maszyn wirtualnych i typy węzłów sieci szkieletowej usług Azure skalowanie zestawów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawia węzeł sieci szkieletowej usług Azure, typy odnoszą się do skalowania maszyny wirtualnej, a na połączenie zdalne skali zestawu wystąpienia lub węzła klastra.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: ce3b3e6f3c7ee0c7198c27ed0ca0e3610d26f143
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Maszyn wirtualnych i typy węzłów sieci szkieletowej usług Azure skalowanie zestawów
[Zestawy skalowania maszyny wirtualnej](/azure/virtual-machine-scale-sets) są zasoby obliczeniowe platformy Azure. Zestawy skalowania służy do wdrażania i zarządzania nimi jako zestaw kolekcji maszyn wirtualnych. Każdy typ węzła zdefiniowanej w klastrze usługi sieć szkieletowa usług Azure konfiguruje oddzielne skali.  Ustaw środowisko uruchomieniowe usługi sieć szkieletowa zainstalowany na wszystkich maszynach wirtualnych w skali. Można niezależnie skalować każdy typ węzła w górę lub w dół, zmienianie wersji systemu operacyjnego działającego na każdym węźle klastra, mają różne zestawy otwartych portów i metryki pojemności różnych.

Na poniższej ilustracji przedstawiono klastra, która ma dwa typy węzłów, o nazwie frontonu i wewnętrznej bazy danych. Każdy typ węzła ma pięć węzłów.

![Klastra, która ma dwa typy węzłów][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapowanie wystąpień zestawu skalowania maszyn wirtualnych do węzłów
Jak pokazano na poprzedniej ilustracji, wystąpienia zestawu skali rozpoczęli wystąpienie 0, a dopiero potem zwiększyć jej przez 1. Numerowanie znajduje odzwierciedlenie w nazwach węzła. Na przykład węzeł BackEnd_0 jest wystąpienie 0 zestaw skali zaplecza. Ten zestaw skalowania określonego ma pięć wystąpień, o nazwie BackEnd_0, BackEnd_1 BackEnd_2, BackEnd_3 i BackEnd_4.

Skalowanie w górę zestaw skalowania utworzono nowe wystąpienie. Nazwa nowego wystąpienia zestawu skali jest zwykle zestawu skali nazwy oraz dalej liczby wystąpień. W naszym przykładzie jest BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapowania usługi równoważenia obciążenia zestaw skalowania typy węzła i skalowanie zestawów
Jeśli wdrażane klastrem w portalu Azure lub użyć przykładowego szablonu usługi Azure Resource Manager, są wyświetlane wszystkie zasoby w grupie zasobów. Widać usługi równoważenia obciążenia dla każdego typu zestawu lub węzeł skali. Nazwa modułu równoważenia obciążenia jest używany następujący format: **LB -&lt;Nazwa typu węzła&gt;**. Przykładem jest LB-sfcluster4doc-0, jak pokazano na poniższej ilustracji:

![Zasoby][Resources]


## <a name="next-steps"></a>Kolejne kroki
* Zobacz [omówienie funkcji "Wdrażanie z dowolnego miejsca" i porównanie z klastrami zarządzane Azure](service-fabric-deploy-anywhere.md).
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* [Połączenie zdalne](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) do wystąpienia zestawu skali określonych
* [Zaktualizuj wartości zakresu portów protokołu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) w klastrze maszyn wirtualnych po wdrożeniu
* [Zmień nazwa użytkownika i hasło](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) klastra maszyny wirtualne

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
