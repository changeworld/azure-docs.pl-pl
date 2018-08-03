---
title: Użyj plików platformy Azure za pomocą usługi AKS
description: Dyski platformy Azure za pomocą usługi AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bac80e354a4d629bfbfc8207b9fed7c69c765839
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429271"
---
# <a name="persistent-volumes-with-azure-files"></a>Trwałe woluminów przy użyciu usługi Azure files

Trwały wolumin jest fragmentem magazynu, który został utworzony do użycia w klastrze Kubernetes. Trwały wolumin może być używany przez jeden lub wiele zasobników i można tworzyć dynamicznie lub statycznie. Ten dokument zawiera szczegółowe informacje **dynamiczne tworzenie** udziału plików platformy Azure jako trwały wolumin.

Aby uzyskać więcej informacji na temat usługi Kubernetes trwałego woluminami, w tym tworzenia statycznych, zobacz [woluminy trwałe Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Gdy dynamiczne tworzenie udziału plików platformy Azure jako wolumin Kubernetes, wszystkich kont magazynu można tak długo, jak znajduje się w usłudze AKS **węzła** grupy zasobów. Jest to jeden z `MC_` prefiks, który został utworzony przez Inicjowanie obsługi administracyjnej zasobów klastra usługi AKS. Pobierz nazwę grupy zasobów przy użyciu [az resource show] [ az-resource-show] polecenia.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Użyj [Tworzenie konta magazynu az] [ az-storage-account-create] polecenie, aby utworzyć konto magazynu.

Aktualizacja `--resource-group` nazwą grupy zasobów zebranych w ostatnim kroku i `--name` nazwę ulubionego.

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

> Usługa pliki systemu Azure, która jest tylko obecnie współpracujesz z magazynu w warstwie standardowa. Jeśli używasz usługi premium storage, wolumin zakończy się niepowodzeniem do aprowizowania.

## <a name="create-storage-class"></a>Utwórz klasę magazynu

Klasa magazynu jest używane do definiowania sposobu tworzenia udziału plików platformy Azure. Można określić konto magazynu określone w klasie. Jeśli nie określono konta magazynu, `skuName` i `location` muszą być określone, i wszystkich kont magazynu w skojarzonej grupy zasobów są oceniane pod kątem dopasowania.

Aby uzyskać więcej informacji na temat klasy magazynu Kubernetes dla usługi Azure files, zobacz [klasy magazynu w usłudze Kubernetes][kubernetes-storage-classes].

Utwórz plik o nazwie `azure-file-sc.yaml` i skopiuj następujące manifestu. Aktualizacja `storageAccount` nazwą konta magazynu docelowego. Zobacz sekcję [opcje instalacji] Aby uzyskać więcej informacji o `mountOptions`.

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

Utwórz klasę magazynu za pomocą [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Tworzenie oświadczenia trwały wolumin

Oświadczenie trwały wolumin (PVC) używa obiektu klasy magazynu, aby dynamicznie aprowizuj udziału plików platformy Azure.

Poniższego kodu YAML może służyć do tworzenia oświadczeń trwały wolumin `5GB` w rozmiarze `ReadWriteMany` dostępu. Aby uzyskać więcej informacji na temat trybów dostępu, zobacz [trwały wolumin Kubernetes] [ access-modes] dokumentacji.

Utwórz plik o nazwie `azure-file-pvc.yaml` i skopiuj do poniższego kodu YAML. Upewnij się, że `storageClassName` odpowiada klasę magazynu utworzone w poprzednim kroku.

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

Tworzenie oświadczenia trwały wolumin z [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

Po zakończeniu zostanie utworzony udział plików. Wpisie tajnym rozwiązania Kubernetes jest tworzona zawierający informacje o połączeniu oraz poświadczenia.

## <a name="using-the-persistent-volume"></a>Za pomocą trwały wolumin

Poniższego kodu YAML tworzy zasobnik, który używa oświadczeń trwały wolumin `azurefile` zainstalować udział plików platformy Azure w `/mnt/azure` ścieżki.

Utwórz plik o nazwie `azure-pvc-files.yaml`, a następnie skopiuj do poniższego kodu YAML. Upewnij się, że `claimName` odpowiada PVC utworzony w poprzednim kroku.

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

Utwórz zasobnik za pomocą [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

Masz teraz uruchomiony zasobnik z dysku platformy Azure zainstalowany w `/mnt/azure` katalogu. Ta konfiguracja może być moment zapoznanie się tym zasobniku za pośrednictwem `kubectl describe pod mypod`.

## <a name="mount-options"></a>Opcje instalacji

Wartości domyślne fileMode i dirMode różnią się między wersjami usługi Kubernetes, zgodnie z opisem w poniższej tabeli.

| wersja | wartość |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 lub nowszy | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 lub nowszy | 0755 |

Jeśli przy użyciu klastra wersji 1.8.5 lub nowszej i dynamicznie tworzenie wolumin trwałych, za pomocą klasy magazynu, opcje instalacji może być określony dla obiektu klasy magazynowania. Poniższy przykład ustawia `0777`.

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

Jeśli przy użyciu klastra wersji 1.8.5 lub nowszej i statycznie tworzenia obiektu wolumin trwałych, opcje instalacji muszą być określone w `PersistentVolume` obiektu. Aby uzyskać więcej informacji na temat tworzenia statycznie wolumin trwałych, zobacz [statyczne woluminy trwałe][pv-static].

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

Jeśli przy użyciu klastra wersji 1.8.0 - 1.8.4, kontekstu zabezpieczeń można określić za pomocą `runAsUser` wartość `0`. Aby uzyskać więcej informacji w kontekście zabezpieczeń zasobników, zobacz [skonfigurować kontekstu zabezpieczeń][kubernetes-security-context].

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o woluminy trwałe Kubernetes za pomocą usługi Azure Files.

> [!div class="nextstepaction"]
> [Wtyczka usługi Kubernetes dla usługi Azure Files][kubernetes-files]

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
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
