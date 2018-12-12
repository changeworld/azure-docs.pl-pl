---
title: Interfejs wiersza polecenia Azure przykładowy skrypt — Tworzenie pamięci podręcznej Azure dla usługi Redis | Dokumentacja firmy Microsoft
description: Interfejs wiersza polecenia Azure przykładowy skrypt — Tworzenie pamięci podręcznej Azure dla usługi Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: afd7f6e0-9297-4c98-a95e-597be939cef7
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 4907bbc9ed7a32e74175a4c6a178ac41bc165961
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085216"
---
# <a name="create-an-azure-cache-for-redis"></a>Tworzenie pamięci podręcznej Azure dla usługi Redis

W tym scenariuszu dowiesz się, jak utworzyć pamięć podręczna systemu Azure dla usługi Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń, aby utworzyć grupę zasobów i pamięć podręczna systemu Azure dla usługi Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Utwórz az redis](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Tworzenie usługi Azure Cache dla wystąpienia usługi Redis. |


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe usługi Azure Cache dla przykładowych skryptów interfejsu wiersza polecenia platformy Redis można znaleźć w [usługi Azure Cache dokumentacja pamięci podręcznej Redis](../cli-samples.md).