---
title: Tworzenie woluminu statyczne zasobników w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin z dyskami platformy Azure do użytku z zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: 20c7d20399392e653668953029bcb81886863ce4
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404623"
---
# <a name="manually-create-and-use-kubernetes-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ręcznie utworzyć i wolumin Kubernetes za pomocą usługi Azure disks w usłudze Azure Kubernetes Service (AKS)

Oparte na kontenerach aplikacje często muszą uzyskać dostęp do utrwalania danych w woluminie danych zewnętrznych. Dyski platformy Azure może służyć jako ten magazyn danych zewnętrznych. W usłudze AKS woluminy można tworzyć dynamicznie przy użyciu oświadczeń trwały wolumin lub można ręcznie utworzyć i dołączyć bezpośrednio dysku platformy Azure. W tym artykule pokazano, jak ręcznie utworzyć dysku platformy Azure i dołączyć go do zasobników w usłudze AKS.

Aby uzyskać więcej informacji na woluminach Kubernetes, zobacz [woluminów Kubernetes][kubernetes-volumes].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.46 lub później zainstalowane i skonfigurowane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Tworzenie dysku platformy Azure

Podczas tworzenia dysku platformy Azure do użycia za pomocą usługi AKS, można utworzyć zasobu dysku w **węzła** grupy zasobów. Takie podejście umożliwia klastra AKS dostępu i zarządzanie zasobu dysku. Jeśli zamiast tego utworzyć dysk w oddzielnej grupie zasobów, należy udzielić uprawnień jednostki usługi Azure Kubernetes Service (AKS) dla klastra `Contributor` roli do grupy zasobów na dysku.

W tym artykule należy utworzyć dysku w węźle grupy zasobów. Najpierw Uzyskaj nazwę grupy zasobów za pomocą [az aks show] [ az-aks-show] polecenie i Dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera węzeł grupy zasobów dla nazwy klastra AKS *myAKSCluster* w polu Nazwa grupy zasobów *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz Utwórz dysk za pomocą [tworzenia dysku az] [ az-disk-create] polecenia. Określ nazwę grupy zasobów węzła, takich jak uzyskane w poprzednim poleceniu, a następnie nazwę zasobu dyskowego *myAKSDisk*. Poniższy przykład tworzy *20*GiB dysku i dane wyjściowe identyfikator dysku, po utworzeniu:

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Dyski platformy Azure są rozliczane według jednostki SKU dla określonego rozmiaru. Te jednostki SKU w zakresie od 32GiB S4 lub P4 dysków do 8TiB S60 lub P60 dysków. Przepływność i wydajność operacji We/Wy — wersja Premium managed dysku zależy od rozmiaru wystąpienia węzłów w klastrze AKS i jednostki SKU. Zobacz [ceny i wydajności dysków zarządzanych][managed-disk-pricing-performance].

Identyfikator zasobu dysku jest wyświetlany, gdy polecenie zostanie pomyślnie zakończony, jak pokazano w następujących przykładowych danych wyjściowych. Ten identyfikator dysku jest używany do zainstalowania dysku w następnym kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Zainstaluj dysk jako wolumin

Aby zainstalować dysku platformy Azure, w tym zasobniku, skonfiguruj wolumin w specyfikacji kontenera. Utwórz nowy plik o nazwie `azure-disk-pod.yaml` z następującą zawartością. Aktualizacja `diskName` o nazwie dysk utworzony w poprzednim kroku i `diskURI` o identyfikatorze dysku wyświetlany w danych wyjściowych dysku utworzyć polecenie. W razie potrzeby zaktualizuj `mountPath`, czyli ścieżkę, w którym dysku platformy Azure jest zainstalowany w zasobnik.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
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

Masz teraz uruchomiony zasobnik z dysku platformy Azure zainstalowany w lokalizacji `/mnt/azure`. Możesz użyć `kubectl describe pod azure-disk-pod` Aby sprawdzić, dysk jest zainstalowany pomyślnie.

## <a name="next-steps"></a>Kolejne kroki

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