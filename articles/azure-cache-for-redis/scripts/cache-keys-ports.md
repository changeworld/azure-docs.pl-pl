---
title: Skrypt interfejsu wiersza polecenia platformy Azure przykładowy — Pobierz nazwę hosta, portów i kluczy dla usługi Azure Cache dla pamięci podręcznej Redis | Dokumentacja firmy Microsoft
description: Skrypt interfejsu wiersza polecenia platformy Azure przykładowy — Pobierz nazwę hosta, portów i kluczy dla usługi Azure Cache dla wystąpienia usługi Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fd69c3182f026996618fea5e26466e8434ea36f6
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754459"
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


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe usługi Azure Cache dla przykładowych skryptów interfejsu wiersza polecenia platformy Redis można znaleźć w [usługi Azure Cache dokumentacja pamięci podręcznej Redis](../cli-samples.md).
