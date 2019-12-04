---
title: Ograniczenia — Azure Database for MariaDB
description: W tym artykule opisano ograniczenia w Azure Database for MariaDB, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fc89b6233602c81ea622a528c223adf2003f0f68
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772500"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia w Azure Database for MariaDB
W poniższych sekcjach opisano pojemność, obsługę aparatu magazynu, obsługę uprawnień, obsługę instrukcji manipulowania danymi oraz limity funkcjonalne w usłudze bazy danych.

## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwę cenową i rdzeni wirtualnych są następujące:

|**Warstwa cenowa**|**Rdzeń wirtualny**| **Maksymalna liczba połączeń**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Ogólne zastosowanie| 2| 300|
|Ogólne zastosowanie| 4| 625|
|Ogólne zastosowanie| 8| 1250|
|Ogólne zastosowanie| 16| 2500|
|Ogólne zastosowanie| 32| 5000|
|Ogólne zastosowanie| 64| 10 000|
|Zoptymalizowane pod kątem pamięci| 2| 600|
|Zoptymalizowane pod kątem pamięci| 4| 1250|
|Zoptymalizowane pod kątem pamięci| 8| 2500|
|Zoptymalizowane pod kątem pamięci| 16| 5000|
|Zoptymalizowane pod kątem pamięci| 32| 10 000|

Gdy połączenia przekroczą limit, może zostać wyświetlony następujący błąd:
> BŁĄD 1040 (08004): zbyt wiele połączeń

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [ROZMIAR](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [FOLDERU](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola DBA: wiele parametrów serwera i ustawień może przypadkowo obniżyć wydajność serwera lub Negate właściwości KWASów systemu DBMS. W związku z tym, aby zachować integralność usługi i umowę SLA na poziomie produktu, ta usługa nie ujawnia roli DBA. Domyślne konto użytkownika, które jest konstruowane podczas tworzenia nowego wystąpienia bazy danych, umożliwia temu użytkownikowi wykonywanie większości instrukcji języka DDL i DML w zarządzanym wystąpieniu bazy danych.
- Uprawnienie "noprivileged": podobne [nieuprzywilejowane](https://mariadb.com/kb/en/library/grant/#global-privileges) również jest ograniczone.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu polecenia `--skip-definer` podczas wykonywania mysqldump.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulowania danymi

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE` jest obsługiwana, ale parametr `[LOCAL]` musi być określony i kierowany do ścieżki UNC (usługa Azure Storage zainstalowana za pomocą protokołu SMB).

### <a name="unsupported"></a>Nieobsługiwane
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z podstawowych warstw cenowych nie jest obecnie obsługiwane.
- Zmniejszenie rozmiaru magazynu serwera nie jest obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatyczna Migracja między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwana.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- W przypadku korzystania z funkcji kopie nowy serwer jest tworzony z tymi samymi konfiguracjami co serwer, na którym jest oparta.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamiczne przeniesienie wstępnie utworzonych serwerów między subskrypcją i grupą zasobów nie jest obecnie obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Zapoznaj się z [warstwami cenowymi](concepts-pricing-tiers.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="current-known-issues"></a>Bieżące znane problemy
- Wystąpienie serwera MariaDB wyświetla nieprawidłową wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu wystąpienia serwera, użyj polecenia `select version();`.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MariaDB](concepts-supported-versions.md)
