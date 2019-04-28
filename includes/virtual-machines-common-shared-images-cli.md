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
ms.openlocfilehash: 36c4757feb367fd39ae94640cb8e8a0f1714a0d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542495"
---
## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć przykład, w tym artykule, konieczne jest posiadanie istniejącego obrazu zarządzanego uogólnionej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie niestandardowego obrazu maszyny wirtualnej portalu Azure za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

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
 
Tworzenie wersji obrazu, stosownie do potrzeb, przy użyciu [az obraz galerii tworzenia obrazu — wersji](/cli/azure/sig/image-version#az-sig-image-version-create). Musisz przekazać identyfikator obrazu zarządzanego do użycia jako podstawy do tworzenia wersję obrazu. Możesz użyć [listy obrazów az](/cli/azure/image?view#az-image-list) można pobrać informacji o obrazach znajdujących się w grupie zasobów. W tym przykładzie jest wersja naszych obrazu *1.0.0* i zamierzamy utworzyć 5 replik w *zachodnio-środkowe stany USA* region, 1 repliki w *południowo-środkowe stany USA* region i 1 repliki w *wschodnie stany USA 2* regionu.

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

