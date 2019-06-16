---
title: Tworzenie woluminu statyczne zasobników w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin z dyskami platformy Azure do użytku z programem zasobnik w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: b166f70186b063782fb2c2245e351d6dfca6f978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072155"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ręcznie utworzyć i korzystać z woluminu z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Oparte na kontenerach aplikacje często muszą uzyskać dostęp do utrwalania danych w woluminie danych zewnętrznych. Jeśli pojedynczy zasobnik potrzebuje dostępu do magazynu, można użyć dysków platformy Azure do przedstawienia natywnych woluminu do użytku aplikacji. W tym artykule pokazano, jak ręcznie utworzyć dysku platformy Azure i dołączyć go do zasobników w usłudze AKS.

> [!NOTE]
> Dysku platformy Azure można instalować tylko pojedynczy zasobnik w danym momencie. Jeśli musisz udostępniać trwały wolumin w wielu zasobnikach, użyj [usługi Azure Files][azure-files-volume].

Aby uzyskać więcej informacji na woluminach Kubernetes, zobacz [opcji magazynu dla aplikacji w usłudze AKS][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.59 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Tworzenie dysku platformy Azure

Podczas tworzenia dysku platformy Azure do użycia za pomocą usługi AKS, można utworzyć zasobu dysku w **węzła** grupy zasobów. Takie podejście umożliwia klastra AKS dostępu i zarządzanie zasobu dysku. Jeśli zamiast tego utworzyć dysk w oddzielnej grupie zasobów, należy udzielić uprawnień jednostki usługi Azure Kubernetes Service (AKS) dla klastra `Contributor` roli do grupy zasobów na dysku.

W tym artykule należy utworzyć dysku w węźle grupy zasobów. Najpierw Uzyskaj nazwę grupy zasobów za pomocą [az aks show] [ az-aks-show] polecenie i Dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera węzeł grupy zasobów dla nazwy klastra AKS *myAKSCluster* w polu Nazwa grupy zasobów *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz Utwórz dysk za pomocą [tworzenia dysku az] [ az-disk-create] polecenia. Określ nazwę grupy zasobów węzła, takich jak uzyskane w poprzednim poleceniu, a następnie nazwę zasobu dyskowego *myAKSDisk*. Poniższy przykład tworzy *20*GiB dysku i dane wyjściowe identyfikator dysku, po utworzeniu. Jeśli musisz utworzyć dysku do użycia z kontenerami systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS), należy dodać `--os-type windows` parametru, aby poprawnie sformatować dysk.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Dyski platformy Azure są rozliczane według jednostki SKU dla określonego rozmiaru. Te jednostki SKU w zakresie od 32GiB S4 lub P4 dysków do 32TiB S80 lub P80 dysków (w wersji zapoznawczej). Przepływność i wydajność operacji We/Wy — wersja Premium managed dysku zależy od rozmiaru wystąpienia węzłów w klastrze AKS i jednostki SKU. Zobacz [ceny i wydajności dysków zarządzanych][managed-disk-pricing-performance].

Identyfikator zasobu dysku jest wyświetlany, gdy polecenie zostanie pomyślnie zakończony, jak pokazano w następujących przykładowych danych wyjściowych. Ten identyfikator dysku jest używany do zainstalowania dysku w następnym kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Zainstaluj dysk jako wolumin

Aby zainstalować dysku platformy Azure, w tym zasobniku, skonfiguruj wolumin w specyfikacji kontenera. Utwórz nowy plik o nazwie `azure-disk-pod.yaml` z następującą zawartością. Aktualizacja `diskName` o nazwie dysk utworzony w poprzednim kroku i `diskURI` o identyfikatorze dysku wyświetlany w danych wyjściowych dysku utworzyć polecenie. W razie potrzeby zaktualizuj `mountPath`, czyli ścieżkę, w którym dysku platformy Azure jest zainstalowany w zasobnik. W systemie Windows Server kontenerów (obecnie dostępna w wersji zapoznawczej w usłudze AKS), określ *mountPath* przy użyciu konwencji ścieżkę Windows, takich jak *"D:"* .

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
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Użyj `kubectl` polecenie, aby utworzyć zasobnik.

```console
kubectl apply -f azure-disk-pod.yaml
```

Masz teraz uruchomiony zasobnik z dysku platformy Azure zainstalowany w lokalizacji `/mnt/azure`. Możesz użyć `kubectl describe pod mypod` Aby sprawdzić, dysk jest zainstalowany pomyślnie. Następujące dane wyjściowe skróconego przykładu przedstawia wolumin zainstalowany w kontenerze:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Kolejne kroki

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze AKS][operator-best-practices-storage].

Aby uzyskać więcej informacji o usłudze AKS klastrów oddziałują na dyskach platformy Azure, zobacz [wtyczka Kubernetes dla usługi Azure Disks][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
