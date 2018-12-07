---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Uzyskaj szczegóły pamięci podręcznej Azure redis Cache | Dokumentacja firmy Microsoft
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Uzyskaj szczegóły pamięci podręcznej Azure redis Cache
services: azure-cache-for-redis
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
ms.openlocfilehash: 94786a17589cdae93ffa498eaaeeb1c1a81ffaf4
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019731"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Uzyskaj szczegółowe informacje o pamięci podręcznej Azure dla usługi Redis

W tym scenariuszu dowiesz się, jak pobrać szczegółowe informacje o pamięci podręcznej Azure wystąpienia pamięci podręcznej Redis, w tym jego stan inicjowania obsługi administracyjnej.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Azure Cache for Redis")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń, można pobrać szczegółów dla wystąpienia pamięci podręcznej Redis Azure Cache. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Pobierz szczegóły usługi Azure Cache dla wystąpienia usługi Redis. |


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe usługi Azure Cache dla przykładowych skryptów interfejsu wiersza polecenia platformy Redis można znaleźć w [usługi Azure Cache dokumentacja pamięci podręcznej Redis](../cli-samples.md).