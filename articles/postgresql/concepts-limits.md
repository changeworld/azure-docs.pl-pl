---
title: Ograniczenia w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano limity w usłudze Azure Database for PostgreSQL — pojedynczego serwera, takie jak liczba połączeń i opcje aparatu magazynu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: e4752112acf136d9ffb19a0b7383bc3aff5de5e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448099"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Ograniczenia w usłudze Azure Database for PostgreSQL — pojedynczy serwer
W poniższych sekcjach opisano, pojemnością i limitami funkcjonalności w usłudze bazy danych. Jeśli chcesz dowiedzieć się więcej o warstwach zasobów (obliczeniowych, pamięci, magazynu), zobacz [warstw cenowych](concepts-pricing-tiers.md) artykułu.


## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwa cenowa i rdzeni wirtualnych są następujące: 

|**Warstwa cenowa**| **rdzenie wirtualne:**| **Maksymalna liczba połączeń** |
|---|---|---|
|Podstawowa| 1| 50 |
|Podstawowa| 2| 100 |
|Ogólne zastosowanie| 2| 150|
|Ogólne zastosowanie| 4| 250|
|Ogólne zastosowanie| 8| 480|
|Ogólne zastosowanie| 16| 950|
|Ogólne zastosowanie| 32| 1500|
|Ogólne zastosowanie| 64| 1900|
|Pamięć| 2| 300|
|Pamięć| 4| 500|
|Pamięć| 8| 960|
|Pamięć| 16| 1900|
|Pamięć| 32| 1987|

Po przekroczeniu limitu połączeń może zostać wyświetlony następujący błąd:
> Błąd krytyczny: Niestety, zbyt wielu klientów już

Azure system wymaga pięć połączeń do monitorowania usługi Azure Database for postgresql w warstwie serwera. 

## <a name="functional-limitations"></a>Ograniczenia funkcjonalności
### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z warstw cenowych podstawowa nie jest obecnie obsługiwane.
- Zmniejsza rozmiar magazynu serwera nie jest obecnie obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatycznej migracji między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwane. Jeśli chcesz uaktualnić do następnej wersji głównej, [zrzucanie i przywracanie](./howto-migrate-using-dump-and-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

> Należy pamiętać, że przed PostgreSQL w wersji 10, [zasad przechowywania wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uważane za _wersji głównej_ uaktualnienie do wzrost pierwszy _lub_ drugi numer ( przykład 9.5 lub 9.6 została uznana za _głównych_ uaktualniania wersji).
> Począwszy od wersji 10 tylko zmiana pierwsza liczba jest uważany za uaktualnienie wersji głównej (na przykład jest 10.0 i 10.1 _pomocnicza_ uaktualniania wersji i 10-11 jest _głównych_ uaktualniania wersji).

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="restoring-a-server"></a>Przywrócenie serwera
- Podczas korzystania z funkcji Odzyskiwanie, nowy serwer jest tworzony z takie same konfiguracje warstwy cenowej jako serwer, na którym opiera się na.
- Nowy serwer utworzone podczas przywracania nie ma reguł zapory, które istniały na oryginalnym serwerze. Reguły zapory muszą ustawić oddzielnie dla tego nowego serwera.
- Przywracanie usuniętych serwera nie jest obsługiwana.

### <a name="utf-8-characters-on-windows"></a>Znaki UTF-8 na Windows
- W niektórych scenariuszach znaków UTF-8 nie są obsługiwane w pełni w bazy danych PostgreSQL na Windows, który ma wpływ na — Azure Database for PostgreSQL typu open source. Zapoznaj się wątek [15476 # usterkę w postgresql w warstwie archiwum](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
- Zrozumienie [co jest dostępne w każdej warstwy cenowej](concepts-pricing-tiers.md)
- Dowiedz się więcej o [obsługiwane wersje bazy danych PostgreSQL](concepts-supported-versions.md)
- Przegląd [jak tworzenie kopii zapasowej i przywracania serwera w usłudze Azure Database for PostgreSQL za pomocą witryny Azure portal](howto-restore-server-portal.md)
