---
title: Ograniczenia — Azure Database for MariaDB
description: W tym artykule opisano ograniczenia w Azure Database for MariaDB, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: c982181dee34a7eb0715d5e1271ef5ed794f3809
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296748"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia w Azure Database for MariaDB
Poniżej opisano pojemności, obsługa aparatu magazynu, uprawnień obsługę, dane manipulowania instrukcji oraz ograniczenia funkcjonalności w usłudze bazy danych.

## <a name="server-parameters"></a>Parametry serwera

Minimalne i maksymalne wartości kilku popularnych parametrów serwera są określane przez warstwę cenową i rdzeni wirtualnych. Limity można znaleźć w poniższych tabelach.

### <a name="max_connections"></a>max_connections

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa|1|50|10|50|
|Podstawowa|2|100|10|100|
|Ogólnego przeznaczenia|2|300|10|600|
|Ogólnego przeznaczenia|4|625|10|1250|
|Ogólnego przeznaczenia|8|1250|10|2500|
|Ogólnego przeznaczenia|16|2500|10|5000|
|Ogólnego przeznaczenia|32|5000|10|10 000|
|Ogólnego przeznaczenia|64|10 000|10|20000|
|Pamięć|2|600|10|800|
|Pamięć|4|1250|10|2500|
|Pamięć|8|2500|10|5000|
|Pamięć|16|5000|10|10 000|
|Pamięć|32|10 000|10|20000|

Po przekroczeniu limitu połączeń może zostać wyświetlony następujący błąd:
> Błąd 1040 (08004): Zbyt wiele połączeń

> [!IMPORTANT]
> W celu uzyskania najlepszego środowiska zalecamy użycie połączenia pulę, takiego jak ProxySQL, aby efektywnie zarządzać połączeniami.

Tworzenie nowych połączeń klientów do MariaDB trwa po upływie czasu i po jego ustanowieniu te połączenia zajmują zasoby bazy danych, nawet jeśli są bezczynne. Większość aplikacji żąda wielu krótkich połączeń, które są związane z tą sytuacją. Wynikiem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pulę połączenia, który zmniejsza bezczynne połączenia i ponownie używa istniejących połączeń, będzie pomóc w uniknięciu tego działania. Aby dowiedzieć się więcej o konfigurowaniu ProxySQL, odwiedź nasz [wpis w blogu](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

## <a name="query_cache_size"></a>query_cache_size

Pamięć podręczna zapytań jest domyślnie wyłączona. Aby włączyć pamięć podręczną zapytań, należy skonfigurować parametr `query_cache_type`. 

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Ogólnego przeznaczenia|2|0|0|16777216|
|Ogólnego przeznaczenia|4|0|0|33554432|
|Ogólnego przeznaczenia|8|0|0|67108864|
|Ogólnego przeznaczenia|16|0|0|134217728|
|Ogólnego przeznaczenia|32|0|0|134217728|
|Ogólnego przeznaczenia|64|0|0|134217728|
|Pamięć|2|0|0|33554432|
|Pamięć|4|0|0|67108864|
|Pamięć|8|0|0|134217728|
|Pamięć|16|0|0|134217728|
|Pamięć|32|0|0|134217728|

## <a name="sort_buffer_size"></a>sort_buffer_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Ogólnego przeznaczenia|2|524288|32768|4194304|
|Ogólnego przeznaczenia|4|524288|32768|8388608|
|Ogólnego przeznaczenia|8|524288|32768|16777216|
|Ogólnego przeznaczenia|16|524288|32768|33554432|
|Ogólnego przeznaczenia|32|524288|32768|33554432|
|Ogólnego przeznaczenia|64|524288|32768|33554432|
|Pamięć|2|524288|32768|8388608|
|Pamięć|4|524288|32768|16777216|
|Pamięć|8|524288|32768|33554432|
|Pamięć|16|524288|32768|33554432|
|Pamięć|32|524288|32768|33554432|

## <a name="join_buffer_size"></a>join_buffer_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Ogólnego przeznaczenia|2|262144|128|268435455|
|Ogólnego przeznaczenia|4|262144|128|536870912|
|Ogólnego przeznaczenia|8|262144|128|1073741824|
|Ogólnego przeznaczenia|16|262144|128|2147483648|
|Ogólnego przeznaczenia|32|262144|128|4294967295|
|Ogólnego przeznaczenia|64|262144|128|4294967295|
|Pamięć|2|262144|128|536870912|
|Pamięć|4|262144|128|1073741824|
|Pamięć|8|262144|128|2147483648|
|Pamięć|16|262144|128|4294967295|
|Pamięć|32|262144|128|4294967295|

## <a name="max_heap_table_size"></a>max_heap_table_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Ogólnego przeznaczenia|2|16777216|16384|268435455|
|Ogólnego przeznaczenia|4|16777216|16384|536870912|
|Ogólnego przeznaczenia|8|16777216|16384|1073741824|
|Ogólnego przeznaczenia|16|16777216|16384|2147483648|
|Ogólnego przeznaczenia|32|16777216|16384|4294967295|
|Ogólnego przeznaczenia|64|16777216|16384|4294967295|
|Pamięć|2|16777216|16384|536870912|
|Pamięć|4|16777216|16384|1073741824|
|Pamięć|8|16777216|16384|2147483648|
|Pamięć|16|16777216|16384|4294967295|
|Pamięć|32|16777216|16384|4294967295|

## <a name="tmp_table_size"></a>tmp_table_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|N/D|N/D|
|Ogólnego przeznaczenia|2|16777216|1024|67108864|
|Ogólnego przeznaczenia|4|16777216|1024|134217728|
|Ogólnego przeznaczenia|8|16777216|1024|268435456|
|Ogólnego przeznaczenia|16|16777216|1024|536870912|
|Ogólnego przeznaczenia|32|16777216|1024|1073741824|
|Ogólnego przeznaczenia|64|16777216|1024|1073741824|
|Pamięć|2|16777216|1024|134217728|
|Pamięć|4|16777216|1024|268435456|
|Pamięć|8|16777216|1024|536870912|
|Pamięć|16|16777216|1024|1073741824|
|Pamięć|32|16777216|1024|1073741824|

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
- Rola Administrator: wiele parametrów serwera i ustawienia mogą przypadkowo obniżają wydajność serwerów lub odwrócić właściwości ACID systemu DBMS. Jako takie utrzymanie integralności usługi i umowa SLA na poziomie produktu, ta usługa nie ujawnia roli Administrator. Domyślne konto użytkownika, który jest tworzony, gdy tworzone jest nowe wystąpienie bazy danych, umożliwia użytkownikowi wykonać większość instrukcji DDL i DML w wystąpieniu zarządzanym bazy danych.
- Uprawnienie "noprivileged": podobne [nieuprzywilejowane](https://mariadb.com/kb/en/library/grant/#global-privileges) również jest ograniczone.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu polecenia `--skip-definer` podczas wykonywania mysqldump.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulacji danych

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE` jest obsługiwana, ale parametr `[LOCAL]` musi być określony i kierowany do ścieżki UNC (usługa Azure Storage zainstalowana za pomocą protokołu SMB).

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
- Wystąpienie serwera MariaDB wyświetla nieprawidłową wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu wystąpienia serwera, użyj polecenia `select version();`.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MariaDB](concepts-supported-versions.md)
