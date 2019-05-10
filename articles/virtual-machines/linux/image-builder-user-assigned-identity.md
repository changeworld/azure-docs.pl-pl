---
title: Tworzenie obrazu maszyny wirtualnej i korzystania z tożsamości zarządzanej użytkownik przypisany na dostęp do plików w usłudze Azure Storage (wersja zapoznawcza)
description: Tworzenie obrazu maszyny wirtualnej przy użyciu Kreatora obrazów platformy Azure, mają dostęp do plików przechowywanych w usłudze Azure Storage za pomocą tożsamości zarządzanych przypisanych przez użytkownika.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b0a6c016b2be12ac6686b3748b4b16281899323e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511057"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Tworzenie obrazu i korzystania z tożsamości zarządzanej użytkownik przypisany na dostęp do plików w usłudze Azure Storage 

Kreator obrazów obsługiwane przez platformę Azure za pomocą skryptów lub kopiowania plików z wielu lokalizacji, takich jak GitHub i Azure storage itp. Można ich używać, muszą być dostępne z zewnątrz do kreatora obrazów platformy Azure, ale można chronić przy użyciu tokenów sygnatur dostępu Współdzielonego obiektów blob usługi Storage platformy Azure.

W tym artykule przedstawiono sposób tworzenia dostosowanego obrazu za pomocą Kreatora obrazów maszyn wirtualnych platformy Azure, w której będzie używać usługa programu [przypisanych przez użytkownika z tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) dostępu do plików w usłudze Azure storage do dostosowywania obrazu, bez konieczności wprowadzania pliki dostępne publicznie lub konfigurowania tokeny sygnatur dostępu Współdzielonego.

W poniższym przykładzie utworzysz dwie grupy zasobów, będzie używana dla niestandardowego obrazu, a drugi będzie obsługiwać konto usługi Azure Storage, który zawiera plik skryptu. Symuluje to realnym scenariusz, gdzie może mieć artefaktów kompilacji lub plików obrazów w różnych kontach magazynu, poza kreatora obrazów. Spowoduje utworzenie tożsamości przypisanych przez użytkownika, a następnie udziel, że uprawnienia do odczytu z pliku skryptu, ale nie ustawi wszelkie publicznego dostępu do tego pliku. Następnie użyje konfiguratora powłoki do pobierania i uruchamiania tego skryptu z konta magazynu.


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

Utwórz zmienną dla Twojego identyfikatora subskrypcji. Można uzyskać, to przy użyciu `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Tworzenie grupy zasobów do przechowywania skryptu i obrazu.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Utwórz magazyn i skopiuj przykładowy skrypt do niej z usługi GitHub.

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



Nadaj uprawnienia kreatora obrazów do tworzenia zasobów w grupie zasobów obrazu. `--assignee` Wartość Identyfikatora rejestracji aplikacji dla usługi kreatora obrazów. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Utwórz tożsamość zarządzaną przypisanych przez użytkownika

Utwórz tożsamość i przypisać uprawnienia dla konta magazynu skryptu. Aby uzyskać więcej informacji, zobacz [tożsamości zarządzanej User-Assigned](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

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


## <a name="modify-the-example"></a>Zmodyfikuj przykład

Pobierz przykładowy plik JSON i skonfiguruj ją za pomocą zmiennych, który został utworzony.

```azurecli-interactive
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

Prześlij konfigurację obrazu, aby usługa kreatora obrazów platformy Azure.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Poczekaj na zakończenie kompilacji. Może to potrwać około 15 minut.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz Maszynę wirtualną z obrazu. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Po utworzeniu maszyny Wirtualnej, należy uruchomić sesję SSH z maszyną Wirtualną.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Powinien pojawić się obraz, który został dostosowany komunikatem dnia, jak najszybciej po nawiązaniu połączenia SSH!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Czyszczenie

Po zakończeniu możesz usunąć zasoby, jeśli są już potrzebne.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz problemy z pracy przy użyciu Kreatora obrazów platformy Azure, zobacz [Rozwiązywanie problemów](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).