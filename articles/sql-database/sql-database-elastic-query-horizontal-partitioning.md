---
title: Raportowanie w powiększonych na poziomie bazach danych w chmurze
description: jak skonfigurować zapytania elastyczne na partycjach poziomych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 79abaade22fc107fa4c848607ff48232eeeb58ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823764"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Raportowanie w powiększonych na poziomie bazach danych w chmurze (wersja zapoznawcza)

![Kwerenda między fragmentami][1]

Podzielone bazy danych rozprowadzają wiersze w warstwie danych skalowanych w poziomie. Schemat jest identyczny we wszystkich uczestniczących baz danych, znany również jako partycjonowanie poziome. Za pomocą zapytania elastycznego można tworzyć raporty obejmujące wszystkie bazy danych w podzielonej bazie danych.

Aby uzyskać szybki start, zobacz [Raportowanie w powiększonych na poziomie bazach danych w chmurze](sql-database-elastic-query-getting-started.md).

W przypadku nierozwojowanych baz danych zobacz [Kwerenda między bazami danych w chmurze z różnymi schematami](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz mapę niezależnego fragmentu przy użyciu biblioteki klienta elastycznej bazy danych. zobacz [Zarządzanie mapami niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md). Lub użyj przykładowej aplikacji w [wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).
* Zobacz [też: Migrowanie istniejących baz danych do skalowane w poziomie baz danych.](sql-database-elastic-convert-to-use-elastic-tools.md)
* Użytkownik musi posiadać alter wszelkie uprawnienia zewnętrznego źródła danych. To uprawnienie jest dołączone do uprawnienia ALTER DATABASE.
* ALTER wszelkie uprawnienia zewnętrznego źródła danych są potrzebne do odwoływania się do źródłowego źródła danych.

## <a name="overview"></a>Omówienie

Te instrukcje tworzą reprezentację metadanych warstwy danych podzielonych na fragmenty w bazie danych zapytań elastycznych.

1. [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx)
2. [TWORZENIE POŚWIADCZEŃ O ZAKRESIE BAZY DANYCH](https://msdn.microsoft.com/library/mt270260.aspx)
3. [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
4. [TWORZENIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Tworzenie klucza głównego i poświadczeń o zakresie bazy danych

Poświadczenia są używane przez kwerendę elastyczną do łączenia się ze zdalnymi bazami danych.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Upewnij się, że *"\<nazwa użytkownika\>"* nie zawiera żadnego sufiksu *"nazwa_serwera".\@*

## <a name="12-create-external-data-sources"></a>1.2 Tworzenie zewnętrznych źródeł danych

Składnia:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>,
            SHARD_MAP_NAME = ‘<shardmapname>’
                   ) [;]

### <a name="example"></a>Przykład

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net',
        DATABASE_NAME='ShardMapDatabase',
        CREDENTIAL= SMMUser,
        SHARD_MAP_NAME='ShardMap'
    );

Pobierz listę bieżących zewnętrznych źródeł danych:

    select * from sys.external_data_sources;

Zewnętrzne źródło danych odwołuje się do mapy niezależnego fragmentu. Zapytanie elastyczne następnie używa zewnętrznego źródła danych i podstawowej mapy niezależnego fragmentu do wyliczenia baz danych, które uczestniczą w warstwie danych.
Te same poświadczenia są używane do odczytu mapy niezależnego fragmentu i dostępu do danych na fragmenty podczas przetwarzania zapytania elastycznego.

## <a name="13-create-external-tables"></a>1.3 Tworzenie tabel zewnętrznych

Składnia:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::=
      DATA_SOURCE = <External_Data_Source>,
      [ SCHEMA_NAME = N'nonescaped_schema_name',]
      [ OBJECT_NAME = N'nonescaped_object_name',]
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Przykład**

    CREATE EXTERNAL TABLE [dbo].[order_line](
         [ol_o_id] int NOT NULL,
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL,
         [ol_number] tinyint NOT NULL,
         [ol_i_id] int NOT NULL,
         [ol_delivery_d] datetime NOT NULL,
         [ol_amount] smallmoney NOT NULL,
         [ol_supply_w_id] int NOT NULL,
         [ol_quantity] smallint NOT NULL,
         [ol_dist_info] char(24) NOT NULL
    )

    WITH
    (
        DATA_SOURCE = MyExtSrc,
         SCHEMA_NAME = 'orders',
         OBJECT_NAME = 'order_details',
        DISTRIBUTION=SHARDED(ol_w_id)
    );

Pobierz listę tabel zewnętrznych z bieżącej bazy danych:

    SELECT * from sys.external_tables;

Aby upuścić tabele zewnętrzne:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Uwagi

Klauzula\_ŹRÓDŁO DANYCH definiuje zewnętrzne źródło danych (mapę niezależnego fragmentu), które jest używane dla tabeli zewnętrznej.  

Klauzule\_NAZWA SCHEMATU i NAZWA OBIEKTU\_mapują definicję tabeli zewnętrznej na tabelę w innym schemacie. W przypadku pominięcia schemat obiektu zdalnego przyjmuje się jako "dbo", a jego nazwa jest uważana za identyczną z zdefiniowaną nazwą tabeli zewnętrznej. Jest to przydatne, jeśli nazwa tabeli zdalnej jest już zajęta w bazie danych, w której chcesz utworzyć tabelę zewnętrzną. Na przykład chcesz zdefiniować tabelę zewnętrzną, aby uzyskać widok agregacji widoków katalogu lub dmvs w warstwie danych skalowane w poziomie. Ponieważ widoki katalogu i dmvs już istnieją lokalnie, nie można użyć ich nazwy dla definicji tabeli zewnętrznej. Zamiast tego należy użyć innej nazwy i użyć widoku katalogu lub nazwy DMV w\_klauzulach NAZWA SCHEMATU i/lub NAZWA OBIEKTU.\_ (Zobacz poniższy przykład).

Klauzula ROZKŁAD określa rozkład danych używany dla tej tabeli. Procesor zapytań wykorzystuje informacje zawarte w klauzuli DYSTRYBUCJA do tworzenia najbardziej efektywnych planów zapytań.

1. **SHARDED** oznacza, że dane są poziomo podzielone na partycje między bazami danych. Kluczem partycjonowania dla dystrybucji danych jest **parametr<sharding_column_name>.**
2. **REPLIKOWANE** oznacza, że identyczne kopie tabeli są obecne w każdej bazie danych. Jest twoim obowiązkiem, aby upewnić się, że repliki są identyczne w bazach danych.
3. **ROUND\_ROBIN** oznacza, że tabela jest poziomo podzielony na partycje przy użyciu metody dystrybucji zależnej od aplikacji.

**Odwołanie do warstwy danych:** Tabela zewnętrzna DDL odnosi się do zewnętrznego źródła danych. Zewnętrzne źródło danych określa mapę niezależnego fragmentu, która zawiera tabelę zewnętrzną z informacjami niezbędnymi do zlokalizowania wszystkich baz danych w warstwie danych.

### <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Użytkownicy z dostępem do tabeli zewnętrznej automatycznie uzyskują dostęp do podstawowych tabel zdalnych w ramach poświadczeń podanych w definicji zewnętrznego źródła danych. Unikaj niepożądanego podniesienia uprawnień za pośrednictwem poświadczeń zewnętrznego źródła danych. Użyj GRANT lub REVOKE dla tabeli zewnętrznej, tak jakby była to zwykła tabela.  

Po zdefiniowaniu zewnętrznego źródła danych i tabel zewnętrznych można teraz używać pełnego języka T-SQL w tabelach zewnętrznych.

## <a name="example-querying-horizontal-partitioned-databases"></a>Przykład: wykonywanie zapytań o poziome partycjonowane bazy danych

Poniższa kwerenda wykonuje trójdrożne sprzężenie między magazynami, zamówieniami i wierszami zamówień i używa kilku agregatów i filtru selektywnego. Zakłada się(1) partycjonowanie poziome (dzielenie na fragmenty) i (2), że magazyny, zamówienia i wiersze zamówienia są podzielone na fragmenty przez kolumnę identyfikatora magazynu i że zapytanie elastyczne może współlokować sprzężenia na fragmentach i przetwarzać kosztowną część zapytania na fragmentach w Równoległe.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedura składowana dla zdalnego\_wykonywania T-SQL: sp execute_remote

Zapytanie elastyczne wprowadza również procedurę składowaną, która zapewnia bezpośredni dostęp do fragmentów. Procedura składowana jest wywoływana [sp\_wykonać \_zdalnie](https://msdn.microsoft.com/library/mt703714) i może służyć do wykonywania zdalnych procedur składowanych lub kodu T-SQL na zdalnych baz danych. Przyjmuje następujące parametry:

* Nazwa źródła danych (nvarchar): nazwa zewnętrznego źródła danych typu RDBMS.
* Query (nvarchar): Kwerenda T-SQL, która ma być wykonywana na każdym niezależnuze.
* Deklaracja parametrów (nvarchar) - opcjonalnie: Ciąg z definicjami typów danych dla parametrów używanych w parametrze Query (takich jak sp_executesql).
* Lista wartości parametrów - opcjonalnie: Oddzielona przecinkami lista wartości parametrów (takich jak sp_executesql).

Sp\_execute\_remote używa zewnętrznego źródła danych podanego w parametrach wywołania do wykonania podanej instrukcji T-SQL w zdalnych bazach danych. Używa poświadczeń zewnętrznego źródła danych do łączenia się z bazą danych menedżera map niezależnej map i zdalnymi bazami danych.  

Przykład:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Użyj zwykłych ciągów połączeń programu SQL Server, aby połączyć aplikację, narzędzia bi i integracji danych z bazą danych z definicjami tabel zewnętrznych. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla narzędzia. Następnie odwołaj się do bazy danych zapytań elastycznych, jak każda inna baza danych programu SQL Server połączona z narzędziem i użyj tabel zewnętrznych z narzędzia lub aplikacji, tak jakby były tabelami lokalnymi.

## <a name="best-practices"></a>Najlepsze rozwiązania

* Upewnij się, że baza danych punktu końcowego zapytania elastycznego uzyskała dostęp do bazy danych mapy niezależnej i wszystkich fragmentów za pośrednictwem zapór bazy danych SQL DB.  
* Sprawdzanie poprawności lub wymuszanie dystrybucji danych zdefiniowanych przez tabelę zewnętrzną. Jeśli rzeczywista dystrybucja danych różni się od dystrybucji określonej w definicji tabeli, zapytania mogą przynieść nieoczekiwane wyniki.
* Elastyczne zapytanie obecnie nie wykonuje eliminacji niezależnego fragmentu, gdy predykaty za pomocą klucza dzielenia na fragmenty pozwoliłyby bezpiecznie wykluczyć niektóre fragmenty z przetwarzania.
* Zapytanie elastyczne działa najlepiej dla kwerend, gdzie większość obliczeń można wykonać na fragmenty. Zazwyczaj uzyskać najlepszą wydajność kwerendy z predykatów filtru selektywnego, które mogą być oceniane na fragmenty lub sprzężenia za pomocą kluczy partycjonowania, które mogą być wykonywane w sposób wyrównany do partycji na wszystkie fragmenty. Inne wzorce zapytań mogą wymagać załadowania dużych ilości danych z fragmentów do węzła głównego i mogą działać słabo

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem kwerendy elastycznej, zobacz [omówienie kwerendy elastycznej](sql-database-elastic-query-overview.md).
* Aby uzyskać samouczek partycjonowania pionowego, zobacz [Wprowadzenie do kwerendy między bazami danych (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Aby uzyskać składnię i przykładowe kwerendy dotyczące danych podzielonych pionowo, zobacz [Wyszukiwanie danych podzielonych pionowo na partycje)](sql-database-elastic-query-vertical-partitioning.md)
* Aby uzyskać poziome partycjonowanie (dzielenie na fragmenty) samouczek, zobacz [Wprowadzenie do elastycznego zapytania do partycjonowania poziomego (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).
* Zobacz [\_sp \_wykonać zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej bazy danych SQL azure lub zestaw baz danych służących jako fragmenty w schemat partycjonowania poziomego.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
