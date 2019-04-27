---
title: Zapytania w bazach danych w chmurze z różnymi schematami | Dokumentacja firmy Microsoft
description: sposób konfigurowania wielu baz danych za pośrednictwem partycje pionowe
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein
manager: digimobile
origin.date: 01/25/2019
ms.date: 02/25/2019
ms.openlocfilehash: e7ba8057cd22c5cc1080b4a6d95f17bf76d4acb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585429"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Wykonywanie zapytań w bazach danych w chmurze z różnymi schematami (wersja zapoznawcza)

![Wykonywanie zapytań względem tabel w różnych bazach danych][1]

Partycje pionowe bazy danych używać różnych zestawów tabel z różnymi bazami danych. Oznacza to, że schemat różni się w różnych bazach danych. Na przykład wszystkie tabele spisu znajdują się na jedną bazę danych podczas, gdy wszystkie tabele powiązane księgowości znajdują się w drugiej bazy danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Użytkownik musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączony do uprawnienie ALTER DATABASE.
* Aby odwołać się do bazowego źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.

## <a name="overview"></a>Omówienie

> [!NOTE]
> W odróżnieniu od z partycjami poziomymi, tych instrukcji DDL nie są zależne od definiowania warstwą danych za pomocą mapowania fragmentów w postaci za pomocą biblioteki klienckiej elastycznej bazy danych.
>

