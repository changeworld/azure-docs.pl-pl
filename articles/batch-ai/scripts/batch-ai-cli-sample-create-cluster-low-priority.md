---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie klastra usługi Batch AI o niskim priorytecie | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie klastra usługi Batch AI składającego się z dedykowanych węzłów (maszyn wirtualnych) o niskim priorytecie
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ms.openlocfilehash: 1fb21ab754e85dd347ff3bd66bafc2fadd95f8b1
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44058158"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-low-priority-nodes"></a>Przykład użycia interfejsu wiersza polecenia: tworzenie klastra usługi Batch AI składającego się z węzłów o niskim priorytecie i zarządzanie nim

Ten skrypt demonstruje niektóre polecenia dostępne w interfejsie wiersza polecenia platformy Azure służące do tworzenia klastra usługi Batch AI składającego się z węzłów (maszyn wirtualnych) o niskim priorytecie i zarządzania nim.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagać interfejsu wiersza polecenia platformy Azure w wersji 2.0.38 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-low-priority.sh "Create Batch AI cluster - low-priority nodes")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenia, aby usunąć grupy zasobów i wszystkie skojarzone z nimi zasoby.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Tworzy obszar roboczy usługi Batch AI. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Tworzy klaster usługi Batch AI. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Wyświetla informacje o klastrze usługi Batch AI. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node#az-batchai-cluster-show) | Wyświetla listę węzłów w klastrze usługi Batch AI. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Zmienia rozmiar klastra usługi Batch AI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
