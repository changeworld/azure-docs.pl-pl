---
title: Usuwanie typu węzła w usłudze Azure Service Fabric
description: Dowiedz się, jak usunąć typ węzła z klastra Service Fabric działającego na platformie Azure.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f3dc3210fdb436038174bb8d9347424f14d3faa3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464493"
---
# <a name="remove-a-service-fabric-node-type"></a>Usuwanie Service Fabric typu węzła
W tym artykule opisano sposób skalowania klastra Service Fabric platformy Azure przez usunięcie istniejącego typu węzła z klastra. Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno. Po utworzeniu klastra Service Fabric można skalować klaster w poziomie, usuwając typ węzła (zestaw skalowania maszyn wirtualnych) i wszystkie jego węzły.  Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.  W miarę skalowania klastra aplikacje są automatycznie skalowane.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Użyj [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) , aby usunąć typ węzła Service Fabric.

Trzy operacje występujące po wywołaniu metody Remove-AzServiceFabricNodeType są następujące:
1.  Zestaw skalowania maszyn wirtualnych za typem węzła jest usuwany.
2.  Typ węzła zostanie usunięty z klastra.
3.  Dla każdego węzła w ramach tego typu węzła, cały stan dla tego węzła zostanie usunięty z systemu. Jeśli w tym węźle znajdują się usługi, usługi są najpierw przenoszone do innego węzła. Jeśli Menedżer klastra nie może znaleźć węzła dla repliki/usługi, operacja zostanie opóźniona/zablokowana.

> [!WARNING]
> Nie zaleca się używania polecenia Remove-AzServiceFabricNodeType w celu usunięcia typu węzła z klastra produkcyjnego. Jest to niebezpieczne polecenie, ponieważ usuwa zasób zestawu skalowania maszyn wirtualnych za typem węzła. 

## <a name="durability-characteristics"></a>Charakterystyki trwałości
W przypadku korzystania z polecenia Remove-AzServiceFabricNodeType zabezpieczenia są ustalane jako priorytetowe. Typ węzła musi być [poziomem trwałości](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)Silver lub Gold, ponieważ:
- Pakiet Bronze nie zapewnia żadnych gwarancji dotyczących zapisywania informacji o stanie.
- Pułapki srebrne i złota są dowolnymi zmianami w zestawie skalowania.
- Gold oferuje również kontrolę nad aktualizacjami platformy Azure w zestawie skalowania.

Service Fabric "organizuje" podstawowe zmiany i aktualizacje, aby dane nie zostały utracone. Jednak po usunięciu węzła z trwałością Bronze można utracić informacje o stanie. Jeśli usuniesz typ węzła podstawowego, a aplikacja jest bezstanowa, akceptowalny jest brązowy. W przypadku uruchamiania obciążeń stanowych w środowisku produkcyjnym minimalna konfiguracja powinna być Silver. Podobnie w przypadku scenariuszy produkcyjnych typ węzła podstawowego powinien zawsze mieć wartość Silver lub Gold.

### <a name="more-about-bronze-durability"></a>Więcej informacji na temat trwałości Bronze

W przypadku usuwania typu węzła, który jest brązowy, wszystkie węzły w typie węzła zostaną natychmiast umieszczone w dół. Service Fabric nie jest Zalewka żadnych aktualizacji zestawu skalowania, a więc wszystkie maszyny wirtualne natychmiast przechodzą w dół. Jeśli w tych węzłach istniały stanowe elementy, dane są tracone. Teraz, nawet jeśli nie są bezstanowe, wszystkie węzły w Service Fabric biorą udział w pierścieniu, dzięki czemu cała klub może zostać utracona, co może spowodować utratę samego klastra.

## <a name="recommended-node-type-removal-process"></a>Zalecany proces usuwania typu węzła

Aby usunąć typ węzła, uruchom polecenie cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .  Wykonanie polecenia cmdlet zajmuje trochę czasu.  Gdy wszystkie maszyny wirtualne zostaną usunięte (reprezentowane jako "w dół"), w obszarze Sieć szkieletowa:/system/InfrastructureService/[nazwa NodeType] zostanie wyświetlony stan błędu.

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
```

Następnie można zaktualizować zasób klastra, aby usunąć typ węzła. Możesz użyć wdrożenia szablonu ARM lub edytować zasób klastra za pomocą [usługi Azure Resource Manager](https://resources.azure.com). Spowoduje to uruchomienie uaktualnienia klastra, co spowoduje usunięcie usługi sieci szkieletowej:/system/InfrastructureService/[nazwa NodeType], która jest w stanie błędu.

W Service Fabric Explorer nadal będą widoczne węzły "w dół". Uruchom [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) na każdym z węzłów, które chcesz usunąć.


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [charakterystyce trwałości](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)klastra.
- Dowiedz się więcej na temat [typów węzłów i Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md).
- Dowiedz się więcej na temat [skalowania klastra Service Fabric](service-fabric-cluster-scaling.md).
