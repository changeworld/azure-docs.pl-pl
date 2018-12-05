---
title: 'SSMS: nawiązywanie połączenia i wykonywanie zapytań dotyczących danych w bazie danych Azure SQL Database | Microsoft Docs'
description: Dowiedz się, jak łączyć się z bazą danych SQL Database na platformie Azure przy użyciu programu SQL Server Management Studio (SSMS). Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: łączenie z bazą danych SQL, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: f8d153ef415bcab85720f6733d4e01e9b44c9e50
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871008"
---
# <a name="quickstart-azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Szybki start: Azure SQL Database: używanie programu SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych

[SQL Server Management Studio][ssms-install-latest-84g] (SSMS) to zintegrowane środowisko do zarządzania dowolną infrastrukturą SQL — od programu SQL Server po usługę SQL Database dla systemu Microsoft Windows. W tym przewodniku Szybki start pokazano, jak używać narzędzia SSMS w celu nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane, a także wstawiać, aktualizować i usuwać dane z bazy danych. 

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start używa jako punktu początkowego zasobów utworzonych w jednym z poniższych przewodników Szybki start:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

Ten przewodnik Szybki start wymaga również skonfigurowania reguły zapory na poziomie serwera. W celu zapoznania się z samouczkiem przedstawiający sposób wykonywania tej czynności zobacz [Create server-level firewall rule](sql-database-get-started-portal-firewall.md) (Tworzenie reguły zapory na poziomie serwera).

#### <a name="install-the-latest-ssms"></a>Instalowanie najnowszej wersji środowiska SSMS

Przed rozpoczęciem upewnij się, że zainstalowano najnowszą wersję środowiska [SSMS][ssms-install-latest-84g]. 

## <a name="sql-server-connection-information"></a>Informacje o połączeniu z serwerem SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

Użyj programu SQL Server Management Studio, aby nawiązać połączenie z serwerem Azure SQL Database. 

> [!IMPORTANT]
> Serwer logiczny usługi Azure SQL Database nasłuchuje na porcie 1433. Jeśli próbujesz nawiązać połączenie z serwerem logicznym usługi Azure SQL Database za pośrednictwem zapory firmowej, to aby połączenie się powiodło, ten port musi być otwarty w zaporze firmowej.
>

1. Otwórz program SQL Server Management Studio.

2. W oknie dialogowym **Połącz z serwerem** wprowadź następujące informacje:

   | Ustawienie      | Sugerowana wartość    | Opis | 
   | ------------ | ------------------ | ----------- | 
   | **Typ serwera** | Aparat bazy danych | Ta wartość jest wymagana. |
   | **Nazwa serwera** | W pełni kwalifikowana nazwa serwera | Nazwa może mieć taką formę: **mynewserver20170313.database.windows.net**. |
   | **Uwierzytelnianie** | Uwierzytelnianie programu SQL Server | Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
   | **Logowanie** | Konto administratora serwera | To konto określono podczas tworzenia serwera. |
   | **Hasło** | Hasło konta administratora serwera | To hasło określono podczas tworzenia serwera. |
   ||||

   ![łączenie z serwerem](./media/sql-database-connect-query-ssms/connect.png)  

3. Kliknij przycisk **Opcje** w oknie dialogowym **Połącz z serwerem**. W sekcji **Nawiązywanie połączenia z bazą danych** wprowadź ciąg **mySampleDatabase**, aby nawiązać połączenie z tą bazą danych.

   ![nawiązywanie połączenia z bazą danych na serwerze](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kliknij przycisk **Połącz**. W programie SSMS zostanie otwarte okno Eksplorator obiektów. 

   ![nawiązane połączenie z serwerem](./media/sql-database-connect-query-ssms/connected.png)  

5. W Eksploratorze obiektów rozwiń pozycję **Bazy danych**, a następnie rozwiń pozycję **mySampleDatabase**, aby wyświetlić obiekty w przykładowej bazie danych.

## <a name="query-data"></a>Zapytania o dane

Użyj następującego kodu, aby wykonać zapytanie o 20 najpopularniejszych produktów według kategorii, używając instrukcji [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) języka Transact-SQL.

1. W Eksploratorze obiektów kliknij prawym przyciskiem myszy pozycję **mySampleDatabase** i kliknij opcję **Nowe zapytanie**. Zostanie otwarte puste okno zapytania, które jest połączone z Twoją bazą danych.
2. W oknie zapytania wprowadź następujące zapytanie:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na pasku narzędzi kliknij opcję **Wykonaj**, aby pobrać dane z tabel Product i ProductCategory.

    ![query](./media/sql-database-connect-query-ssms/query.png)

## <a name="insert-data"></a>Wstawianie danych

Użyj następującego kodu, aby wstawić nowy produkt do tabeli SalesLT.Product przy użyciu instrukcji [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) języka Transact-SQL.

1. W oknie zapytania zastąp poprzednie zapytanie następującym zapytaniem:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Na pasku narzędzi kliknij opcję **Wykonaj**, aby wstawić nowy wiersz w tabeli Product.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Aktualizowanie danych

Użyj następującego kodu, aby zaktualizować nowy, wcześniej dodany produkt przy użyciu instrukcji [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) języka Transact-SQL.

1. W oknie zapytania zastąp poprzednie zapytanie następującym zapytaniem:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Na pasku narzędzi kliknij opcję **Wykonaj**, aby zaktualizować określony wiersz w tabeli Product.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Usuwanie danych

Użyj następującego kodu, aby usunąć nowy, wcześniej dodany produkt przy użyciu instrukcji [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) języka Transact-SQL.

1. W oknie zapytania zastąp poprzednie zapytanie następującym zapytaniem:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Na pasku narzędzi kliknij opcję **Wykonaj**, aby usunąć określony wiersz w tabeli Product.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o programie SSMS, zobacz [Korzystanie z programu SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Aby połączyć się i wykonać zapytanie za pomocą witryny Azure Portal, zobacz [Connect and query with the Azure portal SQL Query editor (Nawiązywanie połączeń i wykonywanie zapytań za pomocą edytora zapytań SQL w witrynie Azure Portal)](sql-database-connect-query-portal.md).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą programu Visual Studio Code, zobacz [Connect and query with Visual Studio Code](sql-database-connect-query-vscode.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą programu Visual Studio Code).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą platformy .NET, zobacz [Connect and query with .NET](sql-database-connect-query-dotnet.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą platformy .NET).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka PHP, zobacz [Connect and query with PHP](sql-database-connect-query-php.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka PHP).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą oprogramowania Node.js, zobacz [Connect and query with Node.js](sql-database-connect-query-nodejs.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą oprogramowania Node.js).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Java, zobacz [Connect and query with Java](sql-database-connect-query-java.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Java).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Python, zobacz [Connect and query with Python](sql-database-connect-query-python.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Python).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Ruby, zobacz [Connect and query with Ruby](sql-database-connect-query-ruby.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Ruby).


<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms

