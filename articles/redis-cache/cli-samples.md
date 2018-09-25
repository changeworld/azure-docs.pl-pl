---
title: Przykłady pamięci podręcznej Redis interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Redis Cache.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 01773e1ec24e6ab7d1899df6774230b7ce5b5676
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957901"
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Przykłady wiersza polecenia platformy Azure dla usługi Azure Redis Cache

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| | |
|---|---|
|**Tworzenie pamięci podręcznej**||
| [Tworzenie pamięci podręcznej](./scripts/create-cache.md) | Tworzy grupę zasobów i warstwa podstawowa usługi Azure Redis Cache. |
| [Tworzenie pamięci podręcznej — wersja premium z klastrowaniem](./scripts/create-premium-cache-cluster.md) | Tworzy grupę zasobów i pamięć podręczna warstwy premium przy włączonym klastrowaniu.|
| [Uzyskiwanie szczegółowych informacji z pamięci podręcznej](./scripts/show-cache.md) | Pobiera Szczegóły wystąpienia usługi Azure Redis Cache, w tym stan aprowizacji. |
| [Pobierz nazwę hosta, portów i kluczy](./scripts/cache-keys-ports.md) | Pobiera nazwę hosta, porty i klucze dla wystąpienia usługi Azure Redis Cache. |
|**Aplikacja sieci Web oraz pamięć podręczną**||
| [Łączenie aplikacji internetowej z pamięcią podręczną redis](./../app-service/scripts/app-service-cli-app-service-redis.md) | Tworzy aplikację internetową platformy Azure i usługi redis cache, a następnie dodaje szczegóły połączenia redis do ustawień aplikacji. |
|**Usuń pamięć podręczną**||
| [Usuń pamięć podręczną](./scripts/delete-cache.md) | Usuwa wystąpienie usługi Azure Redis Cache  |
| | |

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
