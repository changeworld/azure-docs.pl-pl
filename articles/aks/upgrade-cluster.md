---
title: Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić klaster usługi Azure Kubernetes (AKS), aby uzyskać najnowsze funkcje i aktualizacje zabezpieczeń.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 183e0a85f65d24dc7133307391931bea754a456d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632608"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)

W ramach cyklu życia klastra AKS często trzeba uaktualnić do najnowszej wersji kubernetes. Ważne jest, aby zastosować najnowsze wersje zabezpieczeń firmy Kubernetes lub uaktualnić, aby uzyskać najnowsze funkcje. W tym artykule pokazano, jak uaktualnić składniki główne lub pojedynczą, domyślną pulę węzłów w klastrze AKS.

W przypadku klastrów AKS, które używają wielu pul węzłów lub węzłów systemu Windows Server (obecnie w wersji zapoznawczej w systemie AKS), zobacz [Uaktualnianie puli węzłów w usłudze AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga, aby ślić wersję interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!WARNING]
> Uaktualnienie klastra AKS wyzwala kordon i drenaż węzłów. Jeśli masz niski przydział obliczeń dostępne, uaktualnienie może zakończyć się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [zwiększanie przydziałów.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)
> Jeśli używasz własnego wdrożenia skalowania automatycznego klastra, wyłącz go (można skalować do zera replik) podczas uaktualniania, ponieważ istnieje szansa, że będzie kolidować z procesem uaktualniania. Zarządzany skalowanie automatyczne automatycznie obsługuje to. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Sprawdzanie dostępności uaktualnień klastra AKS

Aby sprawdzić, które wersje kubernetes są dostępne dla klastra, użyj polecenia [az aks get-upgrades.][az-aks-get-upgrades] Poniższy przykład sprawdza dostępność uaktualnień do klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup:*

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Podczas uaktualniania klastra AKS nie można pominąć wersji pomocniczych usługi Kubernetes. Na przykład aktualizacje między *1.12.x* -> *1.13.x* lub *1.13.x* -> *1.14.x* są dozwolone, jednak *1.12.x* -> *1.14.x* nie jest.
>
> Aby uaktualnić, z *1.12.x* -> *1.14.x*, najpierw uaktualnić z *1.12.x* -> *1.13.x*, a następnie uaktualnić z *1.13.x* -> *1.14.x*.

Poniższe przykładowe dane wyjściowe pokazują, że klaster można uaktualnić do wersji *1.13.9* i *1.13.10:*

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Jeśli uaktualnienie nie jest dostępne, otrzymasz:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Uaktualnianie klastra AKS

Aby uaktualnić, z listą dostępnych wersji dla klastra AKS, użyj polecenia [az aks upgrade.][az-aks-upgrade] Podczas procesu uaktualniania usługa AKS dodaje nowy węzeł do klastra, który uruchamia określoną wersję usługi Kubernetes, a następnie ostrożnie [kordon i opróżnia][kubernetes-drain] jeden ze starych węzłów, aby zminimalizować zakłócenia w uruchamianiu aplikacji. Gdy nowy węzeł zostanie potwierdzony jako uruchomione zasobników aplikacji, stary węzeł jest usuwany. Ten proces powtarza się, dopóki wszystkie węzły w klastrze nie zostały uaktualnione.

Poniższy przykład uaktualnia klaster do wersji *1.13.10:*

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Uaktualnienie klastra zajmuje kilka minut, w zależności od liczby posiadanych węzłów. 

> [!NOTE]
> Istnieje całkowity dozwolony czas na ukończenie uaktualnienia klastra. Czas ten jest obliczany `10 minutes * total number of nodes in the cluster`przez biorąc produkt . Na przykład w klastrze 20 węzłów operacje uaktualniania muszą zakończyć się pomyślnie w ciągu 200 minut lub usługa AKS zakończy się niepowodzeniem operacji, aby uniknąć nieodwracalnego stanu klastra. Aby odzyskać po awarii uaktualnienia, ponów próbę uaktualnienia operacji po upływie limitu czasu.

Aby potwierdzić, że uaktualnienie zakończyło się pomyślnie, użyj polecenia [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Poniższe przykładowe dane wyjściowe pokazują, że klaster działa teraz *1.13.10:*

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak uaktualnić istniejący klaster AKS. Aby dowiedzieć się więcej na temat wdrażania klastrów AKS i zarządzania nimi, zobacz zestaw samouczków.

> [!div class="nextstepaction"]
> [Samouczki AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
