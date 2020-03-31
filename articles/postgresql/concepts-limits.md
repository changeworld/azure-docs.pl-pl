---
title: Limity — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano limity w usłudze Azure Database dla postgreSql — pojedynczy serwer, takie jak liczba opcji aparatu połączeń i magazynu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836460"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limity w usłudze Azure Database dla postgreSql — pojedynczy serwer
W poniższych sekcjach opisano pojemności i limity funkcjonalności w usłudze bazy danych. Jeśli chcesz dowiedzieć się więcej o warstwach zasobów (obliczeniowych, pamięci, magazynu), zobacz artykuł [warstw cenowych.](concepts-pricing-tiers.md)


## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Poniżej przedstawiono maksymalną liczbę połączeń na warstwę cenową i rów wirtualnych. System platformy Azure wymaga pięciu połączeń do monitorowania usługi Azure Database dla serwera PostgreSQL. 

|**Warstwa cenowa**| **vCore(s)**| **Maksymalna liczba połączeń** | **Maksymalna liczba połączeń z użytkownikiem** |
|---|---|---|---|
|Podstawowa (Basic)| 1| 55 | 50|
|Podstawowa (Basic)| 2| 105 | 100|
|Ogólnego przeznaczenia| 2| 150| 145|
|Ogólnego przeznaczenia| 4| 250| 245|
|Ogólnego przeznaczenia| 8| 480| 475|
|Ogólnego przeznaczenia| 16| 950| 945|
|Ogólnego przeznaczenia| 32| 1500| 1495|
|Ogólnego przeznaczenia| 64| 1900| 1895|
|Optymalizacja pod kątem pamięci| 2| 300| 295|
|Optymalizacja pod kątem pamięci| 4| 500| 495|
|Optymalizacja pod kątem pamięci| 8| 960| 955|
|Optymalizacja pod kątem pamięci| 16| 1900| 1895|
|Optymalizacja pod kątem pamięci| 32| 1987| 1982|

Gdy połączenia przekraczają limit, może pojawić się następujący błąd:
> FATAL: przepraszam, zbyt wielu klientów już

> [!IMPORTANT]
> Aby uzyskać najlepsze wrażenia, zaleca się użycie pooler połączenia, takich jak pgBouncer do efektywnego zarządzania połączeniami.

Połączenie PostgreSQL, nawet bezczynne, może zajmować około 10 MB pamięci. Ponadto tworzenie nowych połączeń wymaga czasu. Większość aplikacji żąda wielu krótkotrwałych połączeń, co potęguje tę sytuację. Rezultatem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pooler połączenia, który zmniejsza bezczynne połączenia i ponownie używa istniejących połączeń pomoże uniknąć tego. Aby dowiedzieć się więcej, odwiedź nasz [wpis na blogu](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne
### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z podstawowych warstw cenowych nie jest obecnie obsługiwane.
- Zmniejszanie rozmiaru magazynu serwera nie jest obecnie obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatyczna migracja między głównymi wersjami aparatu bazy danych nie jest obecnie obsługiwana. Jeśli chcesz uaktualnić do następnej wersji głównej, zrób [zrzut i przywróć](./howto-migrate-using-dump-and-restore.md) go na serwerze, który został utworzony z nową wersją aparatu.

> Należy zauważyć, że przed PostgreSQL w wersji 10, [PostgreSQL zasady przechowywania wersji](https://www.postgresql.org/support/versioning/) za _uaktualnienie wersji głównej_ do zwiększenia w pierwszym _lub_ drugim numerze (na przykład 9.5 do 9.6 został uznany za uaktualnienie wersji _głównej)._
> W wersji 10 tylko zmiana w pierwszym numerze jest uważana za uaktualnienie wersji głównej (na przykład 10.0 do 10.1 jest uaktualnieniem wersji _pomocniczej,_ a 10 do 11 jest uaktualnieniem wersji _głównej)._

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="restoring-a-server"></a>Przywracanie serwera
- Podczas korzystania z funkcji PITR nowy serwer jest tworzony z tymi samymi konfiguracjami warstwy cenowej, co serwer, na którym jest oparty.
- Nowy serwer utworzony podczas przywracania nie ma reguł zapory, które istniały na oryginalnym serwerze. Reguły zapory muszą być skonfigurowane oddzielnie dla tego nowego serwera.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="utf-8-characters-on-windows"></a>Znaki UTF-8 w systemie Windows
- W niektórych scenariuszach znaki UTF-8 nie są w pełni obsługiwane w open source PostgreSQL w systemie Windows, co wpływa na usługę Azure Database for PostgreSQL. Zobacz wątek na [Bug #15476 w postgresql-archive,](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
- Dowiedz [się, co jest dostępne w każdej warstwie cenowej](concepts-pricing-tiers.md)
- Dowiedz się więcej o [obsługiwanych wersjach bazy danych PostgreSQL](concepts-supported-versions.md)
- Sprawdź, [jak zrobić kopię zapasową i przywrócić serwer w usłudze Azure Database for PostgreSQL przy użyciu witryny Azure portal](howto-restore-server-portal.md)
