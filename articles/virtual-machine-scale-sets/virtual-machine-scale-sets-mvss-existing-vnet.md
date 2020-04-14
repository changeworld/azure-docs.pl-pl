---
title: Odwoływanie się do istniejącej sieci wirtualnej w szablonie zestawu skalowania platformy Azure
description: Dowiedz się, jak dodać sieć wirtualną do istniejącego szablonu zestawu skalowania maszyny wirtualnej platformy Azure
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273668"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Dodawanie odwołania do istniejącej sieci wirtualnej w szablonie zestawu skalowania platformy Azure

W tym artykule pokazano, jak zmodyfikować [szablon zestawu skalowania podstawowego,](virtual-machine-scale-sets-mvss-start.md) aby wdrożyć go w istniejącej sieci wirtualnej zamiast tworzyć nowy.

## <a name="change-the-template-definition"></a>Zmienianie definicji szablonu

W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) utworzyliśmy podstawowy szablon zestawu skalowania. Teraz użyjemy tego wcześniejszego szablonu i zmodyfikujemy go, aby utworzyć szablon, który wdraża zestaw skalowania w istniejącej sieci wirtualnej. 

Najpierw dodaj `subnetId` parametr. Ten ciąg jest przekazywany do konfiguracji zestawu skalowania, umożliwiając zestaw skalowania do identyfikowania wstępnie utworzonej podsieci do wdrażania maszyn wirtualnych. Ten ciąg musi być w formularzu:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Na przykład, aby wdrożyć zestaw skalowania `myvnet`w istniejącej sieci `myrg`wirtualnej `00000000-0000-0000-0000-000000000000`z nazwą , podsiecią, `mysubnet`grupą zasobów i subskrypcją, identyfikator podsieci będzie: . `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`

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

Następnie usuń zasób sieci `resources` wirtualnej z tablicy, ponieważ używasz istniejącej sieci wirtualnej i nie trzeba wdrażać nowej.

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

Sieć wirtualna istnieje już przed wdrożeniem szablonu, więc nie ma potrzeby określania klauzuli dependsOn od skali ustawionej do sieci wirtualnej. Usuń następujące wiersze:

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

Na koniec przekazać `subnetId` parametr ustawiony przez użytkownika (zamiast `resourceId` używać, aby uzyskać identyfikator sieci wirtualnej w tym samym wdrożeniu, który jest co podstawowe szablon zestawu skali realną nie).

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




## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
