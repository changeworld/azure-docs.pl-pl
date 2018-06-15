---
title: Odwołanie obraz niestandardowy w skali Azure Ustaw szablon | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać do istniejącego zestawu skalowania maszyn wirtualnych Azure szablonu niestandardowego obrazu
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
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: 28d2c080048a7f82e83ad9c1794c9757b330a8c7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26780922"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Dodawanie niestandardowego obrazu do szablonu zestaw skalowania Azure

W tym artykule przedstawiono sposób modyfikowania [minimalnej wielkości Ustaw szablon](./virtual-machine-scale-sets-mvss-start.md) do wdrożenia z niestandardowego obrazu.

## <a name="change-the-template-definition"></a>Zmiany definicji szablonu

Ustaw szablon minimalnej wielkości są widoczne [tutaj](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), i szablon dla wdrażania skali zestawu z niestandardowego obrazu widoczne [tutaj](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Przeanalizujmy różnicowego używany do tworzenia tego szablonu (`git diff minimum-viable-scale-set custom-image`) element przez element:

### <a name="creating-a-managed-disk-image"></a>Tworzenie obrazu dysku zarządzanego

Jeśli masz już obraz niestandardowy dysku zarządzanego (zasobu typu `Microsoft.Compute/images`), a następnie można pominąć tę sekcję.

Najpierw dodaj `sourceImageVhdUri` parametr, który jest identyfikatorem URI do ogólnych obiektu blob w magazynie Azure, który zawiera niestandardowy obraz do wdrożenia z.


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

Następnie dodaj zasób typu `Microsoft.Compute/images`, która jest oparta na ogólnych znajdujący się pod identyfikatorem URI obiektu blob obrazu dysków zarządzanych w `sourceImageVhdUri`. Ten obraz musi być w tym samym regionie co zestaw skalowania, która korzysta z niego. We właściwościach obrazu, określ typ systemu operacyjnego, lokalizacji obiektu blob (z `sourceImageVhdUri` parametru), a typ konta magazynu:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
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

W zestawie skalowania zasobu, Dodaj `dependsOn` klauzuli odwołujących się do niestandardowego obrazu, aby upewnić się, że obraz jest tworzony przed skali podejmuje próbę wdrożenia z tego obrazu:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Zmiana skali Ustaw właściwości, aby używać obrazu dysku twardego zarządzanego

W `imageReference` skali ustawić `storageProfile`, zamiast określania wydawcy, oferty, jednostki sku, i określ wersję obrazu platformy, `id` z `Microsoft.Compute/images` zasobów:

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

W tym przykładzie należy użyć `resourceId` funkcji, aby uzyskać identyfikator zasobu obrazu utworzonego w tym samym szablonie. Jeśli wcześniej utworzono obrazu dysku twardego zarządzanego, należy zamiast tego Podaj identyfikator obrazu. Ten identyfikator musi mieć postać: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
