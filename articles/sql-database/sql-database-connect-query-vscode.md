---
title: 'VS Code: nawiązywanie połączenia i wykonywanie zapytań dotyczących danych w bazie danych Azure SQL Database | Microsoft Docs'
description: Dowiedz się, jak łączyć się z bazą danych SQL Database na platformie Azure przy użyciu programu Visual Studio Code. Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: nawiązywanie połączenia z bazą danych SQL
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e1294b8f83da2ad6748f051925219cdc7683d032
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38701879"
---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Baza danych Azure SQL Database: używanie programu Visual Studio Code do nawiązywania połączenia i wysyłania zapytań dotyczących danych

[Visual Studio Code](https://code.visualstudio.com/docs) to graficzny edytor kodu dla systemów Linux, macOS i Windows obsługujący rozszerzenia, w tym [rozszerzenie mssql](https://aka.ms/mssql-marketplace), używany do wysyłania zapytań do programów Microsoft SQL Server, Azure SQL Database i SQL Data Warehouse. W tym przewodniku Szybki start pokazano, jak używać edytora Visual Studio Code w celu nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane, a także wstawiać, aktualizować i usuwać dane z bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start używa jako punktu początkowego zasobów utworzonych w jednym z poniższych przewodników Szybki start:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-vs-code"></a>Instalowanie programu VS Code

Przed rozpoczęciem upewnij się, że zainstalowano najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com/Download) i załadowano [rozszerzenie mssql](https://aka.ms/mssql-marketplace). Aby uzyskać wskazówki dotyczące instalacji rozszerzenia mssql, zobacz [Install VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) (Instalacja programu VS Code) i [mssql for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) (Rozszerzenie mssql dla programu Visual Studio Code). 

## <a name="configure-vs-code"></a>Konfigurowanie kodu programu VS 

### <a name="mac-os"></a>**Mac OS**
W systemie macOS należy zainstalować protokół OpenSSL, który jest wymaganiem wstępnym dla narzędzi .Net Core używanych przez rozszerzenie mssql. Otwórz terminal i wprowadź następujące polecenia, aby zainstalować rozwiązania **brew** i **OpenSSL**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Nie jest potrzebna specjalna konfiguracja.

### <a name="windows"></a>**Windows**

Nie jest potrzebna specjalna konfiguracja.

## <a name="sql-server-connection-information"></a>Informacje o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera, nazwa bazy danych i informacje logowania.

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="set-language-mode-to-sql"></a>Ustawianie trybu języka na SQL

Ustaw tryb języka na **SQL** w programie Visual Studio Code, aby włączyć polecenia mssql i T-SQL IntelliSense.

1. Otwórz nowe okno programu Visual Studio Code. 

2. Kliknij pozycję **Zwykły tekst** w prawym dolnym rogu paska stanu.
3. W otwartym menu rozwijanym **Wybierz tryb języka** wpisz **SQL**, a następnie naciśnij klawisz **ENTER**, aby ustawić tryb języka na SQL. 

   ![Tryb języka SQL](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

Użyj programu Visual Studio Code, aby nawiązać połączenie z serwerem Azure SQL Database.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że masz przygotowany serwer, bazę danych i informacje logowania. Jeśli zmienisz fokus z programu Visual Studio Code po rozpoczęciu wprowadzania informacji o profilu połączenia, konieczne będzie ponowne rozpoczęcie procesu tworzenia profilu połączenia.
>

1. W programie VS Code naciśnij klawisze **CTRL + SHIFT + P** (lub klawisz **F1**), aby otworzyć paletę poleceń.

2. Wpisz **sqlcon** i naciśnij klawisz **ENTER**.

3. Naciśnij klawisz **ENTER**, aby wybrać opcję **Utwórz profil połączenia**. W ten sposób zostanie utworzony profil połączenia dla wystąpienia programu SQL Server.

4. Postępuj zgodnie z monitami, aby określić właściwości połączenia dla nowego profilu połączenia. Po określeniu każdej wartości naciśnij klawisz **ENTER**, aby kontynuować. 

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nazwa serwera | W pełni kwalifikowana nazwa serwera | Nazwa może mieć taką formę: **mynewserver20170313.database.windows.net**. |
   | **Nazwa bazy danych** | mySampleDatabase | Nazwa bazy danych, z którą chcesz się połączyć. |
   | **Uwierzytelnianie** | Identyfikator logowania SQL| Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
   | **Nazwa użytkownika** | Konto administratora serwera | To konto określono podczas tworzenia serwera. |
   | **Hasło (identyfikator logowania SQL)** | Hasło konta administratora serwera | To hasło określono podczas tworzenia serwera. |
   | **Zapisać hasło?** | Tak lub Nie | Wybierz opcję Tak, jeśli nie chcesz wprowadzać hasła za każdym razem. |
   | **Wprowadź nazwę dla tego profilu** | Nazwa profilu, np. **mySampleDatabase** | Zapisana nazwa profilu przyspiesza połączenie podczas kolejnych logowań. | 

5. Naciśnij klawisz **ESC**, aby zamknąć komunikat z informacją o utworzeniu profilu i nawiązaniu z nim połączenia.

6. Sprawdź połączenie na pasku stanu.

   ![Stan połączenia](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Zapytania o dane

Użyj następującego kodu, aby wykonać zapytanie o 20 najpopularniejszych produktów według kategorii, używając instrukcji [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) języka Transact-SQL.

1. W oknie **Edytor** wprowadź następujące zapytanie w pustym oknie zapytania:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Naciśnij klawisze **CTRL+SHIFT+E**, aby pobrać dane z tabel Product i ProductCategory.

    ![Zapytanie](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Wstawianie danych

Użyj następującego kodu, aby wstawić nowy produkt do tabeli SalesLT.Product przy użyciu instrukcji [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) języka Transact-SQL.

1. W oknie **Edytor** usuń poprzednie zapytanie i wprowadź następujące:

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

2. Naciśnij klawisze **CTRL+SHIFT+E**, aby wstawić nowy wiersz w tabeli Product.

## <a name="update-data"></a>Aktualizowanie danych

Użyj następującego kodu, aby zaktualizować nowy, wcześniej dodany produkt przy użyciu instrukcji [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) języka Transact-SQL.

1.  W oknie **Edytor** usuń poprzednie zapytanie i wprowadź następujące:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Naciśnij klawisze **CTRL+SHIFT+E**, aby zaktualizować wiersz w tabeli Product.

## <a name="delete-data"></a>Usuwanie danych

Użyj następującego kodu, aby usunąć nowy, wcześniej dodany produkt przy użyciu instrukcji [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) języka Transact-SQL.

1. W oknie **Edytor** usuń poprzednie zapytanie i wprowadź następujące:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Naciśnij klawisze **CTRL+SHIFT+E**, aby usunąć wiersz w tabeli Product.

## <a name="next-steps"></a>Następne kroki

- Aby nawiązywać połączenia i wykonywać zapytania za pomocą programu SQL Server Management Studio, zobacz artykuł [Connect and query with SSMS](sql-database-connect-query-ssms.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą programu SSMS).
- Aby połączyć się i wykonać zapytanie za pomocą witryny Azure Portal, zobacz [Connect and query with the Azure portal SQL Query editor ](sql-database-connect-query-portal.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą edytora zapytań SQL w witrynie Azure Portal).
- Aby zapoznać się z artykułem w magazynie MSDN dotyczącym programu Visual Studio Code, zobacz temat [Create a database IDE with MSSQL extension blog post](https://msdn.microsoft.com/magazine/mt809115) (Tworzenie bazy danych w środowisku IDE, korzystając z wpisu na blogu dotyczącym rozszerzenia MSSQL).
