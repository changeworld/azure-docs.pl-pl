---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — uruchamianie zadania usługi Batch | Microsoft Docs
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — uruchamianie zadania za pomocą usługi Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: d63854b213b33a3c26358df17761999f5270d7a2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38455139"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Przykład interfejsu wiersza polecenia: uruchamianie zadania i zadań podrzędnych za pomocą usługi Azure Batch

Ten skrypt tworzy zadanie usługi Batch i dodaje serię zadań podrzędnych do tego zadania. Demonstruje również sposób monitorowania zadania i jego zadań podrzędnych. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Tworzy konto usługi Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Tworzy pulę węzłów obliczeniowych.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Tworzy zadanie usługi Batch.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | Dodaje zadanie podrzędne do określonego zadania usługi Batch.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | Aktualizuje właściwości zadania usługi Batch.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | Pobiera szczegóły określonego zadania usługi Batch.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | Pobiera szczegóły zadania podrzędnego z określonego zadania usługi Batch.  |
| [az group delete](/cli/azure/group#az_group_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).