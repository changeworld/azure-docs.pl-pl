---
title: Tworzenie pamięci podręcznej platformy Azure dla redis — interfejsu wiersza polecenia platformy Azure
description: W tym przykładzie kodu interfejsu wiersza polecenia platformy Azure pokazano, jak utworzyć pamięć podręczną Azure dla wystąpienia Redis przy użyciu polecenia az redis create.
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 79b749c0d02a21c1225ee0d046d73ed3fdd98904
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411313"
---
# <a name="create-an-azure-cache-for-redis"></a>Tworzenie usługi Azure Cache for Redis

W tym scenariuszu dowiesz się, jak utworzyć pamięć podręczną Azure dla redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do utworzenia grupy zasobów i pamięci podręcznej Azure dla redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az redis tworzenie](https://docs.microsoft.com/cli/azure/redis) | Utwórz pamięć podręczną platformy Azure dla wystąpienia Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza platformy Azure dla modułów interfejsu wiersza polecenia Redis można znaleźć w [dokumentacji usługi Azure Cache for Redis.](../cli-samples.md)
