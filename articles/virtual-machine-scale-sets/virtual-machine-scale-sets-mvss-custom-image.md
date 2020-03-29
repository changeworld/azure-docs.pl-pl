---
title: Odwoływanie się do obrazu niestandardowego w szablonie zestawu skalowania platformy Azure
description: Dowiedz się, jak dodać obraz niestandardowy do istniejącego szablonu zestawu skalowania maszyny wirtualnej platformy Azure
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: fd1a567af1c35cf6b659995e998b11a61a526508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275595"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Add a custom image to an Azure scale set template (Dodawanie obrazu niestandardowego do szablonu zestawu skalowania platformy Azure)

W tym artykule pokazano, jak zmodyfikować [szablon zestawu skalowania podstawowego](virtual-machine-scale-sets-mvss-start.md) do wdrożenia z obrazu niestandardowego.

## <a name="change-the-template-definition"></a>Zmienianie definicji szablonu
W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) utworzyliśmy podstawowy szablon zestawu skalowania. Teraz użyjemy tego wcześniejszego szablonu i zmodyfikujemy go, aby utworzyć szablon, który wdraża zestaw skalowania z obrazu niestandardowego.  

### <a name="creating-a-managed-disk-image"></a>Tworzenie obrazu dysku zarządzanego

Jeśli masz już niestandardowy obraz dysku zarządzanego (zasób typu), `Microsoft.Compute/images`możesz pominąć tę sekcję.

Najpierw dodaj `sourceImageVhdUri` parametr, który jest identyfikatorem URI do uogólnionego obiektu blob w usłudze Azure Storage, który zawiera obraz niestandardowy do wdrożenia.


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

Następnie dodaj zasób `Microsoft.Compute/images`typu , który jest obrazem dysku zarządzanego na `sourceImageVhdUri`podstawie uogólnionego obiektu blob znajdującego się w URI . Ten obraz musi znajdować się w tym samym regionie co zestaw skalowania, który go używa. We właściwościach obrazu określ typ systemu operacyjnego, lokalizację obiektu blob (z parametru) `sourceImageVhdUri` i typ konta magazynu:

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

W zasobie zestawu skalowania dodaj klauzulę odnoszącą `dependsOn` się do obrazu niestandardowego, aby upewnić się, że obraz zostanie utworzony przed próbą wdrożenia z tego obrazu zestawu skalowania:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Zmienianie właściwości zestawu skalowania w celu użycia obrazu dysku zarządzanego

W `imageReference` zestawie `storageProfile`skalowania zamiast określania wydawcy, oferty, sku i wersji obrazu platformy `id` należy `Microsoft.Compute/images` określić zasób:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

W tym przykładzie `resourceId` użyj funkcji, aby uzyskać identyfikator zasobu obrazu utworzonego w tym samym szablonie. Jeśli obraz dysku zarządzanego został wcześniej utworzony, należy podać identyfikator tego obrazu. Ten identyfikator musi być w `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`formularzu: .


## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
