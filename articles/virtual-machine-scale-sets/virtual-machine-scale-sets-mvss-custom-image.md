---
title: Odwoływać się do obrazu niestandardowego w szablonie zestawu skalowania na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać obraz niestandardowy do istniejącego szablonu zestawu skalowania maszyn wirtualnych platformy Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: 2415d0dc2b9a2c4229d9910b42eb8ec9309ac7a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869114"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Dodaj obraz niestandardowy do szablonu zestawu skalowania na platformie Azure

W tym artykule przedstawiono sposób modyfikowania [szablon zestawu skalowania podstawowe](virtual-machine-scale-sets-mvss-start.md) wdrażania za pomocą niestandardowego obrazu.

## <a name="change-the-template-definition"></a>Zmiana definicji szablonu
W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) miał utworzyliśmy szablonu zestawu skalowania podstawowe. Teraz możemy użyć szablonu, którego wcześniej i zmodyfikuj go, aby utworzyć szablon, który służy do wdrażania zestawu skalowania na podstawie niestandardowego obrazu.  

### <a name="creating-a-managed-disk-image"></a>Tworzenie obrazu dysku zarządzanego

Jeśli masz już obrazu niestandardowego dysku zarządzanego (zasób typu `Microsoft.Compute/images`), a następnie pominąć tę sekcję.

Najpierw dodaj `sourceImageVhdUri` parametr, który jest identyfikatorem URI do ogólnych obiektów blob w usłudze Azure Storage, który zawiera niestandardowy obraz do wdrożenia z.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Następnie dodaj zasób typu `Microsoft.Compute/images`, czyli obrazu dysku zarządzanego na podstawie uogólnionego obiektu blob znajdującego się w identyfikatorze URI `sourceImageVhdUri`. Ten obraz musi być w tym samym regionie co zestaw skalowania, która go używa. We właściwościach obrazu, należy określić typ systemu operacyjnego, lokalizację obiektu blob (z `sourceImageVhdUri` parametru), a typ konta magazynu:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

Zasobu zestawu skalowania, Dodaj `dependsOn` klauzuli odwołujące się do niestandardowego obrazu, aby upewnić się, że obraz zostanie utworzona zanim stara się wdrażanie na podstawie tego obrazu zestawu skalowania:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Zmiana skali Ustaw właściwości, aby użyć obrazu dysku zarządzanego

W `imageReference` skalowania ustaw `storageProfile`, zamiast określania wydawcy, oferty, jednostki sku, i określ wersję obrazu platformy, `id` z `Microsoft.Compute/images` zasobów:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

W tym przykładzie użyj `resourceId` funkcję, aby uzyskać identyfikator zasobu obrazu utworzonego w tym samym szablonie. Jeśli wcześniej utworzono obrazu dysku zarządzanego, zamiast tego należy podać identyfikator tego obrazu. Ten identyfikator musi mieć postać: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
