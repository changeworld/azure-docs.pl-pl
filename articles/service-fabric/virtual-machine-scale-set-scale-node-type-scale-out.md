---
title: Dodaj typ węzła do klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować klaster usługi Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: aljo
ms.openlocfilehash: ed5bf829e2fbff6c286acdb21a8d0158148483d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506726"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skalowanie klastra usługi Service Fabric w poziomie przez dodanie zestawu skalowania maszyn wirtualnych
W tym artykule opisano sposób skalowanie klastra usługi Azure Service Fabric przez dodanie nowego typu węzła do istniejącego klastra. Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrażanie i zarządzanie kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure jest [konfigurowany jako zestaw skalowania oddzielnych](service-fabric-cluster-nodetypes.md). Każdy typ węzła może następnie być zarządzany oddzielnie. Po utworzeniu klastra usługi Service Fabric klaster można skalować w poziomie, przez dodanie nowego typu węzła (zestaw skalowania maszyn wirtualnych) do istniejącego klastra.  Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze.  Jak jest skalowana w klastrze, aplikacje będą skalowane automatycznie również.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Dodaj dodatkowe zestawu skalowania do istniejącego klastra
Dodawanie nowego typu węzła (która jest wspierana przez zestaw skalowania maszyn wirtualnych) do istniejącego klastra jest podobne do [uaktualnienie tego typu węzła podstawowego](service-fabric-scale-up-node-type.md), z wyjątkiem nie będzie używać tego samego elementu NodeTypeRef; oczywiście nie będzie można wyłączyć wszelkie aktywnie używane zestawy skalowania maszyn wirtualnych, a dostępność klastra nie zostaną utracone, jeśli nie zaktualizujesz typu węzła podstawowego. 

Właściwość elementu NodeTypeRef jest zadeklarowany na maszynie wirtualnej usługi Service Fabric właściwości rozszerzenia zestawu skalowania:
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

Ponadto należy dodać ten nowy typ węzła do zasobu klastra usługi Service Fabric:

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

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [skalowanie w górę podstawowy typ węzła](service-fabric-scale-up-node-type.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra usługi Azure wewnątrz lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra usługi Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu fluent Azure compute zestawu SDK.
* [Skalowanie klastra autonomicznego wewnątrz lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

