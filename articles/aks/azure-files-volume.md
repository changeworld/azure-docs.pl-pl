---
title: Tworzenie woluminu statyczne wielu zasobnikach w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin za pomocą usługi Azure Files do użytku z wielu jednoczesnych zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 65e94a271fc8fc72ac74d51af3cf7b717f8410b0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259154"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Ręcznie utworzyć i korzystać z woluminu z udziałem plików platformy Azure w usłudze Azure Kubernetes Service (AKS)

Oparte na kontenerach aplikacje często muszą uzyskać dostęp do utrwalania danych w woluminie danych zewnętrznych. Jeśli wielu zasobnikach równoczesny dostęp do tego samego woluminu magazynu, można użyć usługi Azure Files, połączyć się przy użyciu [protokołu bloku komunikatów serwera (SMB)][smb-overview]. W tym artykule pokazano, jak ręcznie utworzyć udział plików platformy Azure i dołączyć go do zasobników w usłudze AKS.

Aby uzyskać więcej informacji na woluminach Kubernetes, zobacz [opcji magazynu dla aplikacji w usłudze AKS][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.59 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Zanim użyjesz usługi Azure Files jako wolumin Kubernetes, należy utworzyć konto usługi Azure Storage i udziału plików. Następujące polecenia tworzą grupę zasobów o nazwie *myAKSShare*, konto magazynu i udział plików o nazwie *aksshare*:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Zanotuj nazwę konta magazynu i klucz podanym na końcu danych wyjściowych skryptu. Te wartości są wymagane, gdy tworzysz wolumin Kubernetes w jednym z następujących czynności.

## <a name="create-a-kubernetes-secret"></a>Tworzenie wpisu tajnego rozwiązania Kubernetes

Kubernetes wymaga poświadczeń dostępu do udziału plików utworzonych w poprzednim kroku. Te poświadczenia są przechowywane w [wpisie tajnym rozwiązania Kubernetes][kubernetes-secret], który odwołuje się podczas tworzenia zasobnika Kubernetes.

Użyj `kubectl create secret` polecenie, aby utworzyć wpis tajny. Poniższy przykład obejmuje tworzenie udostępnionego o nazwie *azure-secret* i wypełnia *azurestorageaccountname* i *azurestorageaccountkey* z poprzedniego kroku. Aby użyć istniejącego konta usługi Azure storage, należy podać nazwę konta i klucz.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Instalowanie udziału plików jako wolumin

Aby zainstalować udział plików platformy Azure, w tym zasobniku, skonfiguruj wolumin w specyfikacji kontenera. Utwórz nowy plik o nazwie `azure-files-pod.yaml` z następującą zawartością. Jeśli zmienisz nazwę udziału plików lub nazwa wpisu tajnego, zaktualizuj *shareName* i *secretName*. W razie potrzeby zaktualizuj `mountPath`, czyli ścieżkę, w których udostępnianie plików jest zainstalowany w zasobnik.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
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

Użyj `kubectl` polecenie, aby utworzyć zasobnik.

```console
kubectl apply -f azure-files-pod.yaml
```

Masz teraz uruchomiony zasobnik z udziału plików platformy Azure zainstalowany w lokalizacji */mnt/azure*. Możesz użyć `kubectl describe pod mypod` Aby sprawdzić udział jest pomyślnie zainstalowany. Następujące dane wyjściowe skróconego przykładu przedstawia wolumin zainstalowany w kontenerze:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-options"></a>Opcje instalacji

Domyślne *fileMode* i *dirMode* wartości różnią się między wersjami usługi Kubernetes, zgodnie z opisem w poniższej tabeli.

| version | wartość |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 lub nowszy | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 lub nowszy | 0755 |

Jeśli przy użyciu klastra wersji 1.8.5 lub nowszej i statycznie tworzenia obiektu trwały wolumin, opcje instalacji muszą być określone w *PersistentVolume* obiektu.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
```

Jeśli przy użyciu klastra wersji 1.8.0 - 1.8.4, kontekstu zabezpieczeń można określić za pomocą *nazwa_użytkownika* wartość *0*. Aby uzyskać więcej informacji w kontekście zabezpieczeń zasobników, zobacz [skonfigurować kontekstu zabezpieczeń][kubernetes-security-context].

## <a name="next-steps"></a>Kolejne kroki

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze AKS][operator-best-practices-storage].

Aby uzyskać więcej informacji na temat klastrów usługi AKS w interakcję z usługą Azure Files, zobacz [wtyczki Kubernetes dla usługi Azure Files][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
