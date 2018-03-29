---
title: Użyj plików na platformę Azure z AKS
description: Używać dysków Azure AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78f447c96afe7955f115de4bbd28015cd231bb53
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Woluminy trwałe pliki Azure

Trwały wolumin reprezentuje fragment magazynu, które zostały udostępnione do użycia w klastrze Kubernetes. Trwały wolumin mogą być używane przez jedną lub wiele stanowiskami i można za dynamicznie, czy statycznie. Ten dokument zawiera szczegóły dotyczące dynamicznego inicjowania obsługi administracyjnej udziału plików na platformę Azure jako trwały wolumin Kubernetes AKS klastra. 

Aby uzyskać więcej informacji na woluminach trwałe Kubernetes, zobacz [woluminy trwałe Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Podczas dynamicznego inicjowania obsługi administracyjnej udziału plików na platformę Azure jako wolumin Kubernetes dowolne konto magazynu może służyć tak długo, jak są zawarte w tej samej grupie zasobów co klaster AKS. Jeśli to konieczne, Utwórz konto magazynu w tej samej grupie zasobów co klaster AKS. 

Aby zidentyfikować grupę odpowiednich zasobów, użyj [listy grup az] [ az-group-list] polecenia.

```azurecli-interactive
az group list --output table
```

Szukasz grupy zasobów o nazwie podobny do `MC_clustername_clustername_locaton`, gdzie clustername jest nazwą klastra AKS i lokalizacja jest region platformy Azure, w którym została wdrożona klastra.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Użyj [Tworzenie konta magazynu az] [ az-storage-account-create] polecenie, aby utworzyć konto magazynu. 

Za pomocą tego przykładu, zaktualizuj `--resource-group` z nazwą grupy zasobów i `--name` na wybraną nazwę.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
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

Utwórz klasę magazynu z [utworzyć kubectl] [ kubectl-create] polecenia.

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Tworzenie oświadczeń trwały wolumin

Oświadczenie trwały wolumin (PVC) używa obiektu klasy magazynu, aby dynamicznie inicjują obsługę udziału plików na platformę Azure. 

Manifest następujące może służyć do tworzenia oświadczeń trwały wolumin `5GB` rozmiaru w `ReadWriteOnce` dostępu.

Utwórz plik o nazwie `azure-file-pvc.yaml` i skopiuj następujące manifestu. Upewnij się, że `storageClassName` odpowiada klasy magazynowania utworzone w ostatnim kroku.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Tworzenie oświadczenia trwały wolumin o [utworzyć kubectl] [ kubectl-create] polecenia.

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

Po ukończeniu będzie można utworzyć udziału plików. Klucz tajny Kubernetes tworzona jest również zawierający informacje o połączeniu oraz poświadczenia.

## <a name="using-the-persistent-volume"></a>Przy użyciu trwały wolumin

Manifest następujących tworzy pod, używającej oświadczeń trwały wolumin `azurefile` Aby zainstalować udział plików na platformę Azure w `/mnt/azure` ścieżki.

Utwórz plik o nazwie `azure-pvc-files.yaml`i skopiuj następujące manifestu. Upewnij się, że `claimName` odpowiada PVC utworzone w ostatnim kroku.

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

Utwórz pod z [utworzyć kubectl] [ kubectl-create] polecenia.

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

Masz teraz pod uruchomiona z dysku platformy Azure, zamontowane w `/mnt/azure` katalogu. Widać instalacji podczas sprawdzania z pod za pośrednictwem woluminu `kubectl describe pod mypod`.

## <a name="mount-options"></a>Opcje instalacji
 
Domyślną wartością parametru fileMode i dirMode wartości różnią się między wersjami Kubernetes zgodnie z opisem w poniższej tabeli.
 
| wersja | wartość |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 lub nowszy | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 lub nowszy | 0755 |
 
Jeśli przy użyciu klastra wersji 1.8.5 lub nowszego instalacji można określić opcji dla obiekt klasy magazynu. W poniższym przykładzie `0777`.
 
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
 
Jeśli przy użyciu klastra wersji 1.8.0 - 1.8.4, za pomocą można określić kontekstu zabezpieczeń `runAsUser` wartość `0`. Aby uzyskać więcej informacji na Pod kontekstu zabezpieczeń, zobacz [skonfigurować kontekstu zabezpieczeń][kubernetes-security-context].

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o Kubernetes woluminów trwałego za pomocą usługi pliki Azure.

> [!div class="nextstepaction"]
> [Dodatek Kubernetes plików Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
