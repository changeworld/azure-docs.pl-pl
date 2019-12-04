---
title: Ograniczenia — Azure Database for MySQL
description: W tym artykule opisano ograniczenia w Azure Database for MySQL, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 07feb3ebf9720d70da441486fd0b2e6e274b68e4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770921"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Ograniczenia w Azure Database for MySQL
W poniższych sekcjach opisano pojemność, obsługę aparatu magazynu, obsługę uprawnień, obsługę instrukcji manipulowania danymi oraz limity funkcjonalne w usłudze bazy danych. Zapoznaj się również z [ogólnymi ograniczeniami](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) dotyczącymi aparatu bazy danych MySQL.

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
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [ROZMIAR](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [FOLDERU](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERACYJNY](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola DBA: wiele parametrów serwera i ustawień może przypadkowo obniżyć wydajność serwera lub Negate właściwości KWASów systemu DBMS. W związku z tym, aby zachować integralność usługi i umowę SLA na poziomie produktu, ta usługa nie ujawnia roli DBA. Domyślne konto użytkownika, które jest konstruowane podczas tworzenia nowego wystąpienia bazy danych, umożliwia temu użytkownikowi wykonywanie większości instrukcji języka DDL i DML w zarządzanym wystąpieniu bazy danych. 
- Uprawnienie "noprivileged": podobne [nieuprzywilejowane](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) również jest ograniczone.
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
- Automatyczna Migracja między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwana. Jeśli chcesz uaktualnić do następnej wersji głównej, zrób [zrzut i Przywróć](./concepts-migrate-dump-restore.md) go na serwerze, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- W przypadku korzystania z funkcji kopie nowy serwer jest tworzony z tymi samymi konfiguracjami co serwer, na którym jest oparta.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Zapoznaj się z [warstwami cenowymi](concepts-pricing-tiers.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="current-known-issues"></a>Bieżące znane problemy
- Wystąpienie serwera MySQL wyświetla nieprawidłową wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu wystąpienia serwera, użyj polecenia `select version();`.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MySQL](concepts-supported-versions.md)
