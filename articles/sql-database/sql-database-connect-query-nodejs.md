---
title: Korzystanie z narzędzia Node.js do wykonywania zapytań w bazie danych Azure SQL | Microsoft Docs
description: Sposób użycia narzędzia Node.js do utworzenia programu, który nawiązuje połączenie z bazą danych Azure SQL i wykonuje zapytania za pomocą instrukcji języka T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 250f03809a182e541fb58f73469f46d2b281b69f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756054"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Szybki start: Korzystanie z narzędzia Node.js do wykonywania zapytań w bazie danych Azure SQL

W tym artykule przedstawiono sposób nawiązywania połączeń z bazą danych Azure SQL za pomocą języka [Node.js](https://nodejs.org). Następnie można użyć instrukcji języka T-SQL w celu wykonywania zapytań o dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przykład, upewnij się, że dysponujesz następującymi elementami:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Oprogramowanie związane z narzędziem Node.js dla Twojego systemu operacyjnego:

  - **System MacOS**: zainstaluj oprogramowanie Homebrew i Node.js, a następnie zainstaluj sterownik ODBC i program SQLCMD. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **System Ubuntu**: zainstaluj oprogramowanie Node.js, a następnie zainstaluj sterownik ODBC i program SQLCMD. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **System Windows**: zainstaluj oprogramowanie Chocolatey i Node.js, a następnie zainstaluj sterownik ODBC i program SQLCMD. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-database-connection"></a>Uzyskiwanie połączenia bazy danych

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> W przypadku publicznego adresu IP komputera, na którym jest wykonywany ten samouczek, niezbędne jest posiadanie reguły zapory. Jeśli pracujesz na innym komputerze lub masz inny publiczny adres IP, utwórz [regułę zapory poziomu serwera przy użyciu witryny Azure Portal](sql-database-server-level-firewall-rule.md).

## <a name="create-the-project"></a>Tworzenie projektu

Otwórz wiersz polecenia i utwórz folder o nazwie *sqltest*. Przejdź do utworzonego folderu i uruchom następujące polecenie:

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>Dodawanie kodu umożliwiającego wykonywanie zapytań w bazie danych

1. W swoim ulubionym edytorze tekstów utwórz nowy plik o nazwie *sqltest.js*.

1. Zastąp jego zawartość poniższym kodem. Następnie dodaj odpowiednie wartości dla swojego serwera, bazy danych, użytkownika i hasła.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
        }
    }
    var connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on('connect', function(err)
        {
            if (err)
            {
                console.log(err)
            }
            else
            {
                queryDatabase()
            }
        }
    );

    function queryDatabase()
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
            function(err, rowCount, rows)
            {
                console.log(rowCount + ' row(s) returned');
                process.exit();
            }
        );

        request.on('row', function(columns) {
            columns.forEach(function(column) {
                console.log("%s\t%s", column.metadata.colName, column.value);
            });
        });
        connection.execSql(request);
    }
    ```

> [!NOTE]
> W przykładzie kodu użyto przykładowej bazy danych **AdventureWorksLT** dla usługi Azure SQL.

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom program.

    ```bash
    node sqltest.js
    ```

1. Sprawdź, czy zostało zwróconych 20 pierwszych wierszy, i zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Sterownik firmy Microsoft środowiska Node.js dla programu SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Nawiązywanie połączeń i wykonywanie zapytań w systemie Windows/Linux/macOS za pomocą platformy [.NET Core](sql-database-connect-query-dotnet-core.md), programu [Visual Studio Code](sql-database-connect-query-vscode.md) lub programu [SSMS](sql-database-connect-query-ssms.md) (tylko system Windows)

- [Rozpoczynanie pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli)

- Projektowanie pierwszej bazy danych Azure SQL przy użyciu platformy [.NET](sql-database-design-first-database-csharp.md) lub programu [SSMS](sql-database-design-first-database.md)
