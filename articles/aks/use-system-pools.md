---
title: Korzystanie z pul węzłów systemowych w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak tworzyć pule węzłów systemu w usłudze Azure Kubernetes (AKS) i zarządzać nimi.
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: ef5400f19f68fd2da45776d220e17777f58e46e6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986319"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Zarządzanie pulami węzłów systemowych w usłudze Azure Kubernetes Service (AKS)

W usłudze Azure Kubernetes (AKS) węzły tej samej konfiguracji są grupowane w *pule węzłów.* Pule węzłów zawierają podstawowe maszyny wirtualne, które uruchamiają aplikacje. Pule węzłów systemowych i pul węzłów użytkownika to dwa różne tryby puli węzłów dla klastrów AKS. Pule węzłów systemowych obsługują podstawowe usługi systemowe, takie jak CoreDNS. Pule węzłów użytkownika są miejscem, w którym umieszczasz zasobników specyficznych dla aplikacji. Każdy klaster usługi AKS musi zawierać co najmniej jedną pulę węzłów systemowych z co najmniej jednym węzłem. Jeśli uruchomisz pulę pojedynczego węzła systemowego dla klastra AKS, zaleca się użycie co najmniej trzech węzłów dla puli węzłów. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.3.1 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations"></a>Ograniczenia

Następujące ograniczenia mają zastosowanie podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują pule węzłów systemowych.

* Zobacz [Przydziały, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionu w usłudze Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Klaster AKS musi być zbudowany z zestawami skalowania maszyny wirtualnej jako typ maszyny wirtualnej.
* Nazwa puli węzłów może zawierać tylko małe litery alfanumeryczne i musi zaczynać się od małych liter. W przypadku pul węzłów systemu Linux długość musi wynosić od 1 do 12 znaków. W przypadku pul węzłów systemu Windows długość musi wynosić od 1 do 6 znaków.

## <a name="system-and-user-node-pools"></a>Pule węzłów systemowych i użytkowników

Węzły puli węzłów systemowych mają etykietę **kubernetes.azure.com/mode: system**. Każdy klaster usługi AKS zawiera co najmniej jedną pulę węzłów systemowych. Pule węzłów systemowych mają następujące ograniczenia:

* Opiekuń systemowy musi być Linux.
* Typ węzła użytkownika osType może być Linux lub Windows.
* Pule systemowe muszą zawierać co najmniej jeden węzeł, a pule węzłów użytkownika mogą zawierać zero lub więcej węzłów.
* Pule węzłów systemowych wymagają jednostki SKU maszyny Wirtualnej z co najmniej 2 procesorami wirtualnymi i 4 GB pamięci.
* Pule węzłów systemowych muszą obsługiwać co najmniej 30 zasobników zgodnie z [formułą minimalnej i maksymalnej wartości dla zasobników][maximum-pods].
* Pule węzłów punktowych wymagają pul węzłów użytkownika.

Można wykonać następujące operacje z pulami węzłów:

* Zmień pulę węzłów systemowych na pulę węzłów użytkownika, pod warunkiem, że masz inną pulę węzłów systemowych, aby zająć jej miejsce w klastrze AKS.
* Zmień pulę węzłów użytkownika na pulę węzłów systemowych.
* Usuwanie pul węzłów użytkownika.
* Można usunąć pule węzłów systemowych, pod warunkiem, że masz inną pulę węzłów systemowych, aby zająć jego miejsce w klastrze AKS.
* Klaster usługi AKS może mieć wiele pul węzłów systemowych i wymaga co najmniej jednej puli węzłów systemowych.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Tworzenie nowego klastra AKS z pula węzłów systemowych

Podczas tworzenia nowego klastra AKS automatycznie tworzysz pulę węzłów systemowych z jednym węzłem. Początkowa pula węzłów domyślnie ma tryb typu systemu. Podczas tworzenia nowych pul węzłów z az aks nodepool dodać, te pule węzłów są pulami węzłów użytkownika, chyba że jawnie określić parametr mode.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w regionie *eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz klaster AKS za pomocą polecenia [az aks create][az-aks-create]. Poniższy przykład tworzy klaster o nazwie *myAKSCluster* z jedną pulą systemu zawierającą jeden węzeł. W przypadku obciążeń produkcyjnych upewnij się, że używasz pul węzłów systemowych z co najmniej trzema węzłami. Ta operacja może potrwać kilka minut.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Dodawanie puli węzłów systemowych do istniejącego klastra AKS

Do istniejących klastrów AKS można dodać co najmniej jedną pulę węzłów systemu. Następujące polecenie dodaje pulę węzłów systemu typu trybu z domyślną liczbą trzech węzłów.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Pokaż szczegóły puli węzłów

Szczegóły puli węzłów można sprawdzić za pomocą następującego polecenia.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Tryb typu **System** jest zdefiniowany dla puli węzłów systemowych, a tryb typu **Użytkownik** jest zdefiniowany dla puli węzłów użytkownika.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Aktualizowanie puli węzłów systemu i użytkownika

Można zmienić tryby zarówno dla puli węzłów systemu, jak i węzłów użytkownika. Pulę węzłów systemowych można zmienić na pulę użytkowników tylko wtedy, gdy w klastrze AKS istnieje już inna pula węzłów systemowych.

To polecenie zmienia pulę węzłów systemowych na pulę węzłów użytkownika.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

To polecenie zmienia pulę węzłów użytkownika na pulę węzłów systemowych.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Usuwanie puli węzłów systemowych

> [!Note]
> Aby użyć pul węzłów systemowych w klastrach AKS przed wersją interfejsu API 2020-03-02, dodaj nową pulę węzłów systemowych, a następnie usuń oryginalną domyślną pulę węzłów.

Wcześniej nie można było usunąć puli węzłów systemowych, która była początkową domyślną pulą węzłów w klastrze AKS. Teraz masz możliwość usuwania dowolnej puli węzłów z klastrów. Ponieważ klastry usługi AKS wymagają co najmniej jednej puli węzłów systemowych, przed usunięciem jednego z nich musi istnieć co najmniej dwie pule węzłów systemowych w klastrze AKS.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak tworzyć pule węzłów systemu w klastrze AKS i zarządzać nimi. Aby uzyskać więcej informacji na temat używania wielu pul węzłów, zobacz [używanie wielu pul węzłów][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30