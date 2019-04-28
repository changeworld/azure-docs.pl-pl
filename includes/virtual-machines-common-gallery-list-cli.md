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
ms.openlocfilehash: 7fd5b2051f81a5dc34270a608c1518e8a11678b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542473"
---
## <a name="using-rbac-to-share-images"></a>Przy użyciu funkcji RBAC do udostępniania obrazów

Obrazy można udostępniać między subskrypcjami przy użyciu kontroli dostępu na podstawie ról (RBAC). Każdy użytkownik, który ma uprawnienia odczytu do wersji obrazu, nawet w różnych subskrypcjach, będzie można wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

Aby uzyskać więcej informacji na temat udostępniania zasobów przy użyciu funkcji RBAC, zobacz [zarządzanie dostępem przy użyciu RBAC i wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Informacje o liście

Pobieranie lokalizacji, stanu i innych informacji na temat galerie dostępnych obrazów, za pomocą [listy sig az](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Lista definicji obraz w galerii, w tym informacje o typie systemu operacyjnego i stanu, przy użyciu [az sig definicję obrazu listy](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Utwórz listę wersji udostępnionego obrazu w galerii, za pomocą [az sig wersję obrazu listy](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myGalleryImage -o table
```

Pobierz identyfikator wersji obrazów przy użyciu [Pokaż wersję obrazu sig az](/cli/azure/sig/image-version#az-sig-image-version-show).

```
az sig image-version show \
-g myGalleryRG \     
-r myGallery \     
-i myGalleryImage \     
--gallery-image-version-name 1.0.0 \     
--query "id"
```