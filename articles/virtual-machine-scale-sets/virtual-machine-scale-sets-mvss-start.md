---
title: Więcej informacji na temat szablonów zestawów skalowania maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć szablon zestawu minimalnej wielkości dla zestawów skalowania maszyn wirtualnych
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
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: d4a3dd6ae390fd48a8085cca33063a6bb74bd96c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805586"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Więcej informacji na temat szablonów zestawów skalowania maszyn wirtualnych
[Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) to doskonały sposób wdrażania grup powiązanych zasobów. W tej serii samouczków pokazano, jak utworzyć szablon zestawu minimalnej wielkości oraz jak zmodyfikować ten szablon służy do potrzeb różnych scenariuszy. Wszystkie przykłady pochodzą z tego [repozytorium GitHub](https://github.com/gatneil/mvss). 

Ten szablon ma być proste. Bardziej kompletny przykładów dotyczących skalowania zestawu szablonów, zobacz [repozytorium szablonów szybkiego startu platformy Azure w witrynie GitHub](https://github.com/Azure/azure-quickstart-templates) i Wyszukaj foldery, które zawierają ciąg `vmss`.

Jeśli znasz już tworzenia szablonów, możesz przejść do sekcji "Następne kroki", aby zobaczyć, jak zmodyfikować tego szablonu.

## <a name="review-the-template"></a>Sprawdź szablon

Usługa GitHub umożliwia szablon zestawu minimalnej wielkości, przejrzyj [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

W tym samouczku Przeanalizujmy różnice (`git diff master minimum-viable-scale-set`) do utworzenia minimalnej wielkości szablon zestawu eliminujemy.

## <a name="define-schema-and-contentversion"></a>Zdefiniuj $schema i contentversion —
Najpierw należy zdefiniować `$schema` i `contentVersion` w szablonie. `$schema` Element określa wersję języka szablonu i służy do wyróżniania składni w programie Visual Studio i podobne funkcje sprawdzania poprawności. `contentVersion` Element nie jest używany przez platformę Azure. Zamiast tego pomaga Ci śledzić wersję szablonu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Zdefiniuj parametry
Następnie zdefiniuj dwa parametry `adminUsername` i `adminPassword`. Parametry są wartościami, które określisz po ich wdrożeniu. `adminUsername` Parametr jest po prostu `string` typu, ale ponieważ `adminPassword` jest przedstawienie wpisu tajnego, wpisz ją `securestring`. Później te parametry są przekazywane do konfiguracji zestawu skalowania.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definiowanie zmiennych
Szablony usługi Resource Manager pozwalają również zdefiniować zmienne, które ma być używany w dalszej części szablonu. Przykład nie używa żadnych zmiennych, aby obiekt JSON jest pusta.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definiowanie zasobów
Następnym ekranem jest sekcji zasobów szablonu. W tym miejscu możesz zdefiniować, co faktycznie chcesz wdrożyć. W odróżnieniu od `parameters` i `variables` (które są obiektami JSON), `resources` znajduje się lista JSON obiektów JSON.

```json
   "resources": [
```

Wszystkie zasoby wymagają `type`, `name`, `apiVersion`, i `location` właściwości. W tym przykładzie pierwszy zasób ma typ [siecią Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), nazwa `myVnet`i apiVersion `2016-03-30`. (Aby uzyskać najnowszą wersję interfejsu API dla typu zasobu, zobacz [odwołanie do szablonu usługi Azure Resource Manager](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Określ lokalizację
Aby określić lokalizację dla sieci wirtualnej, należy użyć [funkcji szablonu usługi Resource Manager](../azure-resource-manager/resource-group-template-functions.md). Ta funkcja musi być ujęta w cudzysłowy i nawiasy kwadratowe następująco: `"[<template-function>]"`. W takim przypadku należy użyć `resourceGroup` funkcji. Ona przyjmuje żadnych argumentów i zwraca obiekt JSON z metadanych dotyczących grupy zasobów, do której jest wdrażany tego wdrożenia. Grupa zasobów jest ustawiony przez użytkownika w czasie wdrażania. Ta wartość jest następnie indeksować z tym obiektem JSON `.location` można pobrać lokalizacji z obiektu JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Określ właściwości sieci wirtualnej
Każdy zasób usługi Resource Manager ma swój własny `properties` sekcji konfiguracje specyficzne dla zasobu. W takim przypadku określić, że sieci wirtualne powinny mieć przy użyciu zakresu prywatnych adresów IP w jednej podsieci `10.0.0.0/16`. Zestaw skalowania, zawsze jest zawarty w jednej podsieci. Go nie mogą rozciągać się podsieci.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Dodawanie listy dependsOn
Oprócz wymaganego `type`, `name`, `apiVersion`, i `location` właściwości każdego zasobu może mieć opcjonalną `dependsOn` listy ciągów. Ta lista określa, która innych zasobów z tego wdrożenia musi zakończyć się przed wdrożeniem tego zasobu.

W tym przypadku istnieje tylko jeden element na liście sieci wirtualnej z poprzedniego przykładu. Należy określić tę zależność, ponieważ zestaw skalowania musi istnieć przed utworzeniem maszyn wirtualnych, sieci. W ten sposób zestaw skalowania można nadać tych maszyn wirtualnych z prywatnych adresów IP z zakresu adresów IP, wcześniej określona we właściwościach sieci. Format każdego ciągu w liście dependsOn `<type>/<name>`. Użyto tych samych `type` i `name` użytych wcześniej w definicji zasobu sieci wirtualnej.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Określ właściwości zestawu skalowania
Zestawy skalowania ma wiele właściwości dostosowywania maszyn wirtualnych w zestawie skalowania. Aby uzyskać pełną listę tych właściwości, zobacz [odwołanie do szablonu](/azure/templates/microsoft.compute/virtualmachinescalesets). W tym samouczku kilka często używanych właściwości są ustawione.
### <a name="supply-vm-size-and-capacity"></a>Określ rozmiar maszyny Wirtualnej i pojemność
Zestaw skalowania musi wiedzieć, jaki rozmiar maszyny Wirtualnej do utworzenia ("Nazwa jednostki sku") i jak wiele takich maszyn wirtualnych, aby utworzyć ("pojemność jednostki sku"). Aby zobaczyć, które rozmiary maszyn wirtualnych są dostępne, zobacz [dokumentacji rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Wybierz typ aktualizacji
Również zestawu skalowania, trzeba wiedzieć, jak obsługiwać aktualizacje w zestawie skalowania. Obecnie dostępne są dwie opcje `Manual` i `Automatic`. Aby uzyskać więcej informacji na temat różnic między nimi, zobacz dokumentację na [sposobu uaktualniania zestawu skalowania](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Wybierz system operacyjny maszyny Wirtualnej
Musi wiedzieć, jakiego systemu operacyjnego do umieszczenia na maszynach wirtualnych zestawu skalowania. W tym miejscu można utworzyć maszyny wirtualne przy użyciu w pełni poprawionego obrazu Ubuntu 16.04 LTS.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Określ element computerNamePrefix
Zestaw skalowania wdraża wiele maszyn wirtualnych. Zamiast określania nazwy maszyny Wirtualnej, określ `computerNamePrefix`. Zestaw skalowania dołącza indeksu do prefiksu dla każdej maszyny Wirtualnej, więc nazwy maszyny Wirtualnej mają następującą formę `<computerNamePrefix>_<auto-generated-index>`.

W poniższym fragmencie kodu należy użyć parametrów z wcześniej do ustawiania nazwy użytkownika administratora i hasła dla wszystkich maszyn wirtualnych w zestawie skalowania. Ten proces używa `parameters` funkcji szablonu. Ta funkcja przyjmuje ciąg, który określa parametr, który do odwoływania się do, a następnie generuje wartości tego parametru.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Określ konfigurację sieci maszyny Wirtualnej
Na koniec należy określić konfigurację sieci dla maszyn wirtualnych w zestawie skalowania. W takim przypadku tylko należy określić identyfikator podsieci utworzone wcześniej. Oznacza to, zestaw skalowania, aby umieścić interfejsy sieciowe w tej podsieci.

Możesz uzyskać identyfikator sieci wirtualnej, zawierający podsieci za pomocą `resourceId` funkcji szablonu. Ta funkcja przyjmuje typ i nazwę zasobu i zwraca w pełni kwalifikowany identyfikator zasobu. Ten identyfikator ma postać: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Jednak identyfikator sieci wirtualnej nie jest wystarczająco dużo. Zapewniają musi należeć do określonej podsieci, czy maszyny wirtualne zestawu skalowania. Aby to zrobić, należy połączyć `/subnets/mySubnet` identyfikator sieci wirtualnej. Wynik jest w pełni kwalifikowanym Identyfikatorem podsieci. Czy to połączenie przy użyciu `concat` funkcji, która przyjmuje w serii ciągi i zwraca ich łączenia.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