1. [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx)
2. [TWORZENIE BAZY DANYCH O OKREŚLONYM ZAKRESIE POŚWIADCZEŃ](https://msdn.microsoft.com/library/mt270260.aspx)
3. [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
4. [TWORZENIE ZEWNĘTRZNEJ TABELI](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Utwórz klucz główny bazy danych i poświadczeń

Poświadczenie jest używane przez zapytanie elastyczne, aby nawiązać połączenie zdalne bazy danych.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Upewnij się, że `<username>` nie zawiera żadnych **"\@servername"** sufiks. 
>

## <a name="create-external-data-sources"></a>Tworzenie zewnętrznych źródeł danych

Składnia:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = '<fully_qualified_server_name>',
                DATABASE_NAME = '<remote_database_name>',  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> Parametr typu musi być równa **RDBMS**. 
>

### <a name="example"></a>Przykład

Poniższy przykład ilustruje użycie instrukcji CREATE dla zewnętrznych źródeł danych. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.chinacloudapi.cn', 
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

```sql
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
```

Poniższy przykład pokazuje, jak pobrać listę tabel zewnętrznych z bieżącej bazy danych: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Uwagi

Elastyczne zapytanie rozszerza istniejący składni tabeli zewnętrznej do definiowania tabel zewnętrznych używających zewnętrznych źródeł danych typu RDBMS. Partycjonowanie pionowe definicji tabeli zewnętrznej obejmuje następujące aspekty: 

* **Schemat**: Tabela zewnętrzna DDL definiuje schemat, który można użyć zapytań. Podany w definicji tabeli zewnętrznej schemat musi być zgodna schematu tabel w zdalnej bazy danych, gdzie są przechowywane rzeczywistych danych. 
* **Odwołanie do zdalnej bazy danych**: Tabela zewnętrzna DDL odnosi się do zewnętrznego źródła danych. Zewnętrzne źródło danych określa nazwę serwera bazy danych SQL i zdalnej bazy danych, której są przechowywane dane tabeli rzeczywista nazwa bazy danych. 

Składnia służąca do tworzenia tabel zewnętrznych za pomocą zewnętrznego źródła danych, zgodnie z opisem w poprzedniej sekcji, jest następujący: 

Klauzula DATA_SOURCE definiuje zewnętrznego źródła danych (czyli zdalnej bazy danych w przypadku partycjonowania pionowego) używany do tabeli zewnętrznej.  

Klauzule SCHEMA_NAME i OBJECT_NAME zapewniają możliwość mapowania definicji tabeli zewnętrznej tabeli do innego schematu na zdalnej bazy danych lub tabeli o innej nazwie, odpowiednio. Jest to przydatne, jeśli chcesz zdefiniować tabelę zewnętrzną do widoku wykazu lub widok DMV na zdalnej bazy danych — lub w innej sytuacji, gdy nazwa tabeli zdalnej jest już zajęta lokalnie.  

Poniższa instrukcja DDL odrzuca istniejącej definicji tabeli zewnętrznej z katalogu lokalnego. Nie ma wpływu na zdalnej bazy danych. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Uprawnienia dla polecenia CREATE/DROP tabeli zewnętrznej**: Dla tabeli zewnętrznej DDL, który jest również wymagany do odwoływania się do bazowego źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.  

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Użytkownicy z dostępem do tabeli zewnętrznej automatycznie uzyskać dostęp do podstawowych tabel zdalnych, w obszarze poświadczenia podane w definicji zewnętrznego źródła danych. Aby zapobiec niepożądanemu podniesienia uprawnień za pomocą poświadczeń z zewnętrznym źródłem danych, należy dokładnie zarządzać dostępem do tabeli zewnętrznej. Regularne uprawnienia SQL można udzielić lub ODWOŁAĆ dostęp do tabeli zewnętrznej po prostu tak, jakby była zwykłą tabelę.  

## <a name="example-querying-vertically-partitioned-databases"></a>Przykład: wykonywanie zapytań w pionie na partycje bazy danych

Następujące zapytanie wykonuje trzy kierunkową sprzężenie dwóch tabel lokalnych dla zamówienia i kolejności wierszy tabeli zdalnej dla klientów. Jest to przykład zapytania elastycznego w przypadku użycia danych odwołania: 

```sql
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
```

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Przechowywane procedury zdalne wykonywanie kodu T-SQL: sp\_execute_remote

Elastyczne zapytanie wprowadza również procedury przechowywanej, która zapewnia bezpośredni dostęp do zdalnej bazy danych. Jest wywoływana procedura składowana [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) i może służyć do wykonywania procedur składowanych zdalnego lub kod T-SQL w zdalnej bazy danych. Jego przyjmuje następujące parametry: 

* Nazwa źródła danych (nvarchar): Nazwa zewnętrznego źródła danych typu RDBMS. 
* Zapytanie (nvarchar): Zapytania T-SQL do wykonania na bazie zdalnego. 
* Deklaracja parametru (nvarchar) — opcjonalne: Ciąg z danymi definicje typów dla parametrów używanych w parametrze zapytania (na przykład sp_executesql). 
* Lista wartości parametrów - opcjonalne: Rozdzielana przecinkami lista wartości parametrów (na przykład sp_executesql).

PS\_wykonania\_zdalnego używa zewnętrzne źródło danych podane parametrów wywołania do wykonania danej instrukcji języka T-SQL w zdalnej bazy danych. Nawiązać połączenia ze zdalną bazą danych używa poświadczeń do zewnętrznego źródła danych.  

Przykład: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Zwykłe ciągi połączeń programu SQL Server służy do łączenia z narzędzi integracji danych i analizy Biznesowej dla baz danych na serwerze bazy danych SQL, który ma włączonego elastycznych zapytań i zdefiniowane tabele zewnętrzne. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla swojego narzędzia. Następnie zapoznaj się z bazą danych zapytania elastycznego i jego tabele zewnętrzne, podobnie jak wszystkie inne bazy danych SQL Server, można połączyć się z narzędziem. 

## <a name="best-practices"></a>Najlepsze praktyki

* Upewnij się, że baza danych punktu końcowego elastyczne zapytanie ma dostęp do zdalnej bazy danych poprzez umożliwienie dostępu dla usług platformy Azure w konfiguracji zapory bazy danych SQL. Upewnij się również, że poświadczenia podane w definicji źródła danych zewnętrznych może pomyślnie zalogować się do zdalnej bazy danych i ma uprawnienia dostępu do tabeli zdalnej.  
* Zapytanie elastyczne w usłudze sprawdza się najlepiej w zapytaniach gdzie większość obliczeń może odbywać się w zdalnych baz danych. Zazwyczaj można pobrać najlepszą wydajność zapytań za pomocą predykatów selektywne filtru, które mogą być obliczane na zdalne bazy danych lub sprzężenia, które mogą być wykonywane całkowicie w zdalnej bazy danych. Inne wzorce zapytań może wymagać obciążenia z dużymi ilościami danych ze zdalną bazą danych i może działać nieprawidłowo. 

## <a name="next-steps"></a>Kolejne kroki

* Omówienie zapytanie elastyczne, zobacz [omówienie zapytania elastycznego](sql-database-elastic-query-overview.md).
* Pionowe samouczek partycjonowania, zobacz [rozpoczęcie pracy z bazami danych zapytań (partycje pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Samouczek partycjonowania poziomego (fragmentowania), zobacz [wprowadzenie do zapytań elastycznych dla partycjonowania poziomego (fragmentowania)](sql-database-elastic-query-getting-started.md).
* Aby uzyskać składnię i przykładowe zapytania dotyczące danych partycjonowanej w poziomie, zobacz [zapytań w poziomie partycjonowania danych)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury przechowywanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej usługi Azure SQL Database lub zestawu baz danych, służąc jako fragmentów w poziomie schematu partycjonowania.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->