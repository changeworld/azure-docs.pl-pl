---
title: Zapytanie dla baz danych chmury z innym schematem | Dokumentacja firmy Microsoft
description: jak skonfigurować zapytań między bazami danych za pośrednictwem partycji pionowej
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: 243110e47ab7c98c9fec9b2747fde73ccb775fbc
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Zapytanie dla baz danych chmury z różnych schematach (wersja zapoznawcza)
![Zapytanie między tabelami w różnych baz danych][1]

W pionie na partycje bazy danych używać różnych zestawów tabel na różnych baz danych. Oznacza to, że schemat jest różne na różnych baz danych. Na przykład wszystkie tabele spisu są na jedną bazę danych, gdy wszystkie tabele powiązane ewidencjonowania aktywności znajdują się w drugiej bazy danych. 

## <a name="prerequisites"></a>Wymagania wstępne
* Użytkownik musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączany uprawnienie ALTER DATABASE.
* Aby odwołać się do źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.

## <a name="overview"></a>Przegląd

> [!NOTE]
> W odróżnieniu od z partycjonowania poziomy tych instrukcji DDL nie zależą od Definiowanie warstwą danych z mapą niezależnego fragmentu za pomocą biblioteki klienta elastycznej bazy danych.
>

1. [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
4. [TWORZENIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Tworzenie klucza głównego bazy danych i poświadczeń
Poświadczenie jest używany przez elastycznej zapytania do nawiązania połączenia zdalnego baz danych.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Upewnij się, że `<username>` nie zawiera żadnych **"@servername"** sufiks. 
>

## <a name="create-external-data-sources"></a>Tworzenie zewnętrznych źródeł danych
Składnia:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> Parametr typu musi być ustawiony na **RDBMS**. 
>

### <a name="example"></a>Przykład
Poniższy przykład przedstawia użycie instrukcji CREATE dla zewnętrznych źródeł danych. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Aby pobrać listę bieżących źródeł danych zewnętrznych: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabele zewnętrzne
Składnia:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Przykład
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

Poniższy przykład pokazuje, jak pobrać listę tabel zewnętrznych z bieżącej bazy danych: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Uwagi
Elastyczne zapytania rozszerza istniejący składni tabeli zewnętrznej do definiowania tabel zewnętrznych, korzystających z zewnętrznych źródeł danych typu RDBMS. Definicja tabeli zewnętrznej dla partycjonowanie pionowe obejmuje następujące aspekty: 

* **Schemat**: tabeli zewnętrznej DDL definiuje schemat, który można użyć zapytań. Wybrany schemat w definicji tabeli zewnętrznej musi pasuje do schematu tabel w zdalnej bazy danych, gdzie są przechowywane dane. 
* **Odwołanie do zdalnej bazy danych**: tabeli zewnętrznej DDL odwołuje się do zewnętrznego źródła danych. Zewnętrzne źródło danych określa nazwy serwera logicznego i bazy danych, zdalnego przechowywania danych rzeczywistych tabeli bazy danych. 

Składnia służąca do tworzenia tabel zewnętrznych za pomocą zewnętrznego źródła danych, zgodnie z opisem w poprzedniej sekcji, jest następujący: 

W klauzuli DATA_SOURCE definiuje zewnętrznego źródła danych (tj. zdalnej bazy danych w przypadku partycjonowanie pionowe) używany do tabeli zewnętrznej.  

Klauzule SCHEMA_NAME i OBJECT_NAME zapewniają możliwość mapowania definicji tabeli zewnętrznej tabeli do innego schematu na zdalnej bazy danych lub tabeli o innej nazwie, odpowiednio. Jest to przydatne, jeśli chcesz zdefiniować tabelę zewnętrzną widoku wykazu lub DMV na zdalnej bazy danych — lub w innej sytuacji, gdy nazwa tabeli zdalnej jest już zajęta lokalnie.  

Następująca instrukcja DDL porzuca istniejącej definicji tabeli zewnętrznej z katalogu lokalnego. Nie ma ona wpływu zdalnej bazy danych. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Uprawnienia CREATE/DROP tabeli zewnętrznej**: potrzebne są uprawnienia ALTER ANY zewnętrznego źródła danych dla tabeli zewnętrznej DDL, który również jest wymagany do odwoływania się do źródła danych.  

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami
Użytkownicy z dostępem do tabeli zewnętrznej automatycznie uzyskują dostęp do tabel zdalnym w obszarze poświadczenia podane w definicji źródła danych zewnętrznych. Aby zapobiec niepożądanemu podniesienia uprawnień przy użyciu poświadczeń do zewnętrznego źródła danych należy starannie zarządzanie dostępem do tabeli zewnętrznej. Regularne SQL uprawnienia można PRZYDZIELIĆ lub ODWOŁAĆ dostęp do tabeli zewnętrznej, tak jakby był on zwykłą tabelę.  

## <a name="example-querying-vertically-partitioned-databases"></a>Przykład: zapytanie w pionie na partycje baz danych
Następujące zapytanie wykonuje trzystopniowego sprzężenie dwóch tabel lokalnych dla zleceń i kolejność wierszy w tabeli zdalnej dla klientów. Jest to przykład przypadek użycia danych odwołania dla elastycznej zapytania: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Przechowywana procedura zdalne wykonywanie kodu T-SQL: sp\_execute_remote
Elastyczne zapytania wprowadza również procedury przechowywanej, która zapewnia bezpośredni dostęp do zdalnej bazy danych. Procedura składowana jest nazywany [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) i może służyć do wykonania zdalnego procedur składowanych lub kod T-SQL na ze zdalną bazą danych. Go przyjmuje następujące parametry: 

* Nazwa źródła danych (nvarchar): Nazwa źródła danych zewnętrznych typu RDBMS. 
* Zapytania (nvarchar): zapytania T-SQL do wykonania na ze zdalną bazą danych. 
* Deklaracja parametru (nvarchar) - opcjonalne: ciąg zawierający definicje typów danych parametrów parametr zapytania (na przykład sp_executesql). 
* Lista wartości parametrów - opcjonalne: rozdzielana przecinkami lista wartości parametrów (na przykład sp_executesql).

PS\_wykonania\_zdalnego korzysta z zewnętrznym źródłem danych w parametrów wywołania do wykonania danej instrukcji T-SQL na ze zdalną bazą danych. Aby nawiązać połączenie ze zdalną bazą danych używa poświadczeń do zewnętrznego źródła danych.  

Przykład: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 



## <a name="connectivity-for-tools"></a>Połączenie narzędzi
Prawidłowe parametry połączenia SQL Server umożliwia połączenia narzędzi integracyjnych BI i danych do baz danych na serwerze bazy danych SQL, który ma elastycznej zapytania włączone i tabel zewnętrznych zdefiniowane. Upewnij się, czy program SQL Server jest obsługiwana jako źródło danych dla własnych narzędzi. Następnie zobacz kwerendy elastycznej bazy danych i jego tabele zewnętrzne, podobnie jak wszystkie inne bazy danych SQL Server, które można połączyć się z narzędziem. 

## <a name="best-practices"></a>Najlepsze praktyki
* Upewnij się, że elastycznej kwerendy bazy danych punktu końcowego ma dostęp do zdalnej bazy danych przez umożliwienie dostępu do usług platformy Azure w konfiguracji zapory bazy danych SQL. Upewnij się również, że poświadczenia podane w definicji źródła danych zewnętrznych może pomyślnie zalogować się do zdalnej bazy danych i ma uprawnienia dostępu do tabeli zdalnej.  
* Zapytania elastycznej działa najlepiej dla zapytań gdzie na zdalnych baz danych można wykonać większość obliczenia. Zwykle uzyskać najlepszą wydajność zapytań z predykatu filtru selektywnego, które można oszacować na zdalnych baz danych lub sprzężenia, które mogą być wykonywane całkowicie na zdalnej bazy danych. Innymi wzorcami zapytań może być konieczne ładowania dużych ilości danych ze zdalną bazą danych i mogą działać nieprawidłowo. 

## <a name="next-steps"></a>Kolejne kroki

* Omówienie elastycznej zapytania, zobacz [elastycznej zapytań — omówienie](sql-database-elastic-query-overview.md).
* Samouczek partycjonowania pionowego, zobacz [wprowadzenie do korzystania z bazy danych między kwerendy (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Samouczek poziomych partycji (dzielenia na fragmenty), zobacz [wprowadzenie elastycznej zapytania poziome partycjonowania (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).
* Dla zapytań dotyczących danych poziomie podzielonym na partycje i składnię i przykład, zobacz [zapytań w poziomie na partycje danych)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL w jednym zdalnej bazy danych SQL Azure lub zestaw baz danych, służąc jako odłamków w poziomie schemat partycjonowania.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
