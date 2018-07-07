---
title: Użyj plików platformy Azure za pomocą usługi AKS
description: Dyski platformy Azure za pomocą usługi AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 0479e4d80b7490db170255d47ef3190bb744d2d8
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901497"
---
# <a name="volumes-with-azure-files"></a>Woluminy z usługi Azure files

Oparte na kontenerach aplikacje często muszą uzyskać dostęp do utrwalania danych w woluminie danych zewnętrznych. Usługa pliki Azure może służyć jako ten magazyn danych zewnętrznych. Ten artykuł zawiera, jako wolumin Kubernetes w usłudze Azure Kubernetes Service przy użyciu usługi Azure files.

Aby uzyskać więcej informacji na woluminach Kubernetes, zobacz [woluminów Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Przed rozpoczęciem korzystania z udziału plików platformy Azure jako wolumin Kubernetes, należy utworzyć konto usługi Azure Storage i udziału plików. Poniższy skrypt może służyć do wykonywania tych zadań. Zwróć uwagę, lub zaktualizuj wartości parametrów, niektóre z nich są potrzebne podczas tworzenia woluminu Kubernetes.

```azurecli-interactive
#!/bin/bash

# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Utwórz wpisie tajnym rozwiązania Kubernetes

Kubernetes wymaga poświadczeń dostępu do udziału plików. Te poświadczenia są przechowywane w [wpisie tajnym rozwiązania Kubernetes][kubernetes-secret], który odwołuje się podczas tworzenia zasobnika Kubernetes.

Użyj następującego polecenia, aby utworzyć wpis tajny. Zastąp `STORAGE_ACCOUNT_NAME` nazwą konta magazynu, a `STORAGE_ACCOUNT_KEY` przy użyciu klucza magazynu.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Instalowanie udziału plików jako wolumin

Instalowanie udziału plików platformy Azure w tym zasobniku konfigurując woluminu w jego specyfikacji. Utwórz nowy plik o nazwie `azure-files-pod.yaml` z następującą zawartością. Aktualizacja `aksshare` o nazwie, biorąc pod uwagę do usługi Azure Files udostępniania.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Użyj narzędzia kubectl, aby utworzyć zasobnik.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Powstał działający kontener z udziałem plików platformy Azure zainstalowany w `/mnt/azure` katalogu.  Możesz zobaczyć wolumin instalacji podczas sprawdzania tym zasobniku za pośrednictwem `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji o woluminach Kubernetes za pomocą usługi Azure Files.

> [!div class="nextstepaction"]
> [Wtyczka usługi Kubernetes dla usługi Azure Files][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
