---
title: Usuń typ węzła w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usunąć typ węzła z klastra usługi Service Fabric działających na platformie Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 193a24aebff8f7de60752e53bbc1b18dd5c54f33
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482202"
---
# <a name="remove-a-service-fabric-node-type"></a>Usuń typ węzła usługi Service Fabric
W tym artykule opisano sposób skalowanie klastra usługi Azure Service Fabric, usuwając istniejący typ węzła z klastra. Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrażanie i zarządzanie kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure jest [konfigurowany jako zestaw skalowania oddzielnych](service-fabric-cluster-nodetypes.md). Każdy typ węzła może następnie być zarządzany oddzielnie. Po utworzeniu klastra usługi Service Fabric klaster można skalować w poziomie, usuwając typ węzła (zestaw skalowania maszyn wirtualnych) i wszystkich jego węzłów.  Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze.  Jak jest skalowana w klastrze, aplikacje będą skalowane automatycznie również.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Użyj [AzServiceFabricNodeType Usuń](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) można usunąć typu węzła usługi Service Fabric.

Są trzy operacje, które występują, gdy nosi nazwę usuwania AzServiceFabricNodeType:
1.  Maszyna wirtualna zestawie skalowania za modułem typ węzła jest usuwany.
2.  Typ węzła jest usuwany z klastra.
3.  Dla każdego węzła w ramach tego typu węzła całego stanu dla tego węzła jest usuwany z systemu. W przypadku usług w tym węźle, następnie usług są najpierw przenieść do innego węzła. Jeśli Menedżer klastra nie można odnaleźć węzła dla repliki/usługi, operacja jest opóźnione zablokowane.

> [!WARNING]
> Przy użyciu AzServiceFabricNodeType Usuń, aby usunąć typ węzła z klastra produkcyjnego nie jest zalecane do użycia na częste. Jest to niebezpieczne polecenie jak usuwa zasobu zestawu skalowania maszyny wirtualnej za typ węzła. 

## <a name="durability-characteristics"></a>Charakterystyka niezawodności
Bezpieczeństwo ma priorytet nad szybkość, korzystając z Remove AzServiceFabricNodeType. Typ węzła musi być na poziomie Silver lub Gold [poziom trwałości](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster), ponieważ:
- Brązowy daje żadnej gwarancji, zapisywanie informacji o stanie.
- Silver i Gold trwałości pułapki wszelkie zmiany w zestawie skalowania.
- Złoty oferuje również kontrolę nad Azure aktualizuje poniżej zestawu skalowania.

Usługi Service Fabric "służy do aranżacji" podstawowej zmiany i aktualizacje, aby dane nie zostaną utracone. Jednak po usunięciu węzła durability brązowa może utracić informacje o stanie. Jeśli usuwasz typu węzła podstawowego, a aplikacja jest bezstanowy, brązowy jest dopuszczalne. Po uruchomieniu obciążeń stanowych w środowisku produkcyjnym, minimalna konfiguracja powinna być Silver. Podobnie na potrzeby scenariuszy produkcyjnych typu węzła podstawowego powinny zawsze być na poziomie Silver lub Gold.

### <a name="more-about-bronze-durability"></a>Więcej informacji na temat brązowa trwałości

Podczas usuwania typ węzła, który jest brązowy, wszystkie węzły w typ węzła są wyłączane natychmiast. Usługi Service Fabric nie przechwytywania wszystkich aktualizacji zestawu skalowania węzłów brązowy, dlatego wszystkie maszyny wirtualne są wyłączane natychmiast. Jeśli masz nic stanowych w ramach tych węzłów, dane zostaną utracone. Teraz, nawet gdyby bezstanowym, wszystkie węzły w usłudze Service Fabric należą pierścienia, więc całe otoczenie mogą zostać utracone, który zdestabilizować samego klastra.

## <a name="recommended-node-type-removal-process"></a>Zalecane przez proces usuwania typu węzła

Aby usunąć typ węzła, uruchom [AzServiceFabricNodeType Usuń](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) polecenia cmdlet.  Polecenie cmdlet pobiera trochę czasu.  Następnie uruchom [ServiceFabricNodeState Usuń](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) na każdym z węzłów, które mają być usuwane.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o klastrze [charakterystyka niezawodności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Dowiedz się więcej o [typy węzłów i usługa Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md).
- Dowiedz się więcej o [skalowanie klastra usługi Service Fabric](service-fabric-cluster-scaling.md).
