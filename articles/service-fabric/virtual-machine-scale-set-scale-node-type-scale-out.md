---
title: Dodawanie typu węzła do klastra usługi Azure Service Fabric | Microsoft Docs
description: Dowiedz się, jak skalować w poziomie klaster Service Fabric, dodając zestaw skalowania maszyn wirtualnych.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 1414e656a358af1e258c823cc7ec747fefa986ba
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598696"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skalowanie klastra Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych
W tym artykule opisano sposób skalowania klastra Service Fabric platformy Azure przez dodanie nowego typu węzła do istniejącego klastra. Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno. Po utworzeniu klastra Service Fabric można skalować klaster w poziomie, dodając nowy typ węzła (zestaw skalowania maszyn wirtualnych) do istniejącego klastra.  Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.  W miarę skalowania klastra aplikacje są automatycznie skalowane.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Dodawanie dodatkowego zestawu skalowania do istniejącego klastra
Dodanie nowego typu węzła (który jest obsługiwany przez zestaw skalowania maszyn wirtualnych) do istniejącego klastra jest podobne do [uaktualnienia podstawowego typu węzła](service-fabric-scale-up-node-type.md), z tą różnicą, że nie będzie można używać tego samego NodeTypeRef; Oczywiście nie należy wyłączać żadnych aktywnie używanych zestawów skalowania maszyn wirtualnych i nie utracisz dostępności klastra, jeśli nie zaktualizujesz podstawowego typu węzła. 

Właściwość NodeTypeRef jest zadeklarowana w ramach zestawu skalowania maszyn wirtualnych Service Fabric właściwości rozszerzenia:
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

Ponadto trzeba będzie dodać ten nowy typ węzła do zasobu klastra Service Fabric:

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
* Dowiedz się [, jak skalować w górę typ węzła podstawowego](service-fabric-scale-up-node-type.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w poziomie lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Programowo Skaluj klaster platformy Azure](service-fabric-cluster-programmatic-scaling.md) przy użyciu zestawu Azure COMPUTE SDK.
* [Skalowanie klastra autonomicznego w poziomie lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

