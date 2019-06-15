---
title: Użyj Kreatora obrazów platformy Azure z galerii obrazów maszyn wirtualnych Windows (wersja zapoznawcza)
description: Tworzenie obrazów Windows za pomocą Kreatora obrazów platformy Azure i udostępnione galerii obrazów.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-widows
manager: jeconnoc
ms.openlocfilehash: 2453d37720bcf48b95b428cf78c6186de40b31aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160113"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Wersja zapoznawcza: Tworzenie obrazu Windows i rozpowszechnienie go do galerii obrazów udostępnione 

W tym artykule jest pokazanie, jak można użyć Kreatora obrazów platformy Azure do utworzenia wersji obrazu w [galerii obrazów współdzielona](shared-image-galleries.md), następnie globalnie dystrybuować obraz.

Firma Microsoft będzie używany szablon JSON do obrazu. Użyto pliku JSON jest tutaj: [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Aby rozesłać obraz do galerii obrazów udostępnione, szablon używa [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) jako wartość pozycji `distribute` części szablonu.

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

Firma Microsoft będzie używać niektórych rodzajów informacji wielokrotnie, dlatego utworzymy pewne zmienne do przechowywania tych informacji. Zastąp wartości dla zmiennych, takie jak `username` i `vmpassword`, podając własne informacje.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Utwórz zmienną dla Twojego identyfikatora subskrypcji. Można uzyskać, to przy użyciu `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
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
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Pobierz i skonfiguruj .json

Pobierz szablon JSON i skonfigurować ją za pomocą zmiennych.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Ta część dalej utworzy wersję obrazu w galerii. 

Prześlij konfigurację obrazu, aby usługa kreatora obrazów platformy Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Tworzenie obrazu i zreplikowanie ich obu regionów może wymagać trochę czasu. Poczekaj, aż do ukończenia tej części przed przejściem do tworzenia maszyny Wirtualnej.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz Maszynę wirtualną z wersji obrazu, który został utworzony przez kreatora obrazów platformy Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
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

Powinien zostać wyświetlony katalog o nazwie `buildActions` utworzonej podczas dostosowywania obrazu.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli chcesz teraz Wypróbuj ponownie dostosowanie wersję obrazu, aby utworzyć nową wersję tego samego obrazu **Pomiń ten krok** i przejdź do [kreatora obrazów platformy Azure Użyj, aby utworzyć inną wersję obrazu](image-builder-gallery-update-image-version.md).


Spowoduje to usunięcie obrazu, który został utworzony, oraz wszystkie inne pliki zasobów. Upewnij się, że po zakończeniu z tym wdrożeniem przed usunięciem zasobów.

Podczas usuwania zasoby galerii pakietu obrazu, należy usunąć wszystkie wersje obrazów przed usunięciem definicji obrazów użytych do ich utworzenia. Aby usunąć galerii, należy najpierw usunięto wszystkie definicje obraz w galerii.

Usuń szablon konstruktora obrazu.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
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

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zaktualizować wersję obrazu, został utworzony, zobacz [kreatora obrazów platformy Azure Użyj, aby utworzyć inną wersję obrazu](image-builder-gallery-update-image-version.md).