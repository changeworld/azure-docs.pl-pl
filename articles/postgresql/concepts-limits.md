---
title: Ograniczenia dotyczące bazy danych platformy Azure dla PostgreSQL
description: W tym artykule opisano ograniczeń w bazie danych Azure PostgreSQL, takie jak liczba połączeń i opcje aparatu magazynu.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 7e06cdba7c9c9f7e5c1d621e7421a18c342c0fdb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Ograniczenia dotyczące bazy danych platformy Azure dla PostgreSQL
W poniższych sekcjach opisano pojemności i limity funkcjonalności usługi bazy danych.

## <a name="pricing-tier-maximums"></a>Maksymalne wartości warstwy cenowej
Bazy danych platformy Azure dla PostgreSQL ma wiele warstw cenowych, które można wybrać podczas tworzenia serwera. Aby uzyskać więcej informacji, zobacz [warstw cenowych w bazie danych Azure dla PostgreSQL](concepts-pricing-tiers.md).  

Istnieje maksymalna liczba połączeń, jednostek obliczeniowych i magazynu w każdej warstwy cenowej w następujący sposób: 

|Warstwa cenowa| Generowanie obliczeniowe| vCore(s)| Maksymalna liczba połączeń |
|---|---|---|---|
|Podstawowa| 4. generacja| 1| 50 |
|Podstawowa| 4. generacja| 2| 100 |
|Podstawowa| 5. generacja| 1| 50 |
|Podstawowa| 5. generacja| 2| 100 |
|Ogólne zastosowanie| 4. generacja| 2| 150|
|Ogólne zastosowanie| 4. generacja| 4| 250|
|Ogólne zastosowanie| 4. generacja| 8| 480|
|Ogólne zastosowanie| 4. generacja| 16| 950|
|Ogólne zastosowanie| 4. generacja| 32| 1500|
|Ogólne zastosowanie| 5. generacja| 2| 150|
|Ogólne zastosowanie| 5. generacja| 4| 250|
|Ogólne zastosowanie| 5. generacja| 8| 480|
|Ogólne zastosowanie| 5. generacja| 16| 950|
|Ogólne zastosowanie| 5. generacja| 32| 1500|
|Pamięć| 5. generacja| 2| 150|
|Pamięć| 5. generacja| 4| 250|
|Pamięć| 5. generacja| 8| 480|
|Pamięć| 5. generacja| 16| 950|

Gdy połączenia przekracza limit, może zostać wyświetlony następujący błąd:
> Błąd krytyczny: Niestety, jeszcze zbyt wielu klientów

Azure system wymaga pięciu połączeń do monitorowania PostgreSQL serwera bazy danych Azure. 

## <a name="functional-limitations"></a>Ograniczenia funkcjonalności
### <a name="scale-operations"></a>Operacje skalowania
1.  Dynamiczne skalowanie serwerów między warstwa cenowa nie jest obecnie obsługiwane. Oznacza to przełączaniu między warstwy Basic, ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci.
2.  Zmniejszenie rozmiaru magazynu serwera nie jest obecnie obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualniania wersji
- Automatycznej migracji między wersjami aparatu bazy danych głównych nie jest obecnie obsługiwane.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamicznie przenoszenie serwerów w subskrypcji i grup zasobów nie jest obecnie obsługiwane.

### <a name="point-in-time-restore-pitr"></a>Punkt w — czas restore (PITR)
1.  Podczas korzystania z funkcji PITR tworzona jest nowy serwer jako serwer, który jest oparty na takie same konfiguracje.
2.  Przywracanie usuniętych serwera nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
- Zrozumienie [co jest dostępne w każdej warstwy cenowej](concepts-pricing-tiers.md)
- Dowiedz się więcej o [obsługiwane wersje PostgreSQL bazy danych](concepts-supported-versions.md)
- Przegląd [jak kopii zapasowej i przywracania serwera w bazie danych Azure dla PostgreSQL przy użyciu portalu Azure](howto-restore-server-portal.md)
