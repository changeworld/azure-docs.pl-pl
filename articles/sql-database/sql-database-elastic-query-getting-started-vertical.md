---
title: Rozpoczynanie pracy z bazami danych zapytań (partycje pionowe) | Dokumentacja firmy Microsoft
description: jak używać zapytania elastycznej bazy danych z baz danych partycjonowanej w pionie
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 116a465a0ddc913e342e0ffcc1fb29f5bf969419
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60307448"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Rozpoczynanie pracy z bazami danych zapytań (partycje pionowe) (wersja zapoznawcza)

Zapytanie elastycznej bazy danych (wersja zapoznawcza) dla usługi Azure SQL Database umożliwia uruchamianie zapytania T-SQL, które obejmują wiele baz danych przy użyciu pojedynczy punkt połączenia. Ten artykuł dotyczy [partycje pionowe bazy danych](sql-database-elastic-query-vertical-partitioning.md).  

Po zakończeniu będzie: informacje o sposobie konfigurowania i używania usługi Azure SQL Database do wykonywania zapytań obejmujących wiele powiązanych baz danych.

Aby uzyskać więcej informacji na temat funkcji zapytanie elastycznej bazy danych, zobacz [Przegląd zapytanie elastycznej bazy danych Azure SQL Database](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane jest uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączony do uprawnienie ALTER DATABASE. Aby odwołać się do bazowego źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.

## <a name="create-the-sample-databases"></a>Tworzenie przykładowych baz danych

Na początek z utworzyć dwie bazy danych, **klientów** i **zamówienia**, albo na serwerach bazy danych SQL w tej samej lub innej.

Wykonaj następujące zapytania na **zamówienia** bazy danych do utworzenia **OrderInformation** tabeli i przykładowe dane wejściowe.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Teraz, wykonaj następujące zapytanie w **klientów** bazy danych do utworzenia **CustomerInformation** tabeli i przykładowe dane wejściowe.

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

### <a name="database-scoped-master-key-and-credentials"></a>Klucz główny i poświadczeń w zakresie bazy danych

1. Otwórz program SQL Server Management Studio lub SQL Server Data Tools w programie Visual Studio.
2. Połączenia z bazą danych zamówień i wykonaj następujące polecenia języka T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    "Username" i "password" powinna być nazwa użytkownika i hasło używane do logowania do bazy danych klientów.
    Uwierzytelnianie przy użyciu usługi Azure Active Directory za pomocą elastycznych zapytań nie jest obecnie obsługiwane.

### <a name="external-data-sources"></a>Zewnętrzne źródła danych

Aby utworzyć zewnętrzne źródło danych, wykonaj następujące polecenie w bazie danych zamówień:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Tabele zewnętrzne

Tworzenie zewnętrznej tabeli w bazie danych zamówień, który pasuje do definicji tabeli CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowego zapytania T-SQL elastycznej bazy danych

Po zdefiniowaniu zewnętrznym źródle danych i tabel zewnętrznych teraz umożliwia języka T-SQL do wykonywania zapytań tabel zewnętrznych. Wykonaj tę kwerendę w bazie danych zamówień:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Koszty

Obecnie funkcji zapytanie elastycznej bazy danych znajduje się w koszt usługi Azure SQL Database.  

Aby uzyskać informacje o cenach, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Kolejne kroki

* Omówienie zapytanie elastyczne, zobacz [omówienie zapytania elastycznego](sql-database-elastic-query-overview.md).
* Składnia i przykładowe zapytania dotyczące danych partycjonowanych w pionie, można zobaczyć [zapytań w pionie partycjonowania danych)](sql-database-elastic-query-vertical-partitioning.md)
* Samouczek partycjonowania poziomego (fragmentowania), zobacz [wprowadzenie do zapytań elastycznych dla partycjonowania poziomego (fragmentowania)](sql-database-elastic-query-getting-started.md).
* Aby uzyskać składnię i przykładowe zapytania dotyczące danych partycjonowanej w poziomie, zobacz [zapytań w poziomie partycjonowania danych)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury przechowywanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej usługi Azure SQL Database lub zestawu baz danych, służąc jako fragmentów w poziomie schematu partycjonowania.
