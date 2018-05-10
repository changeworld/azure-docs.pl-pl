---
title: Używać dysków Azure AKS
description: Używać dysków Azure AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 33d9a01f063ee8ad531a3f7e01dcfbf1c4ba8901
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="volumes-with-azure-disks"></a>Woluminy z dysku systemu Azure

Aplikacje kontenera często muszą uzyskać dostęp do utrwalenia danych w woluminie danych zewnętrznych. Dyski Azure mogą być używane jako ten magazyn danych zewnętrznych. Szczegóły tego artykułu przy użyciu dysku platformy Azure jako wolumin Kubernetes w klastrze usługi Kubernetes Azure (AKS).

Aby uzyskać więcej informacji na woluminach Kubernetes, zobacz [woluminów Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Tworzenie dysku platformy Azure

Przed odinstalowaniem platformy Azure zarządzanych dysku jako woluminu Kubernetes dysku musi istnieć w tej samej grupie zasobów co AKS zasobów klastra. Aby znaleźć tej grupy zasobów, należy użyć [listy grup az] [ az-group-list] polecenia.

```azurecli-interactive
az group list --output table
```

Szukasz grupy zasobów o nazwie podobny do `MC_clustername_clustername_locaton`, gdzie clustername jest nazwą klastra AKS i lokalizacja jest region platformy Azure, w którym została wdrożona klastra.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Użyj [Tworzenie dysku az] [ az-disk-create] polecenie, aby utworzyć dysku platformy Azure.

Za pomocą tego przykładu, zaktualizuj `--resource-group` z nazwą grupy zasobów i `--name` na wybraną nazwę.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Po utworzeniu dysku, powinny zostać wyświetlone dane wyjściowe podobne do następującego. Ta wartość jest identyfikator dysku, który jest używany podczas instalowania dysku do Kubernetes pod.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Zainstalować dysk jako wolumin

Zainstaluj dysku platformy Azure w sieci pod przez skonfigurowanie woluminu w specyfikacji kontenera.

Utwórz nowy plik o nazwie `azure-disk-pod.yaml` z następującą zawartość. Aktualizacja `diskName` o nazwie nowo utworzony dysk i `diskURI` o identyfikatorze dysku. Ponadto Zanotuj `mountPath`, jest to ścieżka, jaką dysku platformy Azure jest zainstalowany w pod.

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
