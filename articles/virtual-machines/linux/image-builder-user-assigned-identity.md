---
title: Tworzenie obrazu maszyny wirtualnej i używanie tożsamości zarządzanej przypisanej przez użytkownika do uzyskiwania dostępu do plików w usłudze Azure Storage (wersja zapoznawcza)
description: Utwórz obraz maszyny wirtualnej przy użyciu usługi Azure Image Builder, która może uzyskiwać dostęp do plików przechowywanych w usłudze Azure Storage przy użyciu tożsamości zarządzanej przypisanej przez użytkownika.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060746"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Tworzenie obrazu i używanie tożsamości zarządzanej przypisanej przez użytkownika do uzyskiwania dostępu do plików w usłudze Azure Storage 

Usługa Azure Image Builder obsługuje używanie skryptów lub kopiowanie plików z wielu lokalizacji, takich jak GitHub i azure storage itp. Aby z nich korzystać, muszą być dostępne zewnętrznie dla usługi Azure Image Builder, ale można chronić obiekty BLOB usługi Azure Storage przy użyciu tokenów sygnatury dostępu Współdzielonego.

W tym artykule pokazano, jak utworzyć niestandardowy obraz przy użyciu konstruktora obrazów maszyny Wirtualnej platformy Azure, gdzie usługa będzie używać [tożsamości zarządzanej przypisanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) przez użytkownika do uzyskiwania dostępu do plików w magazynie platformy Azure w celu dostosowania obrazu, bez konieczności upubliczniania plików lub konfigurowania tokenów sygnatury dostępu współdzielonego.

W poniższym przykładzie utworzysz dwie grupy zasobów, jedna będzie używana dla obrazu niestandardowego, a druga będzie hostować konto usługi Azure Storage, które zawiera plik skryptu. Symuluje to rzeczywisty scenariusz, w którym możesz mieć artefakty kompilacji lub pliki obrazów na różnych kontach magazynu, poza Konstruktorem obrazów. Utworzysz tożsamość przypisaną przez użytkownika, a następnie przyznasz uprawnienia do odczytu pliku skryptu, ale nie ustawisz żadnego publicznego dostępu do tego pliku. Następnie użyjesz konfiguratora powłoki, aby pobrać i uruchomić ten skrypt z konta magazynu.


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


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Będziemy wielokrotnie wykorzystywać niektóre informacje, więc utworzymy pewne zmienne do przechowywania tych informacji.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to uzyskać `az account show | grep id`za pomocą programu .

```console
subscriptionID=<Your subscription ID>
```

Utwórz grupy zasobów zarówno dla obrazu, jak i magazynu skryptów.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Utwórz magazyn i skopiuj przykładowy skrypt do niego z gitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Nadaj konstruktorowi obrazów uprawnienia do tworzenia zasobów w grupie zasobów obrazów. Wartość `--assignee` jest identyfikatorem rejestracji aplikacji dla usługi Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Utwórz tożsamość i przypisz uprawnienia do konta magazynu skryptów. Aby uzyskać więcej informacji, zobacz [Tożsamość zarządzana przypisana przez użytkownika](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Modyfikowanie przykładu

Pobierz przykładowy plik .json i skonfiguruj go przy tak utworzonych zmiennych.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu do usługi Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Rozpocznij kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Poczekaj na zakończenie kompilacji. Może to potrwać około 15 minut.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na podstawie obrazu. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Po utworzeniu maszyny Wirtualnej uruchom sesję SSH z maszyną wirtualną.

```console
ssh aibuser@<publicIp>
```

Powinieneś zobaczyć obraz został dostosowany z Wiadomością dnia, gdy tylko połączenie SSH zostanie nawiązane!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Czyszczenie

Po zakończeniu można usunąć zasoby, jeśli nie są już potrzebne.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Jeśli masz jakiekolwiek problemy z pracą z usługą Azure Image Builder, zobacz [Rozwiązywanie problemów](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).