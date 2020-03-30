---
title: Ograniczenia — usługa Azure Database dla mysql
description: W tym artykule opisano ograniczenia w usłudze Azure Database dla MySQL, takie jak liczba opcji aparatu połączeń i magazynu.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: bc4694928eceed57692a0d4b0469543c1a8f9678
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532760"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Ograniczenia w usłudze Azure Database dla mysql
W poniższych sekcjach opisano pojemność, obsługę aparatu magazynu, obsługę uprawnień, obsługę instrukcji manipulowania danymi i limity funkcjonalności w usłudze bazy danych. Zobacz także [ogólne ograniczenia](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) mające zastosowanie do aparatu bazy danych MySQL.

## <a name="server-parameters"></a>Parametry serwera

Minimalne i maksymalne wartości kilku popularnych parametrów serwera są określane przez warstwę cenową i pole wirtualne. Limity znajdują się w poniższych tabelach.

### <a name="max_connections"></a>Max_connections

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa (Basic)|1|50|10|50|
|Podstawowa (Basic)|2|100|10|100|
|Ogólnego przeznaczenia|2|300|10|600|
|Ogólnego przeznaczenia|4|625|10|1250|
|Ogólnego przeznaczenia|8|1250|10|2500|
|Ogólnego przeznaczenia|16|2500|10|5000|
|Ogólnego przeznaczenia|32|5000|10|10 000|
|Ogólnego przeznaczenia|64|10 000|10|20000|
|Optymalizacja pod kątem pamięci|2|600|10|800|
|Optymalizacja pod kątem pamięci|4|1250|10|2500|
|Optymalizacja pod kątem pamięci|8|2500|10|5000|
|Optymalizacja pod kątem pamięci|16|5000|10|10 000|
|Optymalizacja pod kątem pamięci|32|10 000|10|20000|

Gdy połączenia przekraczają limit, może pojawić się następujący błąd:
> BŁĄD 1040 (08004): Zbyt wiele połączeń

> [!IMPORTANT]
> Aby uzyskać najlepsze wrażenia, zaleca się używanie puli połączeń, takiej jak ProxySQL, do efektywnego zarządzania połączeniami.

