---
title: Azure CLI skrypt przykładowy — tworzenie podręczna Redis Azure Premium z usługą klastrowania | Dokumentacja firmy Microsoft
description: Azure CLI skrypt przykładowy — Tworzenie warstwy Premium usługi pamięć podręczna Redis Azure z usługą klastrowania
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fb97d19d50ac9845c2495bd167a9cd30805d464b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846302"
---
# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Utwórz podręczna Redis Azure Premium z klastra

W tym scenariuszu możesz dowiedzieć się, jak utworzyć warstwy Premium 6 GB pamięci podręcznej Redis Azure z włączoną funkcją klastrowania i dwa niezależne.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do utworzenia grupy zasobów i pamięć podręczna redis warstwy Premium z usługą klastrowania Włącz. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Tworzenie pamięci podręcznej redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Tworzenie wystąpienia pamięci podręcznej Redis. |


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów w pamięci podręcznej Redis Azure CLI znajdują się w [dokumentacji pamięć podręczna Redis Azure](../cli-samples.md).