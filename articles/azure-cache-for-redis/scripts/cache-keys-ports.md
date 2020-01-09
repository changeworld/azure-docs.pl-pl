---
title: Pobieranie nazwy hosta, portów, kluczy — Azure cache for Redis — interfejs wiersza polecenia platformy Azure
description: Ten przykładowy kod interfejsu wiersza polecenia platformy Azure pokazuje, jak pobrać nazwę hosta, porty i klucze dla wystąpienia usługi Azure cache for Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411303"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Pobierz nazwę hosta, portów i kluczy dla usługi Azure Cache dla usługi Redis

W tym scenariuszu dowiesz się, jak pobrać nazwy hosta, porty i klucze używane do łączenia z usługi Azure Cache dla wystąpienia usługi Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń, można pobrać nazwy hosta, klucze i portów usługi Azure Cache dla wystąpienia usługi Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Pobierz szczegóły usługi Azure Cache dla wystąpienia usługi Redis. |
| [AZ redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Pobierz klucze dostępu dla usługi Azure Cache dla wystąpienia usługi Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe usługi Azure Cache dla przykładowych skryptów interfejsu wiersza polecenia platformy Redis można znaleźć w [usługi Azure Cache dokumentacja pamięci podręcznej Redis](../cli-samples.md).
