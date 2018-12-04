---
title: Ograniczenia dotyczące usługi Azure Database dla serwera MariaDB
description: W tym artykule opisano ograniczenia dotyczące usługi Azure Database dla serwera MariaDB, takie jak liczba połączeń i opcje aparatu magazynu.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 4fb774341b19034d3905341a99be2114a0d1e18b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835713"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia dotyczące usługi Azure Database dla serwera MariaDB
Poniżej opisano pojemności, obsługa aparatu magazynu, uprawnień obsługę, dane manipulowania instrukcji oraz ograniczenia funkcjonalności w usłudze bazy danych.

## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwa cenowa i rdzeni wirtualnych są następujące:

|**Warstwa cenowa**|**rdzenie wirtualne:**| **Maksymalna liczba połączeń**|
|---|---|---|
|Podstawowa| 1| 50|
|Podstawowa| 2| 100|
|Ogólne zastosowanie| 2| 300|
|Ogólne zastosowanie| 4| 625|
|Ogólne zastosowanie| 8| 1250|
|Ogólne zastosowanie| 16| 2500|
|Ogólne zastosowanie| 32| 5000|
|Pamięć| 2| 600|
|Pamięć| 4| 1250|
|Pamięć| 8| 2500|
|Pamięć| 16| 5000|

Po przekroczeniu limitu połączeń może zostać wyświetlony następujący błąd:
> Błąd 1040 (08004): Zbyt wiele połączeń

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [Aparatu InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [NALEŻĄCY](https://mariadb.com/kb/en/library/blackhole/l)
- [ARCHIWUM](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola Administrator: wiele parametrów serwera i ustawienia mogą przypadkowo obniżają wydajność serwerów lub odwrócić właściwości ACID systemu DBMS. Jako takie utrzymanie integralności usługi i umowa SLA na poziomie produktu, ta usługa nie ujawnia roli Administrator. Domyślne konto użytkownika, który jest tworzony, gdy tworzone jest nowe wystąpienie bazy danych, umożliwia użytkownikowi wykonać większość instrukcji DDL i DML w wystąpieniu zarządzanym bazy danych.
- Uprawnienia administratora: Podobnie [uprawnień administratora](https://mariadb.com/kb/en/library/grant/#global-privileges) również jest ograniczony.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulacji danych

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE` jest obsługiwany, ale `[LOCAL]` parametru musi być określona i Przekierowanie do ścieżki UNC (magazynu platformy Azure zainstalowany za pomocą protokołu SMB).

### <a name="unsupported"></a>Nieobsługiwane
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Ograniczenia funkcjonalności

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z warstw cenowych podstawowa nie jest obecnie obsługiwane.
- Zmniejsza rozmiar magazynu serwera nie jest obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatycznej migracji między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwane.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- Podczas korzystania z funkcji Odzyskiwanie, nowy serwer jest tworzony przy użyciu tej samej konfiguracji, co serwer, który opiera się na.
- Przywracanie usuniętych serwera nie jest obsługiwana.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamicznie przenoszenie serwerów wstępnie utworzonych w subskrypcji i grupy zasobów nie jest obecnie obsługiwane.

## <a name="current-known-issues"></a>Obecnie znane problemy
- Wystąpienie serwera MariaDB Wyświetla wersję serwerem nieprawidłowego po nawiązaniu połączenia. Aby uzyskać z właściwym serwerem w wystąpieniu aparatu wersji, użyj `select version();` polecenia.

## <a name="next-steps"></a>Kolejne kroki
- [Co to jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MariaDB](concepts-supported-versions.md)
