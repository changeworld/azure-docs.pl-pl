---
title: Ograniczenia — usługa Azure Database dla mariadb
description: W tym artykule opisano ograniczenia w usłudze Azure Database dla MariaDB, takie jak liczba opcji aparatu połączeń i magazynu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 18f227c1888e0565eebb640fa61ced56dc994865
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632344"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia w usłudze Azure Database dla mariadb
W poniższych sekcjach opisano pojemność, obsługę aparatu magazynu, obsługę uprawnień, obsługę instrukcji manipulowania danymi i limity funkcjonalności w usłudze bazy danych.

## <a name="server-parameters"></a>Parametry serwera

Minimalne i maksymalne wartości kilku popularnych parametrów serwera są określane przez warstwę cenową i pole wirtualne. Limity znajdują się w poniższych tabelach.

### <a name="max_connections"></a>Max_connections

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|50|10|50|
|Podstawowy|2|100|10|100|
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

Tworzenie nowych połączeń klientów z MariaDB wymaga czasu i po ustanowieniu, te połączenia zajmują zasoby bazy danych, nawet wtedy, gdy bezczynny. Większość aplikacji żąda wielu krótkotrwałych połączeń, co potęguje tę sytuację. Rezultatem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pooler połączenia, który zmniejsza bezczynne połączenia i ponownie używa istniejących połączeń pomoże uniknąć tego. Aby dowiedzieć się więcej o konfigurowaniu pliku ProxySQL, odwiedź nasz [wpis na blogu.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

### <a name="query_cache_size"></a>query_cache_size

Pamięć podręczna kwerend jest domyślnie wyłączona. Aby włączyć pamięć podręczną `query_cache_type` kwerend, skonfiguruj parametr. 

Przejrzyj [dokumentację MariaDB,](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowy|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
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

Przejrzyj [dokumentację MariaDB,](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowy|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
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

Przejrzyj [dokumentację MariaDB,](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowy|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
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

Przejrzyj [dokumentację MariaDB,](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowy|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
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

Przejrzyj [dokumentację MariaDB,](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**vCore(s)**|**Wartość domyślna**|**Wartość min.**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
|Podstawowy|2|Nie można konfigurować w warstwie Podstawowa|Nie dotyczy|Nie dotyczy|
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

### <a name="time_zone"></a>time_zone

Tabele stref czasowych można `mysql.az_load_timezone` wypełniać, wywołując procedurę składowaną z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. Zapoznaj się z artykułami [portalu Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) lub [interfejsu wiersza polecenia platformy Azure,](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) aby dowiedzieć się, jak wywołać procedurę składowaną i ustawić strefy czasowe na poziomie globalnym lub sesji.

## <a name="storage-engine-support"></a>Obsługa silnika pamięci masowej

### <a name="supported"></a>Obsługiwane
- [Innodb](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [Pamięci](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nieobsługiwane
- [Myisam](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [Blackhole](https://mariadb.com/kb/en/library/blackhole/)
- [Archiwum](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola DBA: Wiele parametrów i ustawień serwera może przypadkowo obniżyć wydajność serwera lub zanegować właściwości ACID systemu DBMS. W związku z tym, aby zachować integralność usługi i umowy SLA na poziomie produktu, ta usługa nie udostępnia roli DBA. Domyślne konto użytkownika, które jest tworzone podczas tworzenia nowego wystąpienia bazy danych, umożliwia użytkownikowi wykonywanie większości instrukcji DDL i DML w wystąpieniu zarządzanej bazy danych.
- Super privilege: Podobnie [przywilej SUPER](https://mariadb.com/kb/en/library/grant/#global-privileges) jest również ograniczony.
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
- Automatyczna migracja między głównymi wersjami aparatu bazy danych nie jest obecnie obsługiwana.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- Podczas korzystania z funkcji PITR nowy serwer jest tworzony z tymi samymi konfiguracjami co serwer, na którym jest oparty.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamicznie przenoszenie wstępnie utworzonych serwerów w ramach subskrypcji i grupy zasobów nie jest obecnie obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Aby uzyskać limity rozmiaru magazynu dla warstwy cenowej, zapoznaj się z [warstwami cenowymi.](concepts-pricing-tiers.md)

## <a name="current-known-issues"></a>Bieżące znane problemy
- Wystąpienie serwera MariaDB wyświetla niepoprawną wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu `select version();` wystąpienia serwera, użyj polecenia.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w każdej warstwie usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MariaDB](concepts-supported-versions.md)
