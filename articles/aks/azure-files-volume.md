---
title: Tworzenie woluminu statycznego dla wielu zasobników w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin za pomocą usługi Azure Files do użytku z wieloma równoczesnymi zasobnikami w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 084ab5cd6736c9148bcab1faf048d3d9081855d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596406"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Ręczne tworzenie i używanie woluminu z udziałem usługi Azure Files w usłudze Azure Kubernetes Service (AKS)

Aplikacje oparte na kontenerach często muszą uzyskiwać dostęp do danych i utrwalać je w woluminie danych zewnętrznych. Jeśli wiele zasobników potrzebuje równoczesnego dostępu do tego samego woluminu magazynu, można użyć usługi Azure Files do nawiązania połączenia przy użyciu [protokołu Blok komunikatów serwera (SMB).][smb-overview] W tym artykule pokazano, jak ręcznie utworzyć udział usługi Azure Files i dołączyć go do zasobnika w usłudze AKS.

Aby uzyskać więcej informacji na temat woluminów kubernetes, zobacz [Opcje magazynu dla aplikacji w aks][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Aby można było używać usługi Azure Files jako woluminu Kubernetes, należy utworzyć konto usługi Azure Storage i udział plików. Następujące polecenia tworzą grupę zasobów o nazwie *myAKSShare*, konto magazynu i udział Plików o nazwie *aksshare:*

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
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Zanotuj nazwę konta magazynu i klucz wyświetlany na końcu danych wyjściowych skryptu. Te wartości są potrzebne podczas tworzenia woluminu Kubernetes w jednym z następujących kroków.

## <a name="create-a-kubernetes-secret"></a>Tworzenie klucza tajnego kubernetes

Kubernetes potrzebuje poświadczeń, aby uzyskać dostęp do udziału plików utworzonego w poprzednim kroku. Te poświadczenia są przechowywane w klucz tajny [Kubernetes][kubernetes-secret], do którego odwołuje się podczas tworzenia zasobnika Kubernetes.

Użyj `kubectl create secret` polecenia, aby utworzyć klucz tajny. Poniższy przykład tworzy udostępnionego o nazwie *azure-secret* i wypełnia *azurestorageaccountname* i *azurestorageaccountkey* z poprzedniego kroku. Aby użyć istniejącego konta magazynu platformy Azure, podaj nazwę konta i klucz.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Instalowanie udziału plików jako woluminu

Aby zainstalować udział plików platformy Azure w zasobniku, skonfiguruj `azure-files-pod.yaml` wolumin w specyfikacji kontenera. Jeśli zmieniono nazwę udziału plików lub nazwy tajnej, zaktualizuj *shareName* i *secretName*. W razie potrzeby `mountPath`zaktualizuj ścieżkę, która jest ścieżką, w której jest zamontowany udział Pliki w zasobniku. W przypadku kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w programie AKS) określ *mountPath* przy użyciu konwencji ścieżki systemu Windows, takiej jak *"D:"*.

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

Użyj `kubectl` polecenia, aby utworzyć zasobnik.

```console
kubectl apply -f azure-files-pod.yaml
```

Masz teraz uruchomiony zasobnik z udziałem usługi Azure Files zainstalowanym na */mnt/azure*. Można użyć, `kubectl describe pod mypod` aby sprawdzić, czy udział jest zainstalowany pomyślnie. Następujące skondensowane przykładowe wyjście pokazuje objętość zamontowaną w pojemniku:

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

## <a name="mount-options"></a>Mount options (Opcje instalacji)

Domyślną wartością *fileMode* i *dirMode* jest *0755* dla Kubernetes w wersji 1.9.1 i nowszej. W przypadku korzystania z klastra z kuberetes w wersji 1.8.5 lub większej i statycznie tworzenia trwałego obiektu woluminu, należy określić opcje instalacji na *PersistentVolume* obiektu. Poniższy przykład ustawia *0777*:

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
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Jeśli używasz klastra w wersji 1.8.0 - 1.8.4, można określić kontekst zabezpieczeń z *runAsUser* wartość ustawiona na *0*. Aby uzyskać więcej informacji na temat kontekstu zabezpieczeń zasobnika, zobacz [Konfigurowanie kontekstu zabezpieczeń][kubernetes-security-context].

Aby zaktualizować opcje instalacji, utwórz plik *azurefile-mount-options-pv.yaml* za pomocą *pliku PersistentVolume*. Przykład:

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
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Utwórz plik *azurefile-mount-options-pvc.yaml* za pomocą pliku *PersistentVolumeClaim,* który używa *pliku PersistentVolume*. Przykład:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Polecenia `kubectl` służy do tworzenia *funkcji PersistentVolume* i *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Sprawdź, czy *persistentVolumeClaim* jest tworzony i powiązany z *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Zaktualizuj specyfikację kontenera, aby odwoływać się do *trwałegoclumeclaim* i zaktualizować zasobnik. Przykład:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące przechowywania i tworzenia kopii zapasowych w u.][operator-best-practices-storage]

Aby uzyskać więcej informacji na temat klastrów AKS interakcji z usługą Azure Files, zobacz [wtyczkę Kubernetes dla plików platformy Azure][kubernetes-files].

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
