---
title: Usuń klaster usługi Kubernetes Azure (AKS)
description: Usuń i AKS klastra przy użyciu interfejsu wiersza polecenia lub Azure portalu.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 66dcebb702695a6601f6ed17b85a04d5bb4e01f6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100047"
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