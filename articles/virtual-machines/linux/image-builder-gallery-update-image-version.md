---
title: Tworzenie nowej wersji obrazu maszyny Wirtualnej z istniejącej wersji obrazu przy użyciu programu Azure Image Builder (wersja zapoznawcza)
description: Utwórz nową wersję obrazu maszyny Wirtualnej z istniejącej wersji obrazu przy użyciu programu Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 5766e91dc6a17d50c46d396dd8a68d17081e0926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246810"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Wersja zapoznawcza: tworzenie nowej wersji obrazu maszyny Wirtualnej z istniejącej wersji obrazu przy użyciu programu Azure Image Builder

W tym artykule pokazano, jak zrobić istniejącą wersję obrazu w [galerii obrazów udostępnionych,](shared-image-galleries.md)zaktualizować ją i opublikować jako nową wersję obrazu w galerii.

Będziemy używać przykładowego szablonu .json do skonfigurowania obrazu. Plik .json używamy jest tutaj: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Zarejestruj funkcje
Aby korzystać z usługi Azure Image Builder podczas podglądu, należy zarejestrować nową funkcję.

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

Jeśli nie mówią zarejestrowane, uruchom następujące czynności:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Ustawianie zmiennych i uprawnień

Jeśli [użyto utworzenia obrazu i dystrybucji do galerii obrazów udostępnionych](image-builder-gallery.md) w celu utworzenia galerii obrazów udostępnionych, utworzono już niektóre zmienne, których potrzebujemy. Jeśli nie, należy skonfigurować niektóre zmienne, które mają być używane w tym przykładzie.

W przypadku wersji zapoznawczej konstruktor obrazów będzie obsługiwał tworzenie obrazów niestandardowych tylko w tej samej grupie zasobów, co obraz zarządzany źródłowy. Zaktualizuj nazwę grupy zasobów w tym przykładzie, aby była tą samą grupą zasobów co obraz zarządzany źródłowy.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to uzyskać `az account show | grep id`za pomocą programu .

```console
subscriptionID=<Subscription ID>
```

Pobierz wersję obrazu, którą chcesz zaktualizować.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Jeśli masz już własną galerię obrazów udostępnionych i nie postępuj zgodnie z poprzednim przykładem, musisz przypisać uprawnienia do Konstruktora obrazów, aby uzyskać dostęp do grupy zasobów, aby mogła uzyskać dostęp do galerii.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Modyfikowanie przykładu helloImage
Możesz przejrzeć przykład, którego mamy zamiar użyć, otwierając plik .json tutaj: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) wraz z [odwołaniem do szablonu Image Builder](image-builder-json.md). 


Pobierz przykład .json i skonfiguruj go ze zmiennymi. 

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu do usługi konstruktora obrazów maszyn wirtualnych.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Rozpocznij kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Poczekaj, aż obraz zostanie utworzony i replikacji przed przejściem do następnego kroku.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Utwórz połączenie SSH z maszyną wirtualną przy użyciu publicznego adresu IP maszyny Wirtualnej.

```console
ssh azureuser@<pubIp>
```

Powinieneś zobaczyć obraz został dostosowany z "Wiadomość dnia" natychmiast po nawiązaniu połączenia SSH.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Wpisz, `exit` aby zamknąć połączenie SSH.

Można również wyświetlić listę wersji obrazów, które są teraz dostępne w galerii.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach pliku .json użytego w tym artykule, zobacz [Odwołanie do szablonu konstruktora obrazów](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).