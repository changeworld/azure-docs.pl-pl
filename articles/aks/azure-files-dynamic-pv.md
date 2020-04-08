---
title: Dynamiczne tworzenie udziału usługi Azure Files
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak dynamicznie tworzyć wolumin trwały za pomocą usługi Azure Files do użytku z wieloma równoczesnymi zasobnikami w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/12/2019
ms.openlocfilehash: 59b773cd4608187fedb24358eac57715e1c271ea
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803538"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamiczne tworzenie i używanie woluminu trwałego za pomocą usługi Azure Files w usłudze Azure Kubernetes Service (AKS)

Wolumin trwały reprezentuje kawałek magazynu, który został aprowidywnie do użytku z zasobnikami kubernetes. Wolumin trwały może być używany przez jeden lub wiele zasobników i może być dynamicznie lub statycznie aprowizacji. Jeśli wiele zasobników potrzebuje równoczesnego dostępu do tego samego woluminu magazynu, można użyć usługi Azure Files do nawiązania połączenia przy użyciu [protokołu Blok komunikatów serwera (SMB).][smb-overview] W tym artykule pokazano, jak dynamicznie utworzyć udział usługi Azure Files do użytku przez wiele zasobników w klastrze usługi Azure Kubernetes (AKS).

Aby uzyskać więcej informacji na temat woluminów kubernetes, zobacz [Opcje magazynu dla aplikacji w aks][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Tworzenie klasy magazynu

Klasa magazynu służy do definiowania sposobu tworzenia udziału plików platformy Azure. Konto magazynu jest automatycznie tworzone w [grupie zasobów węzła][node-resource-group] do użytku z klasą magazynu do przechowywania udziałów plików platformy Azure. Wybierz następujące [nadmiarowość magazynu platformy Azure][storage-skus] dla *skuName:*

* *Standard_LRS* - standardowa pamięć masowa lokalnie nadmiarowa (LRS)
* *Standard_GRS* - standardowa pamięć masowa geograficzna (GRS)
* *Standard_ZRS* - magazyn strefowy standardowy (ZRS)
* *Standard_RAGRS* - standardowa pamięć masowa geograficzno dostęp do odczytu (RA-GRS)
* *Premium_LRS* - magazyn premium z magazynu lokalnie nadmiarowego (LRS)
* *Premium_ZRS* - magazyn nadmiarowy strefy premium (GRS)

> [!NOTE]
> Usługa Azure Files obsługuje magazyn w wersji premium w klastrach AKS, w których jest uruchamiany program Kubernetes w wersji 1.13 lub wyższej, minimalny udział plików w usłudze Premium wynosi 100 GB

Aby uzyskać więcej informacji na temat klas magazynu kubernetes dla usług Azure Files, zobacz [Klasy magazynu kubernetes][kubernetes-storage-classes].

Utwórz plik `azure-file-sc.yaml` o nazwie i skopiuj w poniższym przykładowym manifeście. Aby uzyskać więcej informacji na temat *mountOptions*, zobacz sekcję [Opcje instalacji.][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

Utwórz klasę magazynu za pomocą polecenia [zastosuj kubectl:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Tworzenie trwałego oświadczenia woluminu

Trwałe oświadczenie woluminu (PVC) używa obiektu klasy magazynu do dynamicznego aprowizowania udziału plików platformy Azure. Następujące YAML może służyć do tworzenia trwałego oświadczenia woluminu *5 GB* w rozmiarze z *ReadWriteMany* dostępu. Aby uzyskać więcej informacji na temat trybów dostępu, zobacz dokumentację [woluminu trwałego Kubernetes.][access-modes]

Teraz utwórz `azure-file-pvc.yaml` plik o nazwie i skopiuj w następującym YAML. Upewnij się, że *storageClassName* pasuje do klasy magazynu utworzonej w ostatnim kroku:

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

> [!NOTE]
> W przypadku korzystania z *Premium_LRS* sku dla klasy pamięci masowej, minimalna wartość *magazynu* musi wynosić *100Gi*.

Utwórz trwałe oświadczenie woluminu za pomocą polecenia [zastosuj kubectl:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

Po zakończeniu zostanie utworzony udział plików. Tworzony jest również klucz tajny kubernetes, który zawiera informacje o połączeniu i poświadczenia. Można użyć [polecenia kubectl get,][kubectl-get] aby wyświetlić stan obwodu PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Korzystanie z woluminu trwałego

Następujący YAML tworzy zasobnik, który używa żądania woluminu trwałego *azurefile* do zainstalowania udziału plików platformy Azure w */mnt/azure* path. W przypadku kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w programie AKS) określ *mountPath* przy użyciu konwencji ścieżki systemu Windows, takiej jak *"D:"*.

Utwórz plik `azure-pvc-files.yaml`o nazwie i skopiuj w następującym pliku YAML. Upewnij się, że *claimName* pasuje do obwodu PVC utworzonego w ostatnim kroku.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Utwórz zasobnik za pomocą polecenia [zastosuj kubectl.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

Masz teraz uruchomiony zasobnik z udziałem usługi Azure Files zainstalowany w *katalogu /mnt/azure.* Ta konfiguracja może być widoczna `kubectl describe pod mypod`podczas sprawdzania zasobnika za pośrednictwem . Następujące skondensowane przykładowe wyjście pokazuje objętość zamontowaną w pojemniku:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Mount options (Opcje instalacji)

Domyślną wartością *fileMode* i *dirMode* jest *0777* dla kubernetes w wersji 1.13.0 i powyżej. Jeśli dynamicznie tworzenie woluminu trwałego z klasy magazynu, można określić opcje instalacji na obiekt klasy magazynu. Poniższy przykład ustawia *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące przechowywania i tworzenia kopii zapasowych w u.][operator-best-practices-storage]

Dowiedz się więcej o woluminach trwałych kubernetes przy użyciu usługi Azure Files.

> [!div class="nextstepaction"]
> [Wtyczka Kubernetes dla plików platformy Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
