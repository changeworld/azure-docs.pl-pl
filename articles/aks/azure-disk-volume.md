---
title: Tworzenie woluminu statycznego dla zasobników w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin z dyskami platformy Azure do użytku z zasobnikiem w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 17795ae696c0d710f099a5c21aa754fc925953ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047937"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ręczne tworzenie i używanie woluminu z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Aplikacje oparte na kontenerach często muszą uzyskiwać dostęp do danych i utrwalać je w woluminie danych zewnętrznych. Jeśli pojedynczy zasobnik potrzebuje dostępu do magazynu, można użyć dysków platformy Azure do przedstawienia woluminu macierzystego do użycia aplikacji. W tym artykule pokazano, jak ręcznie utworzyć dysk platformy Azure i dołączyć go do zasobnika w usłudze AKS.

> [!NOTE]
> Dysk platformy Azure można zamontować tylko do pojedynczego zasobnika w czasie. Jeśli chcesz udostępnić wolumin trwały w wielu zasobnikach, użyj [usługi Azure Files][azure-files-volume].

Aby uzyskać więcej informacji na temat woluminów kubernetes, zobacz [Opcje magazynu dla aplikacji w aks][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Tworzenie dysku platformy Azure

Podczas tworzenia dysku platformy Azure do użytku z usługą AKS można utworzyć zasób dysku w grupie zasobów **węzła.** Takie podejście umożliwia klastrowi AKS dostęp do zasobu dysku i zarządzanie nim. Jeśli zamiast tego utworzyć dysk w oddzielnej grupie zasobów, należy udzielić jednostki usługi Azure Kubernetes Service (AKS) dla klastra `Contributor` roli do grupy zasobów dysku. Alternatywnie można użyć systemu przypisanego tożsamości zarządzanej dla uprawnień zamiast jednostki usługi. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

W tym artykule utwórz dysk w grupie zasobów węzła. Najpierw pobierz nazwę grupy zasobów za pomocą polecenia [az aks show][az-aks-show] i dodaj parametr kwerendy. `--query nodeResourceGroup` W poniższym przykładzie pobiera grupę zasobów węzła dla nazwy klastra AKS *myAKSCluster* w nazwie grupy zasobów *myResourceGroup:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz utwórz dysk za pomocą polecenia [tworzenie dysku az.][az-disk-create] Określ nazwę grupy zasobów węzła uzyskaną w poprzednim poleceniu, a następnie nazwę zasobu dysku, taką jak *myAKSDisk*. Poniższy przykład tworzy dysk *20*GiB i wyprowadza identyfikator dysku po utworzeniu. Jeśli musisz utworzyć dysk do użycia z kontenerami systemu Windows Server (obecnie `--os-type windows` w wersji zapoznawczej w programie AKS), dodaj parametr, aby poprawnie sformatować dysk.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Dyski platformy Azure są rozliczane przez jednostkę SKU dla określonego rozmiaru. Te jednostki SKU wahają się od 32GiB dla dysków S4 lub P4 do 32TiB dla dysków S80 lub P80 (w wersji zapoznawczej). Wydajność przepływności i we/wy dysku zarządzanego w układce zależy zarówno od jednostki SKU, jak i od rozmiaru wystąpienia węzłów w klastrze AKS. Zobacz [Ceny i wydajność dysków zarządzanych][managed-disk-pricing-performance].

Identyfikator zasobu dysku jest wyświetlany po pomyślnym zakończeniu polecenia, jak pokazano w poniższym przykładzie danych wyjściowych. Ten identyfikator dysku służy do instalowania dysku w następnym kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Instalowanie dysku jako woluminu

Aby zainstalować dysk platformy Azure w zasobniku, skonfiguruj `azure-disk-pod.yaml` wolumin w specyfikacji kontenera. Zaktualizuj `diskName` z nazwą dysku utworzonego `diskURI` w poprzednim kroku i z identyfikatorem dysku pokazanym na wyjściu polecenia utwórz dysk. W razie potrzeby `mountPath`zaktualizuj , czyli ścieżkę, na której jest zainstalowany dysk platformy Azure w zasobniku. W przypadku kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w programie AKS) określ *mountPath* przy użyciu konwencji ścieżki systemu Windows, takiej jak *"D:"*.

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

Użyj `kubectl` polecenia, aby utworzyć zasobnik.

```console
kubectl apply -f azure-disk-pod.yaml
```

Masz teraz uruchomiony zasobnik z dyskiem platformy Azure zamontowanym w . `/mnt/azure` Można użyć, `kubectl describe pod mypod` aby sprawdzić, czy dysk jest zainstalowany pomyślnie. Następujące skondensowane przykładowe wyjście pokazuje objętość zamontowaną w pojemniku:

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

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące przechowywania i tworzenia kopii zapasowych w u.][operator-best-practices-storage]

Aby uzyskać więcej informacji na temat klastrów AKS interakcji z dyskami platformy Azure, zobacz [wtyczkę Kubernetes dla dysków platformy Azure][kubernetes-disks].

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
