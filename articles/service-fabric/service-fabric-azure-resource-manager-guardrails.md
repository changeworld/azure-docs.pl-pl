---
title: Barierki ochronne wdrażania usługi Azure Resource Manager w sieci szkieletowej
description: Ten artykuł zawiera omówienie typowych błędów popełnionych podczas wdrażania klastra sieci szkieletowej usług za pośrednictwem usługi Azure Resource Manager i jak ich uniknąć.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368583"
---
# <a name="service-fabric-guardrails"></a>Poręcze tkanin serwisowych 
Podczas wdrażania klastra sieci szkieletowej usług są wprowadzane barierki, które nie powiodą wdrożenia usługi Azure Resource Manager w przypadku nieprawidłowej konfiguracji klastra. Poniższe sekcje zawierają omówienie typowych problemów z konfiguracją klastra i kroki wymagane w celu złagodzenia tych problemów. 

## <a name="durability-mismatch"></a>Niezgodność z trwałością
### <a name="overview"></a>Omówienie
Wartość trwałości dla typu węzła sieci szkieletowej usług jest zdefiniowana w dwóch różnych sekcjach szablonu usługi Azure Resource Manager. Sekcja rozszerzenia Zestaw skalowania maszyny wirtualnej zasobu Zestaw skalowania maszyny wirtualnej oraz sekcja Typ węzła zasobu klastra sieci szkieletowej usług. Jest wymaganiem, aby wartość trwałości w tych sekcjach była zgodna, w przeciwnym razie wdrożenie zasobów zakończy się niepowodzeniem.

Poniższa sekcja zawiera przykład niezgodności trwałości między ustawieniem trwałości rozszerzenia zestawu skalowania maszyny wirtualnej a ustawieniem trwałości typu węzła sieci szkieletowej usługi:  

**Ustawienie trwałości zestawu skalowania maszyny wirtualnej**
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

**Ustawienie trwałości typu węzła sieci szkieletowej usługi** 
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
* Niezgodność trwałości zestawu skalowania maszyny wirtualnej nie odpowiada bieżącemu poziomowi trwałości typu węzła sieci szkieletowej usługi
* Trwałość zestawu skalowania maszyny wirtualnej nie odpowiada docelowemu poziomowi trwałości typu węzła sieci szkieletowej usługi
* Trwałość zestawu skalowania maszyny wirtualnej odpowiada bieżącemu poziomowi trwałości sieci szkieletowej usług lub docelowemu poziomowi trwałości typu węzła sieci szkieletowej usługi 

### <a name="mitigation"></a>Środki zaradcze
Aby naprawić niezgodność trwałości, która jest wskazywana przez którykolwiek z powyższych komunikatów o błędach:
1. Zaktualizuj poziom trwałości w sekcji rozszerzenie zestaw skalowania maszyny wirtualnej lub typ węzła sieci szkieletowej usługi szablonu usługi Azure Resource Manager, aby upewnić się, że wartości są zgodne.
2. Ponownie rozmieszcz szablon usługi Azure Resource Manager ze zaktualizowanymi wartościami.


## <a name="seed-node-deletion"></a>Usunięcie węzła źródłowego 
### <a name="overview"></a>Omówienie
Klaster sieci szkieletowej usług ma właściwość [warstwy niezawodności,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) która służy do określania liczby replik usług systemowych uruchamianych w typie węzła podstawowego klastra. Liczba wymaganych replik określi minimalną liczbę węzłów, które muszą być utrzymywane w typie węzła podstawowego klastra. Jeśli liczba węzłów w typie węzła podstawowego spadnie poniżej wymaganego minimum dla warstwy niezawodności, klaster stanie się niestabilny.  

### <a name="error-messages"></a>Komunikaty o błędach 
Wykryto operację usuwania węzła źródłowego i zostanie odrzucona. 
* Ta operacja spowodowałoby {0} tylko potencjalnych węzłów źródłowych, aby pozostać w klastrze, podczas gdy {1} są potrzebne jako minimum.
* Usunięcie {0} węzłów źródłowych {1} z spowodowałoby klaster będzie w dół z powodu utraty kworum węzła źródłowego. Maksymalna liczba węzłów źródłowych, które {2}można usunąć w czasie jest .
 
### <a name="mitigation"></a>Środki zaradcze 
Upewnij się, że typ węzła podstawowego ma wystarczającą liczbę maszyn wirtualnych dla niezawodności określonej w klastrze. Nie będzie można usunąć maszyny wirtualnej, jeśli spowoduje to, że zestaw skalowania maszyny wirtualnej spadnie poniżej minimalnej liczby węzłów dla danej warstwy niezawodności.
* Jeśli warstwa niezawodności jest poprawnie określona, upewnij się, że masz wystarczającą liczbę węzłów w typie węzła podstawowego, zgodnie z potrzebami dla warstwy niezawodności. 
* Jeśli warstwa niezawodności jest niepoprawna, zainicjuj zmianę zasobu sieci szkieletowej usług, aby najpierw obniżyć poziom niezawodności przed rozpoczęciem jakichkolwiek operacji zestawu skalowania maszyny wirtualnej i poczekaj na jego zakończenie.
* Jeśli warstwa niezawodności jest Brązowa, wykonaj następujące [kroki,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) aby bezpiecznie skalować klaster.

## <a name="next-steps"></a>Następne kroki
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra sieci szkieletowej usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Rozwiązywanie problemów z siecią szkieletową usług: [przewodniki dotyczące rozwiązywania problemów](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
