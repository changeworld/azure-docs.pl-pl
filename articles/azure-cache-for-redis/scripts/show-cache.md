---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Uzyskaj szczegóły pamięci podręcznej Azure redis Cache
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Uzyskaj szczegóły pamięci podręcznej Azure redis Cache
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: f3e6c6dab95722eebdc4a175379444ef5840cad1
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122482"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Uzyskaj szczegółowe informacje o pamięci podręcznej Azure dla usługi Redis

W tym scenariuszu dowiesz się, jak pobrać szczegółowe informacje o pamięci podręcznej Azure wystąpienia pamięci podręcznej Redis, w tym jego stan inicjowania obsługi administracyjnej.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń, można pobrać szczegółów dla wystąpienia pamięci podręcznej Redis Azure Cache. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Pobierz szczegóły usługi Azure Cache dla wystąpienia usługi Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe usługi Azure Cache dla przykładowych skryptów interfejsu wiersza polecenia platformy Redis można znaleźć w [usługi Azure Cache dokumentacja pamięci podręcznej Redis](../cli-samples.md).
