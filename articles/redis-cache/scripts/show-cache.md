---
title: Azure CLI przykładowym skrypcie — pobranie szczegółów pamięci podręcznej Redis Azure | Dokumentacja firmy Microsoft
description: Azure CLI przykładowym skrypcie — pobranie szczegółów pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 00ae8e00d456d4660cadf645d4521173d254bbd7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846251"
---
# <a name="get-details-of-an-azure-redis-cache"></a>Uzyskiwanie szczegółowych informacji o pamięci podręcznej Redis Azure

W tym scenariuszu należy dowiedzieć się, jak można pobrać szczegółów wystąpienia pamięci podręcznej Redis Azure, takich jak jej stan inicjowania obsługi administracyjnej.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt można pobrać szczegółów wystąpienia pamięci podręcznej Redis Azure korzysta z następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Pokaż redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Pobieranie szczegółów wystąpienia pamięci podręcznej Redis Azure. |


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów w pamięci podręcznej Redis Azure CLI znajdują się w [dokumentacji pamięć podręczna Redis Azure](../cli-samples.md).