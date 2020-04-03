---
title: Dynamiczne tworzenie i używanie woluminu trwałego z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak dynamicznie tworzyć wolumin trwały za pomocą dysków platformy Azure w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: f16a6134b1d2065668952ea11c0cab7398a3559a
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617470"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamiczne tworzenie i używanie woluminu trwałego z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Wolumin trwały reprezentuje kawałek magazynu, który został aprowidywnie do użytku z zasobnikami kubernetes. Wolumin trwały może być używany przez jeden lub wiele zasobników i może być dynamicznie lub statycznie aprowizacji. W tym artykule pokazano, jak dynamicznie tworzyć woluminy trwałe za pomocą dysków platformy Azure do użycia przez pojedynczy zasobnik w klastrze usługi Azure Kubernetes (AKS).

> [!NOTE]
> Dysk platformy Azure można zamontować tylko z *trybem dostępu* typu *ReadWriteOnce*, który udostępnia go tylko do jednego zasobnika w usłudze AKS. Jeśli chcesz udostępnić wolumin trwały w wielu zasobnikach, użyj [usługi Azure Files][azure-files-pvc].

Aby uzyskać więcej informacji na temat woluminów kubernetes, zobacz [Opcje magazynu dla aplikacji w aks][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="built-in-storage-classes"></a>Wbudowane klasy pamięci masowej

Klasa magazynu służy do definiowania dynamicznego tworzenia jednostki magazynu za pomocą woluminu trwałego. Aby uzyskać więcej informacji na temat klas magazynu kubernetes, zobacz [Klasy magazynu Kubernetes][kubernetes-storage-classes].

Każdy klaster AKS zawiera dwie wstępnie utworzone klasy magazynu, obie skonfigurowane do pracy z dyskami platformy Azure:

* *Domyślna* klasa magazynu apowich standardowego dysku platformy Azure.
    * Standardowa pamięć masowa jest wspierana przez dyski twarde i zapewnia ekonomiczną pamięć masową, a jednocześnie jest wydajna. Dyski standardowe są idealne dla ekonomicznego obciążenia dewelopernego i testowego.
* Klasa *magazynu w wersji zarządzanej w wersji premium* udostępnia dysk platformy Azure w wersji premium.
    * Dyski w warstwie Premium są wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Jeśli węzły usługi AKS w klastrze używają magazynu w wersji premium, wybierz klasę *managed-premium.*
    
Te domyślne klasy magazynu nie umożliwiają aktualizowania rozmiaru woluminu po utworzeniu. Aby włączyć tę możliwość, dodaj *allowVolumeRozwiń: prawdziwy* wiersz do jednej z domyślnych klas magazynu lub utwórz własną niestandardową klasę magazynu. Za pomocą `kubectl edit sc` polecenia można edytować istniejącą klasę magazynu. Aby uzyskać więcej informacji na temat klas magazynu i tworzenia własnych, zobacz [Opcje magazynowania dla aplikacji w aks][storage-class-concepts].

Użyj [polecenia kubectl get sc,][kubectl-get] aby wyświetlić wstępnie utworzone klasy magazynu. W poniższym przykładzie przedstawiono wstępnie utworzone klasy magazynu dostępne w klastrze usługi AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Trwałe oświadczenia woluminów są określone w GiB, ale dyski zarządzane platformy Azure są rozliczane przez jednostkę SKU dla określonego rozmiaru. Te jednostki SKU wahają się od 32GiB dla dysków S4 lub P4 do 32TiB dla dysków S80 lub P80 (w wersji zapoznawczej). Wydajność przepływności i we/wy dysku zarządzanego w układce zależy zarówno od jednostki SKU, jak i od rozmiaru wystąpienia węzłów w klastrze AKS. Aby uzyskać więcej informacji, zobacz [Ceny i wydajność dysków zarządzanych][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Tworzenie trwałego oświadczenia woluminu

Trwałe oświadczenie woluminu (PVC) jest używany do automatycznego aprowizowania magazynu na podstawie klasy magazynu. W takim przypadku pvc można użyć jednej z wstępnie utworzonych klas magazynu do utworzenia standardowego lub premium dysku zarządzanego platformy Azure.

Utwórz plik `azure-premium.yaml`o nazwie i skopiuj go w poniższym manifeście. Oświadczenie żąda dysku `azure-managed-disk` o nazwie o rozmiarze *5 GB* z *dostępem ReadWriteOnce.* Klasa *magazynu w wersji zarządzanej w ramach premii* jest określona jako klasa magazynu.

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
> Aby utworzyć dysk, który używa `storageClassName: default` magazynu standardowego, należy użyć zamiast *wersji zarządzanej.*

Utwórz trwałe oświadczenie woluminu za pomocą polecenia [zastosuj kubectl][kubectl-apply] i określ plik *azure-premium.yaml:*

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Korzystanie z woluminu trwałego

Po utworzeniu trwałego oświadczenia woluminu i pomyślnym zainicjowaniu obsługi administracyjnej dysku można utworzyć zasobnik z dostępem do dysku. Poniższy manifest tworzy podstawowy zasobnik NGINX, który używa trwałego oświadczenia woluminu o `/mnt/azure`nazwie *azure-managed-disk* do zainstalowania dysku platformy Azure w ścieżce . W przypadku kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w programie AKS) określ *mountPath* przy użyciu konwencji ścieżki systemu Windows, takiej jak *"D:"*.

Utwórz plik `azure-pvc-disk.yaml`o nazwie i skopiuj go w poniższym manifeście.

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

Utwórz zasobnik za pomocą polecenia [zastosuj kubectl,][kubectl-apply] jak pokazano w poniższym przykładzie:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Masz teraz uruchomiony zasobnik z dyskiem `/mnt/azure` platformy Azure zainstalowanym w katalogu. Ta konfiguracja może być widoczna `kubectl describe pod mypod`podczas sprawdzania zasobnika za pośrednictwem, jak pokazano na poniższym skondensowanym przykładzie:

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

## <a name="back-up-a-persistent-volume"></a>Parcie zapasowe woluminu trwałego

Aby uzyskać zapas kopii zapasowej danych na woluminie trwałym, zrób migawkę dysku zarządzanego dla woluminu. Następnie można użyć tej migawki, aby utworzyć przywrócony dysk i dołączyć do zasobników jako środek przywracania danych.

Najpierw uzyskaj nazwę woluminu `kubectl get pvc` za pomocą polecenia, na przykład dla PVC o nazwie *azure-managed-disk:*

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ta nazwa woluminu stanowi podstawową nazwę dysku platformy Azure. Zapytanie o identyfikator dysku z [listą dysków az][az-disk-list] i podanie nazwy woluminu PVC, jak pokazano w poniższym przykładzie:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Użyj identyfikatora dysku, aby utworzyć dysk migawki z [migawką az create][az-snapshot-create]. Poniższy przykład tworzy migawkę o nazwie *pvcSnapshot* w tej samej grupie zasobów co klaster AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Jeśli tworzysz migawki i przywracasz dyski w grupach zasobów, do których klaster AKS nie ma dostępu, mogą wystąpić problemy z uprawnieniami.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

W zależności od ilości danych na dysku może upłynąć kilka minut, aby utworzyć migawkę.

## <a name="restore-and-use-a-snapshot"></a>Przywracanie i używanie migawki

Aby przywrócić dysk i używać go z zasobnikiem Kubernetes, użyj migawki jako źródła podczas tworzenia dysku z [dyskiem az create][az-disk-create]. Ta operacja zachowuje oryginalny zasób, jeśli następnie trzeba uzyskać dostęp do oryginalnej migawki danych. Poniższy przykład tworzy dysk o nazwie *pvcOdtored* z migawki o nazwie *pvcSnapshot:*

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Aby użyć przywróconego dysku z zasobnikiem, należy określić identyfikator dysku w manifeście. Pobierz identyfikator dysku za pomocą polecenia [az disk show.][az-disk-show] W poniższym przykładzie zostanie wyświetlony identyfikator dysku dla *pvcRestored* utworzony w poprzednim kroku:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Utwórz manifest `azure-restored.yaml` zasobnika o nazwie i określ identyfikator URI dysku uzyskany w poprzednim kroku. Poniższy przykład tworzy podstawowy serwer sieci Web NGINX, z przywróconym dyskiem zamontowanym jako wolumin na */mnt/azure:*

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

Utwórz zasobnik za pomocą polecenia [zastosuj kubectl,][kubectl-apply] jak pokazano w poniższym przykładzie:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Można użyć `kubectl describe pod mypodrestored` do wyświetlania szczegółów zasobnika, takich jak następujący skondensowany przykład, który pokazuje informacje o woluminie:

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

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące przechowywania i tworzenia kopii zapasowych w u.][operator-best-practices-storage]

Dowiedz się więcej o woluminach trwałych kubernetes przy użyciu dysków platformy Azure.

> [!div class="nextstepaction"]
> [Wtyczka Kubernetes dla dysków platformy Azure][azure-disk-volume]

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
