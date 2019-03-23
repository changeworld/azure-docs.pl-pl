---
title: Instalacji woluminu plików platformy Azure w usłudze Azure Container Instances
description: Dowiedz się, jak można zainstalować woluminu plików platformy Azure, aby utrwalanie stanu przy użyciu usługi Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370622"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances

Domyślnie usługi Azure Container Instances są bezstanowe. Kontener ulegnie awarii lub przestanie, wszystkie jego stan zostanie utracone. Aby zachować stan poza okres istnienia kontenera, można zainstalować woluminu z magazynu zewnętrznego. W tym artykule pokazano, jak zainstalować udział plików platformy Azure utworzonych za pomocą [usługi Azure Files](../storage/files/storage-files-introduction.md) do użycia z usługą Azure Container Instances. Usługa Pliki Azure oferuje w pełni zarządzane udziały plików w chmurze, dostępne za pośrednictwem protokołu Server Message Block (SMB) będącego standardem branżowym. Za pomocą udziału plików platformy Azure przy użyciu usługi Azure Container Instances zapewnia funkcji udostępniania plików, podobne do udziału plików platformy Azure przy użyciu maszyn wirtualnych platformy Azure.

> [!NOTE]
> Instalowanie udziału plików platformy Azure jest obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje na potrzeby kontenerów systemu Windows, bieżące różnice dotyczące platform możesz znaleźć w temacie [Limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Przed rozpoczęciem korzystania z udziału plików platformy Azure, usługa Azure Container Instances, należy go utworzyć. Uruchom następujący skrypt, aby utworzyć konto magazynu do obsługi udziału plików i udziału. Nazwa konta magazynu musi być globalnie unikatowa, więc skrypt ten dodaje wartość losowe do podstawowego ciągu.

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

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Pobierz poświadczenia magazynu

Aby zainstalować udział plików platformy Azure jako woluminu w usłudze Azure Container Instances, trzy wartości będą potrzebne: Nazwa konta magazynu, nazwę udziału i klucza dostępu do magazynu.

Jeśli używany jest skrypt powyżej, nazwa konta magazynu została zapisana w zmiennej ACI_PERS_STORAGE_ACCOUNT_NAME $. Aby wyświetlić nazwę konta, wpisz:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Nazwa udziału jest już znany (zdefiniowany jako *acishare* w skrypcie powyżej), więc wszystkie, czy nadal jest klucz konta magazynu, w którym można znaleźć, używając następującego polecenia:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Wdrażanie kontenerów i instalacji woluminu

Aby zainstalować udział plików platformy Azure jako woluminu w kontenerze, należy określić, udział i wolumin punktu instalacji podczas tworzenia kontenera za pomocą [utworzyć kontener az][az-container-create]. Po wykonaniu poprzednich kroków możesz zainstalować udział, utworzonego wcześniej przy użyciu następującego polecenia, aby utworzyć kontener:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Wartość `--dns-name-label` musi być unikatowa w regionie platformy Azure, w którym tworzysz wystąpienia kontenera. Zaktualizuj tę wartość w poprzednim poleceniu, jeśli zostanie wyświetlony **etykiety nazwy DNS** komunikat o błędzie podczas wykonywania polecenia.

## <a name="manage-files-in-mounted-volume"></a>Zarządzanie plikami w woluminie

Po uruchomieniu kontenera, można użyć prostej aplikacji sieci web wdrażane za pomocą programu Microsoft [aci hellofiles] [ aci-hellofiles] obrazu do tworzenia małych plików tekstowych w udziale plików platformy Azure w określonej ścieżce instalacji. Uzyskaj aplikację internetową w pełni kwalifikowaną nazwę domeny (FQDN) za pomocą [az container show] [ az-container-show] polecenia:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Możesz użyć [witryny Azure portal] [ portal] lub narzędzia, takie jak [Microsoft Azure Storage Explorer] [ storage-explorer] do pobierania i sprawdź plik zapisany udział plików.

## <a name="mount-multiple-volumes"></a>Zainstaluj wiele woluminów

Aby zainstalować wiele woluminów w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu usługi Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) lub pliku YAML.

Aby użyć szablonu, podaj szczegóły udziału, a następnie zdefiniować woluminy przy wprowadzaniu `volumes` tablicy w `properties` części szablonu. Na przykład, jeśli utworzono dwa udziałów plików platformy Azure o nazwie *share1* i *share2* na koncie magazynu *myStorageAccount*, `volumes` wyglądałby tablicy podobny do następującego:

```JSON
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

Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować woluminów należy wypełnić `volumeMounts` tablicy w `properties` sekcja definicji kontenera. Na przykład instaluje to dwa woluminy *myvolume1* i *myvolume2*, poprzednio zdefiniowanymi:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Aby zapoznać się z przykładem wdrażaniem wystąpienia kontenera za pomocą szablonu usługi Azure Resource Manager, zobacz [. wdrożenie grupy kontenera](container-instances-multi-container-group.md). Na przykład przy użyciu pliku YAML, zobacz [wdrażania grupy wielu kontenerów przy użyciu kodu YAML](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zainstalować inne typy woluminu w usłudze Azure Container Instances:

* [Zainstalować emptyDir woluminu w wystąpień kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Zainstalować wolumin gitRepo w wystąpień kontenera platformy Azure](container-instances-volume-gitrepo.md)
* [Zainstaluj wolumin tajny w usłudze Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show