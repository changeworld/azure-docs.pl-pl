---
title: Service Fabric wdrożenia Azure Resource Manager guardrails
description: Ten artykuł zawiera omówienie typowych błędów wykonywanych podczas wdrażania klastra Service Fabric za pomocą Azure Resource Manager i sposobu ich unikania.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: fe5ff2a5eeb4b2c73165d1577702eb6af7079b61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426735"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
Podczas wdrażania klastra Service Fabric są umieszczane guardrails, co spowoduje niepowodzenie wdrożenia Azure Resource Manager w przypadku nieprawidłowej konfiguracji klastra. Poniższe sekcje zawierają omówienie typowych problemów z konfiguracją klastra oraz czynności wymagane w celu rozwiązania tych problemów. 

## <a name="durability-mismatch"></a>Niezgodność trwałości
### <a name="overview"></a>Przegląd
Wartość trwałości dla typu węzła Service Fabric jest definiowana w dwóch różnych częściach szablonu Azure Resource Manager. Sekcja rozszerzenia zestawu skalowania maszyn wirtualnych w ramach zasobu zestawu skalowania maszyn wirtualnych oraz sekcja typ węzła zasobu klastra Service Fabric. Wymagana jest zgodność wartości trwałości w tych sekcjach, w przeciwnym razie wdrażanie zasobów zakończy się niepowodzeniem.

Poniższa sekcja zawiera przykład zgodności trwałości między ustawieniem trwałości rozszerzenia zestawu skalowania maszyn wirtualnych a ustawieniem trwałości typu węzła Service Fabric:  

**Ustawienie trwałości zestawu skalowania maszyn wirtualnych**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Ustawienie trwałości typu węzła Service Fabric** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Komunikaty o błędach
* Niezgodność trwałości zestawu skalowania maszyn wirtualnych nie jest zgodna z bieżącym poziomem trwałości typu węzła Service Fabric.
* Trwałość zestawu skalowania maszyn wirtualnych nie jest zgodna z elementem docelowym typu węzła Service Fabric
* Trwałość zestawu skalowania maszyn wirtualnych jest zgodna z bieżącym poziomem trwałości Service Fabric lub poziomem trwałości typu węzła Service Fabric docelowego 


### <a name="mitigation"></a>Środki zaradcze
Aby rozwiązać niezgodność trwałości, która jest wskazywana przez którykolwiek z powyższych komunikatów o błędzie:
1. Zaktualizuj poziom trwałości w sekcji rozszerzenia zestawu skalowania maszyn wirtualnych lub Service Fabric typ węzła szablonu Azure Resource Manager, aby upewnić się, że wartości są zgodne.
2. Wdróż ponownie szablon Azure Resource Manager ze zaktualizowanymi wartościami.

## <a name="next-steps"></a>Następne kroki
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Rozwiązywanie problemów Service Fabric: [przewodniki dotyczące rozwiązywania problemów](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
