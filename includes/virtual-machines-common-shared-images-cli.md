---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 7e4ca54d8f97646192d19d5923bee24a906e8df7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149714"
---
## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć przykład, w tym artykule, konieczne jest posiadanie istniejącego obrazu zarządzanego uogólnionej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie niestandardowego obrazu maszyny wirtualnej portalu Azure za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 


## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowy zasób umożliwiający włączenie udostępniania obrazów. Dozwolone znaki dla nazwy galerii są wielkie i małe litery, cyfry, kropki i okresów. Nazwa galerii nie może zawierać łączników.   Galeria nazwy muszą być unikatowe w ramach Twojej subskrypcji. 

Tworzenie galerii obrazów przy użyciu [tworzenie az sig](/cli/azure/sig#az-sig-create). Poniższy przykład tworzy Galeria o nazwie *myGallery* w *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Utwórz definicję obrazu

Definicje obraz Utwórz logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersji obrazu, które są tworzone w ramach ich. Obraz nazwy definicji może składać się z wielkie i małe litery, cyfry, kropki, łączniki i kropki. Aby uzyskać więcej informacji na temat wartości można określić definicję obrazu, zobacz [obrazu definicje](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

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

Tworzenie wersji obrazu, stosownie do potrzeb, przy użyciu [az obraz galerii tworzenia obrazu — wersji](/cli/azure/sig/image-version#az-sig-image-version-create). Musisz przekazać identyfikator obrazu zarządzanego do użycia jako podstawy do tworzenia wersję obrazu. Możesz użyć [listy obrazów az](/cli/azure/image?view#az-image-list) można pobrać informacji o obrazach znajdujących się w grupie zasobów. 

Dozwolone znaki wersję obrazu są liczby i kropki. Numery muszą należeć do zakresu 32-bitową liczbę całkowitą. Format: *Brak elementu MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie jest wersja naszych obrazu *1.0.0* i zamierzamy utworzyć 2 repliki w *zachodnio-środkowe stany USA* region, 1 repliki w *południowo-środkowe stany USA* region i 1 repliki w *wschodnie stany USA 2* regionu.


```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Musisz czekać na wersję obrazu do całkowitego zakończenia są zbudowane i replikowane korzystać z tego samego obrazu zarządzanego, aby utworzyć inną wersję obrazu.