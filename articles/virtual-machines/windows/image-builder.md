---
title: Tworzenie maszyny Wirtualnej z systemem Windows przy użyciu Kreatora obrazów platformy Azure (wersja zapoznawcza)
description: Utwórz Windows maszyny Wirtualnej za pomocą Kreatora obrazów platformy Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: 01109aa83c12bda9b1d21ec25784d663f8abf700
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159723"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Wersja zapoznawcza: Tworzenie Windows maszyny Wirtualnej za pomocą Kreatora obrazów platformy Azure

W tym artykule jest dowiesz się, jak utworzyć dostosowany obraz Windows za pomocą Kreatora obrazów maszyn wirtualnych platformy Azure. W przykładzie w tym artykule używa trzech różnych [twórcom dostosowań](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) dostosowywania obrazu:
- PowerShell (ScriptUri) — pobieranie i uruchamianie [skrypt programu PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Ponowne uruchomienie Windows - powoduje ponowne uruchomienie maszyny Wirtualnej.
- PowerShell (wbudowane) — uruchom określone polecenie. W tym przykładzie tworzy katalog na maszynę Wirtualną przy użyciu `mkdir c:\\buildActions`.
- Plik — skopiować plik z repozytorium GitHub na maszynie Wirtualnej. W tym przykładzie kopiuje [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do `c:\buildArtifacts\index.html` na maszynie Wirtualnej.

Firma Microsoft będzie używać przykładowy szablon JSON do obrazu. Użyto pliku JSON jest tutaj: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Firma Microsoft będzie używać niektórych rodzajów informacji wielokrotnie, dlatego utworzymy pewne zmienne do przechowywania tych informacji.

```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Utwórz zmienną dla Twojego identyfikatora subskrypcji. Można uzyskać, to przy użyciu `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Utwórz grupę zasobów.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Nadaj uprawnienia kreatora obrazów do tworzenia zasobów w danej grupie zasobów. `--assignee` Wartość Identyfikatora rejestracji aplikacji dla usługi kreatora obrazów. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Pobierz przykład .json

Pobierz przykładowy plik JSON i skonfiguruj ją za pomocą zmiennych, który został utworzony.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu, aby usługa kreatora obrazów maszyn wirtualnych

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Zaczekaj, aż kompilacja zostanie zakończona. Może to potrwać około 15 minut.

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny Wirtualnej przy użyciu tworzonej obrazu. Zastąp *<password>* przy użyciu hasła dla `aibuser` na maszynie Wirtualnej.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Sprawdź dostosowania

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną przy użyciu nazwy użytkownika i hasło, którego jest ustawiany podczas tworzenia maszyny Wirtualnej. Wewnątrz maszyny Wirtualnej Otwórz wiersz polecenia i wpisz:

```console
dir c:\
```

Powinny zostać wyświetlone następujące dwa katalogi utworzone podczas dostosowywania obrazu:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Czyszczenie

Gdy wszystko będzie gotowe, Usuń zasoby.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o składnikach plik JSON używanych w tym artykule, zobacz [obrazu odwołanie do szablonu konstruktora](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

