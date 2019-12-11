---
title: Ograniczenia — Azure Database for MariaDB
description: W tym artykule opisano ograniczenia w Azure Database for MariaDB, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 1f5824f349650e340e395221785266096da16d6f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969551"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia w Azure Database for MariaDB
Poniżej opisano pojemności, obsługa aparatu magazynu, uprawnień obsługę, dane manipulowania instrukcji oraz ograniczenia funkcjonalności w usłudze bazy danych.

## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwa cenowa i rdzeni wirtualnych są następujące:

|**Warstwa cenowa**|**rdzenie wirtualne:**| **Maksymalna liczba połączeń**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Ogólne zastosowanie| 2| 600|
|Ogólne zastosowanie| 4| 1250|
|Ogólne zastosowanie| 8| 2500|
|Ogólne zastosowanie| 16| 5000|
|Ogólne zastosowanie| 32| 10 000|
|Ogólne zastosowanie| 64| 20000|
|Zoptymalizowane pod kątem pamięci| 2| 800|
|Zoptymalizowane pod kątem pamięci| 4| 2500|
|Zoptymalizowane pod kątem pamięci| 8| 5000|
|Zoptymalizowane pod kątem pamięci| 16| 10 000|
|Zoptymalizowane pod kątem pamięci| 32| 20000|

Po przekroczeniu limitu połączeń może zostać wyświetlony następujący błąd:
> Błąd 1040 (08004): Zbyt wiele połączeń

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [Aparatu InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [NALEŻĄCY](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIWUM](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola Administrator: wiele parametrów serwera i ustawienia mogą przypadkowo obniżają wydajność serwerów lub odwrócić właściwości ACID systemu DBMS. Jako takie utrzymanie integralności usługi i umowa SLA na poziomie produktu, ta usługa nie ujawnia roli Administrator. Domyślne konto użytkownika, który jest tworzony, gdy tworzone jest nowe wystąpienie bazy danych, umożliwia użytkownikowi wykonać większość instrukcji DDL i DML w wystąpieniu zarządzanym bazy danych.
- Uprawnienia administratora: Podobnie [uprawnień administratora](https://mariadb.com/kb/en/library/grant/#global-privileges) również jest ograniczony.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu polecenia `--skip-definer` podczas wykonywania mysqldump.

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
- Dynamiczne przeniesienie wstępnie utworzonych serwerów między subskrypcją i grupą zasobów nie jest obecnie obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Zapoznaj się z [warstwami cenowymi](concepts-pricing-tiers.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="current-known-issues"></a>Obecnie znane problemy
- Wystąpienie serwera MariaDB wyświetla nieprawidłową wersję serwera po nawiązaniu połączenia. Aby uzyskać z właściwym serwerem w wystąpieniu aparatu wersji, użyj `select version();` polecenia.

## <a name="next-steps"></a>Następne kroki
- [Co to jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MariaDB](concepts-supported-versions.md)
