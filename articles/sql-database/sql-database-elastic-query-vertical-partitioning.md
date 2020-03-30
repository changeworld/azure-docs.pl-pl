---
title: Wykonywanie zapytań w bazach danych w chmurze z innym schematem
description: jak skonfigurować kwerendy między bazami danych na partycjach pionowych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d5983d25685242a696300f293231bbf987e8442d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823731"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Wykonywanie zapytań w bazach danych w chmurze z różnymi schematami (wersja zapoznawcza)

![Wykonywanie zapytań między tabelami w różnych bazach danych][1]

Bazy danych podzielonych pionowo używają różnych zestawów tabel w różnych bazach danych. Oznacza to, że schemat jest inny w różnych bazach danych. Na przykład wszystkie tabele zapasów znajdują się w jednej bazie danych, podczas gdy wszystkie tabele związane z księgowością znajdują się w drugiej bazie danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Użytkownik musi posiadać alter wszelkie uprawnienia zewnętrznego źródła danych. To uprawnienie jest dołączone do uprawnienia ALTER DATABASE.
* ALTER wszelkie uprawnienia zewnętrznego źródła danych są potrzebne do odwoływania się do źródłowego źródła danych.

## <a name="overview"></a>Omówienie

> [!NOTE]
> W przeciwieństwie do partycjonowania poziomego te instrukcje DDL nie zależą od definiowania warstwy danych z mapą niezależnego fragmentu za pośrednictwem biblioteki klienta elastycznej bazy danych.
>

