---
title: Dynamiczne tworzenie woluminu dysku dla wielu zasobnikach w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak dynamicznie utworzyć trwały wolumin z dyskami platformy Azure do użytku z wielu jednoczesnych zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 334e56db97213206d9ab7ed5ef4d1d96ab9325d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956478"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamiczne tworzenie i używanie trwały wolumin z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Trwały wolumin reprezentuje fragment magazynu, który został aprowizowany do użytku z zasobników. Trwały wolumin może być używany przez jeden lub wiele zasobników i mogą być dynamicznie lub statycznie udostępniane. W tym artykule pokazano, jak dynamiczne tworzenie trwałych woluminów z dyskami platformy Azure na potrzeby używania przez pojedynczy zasobnik w klastrze usługi Azure Kubernetes Service (AKS).

> [!NOTE]
> Dysku platformy Azure można instalować tylko za pomocą *tryb dostępu* typu *ReadWriteOnce*, która udostępnia je zadaniom pojedynczy zasobnik w usłudze AKS. Jeśli musisz udostępniać trwały wolumin w wielu zasobnikach, użyj [usługi Azure Files][azure-files-pvc].

Aby uzyskać więcej informacji na woluminach Kubernetes, zobacz [opcji magazynu dla aplikacji w usłudze AKS][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.59 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="built-in-storage-classes"></a>Wbudowane klasy magazynu

Klasa magazynu służy do definiowania sposobu jednostki magazynu jest tworzony dynamicznie z trwały wolumin. Aby uzyskać więcej informacji na temat klasy magazynu w usłudze Kubernetes, zobacz [klasy magazynu w usłudze Kubernetes][kubernetes-storage-classes].

Każdy klaster AKS zawiera dwie klasy magazynu wstępnie utworzone, skonfigurowane do pracy z dyskami platformy Azure:

* *Domyślne* klasy magazynowania aprowizuje standardowa dysku platformy Azure.
    * Magazynu w warstwie standardowa jest objęta najlepszą dysków twardych i oferuje ekonomiczny magazyn, chociaż wciąż wydajne rozwiązanie. Dyski w warstwie Standardowa idealnie nadają się do ekonomicznej obsługi obciążeń tworzenia i testowania.
* *Managed premium* klasy magazynowania aprowizuje premium dysku platformy Azure.
    * Dyski w warstwie Premium są wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Jeśli węzłów AKS w klastrze Użyj usługi premium storage, wybierz opcję *managed premium* klasy.
    
Te klasy magazynu domyślnego nie zezwala na aktualizację rozmiaru woluminu po utworzeniu. Aby włączyć tę możliwość, Dodaj *allowVolumeExpansion: true* wiersz do jednej domyślnej klasy magazynu lub utworzyć klasę własnego magazynu niestandardowego. Można edytować istniejące magazynu przy użyciu klasy `kubectl edit sc` polecenia. Aby uzyskać więcej informacji na temat klas magazynu i youor tworzenia własnych, zobacz [opcji magazynu dla aplikacji w usłudze AKS][storage-class-concepts].

Użyj [kubectl get-sc] [ kubectl-get] polecenie, aby wyświetlić klasy wstępnie utworzonego magazynu. W poniższym przykładzie przedstawiono wstępne utworzenie klasy magazynu są dostępne w ramach klastra usługi AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Trwały wolumin oświadczenia są określone w GiB, ale usługi Azure managed disks rachunek jest wystawiany przez jednostkę SKU na określony rozmiar. Te jednostki SKU w zakresie od 32GiB S4 lub P4 dysków do 32TiB S80 lub P80 dysków (w wersji zapoznawczej). Przepływność i wydajność operacji We/Wy — wersja Premium managed dysku zależy zarówno SKU i rozmiar wystąpienia węzłów w klastrze AKS. Aby uzyskać więcej informacji, zobacz [ceny i wydajności Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Tworzenie oświadczenia trwały wolumin

Oświadczenie trwały wolumin (PVC) jest używany do automatycznej aprowizacji magazynu opartego na klasę magazynu. W tym przypadku PVC umożliwia jednej z klas wstępnie utworzonego magazynu tworzenie standardowa lub premium usługi Azure dysku zarządzanego.

Utwórz plik o nazwie `azure-premium.yaml`i skopiuj następujące manifestu. Oświadczenie żądań jest dysk o nazwie `azure-managed-disk` czyli *5GB* w rozmiarze *ReadWriteOnce* dostępu. *Managed premium* klasę magazynu jest określony jako klasa magazynu.

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
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Użyj trwały wolumin

Po oświadczenia trwały wolumin został utworzony i dysku pomyślnie zainicjowano obsługę administracyjną, mogą być tworzone zasobnik za dostęp do dysku. Następujące manifest tworzy podstawowe zasobnika NGINX, używającej oświadczeń trwały wolumin o nazwie *-dysk zarządzany platformy azure —* do zainstalowania dysku platformy Azure w ścieżce `/mnt/azure`. W systemie Windows Server kontenerów (obecnie dostępna w wersji zapoznawczej w usłudze AKS), określ *mountPath* przy użyciu konwencji ścieżkę Windows, takich jak *"D:"* .

Utwórz plik o nazwie `azure-pvc-disk.yaml`i skopiuj następujące manifestu.

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
        claimName: azure-managed-disk
```

Utwórz zasobnik za pomocą [zastosować kubectl] [ kubectl-apply] polecenia, jak pokazano w poniższym przykładzie:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Masz teraz uruchomiony zasobnik z dysku platformy Azure zainstalowany w `/mnt/azure` katalogu. Ta konfiguracja może być moment zapoznanie się tym zasobniku za pośrednictwem `kubectl describe pod mypod`, jak pokazano na poniższego, skróconego przykładu:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Tworzenie kopii zapasowej trwały wolumin

Aby utworzyć kopię zapasową danych w woluminie trwałego, należy utworzyć migawkę dysku zarządzanego dla woluminu. Następnie służy tę migawkę służącą do tworzenia przywróconego dysku i dołączyć do zasobników jako sposób przywracania danych.

Najpierw Uzyskaj nazwę woluminu za pomocą `kubectl get pvc` polecenia, takie jak PVC o nazwie *-dysk zarządzany platformy azure —* :

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ta nazwa woluminu stanowi podstawowej nazwy dysku platformy Azure. Zapytanie o identyfikator dysku przy użyciu [listy dysków az] [ az-disk-list] i podaj nazwę woluminu PVC, jak pokazano w poniższym przykładzie:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Użyj Identyfikatora dysku do utworzenia migawki dysku przy użyciu [Tworzenie migawki az][az-snapshot-create]. Poniższy przykład tworzy migawkę o nazwie *pvcSnapshot* w tej samej grupie zasobów co klaster AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Jeśli utworzysz migawki i Przywróć dyski w grupach zasobów, które klaster AKS nie ma dostępu do, mogą wystąpić problemy z uprawnieniami.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

W zależności od ilości danych na dysku może upłynąć kilka minut, aby utworzyć migawkę.

## <a name="restore-and-use-a-snapshot"></a>Przywracanie i używanie migawki

Aby przywrócić dysk i używać go z zasobnika Kubernetes, należy użyć migawki jako źródło podczas tworzenia dysku z [tworzenia dysku az][az-disk-create]. Ta operacja zachowuje oryginalny zasób, jeśli potem zechcesz dostęp do oryginalnego migawek chronionych danych. W poniższym przykładzie tworzony jest dysk o nazwie *pvcRestored* z migawki o nazwie *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Za pomocą przywróconego dysku zasobnik, należy określić identyfikator dysku w manifeście. Pobierz identyfikator dysku przy użyciu [Pokaż dysku az] [ az-disk-show] polecenia. Poniższy przykład pobiera identyfikator dysku *pvcRestored* utworzony w poprzednim kroku:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Tworzenie manifestu pod o nazwie `azure-restored.yaml` i określ dysk, identyfikator URI uzyskanego w poprzednim kroku. Poniższy przykład tworzy podstawowego serwera internetowego NGINX za pomocą przywróconego dysku zainstalowanego jako woluminu w */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Utwórz zasobnik za pomocą [zastosować kubectl] [ kubectl-apply] polecenia, jak pokazano w poniższym przykładzie:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Możesz użyć `kubectl describe pod mypodrestored` Aby wyświetlić szczegóły zasobników, takich jak poniższego, skróconego przykładu pokazujący informacji o woluminie:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Kolejne kroki

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze AKS][operator-best-practices-storage].

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
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
