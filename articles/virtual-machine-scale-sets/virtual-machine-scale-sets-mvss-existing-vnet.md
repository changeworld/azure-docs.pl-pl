---
title: Odwoływanie się do istniejącej sieci wirtualnej w szablonie zestawu skalowania platformy Azure
description: Dowiedz się, jak dodać sieć wirtualną do istniejącego szablonu zestawu skalowania maszyn wirtualnych platformy Azure
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275546"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Dodawanie odwołania do istniejącej sieci wirtualnej w szablonie zestawu skalowania platformy Azure

W tym artykule pokazano, jak zmodyfikować [podstawowy szablon zestawu skalowania](virtual-machine-scale-sets-mvss-start.md) w celu wdrożenia go w istniejącej sieci wirtualnej, zamiast tworzyć nowe.

## <a name="change-the-template-definition"></a>Zmiana definicji szablonu

W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) został utworzony podstawowy szablon zestawu skalowania. Teraz użyjemy tego wcześniejszego szablonu i zmodyfikujesz go, aby utworzyć szablon, który wdraża zestaw skalowania w istniejącej sieci wirtualnej. 

Najpierw Dodaj parametr `subnetId`. Ten ciąg jest przesyłany do konfiguracji zestawu skalowania, co pozwala zestawowi skalowania identyfikować wstępnie utworzoną podsieć, w której mają zostać wdrożone maszyny wirtualne. Ten ciąg musi mieć postać: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Na przykład, aby wdrożyć zestaw skalowania do istniejącej sieci wirtualnej o nazwie `myvnet`, `mysubnet`podsieci, `myrg`grupy zasobów i subskrypcji `00000000-0000-0000-0000-000000000000`, subnetId będzie: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Następnie usuń zasób sieci wirtualnej z tablicy `resources`, jeśli używasz istniejącej sieci wirtualnej i nie musisz wdrażać nowej.

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

Sieć wirtualna już istnieje przed wdrożeniem szablonu, dlatego nie ma potrzeby określania klauzuli dependsOn z zestawu skalowania do sieci wirtualnej. Usuń następujące wiersze:

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

Na koniec zapoznaj się z parametrem `subnetId` ustawionym przez użytkownika (zamiast używać `resourceId`, aby uzyskać identyfikator sieci wirtualnej w tym samym wdrożeniu, czyli podstawowy szablon zestawu skalowania w poziomie).

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
