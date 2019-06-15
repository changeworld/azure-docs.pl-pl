---
title: Utwórz nową wersję obrazu z istniejącą wersję obrazu za pomocą Kreatora obrazów platformy Azure (wersja zapoznawcza)
description: Utwórz nową wersję obrazu z istniejącą wersję obrazu za pomocą Kreatora obrazów platformy Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 31ef53abcf9b416500ee70e42cc3cbd12cb11f35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159543"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Wersja zapoznawcza: Utwórz nową wersję obrazu z istniejącą wersję obrazu za pomocą Kreatora obrazów platformy Azure

W tym artykule pokazano, jak wykonać istniejącą wersję obrazu [galerii obrazów współdzielona](shared-image-galleries.md), zaktualizuj go i opublikujesz je jako nową wersję obrazu w galerii.

Firma Microsoft będzie używać przykładowy szablon JSON do obrazu. Użyto pliku JSON jest tutaj: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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

Jeśli użyto [utworzyć obraz i dystrybuować do galerii obrazów współdzielona](image-builder-gallery.md) utworzyć z galerii obrazów udostępnione zostały już utworzone niektóre zmienne, potrzebujemy. W przeciwnym razie skonfiguruj pewnych zmiennych, które ma być używany w tym przykładzie.

W wersji zapoznawczej Kreator obrazów obejmie tylko tworzenie niestandardowych obrazów w tej samej grupie zasobów zarządzanych obrazu źródłowego. Zaktualizuj nazwę grupy zasobów, w tym przykładzie jako źródło obrazu zarządzanego tej samej grupie zasobów.


```azurecli-interactive
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

Utwórz zmienną dla Twojego identyfikatora subskrypcji. Można uzyskać, to przy użyciu `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Pobierz wersję obrazu, który chcesz zaktualizować.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Jeśli już masz własne współdzielona galerii obrazów, a nie korzystał z poprzedniego przykładu, należy przypisać uprawnienia dla Kreatora obrazów na dostęp do grupy zasobów, dzięki czemu może uzyskiwać dostęp galerii.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Zmodyfikuj przykład helloImage
Możesz przejrzeć przykład jesteśmy użycia, otwierając plik JSON w tym miejscu: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) wraz z [odwołanie do szablonu Kreatora obrazów](image-builder-json.md). 


Pobierz przykład JSON i skonfigurować ją za pomocą zmiennych. 

```azurecli-interactive
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

Prześlij konfigurację obrazu, aby usługa Konstruktora obrazów maszyn wirtualnych.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Zaczekaj, aż po skompilowaniu obrazu i replikacji przed przejściem do następnego kroku.


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

Utwórz połączenie SSH z maszyną wirtualną przy użyciu publicznego adresu IP maszyny wirtualnej.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Powinieneś zobaczyć, że obraz został dostosowany za pomocą "komunikat o Day" zaraz po nawiązaniu połączenia SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` można zamknąć połączenia SSH.

Możesz również wyświetlić listę wersji obrazu, które są teraz dostępne w galerii.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o składnikach plik JSON używanych w tym artykule, zobacz [obrazu odwołanie do szablonu konstruktora](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).