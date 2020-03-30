---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66226004"
---
## <a name="using-rbac-to-share-images"></a>Udostępnianie obrazów za pomocą funkcji RBAC

Obrazy można udostępniać w różnych subskrypcjach przy użyciu kontroli dostępu opartej na rolach (RBAC). Każdy użytkownik, który ma uprawnienia do odczytu do wersji obrazu, nawet w ramach subskrypcji, będzie mógł wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

Aby uzyskać więcej informacji na temat udostępniania zasobów przy użyciu funkcji RBAC, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Informacje o liście

Uzyskaj lokalizację, status i inne informacje na temat dostępnych galerii obrazów za pomocą [listy az sig](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Lista definicji obrazów w galerii, w tym informacje o typie i stanie systemu operacyjnego, za pomocą [listy az sig image-definition](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Lista wersji obrazu udostępnionego w galerii, za pomocą [listy az sig image-version](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Pobierz identyfikator wersji obrazu za pomocą [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```