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
ms.date: 06/04/2018
ms.openlocfilehash: 5cd829236d8d8a58e68f7bf766790aa3f0cb656e
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757420"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Ograniczenia dotyczące bazy danych platformy Azure dla PostgreSQL
W poniższych sekcjach opisano pojemności i limity funkcjonalności usługi bazy danych.

## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwa cenowa i vCores są następujące: 

|**Warstwa cenowa**| **vCore(s)**| **Maksymalna liczba połączeń** |
|---|---|---|
|Podstawowa| 1| 50 |
|Podstawowa| 2| 100 |
|Ogólne zastosowanie| 2| 150|
|Ogólne zastosowanie| 4| 250|
|Ogólne zastosowanie| 8| 480|
|Ogólne zastosowanie| 16| 950|
|Ogólne zastosowanie| 32| 1500|
|Pamięć| 2| 150|
|Pamięć| 4| 250|
|Pamięć| 8| 480|
|Pamięć| 16| 950|

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
