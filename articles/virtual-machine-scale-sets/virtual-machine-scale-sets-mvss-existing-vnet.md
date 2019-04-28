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
origin.date: 06/27/2017
ms.date: 11/30/2018
ms.author: v-junlch
ms.openlocfilehash: 1dcb97a94bd5790edc2e40acf890bb47baec7a4b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108031"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Dodaj odwołanie do istniejącej sieci wirtualnej w szablonie zestawu skalowania na platformie Azure

W tym artykule przedstawiono sposób modyfikowania [minimalnego możliwego do użycia zestawu skalowania szablonu](./virtual-machine-scale-sets-mvss-start.md) do wdrożenia w istniejącej sieci wirtualnej, zamiast tworzyć nowy.

## <a name="change-the-template-definition"></a>Zmiana definicji szablonu

Szablon zestawu minimalnej wielkości są widoczne [tutaj](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), i szablon do wdrażania zestawu skalowania w istniejącej sieci wirtualnej są widoczne [tutaj](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Przeanalizujmy diff, używany do tworzenia tego szablonu (`git diff minimum-viable-scale-set existing-vnet`) eliminujemy:

Najpierw dodaj `subnetId` parametru. Ten ciąg jest przekazywany do konfiguracji zestawu skalowania, umożliwiając zidentyfikować wstępnie utworzonych podsieci w celu wdrożenia maszyn wirtualnych w zestawie skalowania. Ten ciąg musi mieć postać: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Na przykład ustawić do wdrożenia skalowania w istniejącej sieci wirtualnej o nazwie `myvnet`, podsieci `mysubnet`, grupy zasobów `myrg`, subskrypcji i `00000000-0000-0000-0000-000000000000`, byłoby subnetId: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Sieć wirtualna już istnieje przed wdrożeniem szablon, więc nie ma potrzeby do określenia klauzuli dependsOn z zestawu skalowania do sieci wirtualnej. Usuń następujące wiersze:

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

Na koniec Przekaż `subnetId` parametru ustawiony przez użytkownika (zamiast `resourceId` Aby uzyskać identyfikator sieci wirtualnej, w tym samym wdrożeniu, czyli co minimalnego możliwego do użycia zestawu skalowania szablonu przeprowadza).

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

<!-- Update_Description: update metedata properties -->