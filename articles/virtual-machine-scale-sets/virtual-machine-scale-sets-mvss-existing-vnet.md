---
title: Odwoływać się do istniejącej sieci wirtualnej w szablonie zestawu skalowania na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można dodać sieci wirtualnej do istniejącego szablonu zestawu skalowania maszyn wirtualnych platformy Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8b75b9898eb767866c0843594a82570cfb65d122
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868950"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Dodaj odwołanie do istniejącej sieci wirtualnej w szablonie zestawu skalowania na platformie Azure

W tym artykule przedstawiono sposób modyfikowania [szablon zestawu skalowania podstawowe](virtual-machine-scale-sets-mvss-start.md) do wdrożenia w istniejącej sieci wirtualnej, zamiast tworzyć nowy.

## <a name="change-the-template-definition"></a>Zmiana definicji szablonu

W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) miał utworzyliśmy szablonu zestawu skalowania podstawowe. Teraz możemy użyć szablonu, którego wcześniej i zmodyfikuj go, aby utworzyć szablon, który służy do wdrażania zestawu skalowania w istniejącej sieci wirtualnej. 

Najpierw dodaj `subnetId` parametru. Ten ciąg jest przekazywany do konfiguracji zestawu skalowania, umożliwiając zidentyfikować wstępnie utworzonych podsieci w celu wdrożenia maszyn wirtualnych w zestawie skalowania. Ten ciąg musi mieć postać: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Na przykład ustawić do wdrożenia skalowania w istniejącej sieci wirtualnej o nazwie `myvnet`, podsieci `mysubnet`, grupy zasobów `myrg`, subskrypcji i `00000000-0000-0000-0000-000000000000`, byłoby subnetId: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Następnie należy usunąć zasób sieci wirtualnej z `resources` tablicy, jak użyć istniejącej sieci wirtualnej i nie trzeba wdrożyć nowe.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

Sieć wirtualna już istnieje przed wdrożeniem szablon, więc nie ma potrzeby do określenia klauzuli dependsOn z zestawu skalowania do sieci wirtualnej. Usuń następujące wiersze:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Na koniec Przekaż `subnetId` parametru ustawiony przez użytkownika (zamiast `resourceId` Aby uzyskać identyfikator sieci wirtualnej, w tym samym wdrożeniu, czyli co szablon zestawu skalowania możliwego do użycia podstawowego przeprowadza).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
