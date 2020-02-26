---
title: Tworzenie woluminu statycznego dla zasobników w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin z dyskami platformy Azure do użycia w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: b84f62dd02aa29a4c1aa64e3235c0a1e7cc66522
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596746"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ręczne tworzenie woluminu i używanie go z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Aplikacje oparte na kontenerach często muszą uzyskiwać dostęp do danych w zewnętrznym woluminie danych i utrwalać je. Jeśli jeden z nich wymaga dostępu do magazynu, możesz użyć dysków platformy Azure, aby przedstawić wolumin natywny do użycia w aplikacji. W tym artykule pokazano, jak ręcznie utworzyć dysk platformy Azure i dołączyć go do usługi pod AKS.

> [!NOTE]
> Dysk platformy Azure można zainstalować tylko w jednym z nich. Jeśli musisz udostępnić wolumin trwały w wielu zasobnikach, użyj [Azure Files][azure-files-volume].

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz [Opcje magazynu dla aplikacji w AKS][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Tworzenie dysku platformy Azure

Podczas tworzenia dysku platformy Azure do użycia z programem AKS można utworzyć zasób dysku w grupie zasobów **węzła** . Takie podejście umożliwia klastrowi AKS dostęp do zasobu dysku i zarządzanie nim. Jeśli zamiast tego utworzysz dysk w oddzielnej grupie zasobów, musisz przyznać jednostki usługi Azure Kubernetes Service (AKS) dla klastra jako rolę `Contributor` do grupy zasobów tego dysku.

W tym artykule Utwórz dysk w grupie zasobów węzła. Najpierw Pobierz nazwę grupy zasobów za pomocą polecenia [AZ AKS show][az-aks-show] i Dodaj parametr zapytania `--query nodeResourceGroup`. Poniższy przykład pobiera grupę zasobów węzła *dla nazwy klastra*AKS *myAKSCluster* w grupie zasobów nazwa zasobu:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz Utwórz dysk za pomocą polecenia [AZ Disk Create][az-disk-create] . Określ nazwę grupy zasobów węzła uzyskaną w poprzednim poleceniu, a następnie nazwę zasobu dyskowego, na przykład *myAKSDisk*. Poniższy przykład powoduje utworzenie dysku o *20*GIB i wyprowadzanie identyfikatora dysku po utworzeniu. Jeśli musisz utworzyć dysk do użycia z kontenerami systemu Windows Server (obecnie w wersji zapoznawczej w AKS), Dodaj parametr `--os-type windows`, aby prawidłowo sformatować dysk.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Usługa Azure disks jest rozliczana według jednostki SKU z określonym rozmiarem. Te jednostki SKU należą do zakresu od 32GiB dla dysków S4 lub P4 do 32TiB dla dysków S80 lub P80 (w wersji zapoznawczej). Wydajność i przepustowość operacji we/wy dysku zarządzanego w warstwie Premium zależy od jednostki SKU i rozmiaru wystąpienia węzłów w klastrze AKS. Zobacz [Cennik i wydajność Managed disks][managed-disk-pricing-performance].

Identyfikator zasobu dysku jest wyświetlany po pomyślnym zakończeniu polecenia, jak pokazano w poniższym przykładzie danych wyjściowych. Ten identyfikator dysku jest używany do instalowania dysku w następnym kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Zainstaluj dysk jako wolumin

Aby zainstalować dysk platformy Azure w obszarze usługi, skonfiguruj wolumin w specyfikacji kontenera. Utwórz nowy plik o nazwie `azure-disk-pod.yaml` z następującą zawartością. Zaktualizuj `diskName` przy użyciu nazwy dysku utworzonego w poprzednim kroku, a `diskURI` z IDENTYFIKATORem dysku przedstawionym w danych wyjściowych polecenia Utwórz dysk. W razie potrzeby zaktualizuj `mountPath`, czyli ścieżkę, w której jest zainstalowany dysk platformy Azure w obszarze. W przypadku kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w AKS) Określ *mountPath* przy użyciu konwencji ścieżki systemu Windows, takiej jak *'d: '* .

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

Użyj `kubectl` polecenie, aby utworzyć pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Masz teraz uruchomione miejsce na dysku platformy Azure, który został zainstalowany w `/mnt/azure`. Można użyć `kubectl describe pod mypod`, aby sprawdzić, czy dysk jest prawidłowo zainstalowany. Następujące wąskie przykładowe dane wyjściowe pokazują wolumin zainstalowany w kontenerze:

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

## <a name="next-steps"></a>Następne kroki

W przypadku skojarzonych najlepszych rozwiązań zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w AKS][operator-best-practices-storage].

Aby uzyskać więcej informacji na temat klastrów AKS związanych z usługą Azure disks, zobacz [dodatek Kubernetes dla dysków platformy Azure][kubernetes-disks].

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
