---
title: Raportowanie w bazach danych skalowanych w poziomie cloud | Dokumentacja firmy Microsoft
description: jak skonfigurować zapytania elastyczne za pośrednictwem poziomych partycji
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 3b2b472407175df307c569704d4c7611737c4ea1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694358"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Raportowanie we wszystkich bazach danych w chmurze skalowanych w poziomie (wersja zapoznawcza)

![Zapytanie między fragmentami][1]

Bazy danych podzielonej na fragmenty Rozmieść wiersze danych skalowanych w poziomie warstwy. Schemat jest taka sama dla wszystkich uczestniczących baz danych, nazywana również partycjonowanie poziome. Przy użyciu zapytania elastycznego, można tworzyć raporty, które rozciągają się wszystkie bazy danych w bazie danych podzielonych na fragmenty.

Aby szybko rozpocząć pracę, zobacz [raportowanie we wszystkich bazach danych w chmurze skalowanych w poziomie](sql-database-elastic-query-getting-started.md).

Dla baz danych innych niż podzielonej na fragmenty, zobacz [zapytań w bazach danych w chmurze z różnymi schematami](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie mapy fragmentów za pomocą biblioteki klienckiej elastycznej bazy danych. zobacz [procesu zarządzania mapą fragmentów](sql-database-elastic-scale-shard-map-management.md). Lub użyj przykładowej aplikacji w [Rozpocznij pracę z narzędziami elastycznej bazy danych](sql-database-elastic-scale-get-started.md).
* Możesz również zapoznać się [Migrowanie istniejących baz danych do baz danych skalowanych w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md).
* Użytkownik musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączony do uprawnienie ALTER DATABASE.
* Aby odwołać się do bazowego źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.

## <a name="overview"></a>Omówienie

Te instrukcje utworzysz reprezentację metadanych warstwę danych podzielonych na fragmenty w bazie danych zapytania elastycznego.

1. [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx)
2. [TWORZENIE BAZY DANYCH O OKREŚLONYM ZAKRESIE POŚWIADCZEŃ](https://msdn.microsoft.com/library/mt270260.aspx)
3. [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
4. [TWORZENIE ZEWNĘTRZNEJ TABELI](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Tworzenie klucza głównego bazy danych i poświadczeń

Poświadczenie jest używane przez zapytanie elastyczne, aby nawiązać połączenie zdalne bazy danych.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Upewnij się, że *"\<username\>"* nie zawiera żadnych *"\@servername"* sufiks.

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

Pobierz listę bieżących źródeł danych zewnętrznych:

    select * from sys.external_data_sources;

Zewnętrzne źródło danych odwołuje się do mapy fragmentów. Elastyczne zapytanie używa następnie zewnętrznego źródła danych i podstawowej mapy fragmentów wyliczyć baz danych, które uczestniczą w warstwie danych.
Te same poświadczenia są używane, można odczytać mapy fragmentów i dostępu do danych na fragmenty podczas przetwarzania zapytania elastycznego.

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

Pobieranie listy tabel zewnętrznych w bieżącej bazie danych:

    SELECT * from sys.external_tables;

Aby porzucić tabel zewnętrznych:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Uwagi

DANE\_klauzuli źródła definiuje zewnętrznego źródła danych (mapowania fragmentów w postaci) używany do tabeli zewnętrznej.  

Schemat\_nazwy i obiekt\_klauzule nazwę mapowania tabeli do innego schematu definicji tabeli zewnętrznej. W przypadku pominięcia schematu obiektu zdalnego zakłada się, że "dbo", a jego nazwa jest zakłada się, że taka sama jak nazwa tabeli zewnętrznej definiowanego. Jest to przydatne, jeśli nazwa tabeli zdalnej jest już zajęta w bazie danych potrzebne do utworzenia tabeli zewnętrznej. Na przykład aby zdefiniować tabeli zewnętrznej można pobrać zagregowany widok widoków wykazu lub dynamicznych widoków zarządzania na podstawie posiadanych danych skalowanych w poziomie warstwy. Ponieważ widoków wykazu i dynamicznych widoków zarządzania już istnieje lokalnie, nie możesz użyć ich nazw, do definicji tabeli zewnętrznej. Zamiast tego należy użyć innej nazwy i użyj widoku wykazu lub DMV nazwy w SCHEMACIE\_nazwę i/lub obiekt\_klauzule nazwy. (Zobacz przykład poniżej).

Klauzula dystrybucji określa dystrybucję danych używane dla tej tabeli. Procesor zapytań korzysta z informacji podanych w klauzuli dystrybucja Tworzenie planów zapytań w najbardziej efektywny sposób.

1. **Podzielonej na FRAGMENTY** oznacza, że dane w poziomie jest podzielona na partycje w bazach danych. Partycjonowania klucza dystrybucji danych **< sharding_column_name >** parametru.
2. **REPLIKOWANE** oznacza identyczne kopie tabeli w każdej bazie danych. Jest odpowiedzialny za zapewnienie, że repliki są identyczne w bazach danych.
3. **ROUND\_OKRĘŻNE** oznacza, że tabela jest poziomo podzielona na partycje za pomocą metody dystrybucji zależnych od aplikacji.

**Warstwa danych odwołania**: Tabela zewnętrzna DDL odnosi się do zewnętrznego źródła danych. Zewnętrzne źródło danych określa mapowania fragmentów, co zapewnia tabeli zewnętrznej informacje niezbędne do zlokalizowania wszystkich baz danych w warstwie danych.

### <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Użytkownicy z dostępem do tabeli zewnętrznej automatycznie uzyskać dostęp do podstawowych tabel zdalnych, w obszarze poświadczenia podane w definicji zewnętrznego źródła danych. Należy unikać niepożądane podniesienia uprawnień za pomocą poświadczeń z zewnętrznym źródłem danych. Na użytek GRANT lub REVOKE tabeli zewnętrznej po prostu tak, jakby była zwykłą tabelę.  

Po zdefiniowaniu zewnętrznym źródle danych i tabel zewnętrznych teraz umożliwia pełne języka T-SQL za pośrednictwem tabel zewnętrznych.

## <a name="example-querying-horizontal-partitioned-databases"></a>Przykład: wykonywanie zapytań poziome podzielone na partycje bazy danych

Następujące zapytanie wykonuje trzy kierunkową sprzężenie magazynów, zamówień i kolejności wierszy i korzysta z kilku wartości zagregowanych i selektywne filtru. Przyjęto założenie, partycjonowania (1) poziome (fragmentowanie) i (2) czy magazyny, zamówień i kolejności wierszy są podzielonej na fragmenty według kolumny identyfikatora magazynu i elastyczne zapytanie może kolokuj sprzężeń na fragmenty i przetworzyć kosztowne części zapytania na fragmenty w równoległe.

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

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Przechowywane procedury zdalne wykonywanie kodu T-SQL: sp\_execute_remote

Elastyczne zapytanie wprowadza również procedury przechowywanej, która zapewnia bezpośredni dostęp do fragmenty. Jest wywoływana procedura składowana [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) i może służyć do wykonywania procedur składowanych zdalnego lub kod T-SQL na zdalne bazy danych. Jego przyjmuje następujące parametry:

* Nazwa źródła danych (nvarchar): Nazwa zewnętrznego źródła danych typu RDBMS.
* Zapytanie (nvarchar): Zapytania T-SQL do wykonania w poszczególnych fragmentach.
* Deklaracja parametru (nvarchar) — opcjonalne: Ciąg z danymi definicje typów dla parametrów używanych w parametrze zapytania (na przykład sp_executesql).
* Lista wartości parametrów - opcjonalne: Rozdzielana przecinkami lista wartości parametrów (na przykład sp_executesql).

PS\_wykonania\_zdalnego używa zewnętrzne źródło danych podane parametrów wywołania do wykonania danej instrukcji języka T-SQL na zdalnym baz danych. Łączenie się z bazy danych Menedżera shardmap i zdalnymi bazami danych używa poświadczeń do zewnętrznego źródła danych.  

Przykład:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Zwykłe ciągi połączeń programu SQL Server umożliwia łączenie aplikacji z narzędzi integracji danych i analizy Biznesowej w bazie danych przy użyciu własnych definicji tabeli zewnętrznej. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla swojego narzędzia. Odwołanie do bazy danych zapytania elastycznego, takich jak każdej innej bazy danych programu SQL Server dołączenie do narzędzie i tabel zewnętrznych do użycia z narzędzia lub aplikacji tak, jakby były lokalne tabele.

## <a name="best-practices"></a>Najlepsze praktyki

* Upewnij się, że baza danych punktu końcowego elastyczne zapytanie ma dostęp w bazie danych shardmap oraz wszystkich fragmentów za pośrednictwem zapór usługi SQL DB.  
* Sprawdzanie poprawności lub wymusić dystrybucję danych zdefiniowane przez tabeli zewnętrznej. Jeśli danej dystrybucji rzeczywistych danych różni się od dystrybucji określone w definicji tabeli, zapytania może przynieść nieoczekiwane wyniki.
* Zapytanie elastyczne aktualnie nie wykonuje eliminacji fragmentów podczas predykatów za pośrednictwem klucz fragmentowania pozwala bezpiecznie wyłączenie niektórych fragmentów z przetwarzania.
* Elastyczne zapytanie działa najlepiej dla zapytań gdzie większość obliczeń może odbywać się na fragmenty. Zazwyczaj można pobrać najlepszą wydajność zapytań za pomocą predykatów selektywne filtru, które mogą być obliczane na fragmenty lub sprzężeń za pośrednictwem partycjonowania kluczy, które mogą być wykonywane w sposób wyrównany do partycji na wszystkich fragmentów. Inne wzorce zapytań może być konieczne ładowanie dużych ilości danych z fragmentami z węzłem głównym i może działać nieprawidłowo

## <a name="next-steps"></a>Kolejne kroki

* Omówienie zapytanie elastyczne, zobacz [omówienie zapytania elastycznego](sql-database-elastic-query-overview.md).
* Pionowe samouczek partycjonowania, zobacz [rozpoczęcie pracy z bazami danych zapytań (partycje pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Składnia i przykładowe zapytania dotyczące danych partycjonowanych w pionie, można zobaczyć [zapytań w pionie partycjonowania danych)](sql-database-elastic-query-vertical-partitioning.md)
* Samouczek partycjonowania poziomego (fragmentowania), zobacz [wprowadzenie do zapytań elastycznych dla partycjonowania poziomego (fragmentowania)](sql-database-elastic-query-getting-started.md).
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury przechowywanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej usługi Azure SQL Database lub zestawu baz danych, służąc jako fragmentów w poziomie schematu partycjonowania.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