1. [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx)
2. [TWORZENIE POŚWIADCZEŃ O ZAKRESIE BAZY DANYCH](https://msdn.microsoft.com/library/mt270260.aspx)
3. [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
4. [TWORZENIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Tworzenie klucza głównego i poświadczeń o zakresie bazy danych

Poświadczenia są używane przez kwerendę elastyczną do łączenia się ze zdalnymi bazami danych.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Upewnij się, że `<username>` nie zawiera żadnych **sufiksów "nazwa\@serwera".** 
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
> Parametr TYPE musi być ustawiony na **RDBMS**. 
>

### <a name="example"></a>Przykład

Poniższy przykład ilustruje użycie CREATE instrukcji dla zewnętrznych źródeł danych. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Aby pobrać listę bieżących zewnętrznych źródeł danych: 

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

W poniższym przykładzie pokazano, jak pobrać listę tabel zewnętrznych z bieżącej bazy danych: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Uwagi

Kwerenda elastyczna rozszerza istniejącą składnię tabeli zewnętrznej w celu zdefiniowania tabel zewnętrznych korzystających z zewnętrznych źródeł danych typu RDBMS. Definicja tabeli zewnętrznej dla partycjonowania pionowego obejmuje następujące aspekty: 

* **Schemat:** Tabela zewnętrzna DDL definiuje schemat, którego mogą używać kwerendy. Schemat podany w definicji tabeli zewnętrznej musi być zgodny ze schematem tabel w zdalnej bazie danych, w której przechowywane są rzeczywiste dane. 
* **Odwołanie do zdalnej bazy danych**: Tabela zewnętrzna DDL odnosi się do zewnętrznego źródła danych. Zewnętrzne źródło danych określa nazwę serwera bazy danych SQL i nazwę bazy danych zdalnej bazy danych, w której są przechowywane rzeczywiste dane tabeli. 

Przy użyciu zewnętrznego źródła danych, jak opisano w poprzedniej sekcji, składnia do tworzenia tabel zewnętrznych jest następująca: 

Klauzula DATA_SOURCE definiuje zewnętrzne źródło danych (tj. zdalną bazę danych w przypadku partycjonowania pionowego), która jest używana dla tabeli zewnętrznej.  

Klauzule SCHEMA_NAME i OBJECT_NAME umożliwiają mapowanie definicji tabeli zewnętrznej do tabeli w innym schemacie w zdalnej bazie danych lub odpowiednio do tabeli o innej nazwie. Jest to przydatne, jeśli chcesz zdefiniować tabelę zewnętrzną do widoku katalogu lub DMV w zdalnej bazie danych — lub w innej sytuacji, w której nazwa tabeli zdalnej jest już zajęta lokalnie.  

Następująca instrukcja DDL porzuca istniejącą definicję tabeli zewnętrznej z katalogu lokalnego. Nie ma to wpływu na zdalną bazę danych. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Uprawnienia do tworzenia/upuszczania tabeli zewnętrznej:** zmienianie wszelkich uprawnień zewnętrznego źródła danych jest potrzebne dla tabeli zewnętrznej DDL, która jest również potrzebna do odwoływania się do źródłowego źródła danych.  

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Użytkownicy z dostępem do tabeli zewnętrznej automatycznie uzyskują dostęp do podstawowych tabel zdalnych w ramach poświadczeń podanych w definicji zewnętrznego źródła danych. Należy starannie zarządzać dostępem do tabeli zewnętrznej, aby uniknąć niepożądanego podniesienia uprawnień za pośrednictwem poświadczeń zewnętrznego źródła danych. Regularne uprawnienia SQL mogą służyć do udzielania lub cofania dostępu do tabeli zewnętrznej, tak jakby była to zwykła tabela.  

## <a name="example-querying-vertically-partitioned-databases"></a>Przykład: wykonywanie zapytań o bazy danych podzielonych pionowo

Poniższa kwerenda wykonuje trójdrożne sprzężenie między dwiema tabelami lokalnymi dla zamówień i wierszy zamówienia oraz tabelą zdalną dla klientów. Jest to przykład przypadku użycia danych referencyjnych dla zapytania elastycznego: 

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

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedura składowana dla zdalnego\_wykonywania T-SQL: sp execute_remote

Zapytanie elastyczne wprowadza również procedurę składowaną, która zapewnia bezpośredni dostęp do zdalnej bazy danych. Procedura składowana jest wywoływana [sp\_wykonać \_zdalnie](https://msdn.microsoft.com/library/mt703714) i może służyć do wykonywania zdalnych procedur składowanych lub kodu T-SQL na zdalnej bazie danych. Przyjmuje następujące parametry: 

* Nazwa źródła danych (nvarchar): nazwa zewnętrznego źródła danych typu RDBMS. 
* Query (nvarchar): kwerenda T-SQL, która ma być wykonywana w zdalnej bazie danych. 
* Deklaracja parametrów (nvarchar) - opcjonalnie: Ciąg z definicjami typów danych dla parametrów używanych w parametrze Query (takich jak sp_executesql). 
* Lista wartości parametrów - opcjonalnie: Oddzielona przecinkami lista wartości parametrów (takich jak sp_executesql).

Sp\_execute\_remote używa zewnętrznego źródła danych podanego w parametrach wywołania do wykonania podanej instrukcji T-SQL w zdalnej bazie danych. Używa poświadczeń zewnętrznego źródła danych do łączenia się ze zdalną bazą danych.  

Przykład: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Zwykłych ciągów połączeń programu SQL Server można używać do łączenia narzędzi bi i integracji danych z bazami danych na serwerze bazy danych SQL, na których włączono kwerendę elastyczną i zdefiniowano tabele zewnętrzne. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla narzędzia. Następnie należy odwołać się do bazy danych zapytań elastycznych i jej tabel zewnętrznych, podobnie jak do każdej innej bazy danych programu SQL Server, z którą można połączyć się z narzędziem. 

## <a name="best-practices"></a>Najlepsze rozwiązania

* Upewnij się, że baza danych punktu końcowego zapytania elastycznego uzyskała dostęp do zdalnej bazy danych, włączając dostęp dla usług platformy Azure w konfiguracji zapory bazy danych SQL. Upewnij się również, że poświadczenia podane w definicji zewnętrznego źródła danych mogą pomyślnie zalogować się do zdalnej bazy danych i mieć uprawnienia dostępu do tabeli zdalnej.  
* Zapytanie elastyczne działa najlepiej w przypadku kwerend, w których większość obliczeń można wykonać w zdalnych bazach danych. Zazwyczaj otrzymujesz najlepszą wydajność kwerendy z predykatami filtrów selektywnych, które mogą być oceniane w zdalnych bazach danych lub sprzężeniach, które można wykonać całkowicie w zdalnej bazie danych. Inne wzorce kwerend może być konieczne załadować duże ilości danych ze zdalnej bazy danych i może działać słabo. 

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem kwerendy elastycznej, zobacz [omówienie kwerendy elastycznej](sql-database-elastic-query-overview.md).
* Aby uzyskać samouczek partycjonowania pionowego, zobacz [Wprowadzenie do kwerendy między bazami danych (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Aby uzyskać poziome partycjonowanie (dzielenie na fragmenty) samouczek, zobacz [Wprowadzenie do elastycznego zapytania do partycjonowania poziomego (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).
* Aby uzyskać składnię i przykładowe kwerendy dotyczące danych podzielonych poziomo na partycje, zobacz Wykonywanie zapytań o [dane podzielone na partycje w poziomie)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [\_sp \_wykonać zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej bazy danych SQL azure lub zestaw baz danych służących jako fragmenty w schemat partycjonowania poziomego.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
