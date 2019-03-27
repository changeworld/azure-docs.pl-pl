---
title: 'Samouczek: Elastyczne zapytanie za pomocą usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft'
description: Ten samouczek używa funkcji elastycznego zapytania do wykonywania zapytań w usłudze Azure SQL Data Warehouse z usługi Azure SQL Database.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: 4dc0be045bceaaac4b71c653d82f7f9db834c3ec
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486062"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Samouczek: Umożliwia dostęp do danych w usłudze Azure SQL Data Warehouse zapytania elastycznego z usługi Azure SQL Database

Ten samouczek używa funkcji elastycznego zapytania do wykonywania zapytań w usłudze Azure SQL Data Warehouse z usługi Azure SQL Database. 

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne dotyczące samouczka

Przed rozpoczęciem tego samouczka, musisz mieć następujące wymagania wstępne:

1. Zainstalowany program SQL Server Management Studio (SSMS).
2. Utworzony serwer Azure SQL z bazy danych i magazynu danych w ramach tego serwera.
3. Konfigurowanie reguł zapory do uzyskiwania dostępu do serwera SQL Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Konfigurowanie połączenia między wystąpieniami programu SQL Data Warehouse i bazy danych SQL 

1. Za pomocą programu SSMS lub innego klienta zapytań Otwórz nowe zapytanie dotyczące bazy danych **wzorca** na serwerze logicznym.

2. Utwórz identyfikator logowania i użytkownika, który reprezentuje bazy danych SQL do połączenia magazynu danych.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Za pomocą programu SSMS lub innego klienta zapytań Otwórz nowe zapytanie dotyczące **wystąpienie magazynu danych SQL** na serwerze logicznym.

4. Utwórz użytkownika na wystąpienie magazynu danych z identyfikatorem logowania, utworzony w kroku 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Przyznaj uprawnienia dla użytkownika z kroku 4, który chcesz chce wykonać bazy danych SQL. W tym przykładzie jest tylko jest udzielane uprawnienie wybierz pozycję określonego schematu, pokazujący, jak firma Microsoft może ograniczyć kwerend z bazy danych SQL do określonej domeny. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Za pomocą programu SSMS lub innego klienta zapytań Otwórz nowe zapytanie dotyczące **wystąpienie usługi SQL database** na serwerze logicznym.

7. Utwórz klucz główny, jeśli nie masz już jeden. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Tworzenie poświadczeń o zakresie bazy danych przy użyciu poświadczeń utworzonego w kroku 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Utwórz źródło danych zewnętrznych, który wskazuje na wystąpienie magazynu danych.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Teraz możesz utworzyć tabele zewnętrzne, odwołujące się do tego zewnętrznego źródła danych. Korzystając z tych tabel są wysyłane do wystąpienie magazynu danych, które mają zostać przetworzone i wysyłane z powrotem do wystąpienia bazy danych.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Zapytania elastycznego z usługi SQL database do usługi SQL data warehouse

W następnych kilku krokach utworzymy tabelę w naszym wystąpienie magazynu danych przy użyciu kilku wartości. Następnie pokażemy sposób konfigurowania tabeli zewnętrznej, aby wysłać zapytanie wystąpienie magazynu danych z wystąpienia bazy danych.

1. Za pomocą programu SSMS lub innego klienta zapytań Otwórz nowe zapytanie dotyczące **SQL Data Warehouse** na serwerze logicznym.

2. Prześlij następujące zapytanie, aby utworzyć **OrdersInformation** tabeli w wystąpieniu z magazynem danych.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL, 
       [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Za pomocą programu SSMS lub innego klienta zapytań Otwórz nowe zapytanie dotyczące **bazy danych SQL** na serwerze logicznym.

4. Prześlij następujące zapytanie w celu utworzenia definicji tabeli zewnętrznej, który wskazuje na **OrderInformation** tabeli w wystąpieniu magazynu danych.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL,
       [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc,
    SCHEMA_NAME = N'dbo',
    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Sprawdź, czy masz teraz definicji tabeli zewnętrznej w swojej **wystąpienie usługi SQL database**.

   ![Zapytanie elastyczne w definicji tabeli zewnętrznej](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Prześlij następujące zapytanie, które wysyła zapytanie wystąpienie magazynu danych. Powinny pojawić się pięciu wartości, które zostały wstawione w kroku 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Należy zauważyć, że mimo kilku wartości, wykonanie tego zapytania zajmuje znaczną ilość czasu do zwrócenia. Jeśli zapytania elastycznego z magazynu danych, brać pod uwagę kosztów ogólnych zapytania przetwarzania i przenoszenia przewodowo. Moc obliczeniową, a nie opóźnienie ma priorytet korzystaj z elastycznego zapytania zdalnego wykonania.

Gratulacje, po skonfigurowaniu podstawy elastycznego zapytania. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać zalecenia, zobacz [najlepsze rozwiązania dotyczące usługi Azure SQL Data Warehouse przy użyciu zapytania elastycznego](how-to-use-elastic-query-with-sql-data-warehouse.md).
