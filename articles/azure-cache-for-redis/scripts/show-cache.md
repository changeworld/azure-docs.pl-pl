---
title: Uzyskaj szczegółowe informacje o pamięci podręcznej platformy Azure dla redis — narzędzie cli platformy Azure
description: W tym przykładzie kodu interfejsu wiersza polecenia platformy Azure pokazano, jak pobrać szczegóły usługi Azure Cache dla wystąpienia Redis, w tym jego stan inicjowania obsługi administracyjnej.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 54920017f4a0598a74a54114e3d1e0a5392d7d2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411055"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Uzyskaj szczegółowe informacje o pamięci podręcznej platformy Azure dla redis

W tym scenariuszu dowiesz się, jak pobrać szczegóły usługi Azure Cache dla wystąpienia Redis, w tym jego stan inicjowania obsługi administracyjnej.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń, aby pobrać szczegóły usługi Azure Cache dla wystąpienia Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az redis pokaż](https://docs.microsoft.com/cli/azure/redis) | Pobierz szczegóły usługi Azure Cache dla wystąpienia Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza platformy Azure dla modułów interfejsu wiersza polecenia Redis można znaleźć w [dokumentacji usługi Azure Cache for Redis.](../cli-samples.md)
