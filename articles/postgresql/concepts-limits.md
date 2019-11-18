---
title: Limity w Azure Database for PostgreSQL-pojedynczym serwerze
description: W tym artykule opisano limity w Azure Database for PostgreSQL-pojedynczym serwerze, takie jak liczba opcji połączenia i aparatu magazynu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b9cef4753b6fd324b38d7254139fe288463a0c0c
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123895"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limity w Azure Database for PostgreSQL-pojedynczym serwerze
W poniższych sekcjach opisano pojemności i limity funkcjonalne usługi bazy danych. Jeśli chcesz dowiedzieć się więcej o warstwach zasobów (obliczeniowych, pamięci, magazynu), zobacz artykuł [warstwy cenowe](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwa cenowa i rdzeni wirtualnych są następujące: 

|**Warstwa cenowa**| **rdzenie wirtualne:**| **Maksymalna liczba połączeń** | **Maksymalna liczba połączeń użytkowników** |
|---|---|---|---|
|Podstawowa| 1| 55 | 50|
|Podstawowa| 2| 105 | 100|
|Ogólnego przeznaczenia| 2| 150| 145|
|Ogólnego przeznaczenia| 4| 250| 245|
|Ogólnego przeznaczenia| 8| 480| 475|
|Ogólnego przeznaczenia| 16| 950| 945|
|Ogólnego przeznaczenia| 32| 1500| 1495|
|Ogólnego przeznaczenia| 64| 1900| 1895|
|Pamięć| 2| 300| 295|
|Pamięć| 4| 500| 495|
|Pamięć| 8| 960| 955|
|Pamięć| 16| 1900| 1895|
|Pamięć| 32| 1987| 1982|

Po przekroczeniu limitu połączeń może zostać wyświetlony następujący błąd:
> Błąd krytyczny: Niestety, zbyt wielu klientów

System Azure wymaga pięciu połączeń do monitorowania serwera Azure Database for PostgreSQL. 

## <a name="functional-limitations"></a>Ograniczenia funkcjonalności
### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z warstw cenowych podstawowa nie jest obecnie obsługiwane.
- Zmniejszenie rozmiaru magazynu serwera nie jest obecnie obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatycznej migracji między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwane. Jeśli chcesz uaktualnić do następnej wersji głównej, [zrzucanie i przywracanie](./howto-migrate-using-dump-and-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

> Należy pamiętać, że przed PostgreSQL wersja 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej (na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ ).
> Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej (na przykład 10,0 do 10,1 jest uaktualnieniem wersji _pomocniczej_ , a 10 do 11 jest uaktualnieniem _głównym_ wersji).

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="restoring-a-server"></a>Przywracanie serwera
- W przypadku korzystania z funkcji kopie nowy serwer jest tworzony z tymi samymi konfiguracjami warstwy cenowej co serwer, na którym jest oparta.
- Nowy serwer utworzony podczas przywracania nie ma reguł zapory istniejących na oryginalnym serwerze. Reguły zapory należy skonfigurować oddzielnie dla tego nowego serwera.
- Przywracanie usuniętych serwera nie jest obsługiwana.

### <a name="utf-8-characters-on-windows"></a>Znaki UTF-8 w systemie Windows
- W niektórych scenariuszach znaki UTF-8 nie są w pełni obsługiwane w programie Open Source PostgreSQL w systemie Windows, co ma wpływ na Azure Database for PostgreSQL. Aby uzyskać więcej informacji, zobacz wątek w [#15476 błędów w archiwum PostgreSQL](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) .

## <a name="next-steps"></a>Następne kroki
- Dowiedz [się, co jest dostępne w poszczególnych warstwach cenowych](concepts-pricing-tiers.md)
- Informacje o [obsługiwanych wersjach bazy danych PostgreSQL](concepts-supported-versions.md)
- Zapoznaj [się z tematem jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for PostgreSQL przy użyciu Azure Portal](howto-restore-server-portal.md)
