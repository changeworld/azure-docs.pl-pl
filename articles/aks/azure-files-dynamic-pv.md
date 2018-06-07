---
title: Użyj plików na platformę Azure z AKS
description: Używać dysków Azure AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d3e92902e711ba2b1664c6497ecb66f035ea9308
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597505"
---
# <a name="persistent-volumes-with-azure-files"></a>Woluminy trwałe pliki Azure

Trwały wolumin jest magazynu, który został utworzony do użycia w klastrze Kubernetes. Trwały wolumin mogą być używane przez jedną lub wiele stanowiskami i można tworzyć dynamiczne lub statycznie. Szczegóły tego dokumentu **dynamicznego tworzenia** udziału plików na platformę Azure jako trwały wolumin.

Aby uzyskać więcej informacji na temat Kubernetes trwałe woluminów, w tym tworzenia statycznych zobacz [woluminy trwałe Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Podczas dynamicznego tworzenia udziału plików na platformę Azure jako wolumin Kubernetes, dowolne konto magazynu może służyć tak długo, jak jest AKS **węzła** grupy zasobów. Pobierz nazwę grupy zasobów z [Pokaż zasobów az] [ az-resource-show] polecenia.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Użyj [Tworzenie konta magazynu az] [ az-storage-account-create] polecenie, aby utworzyć konto magazynu.

Aktualizacja `--resource-group` z nazwą grupy zasobów zebranych w ostatnim kroku i `--name` na wybraną nazwę.

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Tworzenie klasy magazynu

Klasa magazynu jest używany do definiowania sposobu tworzenia udziału plików na platformę Azure. Można określić konto magazynu określonego w klasie. Jeśli nie określono konta magazynu, `skuName` i `location` musi być określona, i wszystkich kont magazynu w grupie zasobów są oceniane pod kątem dopasowania.

Aby uzyskać więcej informacji na Kubernetes klasy magazynu plików Azure, zobacz [klasy magazynu Kubernetes][kubernetes-storage-classes].

Utwórz plik o nazwie `azure-file-sc.yaml` i skopiuj następujące manifestu. Aktualizacja `storageAccount` z nazwą konta magazynu docelowego.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

Utwórz klasę magazynu z [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Tworzenie oświadczeń trwały wolumin

Oświadczenie trwały wolumin (PVC) używa obiektu klasy magazynu, aby dynamicznie inicjują obsługę udziału plików na platformę Azure.

Następujące yaml programu może służyć do tworzenia oświadczeń trwały wolumin `5GB` rozmiaru w `ReadWriteMany` dostępu. Aby uzyskać więcej informacji o trybach dostępu, zobacz [Kubernetes trwały wolumin] [ access-modes] dokumentacji.

Utwórz plik o nazwie `azure-file-pvc.yaml` i skopiuj następujące yaml programu. Upewnij się, że `storageClassName` odpowiada klasy magazynowania utworzone w ostatnim kroku.

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

Tworzenie oświadczenia trwały wolumin o [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

Po ukończeniu będzie można utworzyć udziału plików. Klucz tajny Kubernetes tworzona jest również zawierający informacje o połączeniu oraz poświadczenia.

## <a name="using-the-persistent-volume"></a>Przy użyciu trwały wolumin

Następujące yaml programu tworzy pod, używającej oświadczeń trwały wolumin `azurefile` Aby zainstalować udział plików na platformę Azure w `/mnt/azure` ścieżki.

Utwórz plik o nazwie `azure-pvc-files.yaml`i skopiuj następujące yaml programu. Upewnij się, że `claimName` odpowiada PVC utworzone w ostatnim kroku.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Utwórz pod z [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

Masz teraz pod uruchomiona z dysku platformy Azure, zamontowane w `/mnt/azure` katalogu. Ta konfiguracja może być moment zapoznanie się z pod za pośrednictwem `kubectl describe pod mypod`.

## <a name="mount-options"></a>Opcje instalacji

Domyślną wartością parametru fileMode i dirMode wartości różnią się między wersjami Kubernetes zgodnie z opisem w poniższej tabeli.

| wersja | wartość |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 lub nowszy | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 lub nowszy | 0755 |

Jeśli przy użyciu klastra wersji 1.8.5 lub nowszej i dynamicznie Tworzenie woluminu trwałych z klasy magazynu, opcje instalacji można określić dla obiekt klasy magazynu. W poniższym przykładzie `0777`.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Jeśli przy użyciu klastra wersji 1.8.5 lub nowszej i statycznie tworzenia obiektu woluminu trwałych, opcje instalacji musi być określony na `PersistentVolume` obiektu. Aby uzyskać więcej informacji na temat statycznie tworzenia woluminu trwałych zobacz [statycznych woluminy trwałe][pv-static].

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

Jeśli przy użyciu klastra wersji 1.8.0 - 1.8.4, za pomocą można określić kontekstu zabezpieczeń `runAsUser` wartość `0`. Aby uzyskać więcej informacji na Pod kontekstu zabezpieczeń, zobacz [skonfigurować kontekstu zabezpieczeń][kubernetes-security-context].

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o Kubernetes woluminów trwałego za pomocą usługi pliki Azure.

> [!div class="nextstepaction"]
> [Dodatek Kubernetes plików Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
