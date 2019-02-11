---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a55653c0f23be594fe65e7a322c11edc37ee1ce6
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984996"
---
## <a name="shared-image-management"></a>Zarządzanie udostępnionego obrazu 

Poniżej przedstawiono kilka przykładów typowych zadań zarządzania i jak je wykonać za pomocą programu PowerShell.

Wyświetl listę wszystkich galeriach według nazwy.

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Wyświetl listę wszystkich definicji obrazu według nazwy.

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Wyświetl listę wszystkich wersji obrazu według nazwy.

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Usuń wersję obrazu. W tym przykładzie usuwa wersję obrazu o nazwie *1.0.0*.

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





