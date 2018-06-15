---
title: Odwołanie istniejącej sieci wirtualnej w szablonie zestaw skalowania Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać sieć wirtualną do istniejącego zestawu skalowania maszyn wirtualnych Azure szablonu
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: negat
ms.openlocfilehash: eb35975de5864e129f97b614a61487456dd972ef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782375"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Dodaj odwołanie do istniejącej sieci wirtualnej w szablonie zestaw skalowania Azure

W tym artykule przedstawiono sposób modyfikowania [minimalnej wielkości Ustaw szablon](./virtual-machine-scale-sets-mvss-start.md) do wdrożenia w ramach istniejącej sieci wirtualnej, zamiast tworzyć nowy.

## <a name="change-the-template-definition"></a>Zmiany definicji szablonu

Ustaw szablon minimalnej wielkości są widoczne [tutaj](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), i szablon do wdrażania do istniejącej sieci wirtualnej zestaw skali są widoczne [tutaj](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Przeanalizujmy różnicowego używany do tworzenia tego szablonu (`git diff minimum-viable-scale-set existing-vnet`) element przez element:

Najpierw dodaj `subnetId` parametru. Ten ciąg jest przekazywany do konfiguracji zestaw skali, dzięki czemu zestaw do identyfikowania wstępnie utworzone podsieci do wdrażania maszyn wirtualnych do skalowania. Ten ciąg musi mieć postać: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Na przykład, aby wdrożyć skali należy ustawić w istniejącej sieci wirtualnej o nazwie `myvnet`, podsieci `mysubnet`, grupy zasobów `myrg`i subskrypcji `00000000-0000-0000-0000-000000000000`, będzie subnetId: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Następnie należy usunąć zasób sieci wirtualnej z `resources` tablicy, jak używać istniejącej sieci wirtualnej i nie potrzeba wdrożenia nowej.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
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

Sieć wirtualna już istnieje przed wdrożeniem szablon, więc nie istnieje potrzeba do określenia klauzuli dependsOn od skali ustawioną sieci wirtualnej. Usuń następujące wiersze:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Na koniec należy przekazać w `subnetId` parametru ustawiony przez użytkownika (zamiast `resourceId` można pobrać Identyfikatora sieci wirtualnej w ramach tego samego wdrożenia, który ma co minimalnej wielkości ustawić szablon jest).

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
