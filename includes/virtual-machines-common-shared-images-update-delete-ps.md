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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183194"
---
## <a name="update-resources"></a>Aktualizowanie zasobów

Istnieją pewne ograniczenia dotyczące tego, co można zaktualizować. Można zaktualizować następujące elementy: 

Galeria zdjęć udostępnionych:
- Opis

Definicja obrazu:
- Zalecane procesory wirtualne
- Zalecana pamięć
- Opis
- Data końca daty daty

Wersja obrazu:
- Liczba replik regionalnych
- Regiony docelowe
- Wyłączenie z najnowszych
- Data końca daty daty

Jeśli planujesz dodawanie regionów repliki, nie należy usuwać obrazu zarządzanego źródłowego. Obraz zarządzany źródłowego jest potrzebny do replikowania wersji obrazu do dodatkowych regionów. 

Aby zaktualizować opis galerii, użyj [update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

W tym przykładzie pokazano, jak użyć [Update-AzGalleryImageDefinition,](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) aby zaktualizować datę końca życia dla naszej definicji obrazu.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

W tym przykładzie pokazano, jak użyć [Update-AzGalleryImageVersion,](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) aby wykluczyć tę wersję obrazu z używanego jako *najnowszy* obraz.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Podczas usuwania zasobów, należy rozpocząć z ostatniego elementu w zagnieżdżonych zasobów - wersja obrazu. Po usunięciu wersji można usunąć definicję obrazu. Nie można usunąć galerii, dopóki wszystkie zasoby pod nią nie zostaną usunięte.

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

