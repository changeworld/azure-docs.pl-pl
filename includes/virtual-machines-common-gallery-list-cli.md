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
ms.openlocfilehash: 1e78109472668c0f9a73af6430253a0d709979af
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156173"
---
## <a name="using-rbac-to-share-images"></a>Przy użyciu funkcji RBAC do udostępniania obrazów

Obrazy można udostępniać między subskrypcjami przy użyciu kontroli dostępu opartej na rolach (RBAC). Każdy użytkownik, który ma uprawnienia odczytu do wersji obrazu, nawet w różnych subskrypcjach, będzie można wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

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
az sig image-version list -g myGalleryRG -r myGallery -i myImageDefinition -o table
```

Pobierz identyfikator wersji obrazów przy użyciu [Pokaż wersję obrazu sig az](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   -g myGalleryRG \
   -r myGallery \
   -i myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```