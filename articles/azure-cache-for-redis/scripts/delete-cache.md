---
title: Przykładowy skrypt wiersza polecenia platformy Azure — usuwanie dla pamięci podręcznej Azure redis Cache | Dokumentacja firmy Microsoft
description: Przykładowy skrypt wiersza polecenia platformy Azure — usuwania pamięci podręcznej Azure redis Cache
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 4a7b239010d57e3f352ee29afe267273391ffbcd
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461828"
---
# <a name="delete-an-azure-cache-for-redis"></a>Usunięcie pamięci podręcznej Azure redis Cache

W tym scenariuszu dowiesz się, jak usunąć pamięć podręczna systemu Azure dla usługi Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń, można usunąć usługi Azure Cache dla wystąpienia usługi Redis. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ redis delete](https://docs.microsoft.com/cli/azure/redis) | Usuń pamięć podręczna systemu Azure dla wystąpienia usługi Redis. |


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe usługi Azure Cache dla przykładowych skryptów interfejsu wiersza polecenia platformy Redis można znaleźć w [usługi Azure Cache dokumentacja pamięci podręcznej Redis](../cli-samples.md).
