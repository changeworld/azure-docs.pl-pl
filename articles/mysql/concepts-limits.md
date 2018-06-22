---
title: Ograniczenia dotyczące bazy danych platformy Azure dla programu MySQL
description: W tym artykule opisano ograniczenia w bazie danych Azure dla programu MySQL, takie jak liczba połączeń i opcje aparatu magazynu.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 2fc224445f89a0b0b4afdc0ef1d0eb1b25b45f36
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309925"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Ograniczenia dotyczące bazy danych platformy Azure dla programu MySQL
W poniższych sekcjach opisano pojemności, magazynu aparat obsługę, uprawnień, obsługi instrukcji manipulacji danych oraz limity funkcjonalności usługi bazy danych. Zobacz też [ogólne ograniczenia](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) mające zastosowanie do aparatu bazy danych MySQL.

## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwa cenowa i vCores są następujące: 

|**Warstwa cenowa**|**vCore(s)**| **Maksymalna liczba połączeń**|
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

Gdy połączenia przekracza limit, może zostać wyświetlony następujący błąd:
> Błąd 1040 (08004): Zbyt wiele połączeń

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [NALEŻĄCY](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIWUM](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERACYJNA](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Administrator roli: wiele parametrów serwera i ustawienia mogą przypadkowo obniżają wydajność serwerów lub odwrócić ACID właściwości systemu DBMS. Tak, aby zachować integralności usługi i umowy SLA na poziomie produktu, tego usługa nie uwidacznia roli Administrator. Domyślne konto użytkownika, który jest tworzony podczas tworzenia nowego wystąpienia bazy danych, umożliwia użytkownikowi wykonują większość instrukcji DDL i DML w wystąpieniu bazy danych zarządzanych. 
- Uprawnienie administratora: Podobnie [SUPER uprawnień](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) również jest ograniczony.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulacji danych

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE` jest obsługiwana, ale `[LOCAL]` parametru musi być określona i Przekierowanie do ścieżki UNC (zainstalowane za pomocą protokołu SMB magazynu Azure).

### <a name="unsupported"></a>Nieobsługiwane
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Ograniczenia funkcjonalności

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z warstw cenowych podstawowa nie jest obecnie obsługiwane.
- Zmniejszenie rozmiaru magazynu serwera nie jest obsługiwana.

### <a name="server-version-upgrades"></a>Uaktualniania wersji
- Automatycznej migracji między wersjami aparatu bazy danych głównych nie jest obecnie obsługiwane.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- Podczas korzystania z funkcji PITR tworzona jest nowy serwer jako serwer, który jest oparty na takie same konfiguracje.
- Przywracanie usuniętych serwera nie jest obsługiwane.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamicznie przenoszenie serwerów wstępnie utworzone w subskrypcji i grupy zasobów nie jest obecnie obsługiwane.

## <a name="current-known-issues"></a>Obecnie znane problemy
- Wystąpienie serwera MySQL Wyświetla wersję niewłaściwy serwer, po nawiązaniu połączenia. Aby uzyskać wystąpienia aparatu wersji z właściwym serwerem, użyj `select version();` polecenia.

## <a name="next-steps"></a>Kolejne kroki
- [Co to jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MySQL](concepts-supported-versions.md)
