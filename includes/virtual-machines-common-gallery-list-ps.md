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
ms.openlocfilehash: f7539ed5083a386ef05134bea36426f4a360afad
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192243"
---
## <a name="shared-image-management"></a>Zarządzanie udostępnionego obrazu 

Poniżej przedstawiono kilka przykładów typowych zadań zarządzania i jak je wykonać za pomocą programu PowerShell.

Wyświetl listę wszystkich galeriach według nazwy.

```azurepowershell-interactive
$galleries = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Wyświetl listę wszystkich definicji obrazu według nazwy.

```azurepowershell-interactive
$imageDefinitions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Wyświetl listę wszystkich wersji obrazu według nazwy.

```azurepowershell-interactive
$imageVersions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Usuń wersję obrazu. W tym przykładzie usuwa wersję obrazu o nazwie *1.0.0*.

```azurepowershell-interactive
Remove-AzureRmGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





