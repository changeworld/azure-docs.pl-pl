---
title: Raportowanie dla baz danych w chmurze skalowalnych w poziomie | Dokumentacja firmy Microsoft
description: jak skonfigurować elastycznej zapytań za pośrednictwem poziomych partycji
services: sql-database
documentationcenter: ''
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: dd2219c01f194dad658d08a01f5f525640ef1c7c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Raportowanie w chmurze skalowalnych w poziomie bazy danych (wersja zapoznawcza)
![Wysyłanie zapytań na odłamków][1]

Bazy danych podzielonej rozpowszechniają wiersze danych skalowanej warstwy. Schemat jest takie same na wszystkich uczestniczących baz danych, nazywany również poziomych partycji. Przy użyciu elastycznej kwerendy, możesz utworzyć raporty, obejmujące wszystkie bazy danych podzielonej bazy danych.

Aby uzyskać szybki start, zobacz [raportowania dla baz danych w chmurze skalowalnych w poziomie](sql-database-elastic-query-getting-started.md).

Podzielonej baz danych, zobacz [zapytania dla baz danych chmury z różnych schematach](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Wymagania wstępne
* Tworzenie mapy niezależnego fragmentu za pomocą biblioteki klienta elastycznej bazy danych. zobacz [zarządzania mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md). Lub użyj przykładową aplikację w [wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).
* Zobacz też [migracji istniejących baz danych do baz danych skalowalnych w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md).
* Użytkownik musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączany uprawnienie ALTER DATABASE.
* Aby odwołać się do źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.

## <a name="overview"></a>Przegląd
Te instrukcje tworzenia reprezentację metadanych warstwę danych podzielonej w elastycznej kwerendy bazy danych. 

1. [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
4. [TWORZENIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 utworzyć klucz główny bazy danych i poświadczeń
Poświadczenie jest używany przez elastycznej zapytania do nawiązania połączenia zdalnego baz danych.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Upewnij się, że *"\<username\>"* nie zawiera żadnych *"@servername"* sufiks. 
> 
> 

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

Zewnętrzne źródło danych odwołuje się do mapy niezależnego fragmentu. Elastyczne zapytanie używa następnie zewnętrznego źródła danych i podstawowej mapowanie niezależnych wyliczyć baz danych, które uczestniczą w warstwie danych. Tych samych poświadczeń są używane, można odczytać mapy niezależnego fragmentu i uzyskiwanie dostępu do danych na odłamków podczas przetwarzania zapytania elastycznej. 

## <a name="13-create-external-tables"></a>1.3 tworzenia tabel zewnętrznych
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

Pobieranie listy tabel zewnętrznych z bieżącej bazy danych: 

    SELECT * from sys.external_tables; 

Aby porzucić tabel zewnętrznych:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Uwagi
DANE\_źródło klauzuli definiuje zewnętrznego źródła danych (mapy niezależnego fragmentu) używany do tabeli zewnętrznej.  

Schemat\_nazwy i obiektu\_klauzule nazwę mapowania definicji tabeli zewnętrznej do tabeli w innym schematem. Pominięcie schematu obiektu zdalnego zakłada się, że "właściciela" i przyjęto, że jego nazwa jest taka sama jak nazwa tabeli zewnętrznej definiowanego. Jest to przydatne, zajętej nazwę zdalnego tabeli w bazie danych której chcesz utworzyć tabeli zewnętrznej. Na przykład aby zdefiniować tabeli zewnętrznej można pobrać zagregowany widok widoków wykazu lub widoków DMV skalowanej danych w warstwie. Ponieważ widoków wykazu i widoków DMV już istnieje lokalnie, nie można używać ich nazwy do definicji tabeli zewnętrznej. Zamiast tego należy użyć innej nazwy i użyj widoku wykazu lub DMV nazwie w SCHEMACIE\_nazwę i/lub obiekt\_klauzule nazwy. (Zobacz poniższy przykład). 

W klauzuli dystrybucji określa rozkład danych używany dla tej tabeli. Procesor zapytań korzysta z informacjami podanymi w klauzuli dystrybucji do tworzenia najbardziej efektywny plany zapytań.  

1. **PODZIELONEJ** oznacza danych jest poziomo podzielonym na partycje w baz danych. Klucz partycjonowania danych dystrybucji jest **< sharding_column_name >** parametru.
2. **REPLIKOWANE** oznacza, że identycznych kopii tabeli znajdują się w każdej bazie danych. Jest obowiązek upewnij się, że repliki są identyczne na baz danych.
3. **ROUND\_działania OKRĘŻNEGO** oznacza, że tabela jest partycjonowana poziomie za pomocą metody dystrybucji aplikacji zależnych. 

**Odwołanie do warstwy danych**: tabeli zewnętrznej DDL odwołuje się do zewnętrznego źródła danych. Zewnętrzne źródło danych określa mapy niezależnego fragmentu, która udostępnia informacje niezbędne do zlokalizowania wszystkich baz danych w warstwę danych tabeli zewnętrznej. 

### <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami
Użytkownicy z dostępem do tabeli zewnętrznej automatycznie uzyskują dostęp do tabel zdalnym w obszarze poświadczenia podane w definicji źródła danych zewnętrznych. Unikaj niepożądane podniesienia uprawnień przy użyciu poświadczeń do zewnętrznego źródła danych. Użyj GRANT lub REVOKE dla tabeli zewnętrznej, tak jakby był on zwykłą tabelę.  

Po zdefiniowaniu zewnętrznym źródle danych i tabele zewnętrzne mogą teraz używać pełnej T-SQL w tabelach zewnętrznych.

## <a name="example-querying-horizontal-partitioned-databases"></a>Przykład: badanie poziome partycjonowane baz danych
Następujące zapytanie wykonuje sprzężenie trzystopniowo magazynów, zamówienia i kolejność wierszy i używa kilku wartości zagregowanych i selektywnego filtru. Zakłada się (1) poziomych partycji (dzielenia na fragmenty) i (2) czy magazyny, zamówienia i kolejność wierszy są podzielonej w kolumnie Identyfikator magazynu i elastycznej zapytania można kolokuj sprzężenia na odłamków i przetworzyć kosztowne części zapytania na odłamków w równoległe. 

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

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Przechowywana procedura zdalne wykonywanie kodu T-SQL: sp\_execute_remote
Elastyczne zapytania wprowadza również procedury przechowywanej, która zapewnia bezpośredni dostęp do fragmentów. Procedura składowana jest nazywany [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) i może służyć do wykonania zdalnego procedur składowanych lub kod T-SQL na zdalnym baz danych. Go przyjmuje następujące parametry: 

* Nazwa źródła danych (nvarchar): Nazwa źródła danych zewnętrznych typu RDBMS. 
* Zapytania (nvarchar): zapytania T-SQL do wykonania na każdej niezależnego fragmentu. 
* Deklaracja parametru (nvarchar) - opcjonalne: ciąg zawierający definicje typów danych parametrów parametr zapytania (na przykład sp_executesql). 
* Lista wartości parametrów - opcjonalne: rozdzielana przecinkami lista wartości parametrów (na przykład sp_executesql).

PS\_wykonania\_zdalnego używa zewnętrzne źródło danych podane w parametrów wywołania do wykonania danej instrukcji T-SQL na zdalnym baz danych. Łączenie się z bazy danych Menedżera shardmap i zdalnymi bazami danych używa poświadczeń do zewnętrznego źródła danych.  

Przykład: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Połączenie narzędzi
Użyj parametrów połączenia z regularnych programu SQL Server, aby połączyć aplikację, narzędziami integracji BI i dane do bazy danych z definicji tabeli zewnętrznej. Upewnij się, czy program SQL Server jest obsługiwana jako źródło danych dla własnych narzędzi. Następnie odwołanie elastycznej kwerendy bazy danych, podobnie jak wszystkie inne bazy danych programu SQL Server połączone z narzędzia i użyj tabel zewnętrznych z narzędzia lub aplikacji tak, jakby były lokalne tabele. 

## <a name="best-practices"></a>Najlepsze praktyki
* Upewnij się, że elastycznej kwerendy bazy danych punktu końcowego ma dostęp do bazy danych shardmap i wszystkie odłamków za pośrednictwem zapór bazy danych SQL.  
* Sprawdza, czy lub wymusić dystrybucji danych, wynika z tabeli zewnętrznej. Jeśli dystrybucji rzeczywiste dane różni się od dystrybucji określone w definicji tabeli, kwerend może dać nieoczekiwane wyniki. 
* Elastyczne zapytania aktualnie nie wykonuje eliminacji niezależnych podczas predykaty za pośrednictwem klucza dzielenia na fragmenty pozwala bezpiecznie wykluczyć pewne odłamków z przetwarzania.
* Zapytania elastycznej działa najlepiej dla zapytań gdzie na odłamków można wykonać większość obliczenia. Zwykle uzyskać najlepszą wydajność zapytań z predykatu filtru selektywnego, które może przyjąć odłamków lub sprzężenia za pośrednictwem partycjonowania kluczy, które mogą być wykonywane w sposób wyrównany do partycji na wszystkich fragmentów. Innymi wzorcami zapytań może być konieczne ładowania dużych ilości danych z fragmentów do węzła głównego i mogą działać nieprawidłowo

## <a name="next-steps"></a>Kolejne kroki

* Omówienie elastycznej zapytania, zobacz [elastycznej zapytań — omówienie](sql-database-elastic-query-overview.md).
* Samouczek partycjonowania pionowego, zobacz [wprowadzenie do korzystania z bazy danych między kwerendy (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Dla zapytań dotyczących danych pionowo podzielonym na partycje i składnię i przykład, zobacz [zapytań w pionie na partycje danych)](sql-database-elastic-query-vertical-partitioning.md)
* Samouczek poziomych partycji (dzielenia na fragmenty), zobacz [wprowadzenie elastycznej zapytania poziome partycjonowania (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL w jednym zdalnej bazy danych SQL Azure lub zestaw baz danych, służąc jako odłamków w poziomie schemat partycjonowania.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
