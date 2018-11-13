---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 01d6d933474d4a9c1e428b8df89fc766c9b40f20
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572488"
---
| Zasób | Limit |
| --- | --- |
| Rozmiar pamięci podręcznej |530 GB |
| Bazy danych |64 |
| Maksymalna liczba połączonych klientów |40,000 |
| Redis Cache repliki (w celu zapewnienia wysokiej dostępności) |1 |
| Fragmentów w cache w warstwie premium z klastrowaniem |10 |

Limity usługi Azure Redis Cache i rozmiary różnią się dla każdej warstwy cenowej. Aby wyświetlić warstw cenowych i ich skojarzone rozmiarów, zobacz [cennik usługi Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).

Aby uzyskać więcej informacji na temat limitów konfiguracji usługi Azure Redis Cache, zobacz [Redis domyślna konfiguracja serwera](../articles/redis-cache/cache-configure.md#default-redis-server-configuration).

Ponieważ konfiguracja i Zarządzanie wystąpieniami usługi Azure Redis Cache jest wykonywane przez firmę Microsoft, nie wszystkie polecenia usługi Redis są obsługiwane w usłudze Azure Redis Cache. Aby uzyskać więcej informacji, zobacz [polecenia nie są obsługiwane w usłudze Azure Redis Cache Redis](../articles/redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache).

