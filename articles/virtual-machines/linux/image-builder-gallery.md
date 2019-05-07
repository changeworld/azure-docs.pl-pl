---
title: Użyj Kreatora obrazów platformy Azure z galerii obrazów maszyn wirtualnych systemu Linux (wersja zapoznawcza)
description: Tworzenie obrazów systemu Linux przy użyciu Kreatora obrazów platformy Azure i udostępnione galerii obrazów.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e9a8a30d9f5f170073c0ad671a248703b1078864
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159498"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Wersja zapoznawcza: Tworzenie obrazu systemu Linux i rozpowszechnienie go do galerii obrazów udostępnione 

W tym artykule pokazano, jak można użyć Kreatora obrazów platformy Azure do utworzenia wersji obrazu w [galerii obrazów współdzielona](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/shared-image-galleries), następnie globalnie dystrybuować obraz.


Firma Microsoft będzie używać przykładowy szablon JSON do obrazu. Użyto pliku JSON jest tutaj: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Aby rozesłać obraz do galerii obrazów udostępnione, szablon używa [sharedImage](image-builder-json.md#distribute-sharedimage) jako wartość pozycji `distribute` części szablonu.

> [!IMPORTANT]
> Kreator obrazów platformy Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Rejestrowanie funkcji
Aby użyć Kreatora obrazów platformy Azure w wersji zapoznawczej, musisz zarejestrować nową funkcję.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Sprawdź stan rejestracji funkcji.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Sprawdź swoją rejestrację.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Jeśli nie mówią zarejestrowanych, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Uprawnienia i Ustawianie zmiennych 

Firma Microsoft będzie używać niektórych rodzajów informacji wielokrotnie, dlatego utworzymy pewne zmienne do przechowywania tych informacji.

W wersji zapoznawczej Kreator obrazów obejmie tylko tworzenie niestandardowych obrazów w tej samej grupie zasobów zarządzanych obrazu źródłowego. Zaktualizuj nazwę grupy zasobów, w tym przykładzie jako źródło obrazu zarządzanego tej samej grupie zasobów.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Utwórz zmienną dla Twojego identyfikatora subskrypcji. Można uzyskać, to przy użyciu `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Utwórz grupę zasobów.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Nadaj uprawnienia kreatora obrazów platformy Azure do tworzenia zasobów w danej grupie zasobów. `--assignee` Wartość Identyfikatora rejestracji aplikacji dla usługi kreatora obrazów. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Utwórz definicję obrazu i galerii

Tworzenie galerii obrazów. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Utwórz definicję obrazu.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Pobierz i skonfiguruj .json

Pobierz szablon JSON i skonfigurować ją za pomocą zmiennych.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Ta część dalej utworzy wersję obrazu w galerii. 

Prześlij konfigurację obrazu, aby usługa kreatora obrazów platformy Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Tworzenie obrazu i zreplikowanie ich obu regionów może wymagać trochę czasu. Poczekaj, aż do ukończenia tej części przed przejściem do tworzenia maszyny Wirtualnej.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz Maszynę wirtualną z wersji obrazu, który został utworzony przez kreatora obrazów platformy Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Protokół SSH z maszyną wirtualną.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Powinien zostać wyświetlony obraz, który został dostosowany za pomocą *komunikat dnia* zaraz po nawiązaniu połączenia SSH!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz teraz Wypróbuj ponownie dostosowanie wersję obrazu, aby utworzyć nową wersję tego samego obrazu, możesz pominąć kolejne kroki i przejść do [kreatora obrazów platformy Azure Użyj, aby utworzyć inną wersję obrazu](image-builder-gallery-update-image-version.md).


Spowoduje to usunięcie obrazu, który został utworzony, oraz wszystkie inne pliki zasobów. Upewnij się, że po zakończeniu z tym wdrożeniem przed usunięciem zasobów.

Podczas usuwania zasoby galerii pakietu obrazu, należy usunąć wszystkie wersje obrazów przed usunięciem definicji obrazów użytych do ich utworzenia. Aby usunąć galerii, należy najpierw usunięto wszystkie definicje obraz w galerii.

Usuń szablon konstruktora obrazu.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Pobierz wersję obrazu, utworzone przez kreatora obrazów, tym zawsze zaczyna się od `0.`, a następnie usuń wersję obrazu

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Usuń definicję obrazu.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Usuń galerii.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Usuń grupę zasobów.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [galerie obrazów systemu Azure Shared](shared-image-galleries.md).