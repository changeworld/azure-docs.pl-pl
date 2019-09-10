---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67183635"
---
| Zasób | Limit |
| --- | --- |
| Rozmiar pamięci podręcznej |530 GB |
| Bazy danych |64 |
| Maksymalna liczba podłączonych klientów |40,000 |
| Pamięć podręczna systemu Azure dla replik Redis w celu zapewnienia wysokiej dostępności |1 |
| Fragmentów w cache w warstwie premium z klastrowaniem |10 |

Ogranicza pamięć podręczna systemu Azure dla usługi Redis i rozmiary różnią się dla każdej warstwy cenowej. Aby wyświetlić warstwy cenowe i ich skojarzone rozmiary, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/).

Aby uzyskać więcej informacji na temat usługi Azure Cache limitów konfiguracji pamięci podręcznej Redis, zobacz [Redis domyślna konfiguracja serwera](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Ponieważ konfiguracji i zarządzania usługi Azure cache dla wystąpień usługi Redis odbywa się przez firmę Microsoft, nie wszystkie polecenia usługi Redis są obsługiwane w pamięci podręcznej Azure dla usługi Redis. Aby uzyskać więcej informacji, zobacz [polecenia nie są obsługiwane w usłudze Azure Cache, dla usługi Redis Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

