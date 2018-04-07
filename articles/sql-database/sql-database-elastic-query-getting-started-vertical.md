---
title: Wprowadzenie do zapytań między bazami danych (partycjonowanie pionowe) | Dokumentacja firmy Microsoft
description: jak używać zapytania elastycznej bazy danych z baz danych w pionie podzielonym na partycje
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c7bf6816b457f7e193f53336c48f5e205722067e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Wprowadzenie do zapytań między bazami danych (partycjonowanie pionowe) (wersja zapoznawcza)
Zapytanie elastycznej bazy danych (wersja zapoznawcza) w bazie danych SQL Azure umożliwia uruchamianie zapytania T-SQL, obejmującej wiele baz danych przy użyciu pojedynczy punkt połączenia. Ten temat dotyczy [w pionie na partycje baz danych](sql-database-elastic-query-vertical-partitioning.md).  

Po zakończeniu zostanie: informacje o sposobie konfigurowania i korzystania z bazy danych SQL Azure do wykonywania zapytań, obejmującej wiele powiązanych baz danych. 

Aby uzyskać więcej informacji o funkcji zapytania elastycznej bazy danych, zobacz [bazy danych SQL Azure elastycznej bazy danych zapytań — omówienie](sql-database-elastic-query-overview.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączany uprawnienie ALTER DATABASE. Aby odwołać się do źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.

## <a name="create-the-sample-databases"></a>Tworzenie bazy danych przykładowych
Do uruchomienia z należy utworzyć dwie bazy danych, **klientów** i **zamówień**, albo w tych samych lub różnych serwerów logicznych.   

Wykonaj następujące kwerendy w **zamówień** bazy danych do utworzenia **OrderInformation** tabeli i przykładowych danych wejściowych. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Teraz, wykonaj następujące zapytanie w **klientów** bazy danych do utworzenia **CustomerInformation** tabeli i przykładowych danych wejściowych. 

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
### <a name="database-scoped-master-key-and-credentials"></a>Baza danych o zakresie klucz główny i poświadczenia
1. Otwórz program SQL Server Management Studio lub SQL Server Data Tools w programie Visual Studio.
2. Połączenie z bazą danych zleceń i wykonaj następujące polecenia T-SQL:
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    "Nazwa użytkownika" i "password" powinna być nazwa użytkownika i hasło używane do logowania do bazy danych klientów.
    Uwierzytelnianie przy użyciu usługi Azure Active Directory z zapytaniami elastyczne nie jest obecnie obsługiwane.

### <a name="external-data-sources"></a>Zewnętrzne źródła danych
Aby utworzyć zewnętrznego źródła danych, uruchom następujące polecenie w bazie danych zleceń: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Tabele zewnętrzne
Tworzenie tabeli zewnętrznej w bazie danych zamówień odpowiadającego definicja tabeli CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowego zapytania T-SQL elastycznej bazy danych
Po zdefiniowaniu zewnętrznym źródle danych i tabele zewnętrzne T-SQL można teraz używać do badania tabel zewnętrznych. Wykonaj tę kwerendę w bazie danych zleceń: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Koszty
Obecnie funkcja zapytania elastycznej bazy danych jest dostępna do kosztu bazy danych SQL Azure.  

Aby uzyskać informacje o cenach zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database). 

## <a name="next-steps"></a>Kolejne kroki

* Omówienie elastycznej zapytania, zobacz [elastycznej zapytań — omówienie](sql-database-elastic-query-overview.md).
* Dla zapytań dotyczących danych pionowo podzielonym na partycje i składnię i przykład, zobacz [zapytań w pionie na partycje danych)](sql-database-elastic-query-vertical-partitioning.md)
* Samouczek poziomych partycji (dzielenia na fragmenty), zobacz [wprowadzenie elastycznej zapytania poziome partycjonowania (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).
* Dla zapytań dotyczących danych poziomie podzielonym na partycje i składnię i przykład, zobacz [zapytań w poziomie na partycje danych)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL w jednym zdalnej bazy danych SQL Azure lub zestaw baz danych, służąc jako odłamków w poziomie schemat partycjonowania.
