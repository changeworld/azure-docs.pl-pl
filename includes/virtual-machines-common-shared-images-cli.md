---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814910"
---
## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć przykład w tym artykule, musisz mieć istniejący obraz zarządzany uogólnionej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie niestandardowego obrazu maszyny Wirtualnej platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) Jeśli obraz zarządzany zawiera dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/bash](https://shell.azure.com/bash)osobnej karcie przeglądarki, przechodząc do . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować i używać interfejsu wiersza polecenia lokalnie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazów. Dozwolone znaki nazwy galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek.   Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Tworzenie galerii obrazów przy użyciu [az sig create](/cli/azure/sig#az-sig-create). Poniższy przykład tworzy galerię o nazwie *myGallery* w *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersjach obrazów, które są tworzone w nich. Nazwy definicji obrazu mogą być składane z wielkich lub małych liter, cyfr, kropek, kresek i kropek. Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [Definicje obrazów](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Utwórz początkową definicję obrazu w galerii za pomocą [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Tworzenie wersji obrazu 

Tworzenie wersji obrazu w razie potrzeby za pomocą [galerii obrazów az create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Należy przekazać identyfikatora zarządzanego obrazu, aby użyć go jako linii bazowej do tworzenia wersji obrazu. Za pomocą [listy obrazów az](/cli/azure/image?view#az-image-list) można uzyskać informacje o obrazach w grupie zasobów. 

Dozwolone znaki dla wersji obrazu to liczby i kropki. Liczby muszą mieszczeć się w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja naszego obrazu jest *1.0.0* i mamy zamiar utworzyć 2 repliki w regionie *Zachodnio-środkowe stany USA,* 1 replika w regionie *południowo-środkowej części USA* i 1 repliki w regionie *Wschodnie stany USA 2* przy użyciu magazynu strefowo nadmiarowego.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Musisz poczekać, aż wersja obrazu całkowicie zakończyć budowa i replikowanie, zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.
>
> Można również przechowywać wszystkie repliki wersji obrazu w `--storage-account-type standard_zrs` [magazynie nadmiarowym strefy,](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) dodając podczas tworzenia wersji obrazu.
>

## <a name="share-the-gallery"></a>Udostępnianie galerii

Zalecamy udostępnianie innym użytkownikom na poziomie galerii. Aby uzyskać identyfikator obiektu galerii, użyj [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Użyj identyfikatora obiektu jako zakresu, wraz z adresem e-mail i [az przypisanie roli utworzyć,](/cli/azure/role/assignment#az-role-assignment-create) aby dać użytkownikowi dostęp do galerii obrazów udostępnionych.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


