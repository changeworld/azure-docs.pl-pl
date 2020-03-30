---
title: Dodawanie typu węzła do klastra sieci szkieletowej usług Azure
description: Dowiedz się, jak skalować w poziomie klaster sieci szkieletowej usług, dodając zestaw skalowania maszyny wirtualnej.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 1e7bae89561d43d717eb4d15e95183761b077443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463979"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skalowanie klastra sieci szkieletowej usług przez dodanie zestawu skalowania maszyny wirtualnej
W tym artykule opisano sposób skalowania klastra sieci szkieletowej usług Azure przez dodanie nowego typu węzła do istniejącego klastra. Klaster sieci szkieletowej usług to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem. Zestawy skalowania maszyny wirtualnej to zasób obliczeniowy platformy Azure używany do wdrażania i zarządzania kolekcją maszyn wirtualnych jako zestaw. Każdy typ węzła zdefiniowany w klastrze platformy Azure jest [łączona jako oddzielny zestaw skalowania.](service-fabric-cluster-nodetypes.md) Każdy typ węzła można następnie zarządzać oddzielnie. Po utworzeniu klastra sieci szkieletowej usług można skalować klaster w poziomie, dodając nowy typ węzła (zestaw skalowania maszyny wirtualnej) do istniejącego klastra.  Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze.  W miarę skalowania klastra aplikacje są również skalowane automatycznie.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Dodawanie dodatkowego zestawu skalowania do istniejącego klastra
Dodawanie nowego typu węzła (który jest wspierany przez zestaw skalowania maszyny wirtualnej) do istniejącego klastra jest podobny do [uaktualniania typu węzła podstawowego,](service-fabric-scale-up-node-type.md)z tą różnicą, że nie będzie używać tego samego NodeTypeRef; oczywiście nie będzie wyłączanie żadnych aktywnie używanych zestawów skalowania maszyny wirtualnej i nie utracisz dostępności klastra, jeśli nie zaktualizujesz typu węzła podstawowego. 

Właściwość NodeTypeRef jest zadeklarowana we właściwościach rozszerzenia sieci szkieletowej usługi zestawem maszyn wirtualnych:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Ponadto należy dodać ten nowy typ węzła do zasobu klastra sieci szkieletowej usług:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [skalować typ węzła podstawowego](service-fabric-scale-up-node-type.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra platformy Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu płynnego zestawu SDK obliczeń platformy Azure.
* [Skalowanie autonomicznego klastra w lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

