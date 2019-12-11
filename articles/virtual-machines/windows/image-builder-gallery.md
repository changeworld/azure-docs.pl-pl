---
title: Korzystanie z programu Azure Image Builder z galerią obrazów dla maszyn wirtualnych z systemem Windows (wersja zapoznawcza)
description: Tworzenie obrazów maszyn wirtualnych z systemem Windows za pomocą programu Azure Image Builder i udostępnionej galerii obrazów.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 1d9763ccc5f5967b9fc9932a11fff655e6120fd0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976081"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Wersja zapoznawcza: Tworzenie obrazu systemu Windows i dystrybuowanie go do galerii obrazów udostępnionych 

W tym artykule przedstawiono sposób użycia programu Azure Image Builder do tworzenia wersji obrazu w [galerii obrazów udostępnionych](shared-image-galleries.md), a następnie dystrybuowania obrazu globalnie.

Aby skonfigurować obraz, będziemy używać szablonu JSON. Używany plik JSON jest tutaj: [helloImageTemplateforWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

W celu rozesłania obrazu do galerii obrazów udostępnionych szablon używa [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) jako wartości sekcji `distribute` szablonu.

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Rejestrowanie funkcji
Aby korzystać z usługi Azure Image Builder w wersji zapoznawczej, należy zarejestrować nową funkcję.

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

Jeśli nie powiedzie się, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Ustawianie zmiennych i uprawnień 

Będziemy wielokrotnie używać niektórych informacji, więc utworzymy pewne zmienne do przechowywania tych informacji. Zastąp wartości dla zmiennych, takich jak `username` i `vmpassword`, własnymi informacjami.

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

Utwórz zmienną dla identyfikatora subskrypcji. Można to zrobić przy użyciu `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Utwórz grupę zasobów.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Przyznaj usłudze Azure Image Builder uprawnienia do tworzenia zasobów w tej grupie zasobów. Wartość `--assignee` to identyfikator rejestracji aplikacji dla usługi Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Tworzenie definicji obrazu i galerii

Aby użyć konstruktora obrazów z udostępnioną galerią obrazów, musisz mieć istniejącą galerię obrazów i definicję obrazu. Konstruktor obrazów nie utworzy galerii obrazów i definicji obrazu.

Jeśli nie masz jeszcze galerii i definicji obrazu do użycia, Zacznij od utworzenia ich. Najpierw utwórz galerię obrazów.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Następnie Utwórz definicję obrazu.

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


## <a name="download-and-configure-the-json"></a>Pobierz i Skonfiguruj plik JSON

Pobierz szablon JSON i skonfiguruj go przy użyciu zmiennych.

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

Ta Następna część spowoduje utworzenie wersji obrazu w galerii. 

Prześlij konfigurację obrazu do usługi Azure Image Builder.

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

Tworzenie obrazu i replikowanie go do obu regionów może chwilę potrwać. Przed przejściem do tworzenia maszyny wirtualnej Zaczekaj, aż ta część zostanie zakończona.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na podstawie wersji obrazu, która została utworzona przez usługę Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Sprawdzanie dostosowania
Utwórz połączenie Pulpit zdalny z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawione podczas tworzenia maszyny wirtualnej. Na maszynie wirtualnej Otwórz wiersz polecenia i wpisz:

```console
dir c:\
```

Powinien zostać wyświetlony katalog o nazwie `buildActions`, który został utworzony podczas dostosowywania obrazu.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli chcesz teraz spróbować ponownie dostosować wersję obrazu, aby utworzyć nową wersję tego samego obrazu, **Pomiń ten krok** i przejdź do, aby [użyć usługi Azure Image Builder do utworzenia innej wersji obrazu](image-builder-gallery-update-image-version.md).


Spowoduje to usunięcie utworzonego obrazu wraz ze wszystkimi innymi plikami zasobów. Przed usunięciem zasobów upewnij się, że to wdrożenie zostało zakończone.

W przypadku usuwania zasobów galerii obrazów należy usunąć wszystkie wersje obrazu, aby można było usunąć definicję obrazu używaną do ich tworzenia. Aby usunąć galerię, należy najpierw usunąć wszystkie definicje obrazu z galerii.

Usuń szablon Konstruktor obrazów.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Pobierz wersję obrazu utworzoną przez konstruktora obrazu, która zawsze zaczyna się od `0.`, a następnie Usuń wersję obrazu

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

Aby dowiedzieć się, jak zaktualizować utworzoną wersję obrazu, zobacz [Korzystanie z programu Azure Image Builder w celu utworzenia innej wersji obrazu](image-builder-gallery-update-image-version.md).