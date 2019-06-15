---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66145800"
---
## <a name="update-resources"></a>Aktualizowanie zasobów

Istnieją pewne ograniczenia, w jakie mogą być aktualizowane. Można zaktualizować następujących elementów: 

Galeria obrazów udostępnionych:
- Opis

definicję obrazu:
- Zalecane procesorów wirtualnych Vcpu
- Zalecana ilość pamięci
- Opis
- Data

Wersja obrazu:
- Liczba replik regionalne
- Regiony docelowe
- Wykluczenie z najnowsze
- Data

Jeśli planowane jest dodawanie regionów repliki, nie usuwaj zarządzanego obrazu źródłowego. Obrazu zarządzanego źródła jest wymagany dla replikacji wersję obrazu na dodatkowe regiony. 

Aby zaktualizować opis galerii, użyj [AzGallery aktualizacji](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

W tym przykładzie pokazano, jak używać [AzGalleryImageDefinition aktualizacji](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) Aby zaktualizować datę zakończenia eksploatacji naszych definicję obrazu.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

W tym przykładzie pokazano, jak używać [AzGalleryImageVersion aktualizacji](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) wyłączenie tej wersji obrazu z używany jako *najnowsze* obrazu.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas usuwania zasobów, należy uruchomić z ostatniego elementu w zagnieżdżonych zasobów - wersję obrazu. Usunięcie wersji możesz usunąć definicję obrazu. Nie można usunąć galerii, dopóki nie zostały usunięte wszystkie zasoby znajdujące się poniżej.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

