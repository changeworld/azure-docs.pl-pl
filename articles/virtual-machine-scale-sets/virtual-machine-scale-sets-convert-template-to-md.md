---
title: Konwertowanie szablonu zestawu skalowania w celu używania dysku zarządzanego
description: Konwertuj szablon zestawu skalowania maszyny wirtualnej usługi Azure Resource Manager na szablon zestawu skaly dysku zarządzanego.
keywords: zestawy skalowania maszyn wirtualnych
author: mayanknayar
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: 4ab5c48c6673a2353c70fe808d09aa15675e0424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278126"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konwertowanie szablonu zestawu skalowania na szablon zestawu skal zarządzanych dysków

Klienci z szablonem Menedżera zasobów do tworzenia zestawu skalowania nieużywającego dysku zarządzanego mogą chcieć zmodyfikować go w celu użycia dysku zarządzanego. W tym artykule pokazano, jak używać dysków zarządzanych, używając jako przykład żądania ściągania z [szablonów szybki startu platformy Azure](https://github.com/Azure/azure-quickstart-templates), repozytorium oparte na społeczności dla przykładowych szablonów Menedżera zasobów. Pełne żądanie ściągnięcia można [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998)zobaczyć tutaj: , a odpowiednie części różnicy są poniżej, wraz z wyjaśnieniami:

## <a name="making-the-os-disks-managed"></a>Zarządzanie dyskami systemu operacyjnego

W poniższej różnicy kilka zmiennych związanych z kontem magazynu i właściwości dysku są usuwane. Typ konta magazynu nie jest już konieczne (Standard_LRS jest ustawieniem domyślnym), ale można go określić w razie potrzeby. Tylko Standard_LRS i Premium_LRS są obsługiwane z dyskiem zarządzanym. Nowy sufiks konta magazynu, unikatowa tablica ciągów i liczba sa zostały użyte w starym szablonie do generowania nazw kont magazynu. Te zmienne nie są już potrzebne w nowym szablonie, ponieważ dysk zarządzany automatycznie tworzy konta magazynu w imieniu klienta. Podobnie nazwa kontenera vhd i nazwa dysku systemu operacyjnego nie są już potrzebne, ponieważ dysk zarządzany automatycznie nazywa podstawowe kontenery obiektów blob magazynu i dyski.

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


W poniższej wersji różnicowej obliczania interfejsu API jest aktualizowany do wersji 2016-04-30-preview, która jest najwcześniejszą wymaganą wersją dla obsługi dysku zarządzanego z zestawami skalowania. Można użyć dysków niezarządzanych w nowej wersji interfejsu API ze starą składnią w razie potrzeby. Jeśli tylko zaktualizować wersję interfejsu API obliczeń i nie zmieniać niczego innego, szablon powinien nadal działać jak poprzednio.

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

W poniższej różnicy zasób konta magazynu jest całkowicie usuwany z tablicy zasobów. Zasób nie jest już potrzebny, ponieważ dysk zarządzany tworzy je automatycznie.

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

W poniższej różnicy widzimy, że usuwamy zależy od klauzuli odnoszącej się od skalowania ustawionej do pętli, która tworzyła konta magazynu. W starym szablonie było to zapewnienie, że konta magazynu zostały utworzone przed rozpoczęciem tworzenia zestawu skalowania, ale ta klauzula nie jest już konieczne w dyskach zarządzanych. Właściwość kontenerów vhd jest również usuwana wraz z właściwością nazwa dysku systemu operacyjnego, ponieważ te właściwości są automatycznie obsługiwane pod maską przez dysk zarządzany. Można dodać `"managedDisk": { "storageAccountType": "Premium_LRS" }` w konfiguracji "osDisk", jeśli chcesz dysków systemu operacyjnego premium. Tylko maszyny wirtualne z wielką lub wielką literą "s" w sku maszyny Wirtualnej mogą używać dysków premium.

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

Nie ma jawnej właściwości w konfiguracji zestawu skalowania, czy używać dysku zarządzanego lub niezarządzanego. Zestaw skalowania wie, którego używać na podstawie właściwości, które są obecne w profilu magazynu. W związku z tym jest ważne podczas modyfikowania szablonu, aby upewnić się, że odpowiednie właściwości znajdują się w profilu magazynu zestawu skalowania.


## <a name="data-disks"></a>Dyski z danymi

Po zmianach powyżej zestaw skalowania używa dysków zarządzanych dla dysku systemu operacyjnego, ale co z dyskami z danymi? Aby dodać dyski danych, dodaj właściwość "dataDisks" w obszarze "storageProfile" na tym samym poziomie co "osDisk". Wartość właściwości jest lista JSON obiektów, z których każdy ma właściwości "lun" (które muszą być unikatowe na dysku danych na maszynie wirtualnej), "createOption" ("empty" jest obecnie jedyną obsługiwaną opcją) i "diskSizeGB" (rozmiar dysku w gigabajtach; musi być większa niż 0 i mniej niż 1024), jak w poniższym przykładzie:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Jeśli określisz `n` dyski w tej tablicy, każda `n` maszyna wirtualna w zestawie skalowania pobiera dyski danych. Należy jednak pamiętać, że te dyski danych są urządzeniami nieprzetworzonymi. Nie są one sformatowane. Przed ich użyciem należy do klienta, aby dołączyć, podzielić i sformatować dyski. Opcjonalnie można również `"managedDisk": { "storageAccountType": "Premium_LRS" }` określić w każdym obiekcie dysku danych, aby określić, że powinien być dyskiem danych premium. Tylko maszyny wirtualne z wielką lub wielką literą "s" w sku maszyny Wirtualnej mogą używać dysków premium.

Aby dowiedzieć się więcej na temat używania dysków danych z zestawami skalowania, zobacz [ten artykuł](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Następne kroki
Na przykład szablony Menedżera zasobów przy użyciu zestawów skalowania, wyszukaj "vmss" w [repozytorium Szablony Szybki start platformy Azure GitHub](https://github.com/Azure/azure-quickstart-templates).

Aby uzyskać ogólne informacje, zapoznaj się z [główną stroną docelową zestawów skalowania](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

