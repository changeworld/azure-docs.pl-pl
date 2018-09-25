---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 830deb7569772b610b7e6abde649830b7ad67a92
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047843"
---
## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć przykład, w tym artykule, konieczne jest posiadanie istniejącego obrazu zarządzanego uogólnionej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [samouczek: Tworzenie niestandardowego obrazu maszyny wirtualnej portalu Azure za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>Wersja zapoznawcza: Zarejestruj funkcję

Udostępnione galerie obrazów jest w wersji zapoznawczej, ale trzeba zarejestrować tę funkcję, przed jego użyciem. Aby zarejestrować funkcji udostępnione, galerie obrazów:

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

Może upłynąć kilka minut, aby zarejestrować tę funkcję. Aby sprawdzić, postępu przy użyciu:

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowy zasób umożliwiający włączenie udostępniania obrazów. Galeria nazwy muszą być unikatowe w ramach Twojej subskrypcji. Tworzenie galerii obrazów przy użyciu [tworzenie az sig](/cli/azure/sig#az-sig-create). Poniższy przykład tworzy Galeria o nazwie *myGallery* w *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Utwórz definicję obrazu

Tworzenie definicji początkowej obraz w galerii, używając [utworzyć definicję obrazu az sig](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>Utwórz wersję obrazu 
 
Tworzenie wersji obrazu, stosownie do potrzeb, przy użyciu [az obraz galerii tworzenia obrazu — wersji](/cli/azure/sig/image-version#az-sig-image-version-create). Musisz przekazać identyfikator obrazu zarządzanego do użycia jako podstawy do tworzenia wersję obrazu. Możesz użyć [listy obrazów az](/cli/azure/image?view#az-image-list) można pobrać informacji o obrazach znajdujących się w grupie zasobów. W tym przykładzie jest wersja naszych obrazu *1.0.0* i zamierzamy utworzyć 5, łączna liczba replik w *zachodnio-środkowe stany USA*, *południowo-środkowe stany USA* i wschodnie stany USA 2 * regionów.

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

