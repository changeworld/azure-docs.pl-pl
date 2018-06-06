---
title: Należy użyć dysku platformy Azure z AKS
description: Używać dysków Azure AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 3841222d08b23f43f69e77fa43c469793b70ce63
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801385"
---
# <a name="persistent-volumes-with-azure-disks"></a>Trwałe woluminy z dysku systemu Azure

Trwały wolumin reprezentuje fragment magazynu, które zostały udostępnione do użycia z stanowiskami Kubernetes. Trwały wolumin mogą być używane przez jedną lub wiele stanowiskami i można za dynamicznie, czy statycznie. Aby uzyskać więcej informacji na woluminach trwałe Kubernetes, zobacz [woluminy trwałe Kubernetes][kubernetes-volumes].

Szczegóły tego dokumentu przy użyciu trwałe woluminy z dysku systemu Azure w klastrze usługi Kubernetes Azure (AKS).

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

> [!NOTE]
> Trwały wolumin oświadczenia są określone w GiB, ale zarządzane Azure dyski są rozliczane według jednostki SKU dla określonego rozmiaru. Te jednostki SKU w zakresie od 32GiB S4 lub P4 dysków do 4TiB S50 lub P50 dysków. Ponadto, przepływności i wydajności IOPS Premium zarządzane dysku zależy od obu SKU i rozmiar wystąpienia w węzłach klastra AKS. Zobacz [cen i wydajności dysków zarządzanych][managed-disk-pricing-performance].

## <a name="create-persistent-volume-claim"></a>Tworzenie oświadczeń trwały wolumin

Oświadczenie trwały wolumin (PVC) służy do automatycznie obsługiwać magazyn oparty na klasę magazynu. W takim przypadku PVC umożliwia jednej z klas wstępnie utworzone magazynu tworzenie Azure standard lub premium dysków zarządzanych.

Utwórz plik o nazwie `azure-premium.yaml`i skopiuj następujące manifestu.

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

Tworzenie oświadczenia trwały wolumin o [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f azure-premium.yaml
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

Utwórz pod z [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

Masz teraz pod uruchomiona z dysku platformy Azure, zamontowane w `/mnt/azure` katalogu. Ta konfiguracja może być moment zapoznanie się z pod za pośrednictwem `kubectl describe pod mypod`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o Kubernetes woluminy trwałe przy użyciu dysku systemu Azure.

> [!div class="nextstepaction"]
> [Dodatek plug-in Kubernetes dla dysku systemu Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md 
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
