---
title: Ograniczenia dotyczące usługi Azure Database for PostgreSQL
description: W tym artykule opisano ograniczenia dotyczące usługi Azure Database for PostgreSQL, takie jak liczba połączeń i opcje aparatu magazynu.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/30/2018
ms.openlocfilehash: f24f15134bf189097f20f75ff0b23b72a3e48363
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299610"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Ograniczenia dotyczące usługi Azure Database for PostgreSQL
W poniższych sekcjach opisano, pojemnością i limitami funkcjonalności w usłudze bazy danych.

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
|Pamięć| 2| 300|
|Pamięć| 4| 500|
|Pamięć| 8| 960|
|Pamięć| 16| 1900|

Po przekroczeniu limitu połączeń może zostać wyświetlony następujący błąd:
> Błąd krytyczny: Niestety, zbyt wielu klientów już

Azure system wymaga pięć połączeń do monitorowania usługi Azure Database for postgresql w warstwie serwera. 

## <a name="functional-limitations"></a>Ograniczenia funkcjonalności
### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z warstw cenowych podstawowa nie jest obecnie obsługiwane.
- Zmniejsza rozmiar magazynu serwera nie jest obecnie obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatycznej migracji między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwane. Jeśli chcesz uaktualnić do następnej wersji głównej, [zrzucanie i przywracanie](./howto-migrate-using-dump-and-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="restoring-a-server"></a>Przywrócenie serwera
- Podczas korzystania z funkcji Odzyskiwanie, nowy serwer jest tworzony z takie same konfiguracje warstwy cenowej jako serwer, na którym opiera się na.
- Nowy serwer utworzone podczas przywracania nie ma reguł zapory, które istniały na oryginalnym serwerze. Reguły zapory muszą ustawić oddzielnie dla tego nowego serwera.
- Przywracanie usuniętych serwera nie jest obsługiwana.

## <a name="next-steps"></a>Kolejne kroki
- Zrozumienie [co jest dostępne w każdej warstwy cenowej](concepts-pricing-tiers.md)
- Dowiedz się więcej o [obsługiwane wersje bazy danych PostgreSQL](concepts-supported-versions.md)
- Przegląd [jak tworzenie kopii zapasowej i przywracania serwera w usłudze Azure Database for PostgreSQL za pomocą witryny Azure portal](howto-restore-server-portal.md)
