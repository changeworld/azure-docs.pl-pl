---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie klastra usługi Batch AI przy użyciu pliku konfiguracji | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie klastra usługi Batch AI przez określanie ustawień konfiguracji w pliku JSON.
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
ms.date: 08/16/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 41a3a801214ff00c01397034e26fde6946ab97f0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407817"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>Przykład użycia interfejsu wiersza polecenia: tworzenie klastra usługi Batch AI przy użyciu pliku konfiguracji klastra

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Ten skrypt przedstawia sposób używania pliku konfiguracji JSON do określania ustawień dla klastra usługi Batch AI. Tych ustawień należy użyć zamiast odpowiednich parametrów wiersza polecenia dla polecenia `az batchai cluster create`. Plik konfiguracji jest przydatny, gdy trzeba zainstalować wiele systemów plików w węzłach klastra lub gdy chcesz używać identycznej konfiguracji w wielu klastrach.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.38 lub nowszej. Uruchom polecenie `az --version`, aby określić wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenia, aby usunąć grupy zasobów i wszystkie skojarzone z nimi zasoby.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Tworzy udział plików w ramach konta magazynu. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Tworzy obszar roboczy usługi Batch AI. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Tworzy klaster usługi Batch AI. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Wyświetla informacje o klastrze usługi Batch AI. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
