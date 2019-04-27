---
title: Konwertuj szablon zestawu skalowania usługi Azure Resource Manager w celu używania dysku zarządzanego | Dokumentacja firmy Microsoft
description: Konwertuj szablon zestawu skalowania na szablon zestawu skalowania dysku zarządzanego.
keywords: zestawy skalowania maszyn wirtualnych
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: b2d1738b85799079b3af7ab39c5cb1799a38d382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731741"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konwertowanie szablonu zestawu skalowania na szablon zestawu skalowania dysku zarządzanego

Klientów przy użyciu szablonu usługi Resource Manager do tworzenia zestawu skalowania nie używa dysków zarządzanych mogą chcieć zmodyfikować go pod kątem używania dysku zarządzanego. W tym artykule przedstawiono sposób używania dysków zarządzanych przy użyciu żądania ściągnięcia jako przykład [szablony szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates), repozytorium tworzonych metodą społecznościową dla przykładowych szablonów usługi Resource Manager. Żądanie ściągnięcia pełne są widoczne w tym miejscu: [ https://github.com/Azure/azure-quickstart-templates/pull/2998 ](https://github.com/Azure/azure-quickstart-templates/pull/2998), i odpowiednie części różnicy znajdują się poniżej, oraz objaśnienia:

## <a name="making-the-os-disks-managed"></a>Tworzenie zarządzane dyski systemu operacyjnego

W poniższym różnicy kilku zmiennych związane z właściwości dysk i konta magazynu są usuwane. Typ konta magazynu nie jest już konieczne (Standard_LRS jest wartość domyślna), ale można określić je w razie potrzeby. Tylko Standard_LRS i Premium_LRS są obsługiwane w przypadku dysków zarządzanych. Nowy sufiks konta magazynu, unikatowy ciąg tablicy i liczbę skojarzeń zabezpieczeń były używane w starego szablonu, aby wygenerować nazw kont magazynu. Te zmienne są już potrzebne do nowego szablonu, ponieważ dysk zarządzany automatycznie tworzy konta magazynu w imieniu klienta. Podobnie nazwa kontenera wirtualnego dysku twardego i nazwa dysku systemu operacyjnego są już potrzebne, ponieważ dysk zarządzany automatycznie nazw jest kontenery obiektów blob storage i dyski.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


W poniższym różnicy możesz obliczeniowe interfejsu API został zaktualizowany do wersji 2016-04-30-preview, tak szybko, jak wersja wymagana do obsługi dysków zarządzanych z zestawami skalowania. W razie potrzeby, można użyć usługa unmanaged disks w nowej wersji interfejsu API przy użyciu starej składni. Jeśli tylko zaktualizować wersję interfejsu API obliczeń i nie zmieniaj czymkolwiek, szablon będą nadal działać tak jak poprzednio.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

W poniższym różnicy zasób konta magazynu jest całkowicie usunięty z tablicy zasobów. Zasób jest już potrzebny, jak dysku zarządzanego tworzy je automatycznie.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

W następujących różnic, okaże się, że zostaną usunięte zależy od klauzuli odwołujące się z zestawu skalowania do pętli, która została Tworzenie konta magazynu. Starego szablonu to był zapewnienie, że konta magazynu zostały utworzone przed rozpoczęło się tworzenie zestawu skalowania, ale ta klauzula nie jest już konieczne w przypadku dysków zarządzanych. Wirtualny dysk twardy kontenery właściwości powoduje również usunięcie, oraz właściwość nazwa dysku systemu operacyjnego zgodnie z tych właściwości są automatycznie obsługiwane pod maską przez dysku zarządzanego. Można dodać `"managedDisk": { "storageAccountType": "Premium_LRS" }` w konfiguracji "osDisk", jeśli chce się dyski systemu operacyjnego w warstwie premium. Tylko maszyny wirtualne za pomocą wielkie lub małe firmy "na maszynie wirtualnej i jednostki sku, można użyć dysków w warstwie premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Nie ma jawnych właściwości w konfiguracji zestawu skalowania, czy należy użyć dysku zarządzanym lub niezarządzanym. Zestaw skalowania wie, której mają zostać użyte na podstawie właściwości, które znajdują się w profilu magazynu. Dlatego ważne jest podczas modyfikowania szablonu, aby upewnić się, że odpowiednie właściwości znajdują się w profilu magazynu zestawu skalowania.


## <a name="data-disks"></a>Dyski z danymi

Przy użyciu powyższych zmian scale set używa usługi managed disks dla systemu operacyjnego na dysku, ale co dyski z danymi? Aby dodać dyski z danymi, należy dodać właściwości "dataDisks" w obszarze "storageProfile" na tym samym poziomie jako "osDisk". Wartość właściwości jest JSON listę obiektów, z których każdy ma właściwości "" (numerem lun musi być unikatowa dla dysku danych na maszynie Wirtualnej), "createOption" ("puste" jest obecnie jedyną obsługiwaną opcją), a "diskSizeGB" (rozmiar dysku w gigabajtach; musi być większa niż 0 i mniejsza niż 1024) jak w poniższym przykładzie:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Jeśli określisz `n` dyski w tej tablicy, każda maszyna wirtualna w skali Ustaw pobiera `n` dysków z danymi. Należy jednak pamiętać, że te dyski danych są urządzeniach niesformatowanych. Nie są sformatowane. Jest klientem dołączyć, podzielić na partycje i sformatować dyski przed ich użyciem. Opcjonalnie można również określić `"managedDisk": { "storageAccountType": "Premium_LRS" }` w każdym obiekcie dysku danych, aby określić, by był dysku danych w warstwie premium. Tylko maszyny wirtualne za pomocą wielkie lub małe firmy "na maszynie wirtualnej i jednostki sku, można użyć dysków w warstwie premium.

Aby dowiedzieć się więcej o korzystaniu z dysków z danymi za pomocą zestawów skalowania, zobacz [w tym artykule](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Kolejne kroki
Na przykład szablony usługi Resource Manager przy użyciu zestawów skalowania, wyszukaj termin "zestawu skalowania maszyn wirtualnych" w [repozytorium szablonów szybkiego startu platformy Azure w witrynie GitHub](https://github.com/Azure/azure-quickstart-templates).

Aby uzyskać ogólne informacje, zapoznaj się z [główną stroną docelową dla zestawów skalowania](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

