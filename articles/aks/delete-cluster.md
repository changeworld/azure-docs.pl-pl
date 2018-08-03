---
title: Usuwanie klastra usługi Azure Kubernetes Service (AKS)
description: Usuń i AKS klastra przy użyciu interfejsu wiersza polecenia lub Azure portal.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8eab17a5c635560d9a5274eb038845238968e02
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439939"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Usuwanie klastra usługi Azure Kubernetes Service (AKS)

Podczas usuwania klastra usługi Azure Kubernetes Service, grupy zasobów, w której wdrożono klaster pozostanie, jednak wszystkie powiązane AKS zasoby zostaną usunięte. W tym dokumencie pokazano, jak usunąć klaster usługi AKS przy użyciu wiersza polecenia platformy Azure i witryny Azure portal.

Oprócz usunięcie klastra, można usunąć grupy zasobów, w którym została wdrożona, która spowoduje również usunięcie klastra AKS.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [Usuń az aks] [ az-aks-delete] polecenie, aby usunąć klaster usługi AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Poniższe opcje są dostępne za pomocą `az aks delete` polecenia.

| Argument | Opis | Wymagane |
|---|---|:---:|
| `--name` `-n` | Nazwa zasobu zarządzanego klastra. | tak |
| `--resource-group` `-g` | Nazwa grupy zasobów w usłudze Azure Kubernetes Service. | tak |
| `--no-wait` | Nie Czekaj na zakończenie operacji długotrwałych. | nie |
| `--yes` `-y` | Nie monituj o potwierdzenie. | nie |

## <a name="azure-portal"></a>Azure Portal

Znajduje się w witrynie Azure portal przejdź do grupy zasobów zawierającej zasób usługi Azure Kubernetes Service (AKS), wybierz zasób, a kliknij **Usuń**. Monit o potwierdzenie operacji usuwania.

![Usuwanie klastra AKS w witrynie portal](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az-aks-delete