---
title: Dyski platformy Azure za pomocą usługi AKS
description: Dyski platformy Azure za pomocą usługi AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8aea56017d38b57d36f5f1d42e2d4e9ed1d809e5
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346097"
---
# <a name="volumes-with-azure-disks"></a>Woluminy z dyskami platformy Azure

Oparte na kontenerach aplikacje często muszą uzyskać dostęp do utrwalania danych w woluminie danych zewnętrznych. Dyski platformy Azure może służyć jako ten magazyn danych zewnętrznych. Ten artykuł zawiera przy użyciu dysku platformy Azure jako wolumin Kubernetes w klastrze usługi Azure Kubernetes Service (AKS).

Aby uzyskać więcej informacji na woluminach Kubernetes, zobacz [woluminów Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Tworzenie dysku platformy Azure

Przed odinstalowaniem z usługi Azure managed dysku jako woluminu, Kubernetes, dysk musi istnieć w usłudze AKS **węzła** grupy zasobów. Pobierz nazwę grupy zasobów przy użyciu [az resource show] [ az-resource-show] polecenia.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Użyj [tworzenia dysku az] [ az-disk-create] polecenie, aby utworzyć dysku platformy Azure.

Aktualizacja `--resource-group` nazwą grupy zasobów zebranych w ostatnim kroku i `--name` nazwę ulubionego.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Po utworzeniu dysku powinien pojawić się dane wyjściowe podobne do następujących. Ta wartość jest identyfikator dysku, który jest używany w przypadku instalowania z dysku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```
> [!NOTE]
> Usługi Azure managed disks są rozliczane według jednostki SKU dla określonego rozmiaru. Te jednostki SKU w zakresie od 32GiB S4 lub P4 dysków do 4TiB S50 lub P50 dysków. Ponadto, przepływności i wydajności operacji We/Wy — wersja Premium managed dysku zależy od rozmiaru wystąpienia węzłów w klastrze AKS i jednostki SKU. Zobacz [ceny i wydajności dysków zarządzanych][managed-disk-pricing-performance].

## <a name="mount-disk-as-volume"></a>Zainstaluj dysk jako wolumin

Zainstaluj dysk platformy Azure w tym zasobniku, konfigurując woluminu w specyfikacji kontenera.

Utwórz nowy plik o nazwie `azure-disk-pod.yaml` z następującą zawartością. Aktualizacja `diskName` o nazwie nowo utworzonego dysku i `diskURI` identyfikator dysku. Ponadto Zwróć uwagę na `mountPath`, czyli ścieżkę, w którym dysku platformy Azure jest zainstalowany w zasobnik.

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

Użyj narzędzia kubectl, aby utworzyć zasobnik.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Masz teraz uruchomiony zasobnik z dysku platformy Azure zainstalowany w lokalizacji `/mnt/azure`.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o woluminach Kubernetes przy użyciu dysków platformy Azure.

> [!div class="nextstepaction"]
> [Wtyczka usługi Kubernetes dla usługi Azure Disks][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
