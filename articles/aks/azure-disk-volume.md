---
title: Używać dysków Azure AKS
description: Używać dysków Azure AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: cfad5ebd1212df03cee86b71340d8a73c2594f57
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096097"
---
# <a name="volumes-with-azure-disks"></a>Woluminy z dysku systemu Azure

Aplikacje kontenera często muszą uzyskać dostęp do utrwalenia danych w woluminie danych zewnętrznych. Dyski Azure mogą być używane jako ten magazyn danych zewnętrznych. Szczegóły tego artykułu przy użyciu dysku platformy Azure jako wolumin Kubernetes w klastrze usługi Kubernetes Azure (AKS).

Aby uzyskać więcej informacji na woluminach Kubernetes, zobacz [woluminów Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Tworzenie dysku platformy Azure

Przed odinstalowaniem zarządzane Azure dysku jako woluminu Kubernetes, dysk musi istnieć w AKS **węzła** grupy zasobów. Pobierz nazwę grupy zasobów z [Pokaż zasobów az] [ az-resource-show] polecenia.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Użyj [Tworzenie dysku az] [ az-disk-create] polecenie, aby utworzyć dysku platformy Azure.

Aktualizacja `--resource-group` z nazwą grupy zasobów zebranych w ostatnim kroku i `--name` na wybraną nazwę.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Po utworzeniu dysku, powinny zostać wyświetlone dane wyjściowe podobne do następującego. Ta wartość jest identyfikator dysku, który jest używany w przypadku instalowania z dysku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Zainstalować dysk jako wolumin

Zainstaluj dysku platformy Azure w sieci pod przez skonfigurowanie woluminu w specyfikacji kontenera.

Utwórz nowy plik o nazwie `azure-disk-pod.yaml` z następującą zawartość. Aktualizacja `diskName` o nazwie nowo utworzony dysk i `diskURI` o identyfikatorze dysku. Ponadto Zanotuj `mountPath`, która jest ścieżka, w którym dysku platformy Azure jest zainstalowany w pod.

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

Umożliwia utworzenie pod kubectl.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Masz teraz pod uruchomiona z dysku platformy Azure na zainstalowany `/mnt/azure`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat Kubernetes woluminów na dyskach platformy Azure.

> [!div class="nextstepaction"]
> [Dodatek Kubernetes dysków Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
