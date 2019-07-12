---
title: Utwórz nową wersję obrazu z istniejącą wersję obrazu za pomocą Kreatora obrazów platformy Azure (wersja zapoznawcza)
description: Utwórz nową wersję obrazu z istniejącą wersję obrazu za pomocą Kreatora obrazów platformy Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d60a7680bc283ba015d0649fb1d2671f8e5cf793
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718636"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Wersja zapoznawcza: Utwórz nową wersję obrazu z istniejącą wersję obrazu za pomocą Kreatora obrazów platformy Azure

W tym artykule pokazano, jak wykonać istniejącą wersję obrazu [galerii obrazów współdzielona](shared-image-galleries.md), zaktualizuj go i opublikujesz je jako nową wersję obrazu w galerii.

Firma Microsoft będzie używać przykładowy szablon JSON do obrazu. Użyto pliku JSON jest tutaj: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Jeśli nie mówią zarejestrowanych, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Uprawnienia i Ustawianie zmiennych

Jeśli użyto [utworzyć obraz i dystrybuować do galerii obrazów współdzielona](image-builder-gallery.md) utworzyć z galerii obrazów współdzielona została już utworzona zmiennych, potrzebujemy. W przeciwnym razie skonfiguruj pewnych zmiennych, które ma być używany w tym przykładzie.

W wersji zapoznawczej Kreator obrazów obejmie tylko tworzenie niestandardowych obrazów w tej samej grupie zasobów zarządzanych obrazu źródłowego. Zaktualizuj nazwę grupy zasobów, w tym przykładzie jako źródło obrazu zarządzanego tej samej grupie zasobów.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Utwórz zmienną dla Twojego identyfikatora subskrypcji. Można uzyskać, to przy użyciu `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Pobierz wersję obrazu, który chcesz zaktualizować.

```azurecli-interactive
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
Możesz przejrzeć przykład jesteśmy użycia, otwierając plik JSON w tym miejscu: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) wraz z [odwołanie do szablonu Kreatora obrazów](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


Pobierz przykład JSON i skonfigurować ją za pomocą zmiennych. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu, aby usługa Konstruktora obrazów maszyn wirtualnych.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Zaczekaj, aż po skompilowaniu obrazu i replikacji przed przejściem do następnego kroku.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Sprawdź dostosowania
Utwórz połączenie pulpitu zdalnego z maszyną wirtualną przy użyciu nazwy użytkownika i hasło, którego jest ustawiany podczas tworzenia maszyny Wirtualnej. Wewnątrz maszyny Wirtualnej Otwórz wiersz polecenia i wpisz:

```console
dir c:\
```

Teraz powinny zostać wyświetlone dwa katalogi:
- `buildActions` utworzony w pierwszej wersji obrazu.
- `buildActions2` który został utworzony w ramach się aktualizowanie pierwsza wersja obrazu do tworzenia drugiego wersję obrazu.


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o składnikach plik JSON używanych w tym artykule, zobacz [obrazu odwołanie do szablonu konstruktora](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).