Tworzenie nowych połączeń klientów z MySQL wymaga czasu i po ustanowieniu, połączenia te zajmują zasoby bazy danych, nawet gdy bezczynne. Większość aplikacji żąda wielu krótkotrwałych połączeń, co potęguje tę sytuację. Rezultatem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pooler połączenia, który zmniejsza bezczynne połączenia i ponownie używa istniejących połączeń pomoże uniknąć tego. Aby dowiedzieć się więcej o konfigurowaniu pliku ProxySQL, odwiedź nasz [wpis na blogu.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

### <a name="query_cache_size"></a>query_cache_size

Pamięć podręczna kwerend jest domyślnie wyłączona. Aby włączyć pamięć podręczną `query_cache_type` kwerend, skonfiguruj parametr. 

Przejrzyj [dokumentację MySQL,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) aby dowiedzieć się więcej o tym parametrze.

> [!NOTE]
> Pamięć podręczna zapytań jest przestarzała od mysql 5.7.20 i została usunięta w MySQL 8.0

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa (Basic)|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowa (Basic)|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Ogólnego przeznaczenia|2|0|0|16777216|
|Ogólnego przeznaczenia|4|0|0|33554432|
|Ogólnego przeznaczenia|8|0|0|67108864|
|Ogólnego przeznaczenia|16|0|0|134217728|
|Ogólnego przeznaczenia|32|0|0|134217728|
|Ogólnego przeznaczenia|64|0|0|134217728|
|Optymalizacja pod kątem pamięci|2|0|0|33554432|
|Optymalizacja pod kątem pamięci|4|0|0|67108864|
|Optymalizacja pod kątem pamięci|8|0|0|134217728|
|Optymalizacja pod kątem pamięci|16|0|0|134217728|
|Optymalizacja pod kątem pamięci|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

Przejrzyj [dokumentację MySQL,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa (Basic)|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowa (Basic)|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Ogólnego przeznaczenia|2|524288|32768|4194304|
|Ogólnego przeznaczenia|4|524288|32768|8388608|
|Ogólnego przeznaczenia|8|524288|32768|16777216|
|Ogólnego przeznaczenia|16|524288|32768|33554432|
|Ogólnego przeznaczenia|32|524288|32768|33554432|
|Ogólnego przeznaczenia|64|524288|32768|33554432|
|Optymalizacja pod kątem pamięci|2|524288|32768|8388608|
|Optymalizacja pod kątem pamięci|4|524288|32768|16777216|
|Optymalizacja pod kątem pamięci|8|524288|32768|33554432|
|Optymalizacja pod kątem pamięci|16|524288|32768|33554432|
|Optymalizacja pod kątem pamięci|32|524288|32768|33554432|

### <a name="join_buffer_size"></a>join_buffer_size

Przejrzyj [dokumentację MySQL,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa (Basic)|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowa (Basic)|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Ogólnego przeznaczenia|2|262144|128|268435455|
|Ogólnego przeznaczenia|4|262144|128|536870912|
|Ogólnego przeznaczenia|8|262144|128|1073741824|
|Ogólnego przeznaczenia|16|262144|128|2147483648|
|Ogólnego przeznaczenia|32|262144|128|4294967295|
|Ogólnego przeznaczenia|64|262144|128|4294967295|
|Optymalizacja pod kątem pamięci|2|262144|128|536870912|
|Optymalizacja pod kątem pamięci|4|262144|128|1073741824|
|Optymalizacja pod kątem pamięci|8|262144|128|2147483648|
|Optymalizacja pod kątem pamięci|16|262144|128|4294967295|
|Optymalizacja pod kątem pamięci|32|262144|128|4294967295|

### <a name="max_heap_table_size"></a>max_heap_table_size

Przejrzyj [dokumentację MySQL,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa (Basic)|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowa (Basic)|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Ogólnego przeznaczenia|2|16777216|16384|268435455|
|Ogólnego przeznaczenia|4|16777216|16384|536870912|
|Ogólnego przeznaczenia|8|16777216|16384|1073741824|
|Ogólnego przeznaczenia|16|16777216|16384|2147483648|
|Ogólnego przeznaczenia|32|16777216|16384|4294967295|
|Ogólnego przeznaczenia|64|16777216|16384|4294967295|
|Optymalizacja pod kątem pamięci|2|16777216|16384|536870912|
|Optymalizacja pod kątem pamięci|4|16777216|16384|1073741824|
|Optymalizacja pod kątem pamięci|8|16777216|16384|2147483648|
|Optymalizacja pod kątem pamięci|16|16777216|16384|4294967295|
|Optymalizacja pod kątem pamięci|32|16777216|16384|4294967295|

### <a name="tmp_table_size"></a>tmp_table_size

Przejrzyj [dokumentację MySQL,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa (Basic)|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowa (Basic)|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Ogólnego przeznaczenia|2|16777216|1024|67108864|
|Ogólnego przeznaczenia|4|16777216|1024|134217728|
|Ogólnego przeznaczenia|8|16777216|1024|268435456|
|Ogólnego przeznaczenia|16|16777216|1024|536870912|
|Ogólnego przeznaczenia|32|16777216|1024|1073741824|
|Ogólnego przeznaczenia|64|16777216|1024|1073741824|
|Optymalizacja pod kątem pamięci|2|16777216|1024|134217728|
|Optymalizacja pod kątem pamięci|4|16777216|1024|268435456|
|Optymalizacja pod kątem pamięci|8|16777216|1024|536870912|
|Optymalizacja pod kątem pamięci|16|16777216|1024|1073741824|
|Optymalizacja pod kątem pamięci|32|16777216|1024|1073741824|

## <a name="storage-engine-support"></a>Obsługa silnika pamięci masowej

### <a name="supported"></a>Obsługiwane
- [Innodb](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [Pamięci](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nieobsługiwane
- [Myisam](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [Blackhole](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Archiwum](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [Federacyjnych](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola DBA: Wiele parametrów i ustawień serwera może przypadkowo obniżyć wydajność serwera lub zanegować właściwości ACID systemu DBMS. W związku z tym, aby zachować integralność usługi i umowy SLA na poziomie produktu, ta usługa nie udostępnia roli DBA. Domyślne konto użytkownika, które jest tworzone podczas tworzenia nowego wystąpienia bazy danych, umożliwia użytkownikowi wykonywanie większości instrukcji DDL i DML w wystąpieniu zarządzanej bazy danych. 
- Super privilege: Podobnie [przywilej SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) jest również ograniczony.
- DEFINER: Wymaga super uprawnień do tworzenia i jest ograniczona. Jeśli importowanie danych przy użyciu `CREATE DEFINER` kopii zapasowej, usunąć `--skip-definer` polecenia ręcznie lub za pomocą polecenia podczas wykonywania mysqldump.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulowania danymi

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE`jest obsługiwany, ale `[LOCAL]` parametr musi być określony i skierowany do ścieżki UNC (magazyn platformy Azure zainstalowany za pośrednictwem SMB).

### <a name="unsupported"></a>Nieobsługiwane
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z podstawowych warstw cenowych nie jest obecnie obsługiwane.
- Zmniejszanie rozmiaru magazynu serwera nie jest obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatyczna migracja między głównymi wersjami aparatu bazy danych nie jest obecnie obsługiwana. Jeśli chcesz uaktualnić do następnej wersji głównej, zrób [zrzut i przywróć](./concepts-migrate-dump-restore.md) go na serwerze, który został utworzony z nową wersją aparatu.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- Podczas korzystania z funkcji PITR nowy serwer jest tworzony z tymi samymi konfiguracjami co serwer, na którym jest oparty.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Aby uzyskać limity rozmiaru magazynu dla warstwy cenowej, zapoznaj się z [warstwami cenowymi.](concepts-pricing-tiers.md)

## <a name="current-known-issues"></a>Bieżące znane problemy
- Wystąpienie serwera MySQL wyświetla niewłaściwą wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu `select version();` wystąpienia serwera, użyj polecenia.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w każdej warstwie usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MySQL](concepts-supported-versions.md)
