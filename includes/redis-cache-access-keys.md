---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73720306"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Pobieranie nazwy hosta, portów i kluczy dostępu z witryny Azure portal

Aby połączyć się z pamięcią podręczną platformy Azure dla wystąpienia Redis, klienci pamięci podręcznej potrzebują nazwy hosta, portów i klucza pamięci podręcznej. Niektórzy klienci mogą odwoływać się do tych elementów przy użyciu nieco innych nazw. Nazwę hosta, porty i klucze można uzyskać z [witryny Azure portal](https://portal.azure.com).

- Aby uzyskać klucze dostępu, z lewej strony pamięci podręcznej wybierz pozycję **Klawisze dostępu**. 
  
  ![Klucze usługi Azure Cache for Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Aby uzyskać nazwę hosta i porty, z lewej strony pamięci podręcznej nawigacji wybierz **polecenie Właściwości**. Nazwa hosta jest * \<nazwą DNS formularza>.redis.cache.windows.net*.

  ![Właściwości usługi Azure Cache for Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

