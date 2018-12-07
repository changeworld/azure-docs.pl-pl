---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 8a9322862d63d856f96729e5784b88e0ef098fb6
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53018496"
---
| Zasób | Limit |
| --- | --- |
| Rozmiar pamięci podręcznej |530 GB |
| Bazy danych |64 |
| Maksymalna liczba połączonych klientów |40,000 |
| Pamięć podręczna systemu Azure dla replik Redis (w celu zapewnienia wysokiej dostępności) |1 |
| Fragmentów w cache w warstwie premium z klastrowaniem |10 |

Limity usługi Azure Cache platformy Azure dla usługi Redis i rozmiary różnią się dla każdej warstwy cenowej. Aby wyświetlić warstw cenowych i ich skojarzone rozmiarów, zobacz [usługi Azure Cache Azure redis Cache cennik](https://azure.microsoft.com/pricing/details/cache/).

Więcej informacji na temat usługi Azure Cache Azure limitów konfiguracji pamięci podręcznej Redis, można zobaczyć [Redis domyślna konfiguracja serwera](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Ponieważ konfiguracja i zarządzanie Azure usługi Azure cache dla wystąpień usługi Redis odbywa się przez firmę Microsoft, nie wszystkie polecenia usługi Redis są obsługiwane w usłudze Azure Cache platformy Azure dla usługi Redis. Aby uzyskać więcej informacji, zobacz [polecenia nie są obsługiwane w usłudze Azure usługi Azure Cache, dla usługi Redis Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

