---
title: Wprowadzenie do zapytań między bazami danych
description: jak używać elastycznej kwerendy bazy danych z pionowo podzielonymi bazami danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: af93035766eaf1afa12d124b8379ee55c5567260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823794"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Wprowadzenie do zapytań między bazami danych (partycjonowanie pionowe) (wersja zapoznawcza)

Zapytanie elastycznej bazy danych (wersja zapoznawcza) dla usługi Azure SQL Database umożliwia uruchamianie zapytań T-SQL, które obejmują wiele baz danych przy użyciu jednego punktu połączenia. Ten artykuł dotyczy [pionowo podzielonych na partycje baz danych](sql-database-elastic-query-vertical-partitioning.md).  

Po zakończeniu: dowiedz się, jak skonfigurować i używać usługi Azure SQL Database do wykonywania zapytań, które obejmują wiele powiązanych baz danych.

Aby uzyskać więcej informacji na temat funkcji zapytania elastycznej bazy danych, zobacz [omówienie kwerendy elastycznej bazy danych usługi Azure SQL Database](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

ALTER WYMAGANE jest zezwolenie zewnętrzne źródło danych. To uprawnienie jest dołączone do uprawnienia ALTER DATABASE. ALTER wszelkie uprawnienia zewnętrznego źródła danych są potrzebne do odwoływania się do źródłowego źródła danych.

## <a name="create-the-sample-databases"></a>Tworzenie przykładowych baz danych

Na początek należy utworzyć dwie bazy danych, **Klienci** i **Zamówienia**, na tych samych lub różnych serwerach bazy danych SQL.

Wykonaj następujące kwerendy w bazie danych **Orders,** aby utworzyć tabelę **Informacje o zamówieniu** i wprowadzić przykładowe dane.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Teraz wykonaj następującą kwerendę w bazie danych **Klienci,** aby utworzyć tabelę **Informacje o kliencie** i wprowadzić przykładowe dane.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Tworzenie obiektów bazy danych

### <a name="database-scoped-master-key-and-credentials"></a>Klucz główny i poświadczenia o zakresie bazy danych

1. Otwórz program SQL Server Management Studio lub SQL Server Data Tools w programie Visual Studio.
2. Połącz się z bazą danych Orders i wykonaj następujące polecenia T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    "Nazwa użytkownika" i "hasło" powinny być nazwą użytkownika i hasłem używanym do logowania się do bazy danych klientów.
    Uwierzytelnianie przy użyciu usługi Azure Active Directory z zapytaniami elastycznymi nie jest obecnie obsługiwane.

### <a name="external-data-sources"></a>Zewnętrzne źródła danych

Aby utworzyć zewnętrzne źródło danych, wykonaj następujące polecenie w bazie danych Orders:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Tabele zewnętrzne

Utwórz tabelę zewnętrzną w bazie danych Zamówień, która jest zgodna z definicją tabeli Informacje o kliencie:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowej kwerendy T-SQL elastycznej bazy danych

Po zdefiniowaniu zewnętrznego źródła danych i tabel zewnętrznych można teraz używać funkcji T-SQL do wykonywania zapytań o tabele zewnętrzne. Wykonaj tę kwerendę w bazie danych Orders:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Koszty

Obecnie funkcja zapytania elastycznej bazy danych jest uwzględniona w koszcie bazy danych SQL platformy Azure.  

Aby uzyskać informacje o cenach, zobacz [Cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem kwerendy elastycznej, zobacz [omówienie kwerendy elastycznej](sql-database-elastic-query-overview.md).
* Aby uzyskać składnię i przykładowe kwerendy dotyczące danych podzielonych pionowo, zobacz [Wyszukiwanie danych podzielonych pionowo na partycje)](sql-database-elastic-query-vertical-partitioning.md)
* Aby uzyskać poziome partycjonowanie (dzielenie na fragmenty) samouczek, zobacz [Wprowadzenie do elastycznego zapytania do partycjonowania poziomego (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).
* Aby uzyskać składnię i przykładowe kwerendy dotyczące danych podzielonych poziomo na partycje, zobacz Wykonywanie zapytań o [dane podzielone na partycje w poziomie)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [\_sp \_wykonać zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej bazy danych SQL azure lub zestaw baz danych służących jako fragmenty w schemat partycjonowania poziomego.
