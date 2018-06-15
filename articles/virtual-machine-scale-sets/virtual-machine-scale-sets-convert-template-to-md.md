---
title: Konwertuj szablonu zestaw skalowania usługi Azure Resource Manager pod kątem używania dysku zarządzanego | Dokumentacja firmy Microsoft
description: Konwertuj szablonu zestaw skali do szablonu zestawu dysków zarządzanych w skali.
keywords: zestawy skalowania maszyny wirtualnej
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.openlocfilehash: 760e30f5c6f4ecaff299bae1725548a6a7c5184c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781075"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konwertuj szablonu zestaw skalowania do szablonu zestawu dysków zarządzanych w skali

Klienci z szablonem usługi Resource Manager służący do tworzenia zestawu skali nie przy użyciu dysków zarządzanych w możesz zmodyfikować, aby użyć dysków zarządzanych. W tym artykule przedstawiono sposób użycia dysków zarządzanych za pomocą jako przykład żądanie ściągnięcia z [szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates), społeczność repozytorium dla przykładowych szablonów usługi Resource Manager. Żądanie ściągnięcia pełne są widoczne w tym miejscu: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), oraz odpowiednich części różnicowego są poniżej, oraz objaśnienia:

## <a name="making-the-os-disks-managed"></a>Tworzenie dysków systemu operacyjnego zarządzania

W następujących różnic kilku zmiennych związane z właściwości dysk i konta magazynu zostały usunięte. Typ konta magazynu nie jest już konieczne (Standard_LRS jest wartość domyślna), ale można ją określić w razie potrzeby. Tylko Standard_LRS i Premium_LRS są obsługiwane z dyskiem zarządzanym. Nowy sufiks konta magazynu, macierzy unikatowy ciąg i liczba sa były używane w starego szablonu, aby wygenerować nazw kont magazynu. Te zmienne nie są już wymagane w nowym szablonie ponieważ dysków zarządzanych automatycznie tworzy kont magazynu w imieniu klienta. Podobnie, nazwa kontenera wirtualnego dysku twardego i nazwa dysku systemu operacyjnego nie są już wymagane ponieważ dysków zarządzanych automatycznie nazwy jest kontenery magazynu obiektów blob i dyski.

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


W następujących różnic obliczeniowe interfejsu API zostanie zaktualizowany do wersji 2016-04-30-preview, czyli tak szybko, jak wersja wymagane do obsługi dysków zarządzanych zestawów skalowania. W razie potrzeby można za pomocą niezarządzanych dysków w nowej wersji interfejsu API z stara składnia. Tylko zaktualizować wersję interfejsu API obliczeniowych, nie zmieniaj dowolne inne szablonu będą nadal działać jak wcześniej.

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

W następujących różnic zasobów konta magazynu jest całkowicie usunięte z tablicy zasobów. Zasób nie jest już potrzebna jako dysków zarządzanych w tworzy je automatycznie.

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

W następujących różnic, zobaczysz, że zostaną usunięte zależy od klauzuli odwołujących się od skali ustawioną pętli, które tworzenia kont magazynu. Starego szablonu to został zapewnienie, że konta magazynu zostały utworzone przed zestaw skalowania rozpoczęcia tworzenia, ale ta klauzula nie jest już konieczne z dysków zarządzanych. Właściwość kontenery vhd zostaną również usunięte, wraz z właściwości Nazwa dysku systemu operacyjnego zgodnie z tymi właściwościami automatycznie są obsługiwane pod maską przez dysków zarządzanych. Można dodać `"managedDisk": { "storageAccountType": "Premium_LRS" }` w konfiguracji "osDisk", jeśli chce dysków systemu operacyjnego w warstwie premium. Tylko maszyny wirtualne z wielkie lub małe firmy "w maszynie Wirtualnej sku można było używać dysków premium.

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

Nie ma jawnego właściwości w konfiguracji zestaw skali, czy należy użyć dysku zarządzane lub niezarządzane. Zestaw skali wie, który ma zostać użyty na podstawie właściwości, które znajdują się w profilu magazynu. W związku z tym ważne jest podczas modyfikowania szablonu w celu upewnij się, że właściwości prawa w profilu magazynu zestawu skali.


## <a name="data-disks"></a>Dyski z danymi

Z powyższych zmiany skali zestaw używa zarządzanych dysków dla systemu operacyjnego na dysku, ale informacje o dyskach danych? Aby dodać dysk danych, należy dodać właściwości "dataDisks" w "storageProfile" na tym samym poziomie jako "osDisk". Wartość właściwości jest JSON listę obiektów, z których każdy ma właściwości "lun" (która muszą być unikatowe dla każdego dysku danych na maszynie Wirtualnej), "createOption" ("pusty" jest obecnie jedyną obsługiwaną opcją), a "diskSizeGB" (rozmiar dysku w gigabajtach; musi być większa niż 0 i mniejsza niż 1024) jak w poniższym przykładzie: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Jeśli określisz `n` dysków w tej macierzy, pobiera zestawu każdej maszyny Wirtualnej w skali `n` dysków z danymi. Należy jednak pamiętać, że te dyski danych są urządzeniach niesformatowanych. Nie są sformatowane. Jest klienta, aby dołączyć, podzielić na partycje i sformatować dyski przed ich użyciem. Opcjonalnie można również określić `"managedDisk": { "storageAccountType": "Premium_LRS" }` w każdy obiekt dysku danych, aby określić, czy powinien być dysk danych — warstwa premium. Tylko maszyny wirtualne z wielkie lub małe firmy "w maszynie Wirtualnej sku można było używać dysków premium.

Aby dowiedzieć się więcej o korzystaniu z dysków z danymi z zestawy skalowania, zobacz [w tym artykule](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Kolejne kroki
Na przykład szablony Menedżera zasobów za pomocą zestawów skali, wyszukaj termin "vmss" w [repozytorium github szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates).

Aby uzyskać ogólne informacje, zapoznaj się z [strony głównej docelowej dla zestawów skalowania](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

