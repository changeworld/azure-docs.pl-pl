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
ms.openlocfilehash: 10b95a92f36ad6eb340ae864cbfd9fcbeac371a8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148772"
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

