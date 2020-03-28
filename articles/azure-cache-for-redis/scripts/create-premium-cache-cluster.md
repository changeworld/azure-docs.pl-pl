---
title: Tworzenie pamięci podręcznej platformy Azure premium dla redis z klastrowania — narzędzie interfejsu wiersza polecenia platformy Azure
description: W tym przykładzie kodu interfejsu wiersza polecenia platformy Azure pokazano, jak utworzyć 6 GB premium warstwy Azure Cache dla redis z włączone klastrowanie i dwa fragmenty.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411147"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Tworzenie pamięci podręcznej platformy Azure premium dla redis z klastrem

W tym scenariuszu dowiesz się, jak utworzyć 6 GB warstwy Premium Azure Cache dla Redis z włączone klastrowanie i dwa fragmenty.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do utworzenia grupy zasobów i warstwy Premium Azure Cache dla redis z klastrowania włączyć. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az redis tworzenie](https://docs.microsoft.com/cli/azure/redis) | Utwórz pamięć podręczną platformy Azure dla wystąpienia Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza platformy Azure dla modułów interfejsu wiersza polecenia Redis można znaleźć w [dokumentacji usługi Azure Cache for Redis.](../cli-samples.md)
