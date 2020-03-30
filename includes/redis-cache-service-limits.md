---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71839218"
---
| Zasób | Limit |
| --- | --- |
| Rozmiar pamięci podręcznej |1,2 TB |
| Bazy danych |64 |
| Maksymalna liczba podłączonych klientów |40 000 |
| Usługa Azure Cache dla replik Redis, aby uzyskać wysoką dostępność |1 |
| Fragmenty w pamięci podręcznej premium z klastrem |10 |

Usługa Azure Cache dla limitów i rozmiarów Redis są różne dla każdej warstwy cenowej. Aby wyświetlić warstwy cenowe i skojarzone z nimi rozmiary, zobacz [Usługa Azure Cache dla cen Redis](https://azure.microsoft.com/pricing/details/cache/).

Aby uzyskać więcej informacji na temat limitów konfiguracji usługi Azure Cache for Redis, zobacz [Domyślna konfiguracja serwera Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Ponieważ konfiguracja i zarządzanie pamięcią podręczną Azure dla wystąpień Redis jest wykonywane przez firmę Microsoft, nie wszystkie polecenia Redis są obsługiwane w pamięci podręcznej Azure cache dla programu Redis. Aby uzyskać więcej informacji, zobacz [Polecenia Redis nie są obsługiwane w pamięci podręcznej Azure cache dla redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

