---
title: 'SSMS: Łączenie i wyszukiwanie danych'
description: Dowiedz się, jak łączyć się z bazą danych SQL Database na platformie Azure przy użyciu programu SQL Server Management Studio (SSMS). Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: łączenie z bazą danych SQL, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299298"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Szybki start: łączenie bazy danych SQL SQL Server Management Studio za pomocą programu SQL Server Management Studio i wykonywanie zapytań

W tym przewodniku Szybki start dowiesz się, jak połączyć się z bazą danych SQL SQL I niektóre kwerendami za pomocą programu SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Wymagania wstępne

Ukończenie tego przewodnika Szybki start wymaga następujących elementów:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- Przykładowa baza danych AdventureWorksLT. Jeśli potrzebujesz kopii roboczej bazy danych AdventureWorksLT, utwórz go, wypełniając szybki start [bazy danych Azure SQL.](sql-database-single-database-get-started.md)
    - Skrypty w tym artykule są zapisywane w celu użycia bazy danych AdventureWorksLT. Jeśli używasz wystąpienia zarządzanego, należy zaimportować bazę danych AdventureWorks do bazy danych wystąpienia lub zmodyfikować skrypty w tym artykule, aby użyć bazy danych importerów wszystkich światowych.

Jeśli chcesz po prostu uruchomić niektóre zapytania ad hoc bez instalowania SSMS, zobacz [Szybki start: Użyj edytora zapytań portalu Azure, aby zbadać bazę danych SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj informacje o połączeniu potrzebne do połączenia z bazą danych. Aby ukończyć ten szybki start, musisz mieć w pełni kwalifikowaną nazwę serwera lub nazwę hosta, nazwę bazy danych i dane logowania.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Przejdź do **bazy danych SQL** lub wystąpienia **zarządzanego SQL,** które chcesz zbadać.

3. Na stronie **Przegląd** skopiuj w pełni kwalifikowaną nazwę serwera. Znajduje się obok **nazwy serwera** dla pojedynczej bazy danych lub w pełni kwalifikowanej nazwy serwera obok **hosta** dla wystąpienia zarządzanego. W pełni kwalifikowana nazwa wygląda następująco: *servername.database.windows.net*, z tą różnicą, że ma rzeczywistą nazwę serwera.

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

W systemie SSMS połącz się z serwerem bazy danych SQL platformy Azure.

> [!IMPORTANT]
> Serwer usługi Azure SQL Database nasłuchuje na porcie 1433. Aby można było nawiązać połączenie z serwerem usługi SQL Database spoza firmowej zapory, ten port w zaporze musi być otwarty.

1. Otwórz program SSMS.

2. Zostanie wyświetlone okno dialogowe **Nawiązywanie połączenia z serwerem**. Wprowadź następujące informacje:

   | Ustawienie      | Sugerowana wartość    | Opis |
   | ------------ | ------------------ | ----------- |
   | **Typ serwera** | Aparat bazy danych | Wartość wymagana. |
   | **Nazwa serwera** | W pełni kwalifikowana nazwa serwera | Coś w stylu: **servername.database.windows.net**. |
   | **Uwierzytelnianie** | Uwierzytelnianie programu SQL Server | W tym samouczku używane jest uwierzytelnianie SQL. |
   | **Logowanie** | Identyfikator użytkownika konta administratora serwera | Identyfikator użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   | **Hasło** | Hasło konta administratora serwera | Hasło użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   ||||

   ![łączenie z serwerem](./media/sql-database-connect-query-ssms/connect.png)  

3. Wybierz pozycję **Opcje** w oknie dialogowym **Połącz z serwerem**. Z menu rozwijanego **Połącz z bazą danych** wybierz pozycję **mojaPrzykladowaBazaDanych**. Ukończenie przewodnika Szybki start w [sekcji Wymagania wstępne](#prerequisites) tworzy bazę danych AdventureWorksLT o nazwie mySampleDatabase. Jeśli kopia robocza bazy danych AdventureWorks ma inną nazwę niż mySampleDatabase, wybierz ją zamiast tego.

   ![nawiązywanie połączenia z bazą danych na serwerze](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Wybierz przycisk **Połącz**. Zostanie otwarte okno Eksplorator obiektów.

5. Aby wyświetlić obiekty bazy danych, rozwiń **pozycję Bazy danych,** a następnie rozwiń węzeł bazy danych.

   ![obiekty mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Zapytania o dane

Uruchom następujący kod z instrukcją [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) języka Transact-SQL, aby wykonać zapytanie o 20 najpopularniejszych produktów według kategorii.

1. W Eksploratorze obiektów kliknij prawym przyciskiem myszy pozycję **mojaPrzykladowaBazaDanych** i wybierz opcję **Nowe zapytanie**. Zostanie otwarte nowe okno zapytania połączone z Twoją bazą danych.

2. W oknie kwerendy wklej następującą kwerendę SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na pasku narzędzi wybierz pozycję **Wykonaj,** aby uruchomić `Product` `ProductCategory` kwerendę i pobrać dane z tabel i tabel.

    ![kwerenda, aby pobrać dane z tabeli Kategoria produktu i produktu](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Wstawianie danych

Uruchom następujący kod z instrukcją [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) języka Transact-SQL, aby utworzyć nowy produkt w tabeli `SalesLT.Product`.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Wybierz polecenie **Wykonaj**, aby wstawić nowy wiersz do tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

#### <a name="view-the-result"></a>Wyświetlanie wyniku

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Wybierz pozycję **Wykonaj**. Pojawi się następujący wynik.

   ![wynik kwerendy tabeli produktów](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Aktualizowanie danych

Uruchom ten kod [AKTUALIZACJI](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL, aby zmodyfikować nowy produkt.

1. Zastąp poprzednią kwerendę tą, która zwraca nowy rekord utworzony wcześniej:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Wykonaj**, aby zaktualizować określony wiersz w tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

### <a name="delete-data"></a>Usuwanie danych

Uruchom następujący kod z instrukcją [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) języka Transact-SQL, aby usunąć nowy produkt.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Wykonaj**, aby usunąć określony wiersz z tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o programie SSMS, zobacz [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (Program SQL Server Management Studio).
- Aby połączyć się i wykonać zapytanie za pomocą witryny Azure Portal, zobacz [Connect and query with the Azure portal SQL Query editor (Nawiązywanie połączeń i wykonywanie zapytań za pomocą edytora zapytań SQL w witrynie Azure Portal)](sql-database-connect-query-portal.md).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą programu Visual Studio Code, zobacz [Connect and query with Visual Studio Code](sql-database-connect-query-vscode.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą programu Visual Studio Code).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą platformy .NET, zobacz [Connect and query with .NET](sql-database-connect-query-dotnet.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą platformy .NET).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka PHP, zobacz [Connect and query with PHP](sql-database-connect-query-php.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka PHP).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą oprogramowania Node.js, zobacz [Connect and query with Node.js](sql-database-connect-query-nodejs.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą oprogramowania Node.js).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Java, zobacz [Connect and query with Java](sql-database-connect-query-java.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Java).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Python, zobacz [Connect and query with Python](sql-database-connect-query-python.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Python).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Ruby, zobacz [Connect and query with Ruby](sql-database-connect-query-ruby.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Ruby).
