---
title: Należy użyć dysku platformy Azure z AKS
description: Używać dysków Azure AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: a6bc79d0556299634a78c5232bbab4e20810172c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Trwałe woluminy z dysku systemu Azure

Trwały wolumin reprezentuje fragment magazynu, które zostały udostępnione do użycia z stanowiskami Kubernetes. Trwały wolumin mogą być używane przez jedną lub wiele stanowiskami i można za dynamicznie, czy statycznie. Aby uzyskać więcej informacji na woluminach trwałe Kubernetes, zobacz [woluminy trwałe Kubernetes][kubernetes-volumes].

Szczegóły tego dokumentu przy użyciu trwałe woluminy z dysku systemu Azure w klastrze usługi kontenera platformy Azure (AKS).

> [!NOTE]
> Dysku platformy Azure może być instalowany tylko z typem tryb dostępu ReadWriteOnce, dzięki czemu można tylko jeden węzeł AKS. W przypadku konieczności nie współużytkują woluminu trwałego na wielu węzłach, warto rozważyć użycie [plików Azure][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Wbudowane klasy magazynu

Klasa magazynu jest używany do definiowania dynamicznie tworzenia jednostki magazynu trwałego woluminu. Aby uzyskać więcej informacji na Kubernetes klasy magazynu, zobacz [klasy magazynu Kubernetes][kubernetes-storage-classes].

Każdy klaster AKS zawiera dwie klasy magazynu wstępnie utworzone, skonfigurowane do pracy z dysku systemu Azure. `default` Klasa magazynu udostępnia standardowe dysku platformy Azure. `managed-premium` Klasa magazynu przepisy premium dysku platformy Azure. AKS węzłów w klastrze, użycie magazyn w warstwie premium, wybierz `managed-premium` klasy.

Użyj [kubectl uzyskać sc] [ kubectl-get] polecenie, aby wyświetlić klasy magazynu utworzone wcześniej.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>Tworzenie oświadczeń trwały wolumin

Oświadczenie trwały wolumin (PVC) służy do automatycznie obsługiwać magazyn oparty na klasę magazynu. W takim przypadku PVC umożliwia jednej z klas wstępnie utworzone magazynu tworzenie Azure standard lub premium dysków zarządzanych.

Utwórz plik o nazwie `azure-premimum.yaml`i skopiuj następujące manifestu.

Zwróć uwagę, że `managed-premium` magazynu jest określona w adnotacji i oświadczenia żąda dysku `5GB` rozmiaru w `ReadWriteOnce` dostępu.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Tworzenie oświadczenia trwały wolumin o [utworzyć kubectl] [ kubectl-create] polecenia.

```azurecli-interactive
kubectl create -f azure-premimum.yaml
```

## <a name="using-the-persistent-volume"></a>Przy użyciu trwały wolumin

Po oświadczeń trwały wolumin został utworzony i dysku pomyślnie zainicjowano obsługę administracyjną, pod można tworzyć za pomocą dostępu do dysku. Manifest następujących tworzy pod, używającej oświadczeń trwały wolumin `azure-managed-disk` do zainstalowania dysku platformy Azure na `/mnt/azure` ścieżki.

Utwórz plik o nazwie `azure-pvc-disk.yaml`i skopiuj następujące manifestu.

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
        claimName: azure-managed-disk
```

Utwórz pod z [utworzyć kubectl] [ kubectl-create] polecenia.

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

Masz teraz pod uruchomiona z dysku platformy Azure, zamontowane w `/mnt/azure` katalogu. Ta konfiguracja może być moment zapoznanie się z pod za pośrednictwem `kubectl describe pod mypod`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o Kubernetes woluminy trwałe przy użyciu dysku systemu Azure.

> [!div class="nextstepaction"]
> [Dodatek plug-in Kubernetes dla dysku systemu Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md