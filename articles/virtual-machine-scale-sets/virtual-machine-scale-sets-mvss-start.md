---
title: Dowiedz się więcej o szablonach zestawów skalowania maszyny wirtualnej
description: Dowiedz się, jak utworzyć szablon zestawu podstawowych skalowania dla zestawów skalowania maszyny wirtualnej platformy Azure w kilku prostych krokach.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 9c6a30a5f08b33adfa515973962236516f34fbf3
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273396"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Dowiedz się więcej o szablonach zestawów skalowania maszyny wirtualnej
[Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) to doskonały sposób wdrażania grup powiązanych zasobów. W tej serii samouczków pokazano, jak utworzyć szablon zestawu skalowania podstawowego i jak zmodyfikować ten szablon w zależności od różnych scenariuszy. Wszystkie przykłady pochodzą z tego [repozytorium GitHub](https://github.com/gatneil/mvss).

Ten szablon ma być prosty. Aby uzyskać bardziej kompletne przykłady szablonów zestawów skalowania, zobacz [repozytorium GitHub szablonów szybki startu platformy Azure](https://github.com/Azure/azure-quickstart-templates) i wyszukaj foldery zawierające ciąg `vmss`.

Jeśli znasz już tworzenie szablonów, możesz przejść do sekcji "Następne kroki", aby zobaczyć, jak zmodyfikować ten szablon.

## <a name="define-schema-and-contentversion"></a>Definiowanie $schema i contentVersion
Najpierw zdefiniuj `$schema` i `contentVersion` w szablonie. Element `$schema` definiuje wersję języka szablonu i jest używany do wyróżniania składni programu Visual Studio i podobnych funkcji sprawdzania poprawności. Element `contentVersion` nie jest używany przez platformę Azure. Zamiast tego pomaga śledzić wersję szablonu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definiowanie parametrów
Następnie zdefiniuj dwa parametry `adminUsername` i `adminPassword`. Parametry są wartościami określonymi w czasie wdrażania. Parametr `adminUsername` jest po `string` prostu typem, ale ponieważ `adminPassword` `securestring`jest tajny, nadać mu typ . Później te parametry są przekazywane do konfiguracji zestawu skalowania.

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
Szablony Menedżera zasobów umożliwiają również definiowanie zmiennych, które mają być używane w dalszej części szablonu. W przykładzie nie używa żadnych zmiennych, więc obiekt JSON jest pusty.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definiowanie zasobów
Dalej jest sekcja zasobów szablonu. W tym miejscu można zdefiniować, co faktycznie chcesz wdrożyć. W `parameters` `variables` przeciwieństwie do i (które są obiektami JSON), `resources` jest lista JSON obiektów JSON.

```json
   "resources": [
```

Wszystkie zasoby `type` `name`wymagają `apiVersion`, `location` , i właściwości. W tym przykładzie pierwszy zasób ma typ `myVnet` [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), name i apiVersion `2018-11-01`. (Aby znaleźć najnowszą wersję interfejsu API dla typu zasobu, zobacz [odwołanie do szablonu usługi Azure Resource Manager).](/azure/templates/)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Określ lokalizację
Aby określić lokalizację sieci wirtualnej, użyj [funkcji szablonu Menedżera zasobów](../azure-resource-manager/templates/template-functions.md). Ta funkcja musi być ujęta w cudzysłowie i nawiasy kwadratowe w ten sposób: `"[<template-function>]"`. W takim przypadku `resourceGroup` należy użyć funkcji. Przyjmuje żadnych argumentów i zwraca obiekt JSON z metadanymi o grupie zasobów, do jakiej jest wdrażane to wdrożenie. Grupa zasobów jest ustawiana przez użytkownika w momencie wdrażania. Ta wartość jest następnie indeksowane do `.location` tego obiektu JSON z aby uzyskać lokalizację z obiektu JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Określanie właściwości sieci wirtualnej
Każdy zasób Menedżera `properties` zasobów ma własną sekcję dla konfiguracji specyficznych dla zasobu. W takim przypadku należy określić, że sieć wirtualna powinna `10.0.0.0/16`mieć jedną podsieć przy użyciu zakresu prywatnych adresów IP . Zestaw skalowania jest zawsze zawarty w jednej podsieci. Nie może obejmować podsieci.

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

## <a name="add-dependson-list"></a>Dodaj dependsOn lista
`type`Oprócz wymaganego `name`, , `apiVersion`i `location` właściwości, każdy zasób `dependsOn` może mieć opcjonalną listę ciągów. Ta lista określa, które inne zasoby z tego wdrożenia muszą zostać zakończono przed wdrożeniem tego zasobu.

W takim przypadku istnieje tylko jeden element na liście, sieci wirtualnej z poprzedniego przykładu. Należy określić tę zależność, ponieważ zestaw skalowania potrzebuje sieci istnieć przed utworzeniem żadnych maszyn wirtualnych. W ten sposób zestaw skalowania może nadać tym wirtualnym prywatnym adresom IP z zakresu adresów IP określonego wcześniej we właściwościach sieci. Format każdego ciągu na liście dependsOn to `<type>/<name>`. Użyj tego `type` `name` samego i używane wcześniej w definicji zasobów sieci wirtualnej.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Określanie właściwości zestawu skali
Zestawy skalowania mają wiele właściwości dostosowywania maszyn wirtualnych w zestawie skalowania. Aby uzyskać pełną listę tych właściwości, zobacz [odwołanie do szablonu](/azure/templates/microsoft.compute/virtualmachinescalesets). W tym samouczku ustawiono tylko kilka często używanych właściwości.
### <a name="supply-vm-size-and-capacity"></a>Rozmiar i pojemność maszyny wirtualnej
Zestaw skalowania musi wiedzieć, jaki rozmiar maszyny Wirtualnej do utworzenia ("nazwa sku") i ile takich maszyn wirtualnych do utworzenia ("sku capacity"). Aby zobaczyć, które rozmiary maszyn wirtualnych są dostępne, zobacz [dokumentację Rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Wybieranie typu aktualizacji
Zestaw skalowania musi również wiedzieć, jak obsługiwać aktualizacje w zestawie skalowania. Obecnie istnieją trzy opcje, `Manual` `Rolling` i `Automatic`. Aby uzyskać więcej informacji na temat różnic między nimi, zobacz dokumentację [dotyczącą uaktualniania zestawu skalowania](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Wybierz system operacyjny maszyny Wirtualnej
Zestaw skalowania musi wiedzieć, jaki system operacyjny należy umieścić na maszynach wirtualnych. Tutaj utwórz maszyny wirtualne z w pełni połatanym obrazem Ubuntu 16.04-LTS.

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

### <a name="specify-computernameprefix"></a>Określanie przycisku ComputerNamePrefix
Zestaw skalowania wdraża wiele maszyn wirtualnych. Zamiast określać każdą nazwę maszyny `computerNamePrefix`Wirtualnej, należy określić . Zestaw skalowania dołącza indeks do prefiksu dla każdej maszyny Wirtualnej, więc nazwy maszyn wirtualnych mają formularz `<computerNamePrefix>_<auto-generated-index>`.

We wtorkowym urywku użyj parametrów sprzed, aby ustawić nazwę użytkownika i hasło administratora dla wszystkich maszyn wirtualnych w zestawie skalowania. Ten proces używa `parameters` funkcji szablonu. Ta funkcja przyjmuje ciąg, który określa, który parametr ma się odwoływać i wyprowadza wartość dla tego parametru.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Określanie konfiguracji sieci maszyn wirtualnych
Na koniec określ konfigurację sieci dla maszyn wirtualnych w zestawie skalowania. W takim przypadku wystarczy tylko określić identyfikator podsieci utworzonej wcześniej. To informuje zestaw skalowania, aby umieścić interfejsy sieciowe w tej podsieci.

Identyfikator sieci wirtualnej zawierającej podsieć można uzyskać `resourceId` za pomocą funkcji szablonu. Ta funkcja przyjmuje typ i nazwę zasobu i zwraca w pełni kwalifikowany identyfikator tego zasobu. Ten identyfikator ma formularz:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Jednak identyfikator sieci wirtualnej nie wystarczy. Podaj określoną podsieć, w których powinny znajdować się maszyny wirtualne zestawu skalowania. Aby to zrobić, `/subnets/mySubnet` połącz się z identyfikatorem sieci wirtualnej. Wynikiem jest w pełni kwalifikowany identyfikator podsieci. Wykonaj to łączenia `concat` z funkcją, która przyjmuje w serii ciągów i zwraca ich łączenia.

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

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
