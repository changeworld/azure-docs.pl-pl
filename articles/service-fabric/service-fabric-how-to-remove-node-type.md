---
title: Jak zdalnych węzeł typu w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usunąć typ węzła w usłudze Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980858"
---
# <a name="remove-a-service-fabric-node-type"></a>Usuń typ węzła usługi Service Fabric

Użyj [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) można usunąć typu węzła usługi Service Fabric.

Dostępne są następujące dwie operacje, które występują, gdy jest wywoływana Remove-AzureRmServiceFabricNodeType:
1.  Maszyna wirtualna skalowania Ustaw (zestawu skalowania maszyn wirtualnych) za typ węzła jest usuwany.
2.  Dla wszystkich węzłów w ramach tego typu węzła cały stan dla tego węzła jest usuwany z systemu. W przypadku usług w tym węźle, następnie usług są najpierw przenieść do innego węzła. Jeśli Menedżer klastra nie można odnaleźć węzła dla repliki/usługi, operacja jest opóźnione zablokowane.

> [!NOTE]
> Za pomocą polecenie Remove-AzureRmServiceFabricNodeType Usuń typ węzła z klastra produkcyjnego nie jest zalecane do użycia na częste. W tym przypadku jest to bardzo niebezpieczne polecenie jako zasadniczo usuwa zasobów zestawu skalowania maszyn wirtualnych za modułem typu węzła. Po wywołaniu Remove-AzureRmServiceFabricNodeType system nie ma możliwości znać, jeśli Ci na zachowaniu usuwania bezpieczne. 

## <a name="durability-characteristics"></a>Charakterystyka niezawodności
Bezpieczeństwo ma priorytet nad szybkość, korzystając z Remove-AzureRmServiceFabricNodeType. Na poziomie Silver lub Gold [charakterystyka niezawodności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) są zalecane, ponieważ:
- Brązowy daje żadnej gwarancji, zapisywanie informacji o stanie.
- Silver i Gold trwałości pułapki wszelkie zmiany do zestawu skalowania maszyn wirtualnych.
- Złoty oferuje również kontrolę nad Azure aktualizuje poniżej zestawu skalowania maszyn wirtualnych.

Usługi Service Fabric "służy do aranżacji" podstawowej zmiany i aktualizacje, aby dane nie zostaną utracone. Jednak po usunięciu węzła durability brązowa może utracić informacje o stanie. Jeśli usuwasz typu węzła podstawowego, a aplikacja jest bezstanowy, brązowy jest dopuszczalne. Po uruchomieniu obciążeń stanowych w środowisku produkcyjnym, minimalna konfiguracja powinna być Silver. Podobnie na potrzeby scenariuszy produkcyjnych typu węzła podstawowego powinny zawsze być na poziomie Silver lub Gold.

### <a name="more-about-bronze-durability"></a>Więcej informacji na temat brązowa trwałości

Podczas usuwania typ węzła, który jest brązowy, wszystkie węzły w typ węzła są wyłączane natychmiast. Usługi Service Fabric nie przechwytywania wszystkich aktualizacji zestawu skalowania maszyn wirtualnych węzłów brązowy, dlatego wszystkie maszyny wirtualne są wyłączane natychmiast. Jeśli masz nic stanowych w ramach tych węzłów, dane zostaną utracone. Teraz, nawet gdyby bezstanowym, wszystkie węzły w usłudze Service Fabric należą pierścienia, więc całe otoczenie mogą zostać utracone, które mogą mieć wpływ na sam klaster.

W przeciwieństwie do usuwania jednego węzła, ponieważ w teorii, można usunąć jednego węzła w danym momencie, poczekaj, aż repliki i usługami, aby przenieść, poczekaj, aż system w celu stabilizacji przed usunięciem innego węzła i tak dalej.  Jednak jeśli usuniesz jednocześnie jednocześnie kilka węzłów, klaster może być wyłączane (ponieważ usługa Service Fabric nie pułapki żadnych aktualizacji zestawu skalowania maszyn wirtualnych durability brązowa).

## <a name="recommended-node-type-removal-process"></a>Zalecane przez proces usuwania typu węzła

Aby usunąć typ węzła w najbardziej bezpieczny i szybki sposób:
1.  Jeśli używasz brązowa trwałości lub nie ma systemu, aby przenieść aplikacje, które zawierają informacje o stanie, które zostaną utracone usunięcie typu węzła, pierwszy pustymi danymi stanowych z węzłów, które będą mieć wpływ usunięcia typu węzła.
2.  Uruchom [ServiceFabricNodeState Usuń](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) na każdym z węzłów, które mają być usuwane.
3.  Uruchom [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) dla maszyn wirtualnych, które będą mieć wpływ usunięcia typu węzła.
4. Uruchom [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) można usunąć typu węzła.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o klastrze [charakterystyka niezawodności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Dowiedz się więcej o [typy węzłów i usługa Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md).
- Dowiedz się więcej o [skalowanie klastra usługi Service Fabric](service-fabric-cluster-scaling.md).