---
title: Usuń klaster usługi Kubernetes Azure (AKS)
description: Usuń i AKS klastra przy użyciu interfejsu wiersza polecenia lub Azure portalu.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6391e36eff60634e07a90c1e6b5f0f44ee60d46b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Usuń klaster usługi Kubernetes Azure (AKS)

Podczas usuwania Kubernetes klastra usługi platformy Azure, grupy zasobów, w której wdrożono klaster pozostanie, jednak wszystkie powiązane AKS zasobów zostaną usunięte. Ten dokument pokazuje, jak usunąć klaster AKS przy użyciu wiersza polecenia platformy Azure i portalu Azure.

Oprócz usunięcie klastra, można usunąć grupy zasobów, w którym została wdrożona, który spowoduje również usunięcie klastra AKS.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [usunąć az aks] [ az-aks-delete] polecenie, aby usunąć klaster AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Poniższe opcje są dostępne z `az aks delete` polecenia.

| Argument | Opis | Wymagane |
|---|---|:---:|
| `--name` `-n` | Nazwa zasobu zarządzanego klastra. | tak |
| `--resource-group` `-g` | Nazwa grupy zasobów Azure Kubernetes usługi. | tak |
| `--no-wait` | Nie należy czekać na zakończenie operacji długotrwałych. | nie |
| `--yes` `-y` | Nie monituj o potwierdzenie. | nie |

## <a name="azure-portal"></a>Azure Portal

Znajduje się w portalu Azure, przejdź do grupy zasobów zawiera zasób usługi Kubernetes Azure (AKS), wybierz zasób i kliknij przycisk **usunąć**. Monit o potwierdzenie operacji usuwania.

![Usuń AKS klastra portalu](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete