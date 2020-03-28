---
title: Pobierz nazwy hosta, porty, klucze — Usługa Azure Cache for Redis — Azure CLI
description: W tym przykładzie kodu interfejsu wiersza polecenia platformy Azure pokazano, jak uzyskać nazwy hosta, porty i klucze dla pamięci podręcznej platformy Azure dla wystąpienia Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411303"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Pobierz nazwy hosta, porty i klucze dla usługi Azure Cache for Redis

W tym scenariuszu dowiesz się, jak pobrać nazwa hosta, porty i klucze używane do łączenia się z pamięcią podręczną platformy Azure dla wystąpienia Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do pobierania nazwy hosta, kluczy i portów pamięci podręcznej platformy Azure dla wystąpienia Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az redis pokaż](https://docs.microsoft.com/cli/azure/redis) | Pobierz szczegóły usługi Azure Cache dla wystąpienia Redis. |
| [az redis listy kluczy](https://docs.microsoft.com/cli/azure/redis) | Pobieranie kluczy dostępu dla pamięci podręcznej platformy Azure dla wystąpienia Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza platformy Azure dla modułów interfejsu wiersza polecenia Redis można znaleźć w [dokumentacji usługi Azure Cache for Redis.](../cli-samples.md)
