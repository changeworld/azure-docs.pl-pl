---
title: Zainstalować woluminu plików Azure w wystąpień kontenera platformy Azure
description: Dowiedz się, jak można zainstalować woluminu plików Azure, aby utrwalić stanu z wystąpień kontenera platformy Azure
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 239150c1e752ce6a4f2a19fa1192cd1a910ebea9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166800"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Instalowanie udziału plików na platformę Azure w wystąpień kontenera platformy Azure

Domyślnie bezstanowe są wystąpień kontenera platformy Azure. Kontener ulegnie awarii lub przestanie, wszystkie jego stan zostanie utracone. Aby zachować stan poza okres istnienia kontenera, można zainstalować woluminu z magazynu zewnętrznego. W tym artykule przedstawiono sposób instalacji udziału plików na platformę Azure do użycia z wystąpień kontenera platformy Azure.

> [!NOTE]
> Instalowanie udziału plików Azure jest obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje na potrzeby kontenerów systemu Windows, bieżące różnice dotyczące platform możesz znaleźć w temacie [Limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Przed rozpoczęciem korzystania z udziału plików na platformę Azure z wystąpień kontenera platformy Azure, należy utworzyć ją. Uruchom następujący skrypt, aby utworzyć konto magazynu do obsługi udziałów plików i udziału. Nazwa konta magazynu musi być unikatowe globalnie, więc skrypt ten dodaje losowych wartości do podstawowej ciągu.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>Pobieranie poświadczeń magazynu

Aby zainstalować udział plików na platformę Azure jako wolumin w wystąpień kontenera platformy Azure, potrzebne są trzy wartości: Nazwa konta magazynu, nazwa udziału i klucz dostępu do magazynu.

Jeśli używany jest skrypt powyżej, nazwa konta magazynu został utworzony z losowych wartości na końcu. Aby sprawdzić, ostatni ciąg (w tym losowe części), użyj następujących poleceń:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

Nazwa udziału jest już znany (zdefiniowany jako *acishare* w skrypcie powyżej), więc wszystko, że pozostaje jest klucz konta magazynu, który można znaleźć przy użyciu następującego polecenia:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Wdrażanie kontenera i instalacji woluminu

Aby zainstalować udział plików na platformę Azure jako wolumin w kontenerze, określ udział i wolumin punktu instalacji podczas tworzenia kontenera o [utworzyć kontener az][az-container-create]. Jeśli poprzednie kroki zostały wykonane, można zainstalować utworzonego wcześniej przy użyciu następującego polecenia, aby utworzyć kontener udziału:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image microsoft/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Wartość `--dns-name-label` musi być unikatowa w regionie platformy Azure, w którym tworzysz wystąpienia kontenera. Zaktualizuj tę wartość w poprzednim poleceniu, jeśli zostanie wyświetlony **etykieta nazwy DNS** komunikat o błędzie podczas wykonywania polecenia.

## <a name="manage-files-in-mounted-volume"></a>Zarządzanie plikami w woluminie

Po uruchomieniu kontenera, korzystając z aplikacji sieci web proste wdrażane za pomocą [microsoft/aci-hellofiles] [ aci-hellofiles] obrazu do zarządzania plików w udziale plików na platformę Azure w określonej ścieżce instalacji. Uzyskaj aplikacji sieci web w pełni kwalifikowaną nazwę domeny (FQDN) z [Pokaż kontenera az] [ az-container-show] polecenia:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Można użyć [portalu Azure] [ portal] lub narzędzia, takich jak [Eksploratora usługi Microsoft Azure Storage] [ storage-explorer] do pobierania i sprawdź zapisywane w pliku udziału plików.

## <a name="mount-multiple-volumes"></a>Instalowanie wielu woluminów

Aby zainstalować wiele woluminów w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu usługi Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Najpierw podaj szczegóły udziału i zdefiniować woluminy przy wprowadzaniu `volumes` tablicy w `properties` sekcji szablonu. Na przykład, jeśli utworzono dwa udziały plików platformy Azure o nazwie *share1* i *share2* na koncie magazynu *Mojekontomagazynu*, `volumes` pojawiały się tablicy podobny do następującego:

```json
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Następnie dla każdego kontenera w grupie kontenera, w którym chcesz zainstalować woluminów należy wypełnić `volumeMounts` tablicy w `properties` sekcja definicji kontenera. Na przykład to instaluje dwa woluminy, *myvolume1* i *myvolume2*, wcześniej zdefiniowany:

```json
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Aby zapoznać się przykładem kontenera wystąpienia wdrożenia z szablonem usługi Azure Resource Manager, zobacz [wdrożenia kontenera wielu grup wystąpień kontenera Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zainstalować inne typy woluminu w wystąpień kontenera platformy Azure:

* [Zainstalować emptyDir woluminu w wystąpień kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Zainstalować wolumin gitRepo w wystąpień kontenera platformy Azure](container-instances-volume-gitrepo.md)
* [Zainstalować tajny woluminu w wystąpień kontenera platformy Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/microsoft/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show