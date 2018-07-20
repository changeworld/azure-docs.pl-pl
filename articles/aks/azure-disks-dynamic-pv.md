---
title: Tworzenie trwałych woluminów przy użyciu usługi Azure Kubernetes Service
description: Dowiedz się, jak korzystać z dysków platformy Azure do utworzenia trwałego woluminów na potrzeby zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/10/2018
ms.author: iainfou
ms.openlocfilehash: 34d3a5dbccf2cad7873bf6166e406c7c4817ac09
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158713"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Utwórz trwałe woluminy z dyskami platformy Azure dla usługi Azure Kubernetes Service (AKS)

Trwały wolumin reprezentuje fragment magazynu, który został aprowizowany do użytku z zasobników. Trwały wolumin może być używany przez jeden lub wiele zasobników i mogą być dynamicznie lub statycznie udostępniane. Aby uzyskać więcej informacji na woluminach trwałego rozwiązania Kubernetes, zobacz [woluminy trwałe Kubernetes][kubernetes-volumes]. W tym artykule dowiesz się, jak używać woluminy trwałe z dyskami platformy Azure w klastrze usługi Azure Kubernetes Service (AKS).

> [!NOTE]
> Dysku platformy Azure można instalować tylko za pomocą *tryb dostępu* typu *ReadWriteOnce*, która udostępnia je zadaniom tylko jeden węzeł usługi AKS. Jeśli wymagające udostępnić trwały wolumin w wielu węzłach, rozważ użycie [usługi Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Wbudowane klasy magazynu

Klasa magazynu służy do definiowania sposobu jednostki magazynu jest tworzony dynamicznie z trwały wolumin. Aby uzyskać więcej informacji na temat klasy magazynu w usłudze Kubernetes, zobacz [klasy magazynu w usłudze Kubernetes][kubernetes-storage-classes].

Każdy klaster AKS zawiera dwie klasy magazynu wstępnie utworzone, skonfigurowane do pracy z dyskami platformy Azure. *Domyślne* klasy magazynowania aprowizuje standardowa dysku platformy Azure. *Managed premium* klasy magazynowania aprowizuje premium dysku platformy Azure. Jeśli węzłów AKS w klastrze Użyj usługi premium storage, wybierz opcję *managed premium* klasy.

Użyj [kubectl get-sc] [ kubectl-get] polecenie, aby wyświetlić klasy wstępnie utworzonego magazynu. W poniższym przykładzie przedstawiono wstępne utworzenie klasy magazynu są dostępne w ramach klastra usługi AKS:

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Trwały wolumin oświadczenia są określone w GiB, ale usługi Azure managed disks rachunek jest wystawiany przez jednostkę SKU na określony rozmiar. Te jednostki SKU w zakresie od 32GiB S4 lub P4 dysków do 4TiB S50 lub P50 dysków. Ponadto, przepływności i wydajności operacji We/Wy — wersja Premium managed dysku zależy zarówno SKU i rozmiar wystąpienia węzłów w klastrze AKS. Aby uzyskać więcej informacji, zobacz [ceny i wydajności Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Tworzenie oświadczenia trwały wolumin

Oświadczenie trwały wolumin (PVC) jest używany do automatycznej aprowizacji magazynu opartego na klasę magazynu. W tym przypadku PVC umożliwia jednej z klas wstępnie utworzonego magazynu tworzenie standardowa lub premium usługi Azure dysku zarządzanego.

Utwórz plik o nazwie `azure-premium.yaml`i skopiuj następujące manifestu.

Zwróć uwagę, że *managed premium* klasę magazynu jest określona w adnotacji i oświadczenia żąda dysku *5GB* w rozmiarze *ReadWriteOnce* dostępu.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Aby utworzyć dysk, który używa magazynu w warstwie standardowa, należy użyć `storageClassName: default` zamiast *managed premium*.

Tworzenie oświadczenia trwały wolumin z [zastosować kubectl] [ kubectl-apply] polecenia i podaj swoje *azure premium.yaml* pliku:

```console
kubectl apply -f azure-premium.yaml
```

## <a name="use-the-persistent-volume"></a>Użyj trwały wolumin

Po oświadczenia trwały wolumin został utworzony i dysku pomyślnie zainicjowano obsługę administracyjną, mogą być tworzone zasobnik za dostęp do dysku. Następujące manifest tworzy zasobnik, który używa oświadczeń trwały wolumin *-dysk zarządzany platformy azure —* na dysku platformy Azure na instalowanie `/mnt/azure` ścieżki.

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

Utwórz zasobnik za pomocą [zastosować kubectl] [ kubectl-apply] polecenia.

```console
kubectl apply -f azure-pvc-disk.yaml
```

Masz teraz uruchomiony zasobnik z dysku platformy Azure zainstalowany w `/mnt/azure` katalogu. Ta konfiguracja może być moment zapoznanie się tym zasobniku za pośrednictwem `kubectl describe pod mypod`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat woluminy trwałe Kubernetes za pomocą usługi Azure disks.

> [!div class="nextstepaction"]
> [Wtyczka usługi Kubernetes dla usługi Azure disks][azure-disk-volume]

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
