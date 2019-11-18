---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — pobieranie nazwy hosta, portów i kluczy dla usługi Azure cache for Redis
description: Skrypt interfejsu wiersza polecenia platformy Azure przykładowy — Pobierz nazwę hosta, portów i kluczy dla usługi Azure Cache dla wystąpienia usługi Redis
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 604ec6de3b95a4bc289176d54d9c7b0a6c42eae6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122520"
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
