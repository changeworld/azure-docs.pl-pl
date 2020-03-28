---
title: Usuwanie pamięci podręcznej platformy Azure dla funkcji Redis — narzędzie cli platformy Azure
description: Ten przykład kodu interfejsu wiersza polecenia platformy Azure pokazuje, jak usunąć pamięć podręczną Azure dla wystąpienia Redis przy użyciu polecenia az redis delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: e5178ab4069cc5ffa8607c5feea6ffac86284a5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411079"
---
# <a name="delete-an-azure-cache-for-redis"></a>Usuwanie pamięci podręcznej platformy Azure dla redis

W tym scenariuszu dowiesz się, jak usunąć pamięć podręczną Azure dla redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń, aby usunąć pamięć podręczną Azure dla wystąpienia Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az redis usunąć](https://docs.microsoft.com/cli/azure/redis) | Usuń pamięć podręczną platformy Azure dla wystąpienia Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza platformy Azure dla modułów interfejsu wiersza polecenia Redis można znaleźć w [dokumentacji usługi Azure Cache for Redis.](../cli-samples.md)
