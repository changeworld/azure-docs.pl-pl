---
title: Korzystanie z programu Azure Image Builder z galerią obrazów dla maszyn wirtualnych z systemem Linux (wersja zapoznawcza)
description: Tworzenie obrazów maszyn wirtualnych systemu Linux za pomocą usługi Azure Image Builder i Shared Image Gallery.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945021"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Wersja zapoznawcza: tworzenie obrazu systemu Linux i rozpowszechnianie go w galerii obrazów udostępnionych 

W tym artykule pokazano, jak można użyć usługi Azure Image Builder i interfejsu wiersza polecenia platformy Azure, aby utworzyć wersję obrazu w [galerii obrazów udostępnionych,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)a następnie rozpowszechniać obraz globalnie. Można to również zrobić za pomocą [programu Azure PowerShell](../windows/image-builder-gallery.md).


Będziemy używać przykładowego szablonu .json do skonfigurowania obrazu. Plik .json używamy jest tutaj: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Aby rozpowszechniać obraz w galerii obrazów udostępnionych, `distribute` szablon używa [sharedImage](image-builder-json.md#distribute-sharedimage) jako wartości sekcji szablonu.

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Będziemy wielokrotnie wykorzystywać niektóre informacje, więc utworzymy pewne zmienne do przechowywania tych informacji.

W przypadku wersji zapoznawczej konstruktor obrazów będzie obsługiwał tworzenie obrazów niestandardowych tylko w tej samej grupie zasobów, co obraz zarządzany źródłowy. Zaktualizuj nazwę grupy zasobów w tym przykładzie, aby była tą samą grupą zasobów co obraz zarządzany źródłowy.

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

Utwórz zmienną dla identyfikatora subskrypcji. Można to uzyskać `az account show | grep id`za pomocą programu .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Utwórz grupę zasobów.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Nadaj platformie Azure Image Builder uprawnienia do tworzenia zasobów w tej grupie zasobów. Wartość `--assignee` jest identyfikatorem rejestracji aplikacji dla usługi Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Tworzenie definicji obrazu i galerii

Aby korzystać z Kreatora obrazów z galerią obrazów udostępnionych, musisz mieć istniejącą galerię obrazów i definicję obrazu. Kreator obrazów nie utworzy dla Ciebie galerii obrazów i definicji obrazu.

Jeśli nie masz jeszcze galerii i definicji obrazu do użycia, zacznij od ich utworzenia. Najpierw utwórz galerię obrazów.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Następnie utwórz definicję obrazu.

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


## <a name="download-and-configure-the-json"></a>Pobieranie i konfigurowanie pliku .json

Pobierz szablon .json i skonfiguruj go ze zmiennymi.

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

## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Ta następna część utworzy wersję obrazu w galerii. 

Prześlij konfigurację obrazu do usługi Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Rozpocznij kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Tworzenie obrazu i replikowanie go do obu regionów może zająć trochę czasu. Poczekaj, aż ta część zostanie zakończona przed przejściem do tworzenia maszyny Wirtualnej.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną z wersji obrazu, który został utworzony przez usługę Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Nawiąż połączenie SSH z maszyną wirtualną.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Powinieneś zobaczyć obraz został dostosowany z *Wiadomością dnia,* gdy tylko połączenie SSH zostanie nawiązane!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz teraz spróbować ponownie dostosować wersję obrazu, aby utworzyć nową wersję tego samego obrazu, pomiń kolejne kroki i przejdź do [programu Azure Image Builder, aby utworzyć inną wersję obrazu](image-builder-gallery-update-image-version.md).


Spowoduje to usunięcie obrazu, który został utworzony, wraz ze wszystkimi innymi plikami zasobów. Upewnij się, że to wdrożenie zostało zakończone przed usunięciem zasobów.

Podczas usuwania zasobów galerii obrazów należy usunąć wszystkie wersje obrazów, aby można było usunąć definicję obrazu używaną do ich utworzenia. Aby usunąć galerię, musisz najpierw usunąć wszystkie definicje obrazów w galerii.

Usuń szablon konstruktora obrazów.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Pobierz wersję obrazu utworzoną przez konstruktora obrazów, zawsze zaczyna się od `0.`, a następnie usuń wersję obrazu

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

Usuń galerię.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Usuń grupę zasobów.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [galeriach obrazów udostępnionych platformy Azure](shared-image-galleries.md).