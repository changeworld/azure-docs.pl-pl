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
ms.date: 02/12/2019
ms.openlocfilehash: f92b40aad9de59e1859b3689fb4bdb119491adf1
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418044"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Szybki start: Korzystanie z narzędzia Node.js do wykonywania zapytań w bazie danych Azure SQL

W tym artykule przedstawiono sposób nawiązywania połączeń z bazą danych Azure SQL za pomocą języka [Node.js](https://nodejs.org). Następnie można użyć instrukcji języka T-SQL w celu wykonywania zapytań o dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przykład, upewnij się, że dysponujesz następującymi elementami:

- Baza danych Azure SQL. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  || Pojedyncza baza danych | Wystąpienie zarządzane |
  |:--- |:--- |:---|
  | Przycisk Utwórz| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [Interfejs wiersza polecenia](scripts/sql-database-create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Program PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [Program PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Konfigurowanie | [Reguła zapory IP na poziomie serwera](sql-database-server-level-firewall-rule.md)| [Łączność z maszyny wirtualnej](sql-database-managed-instance-configure-vm.md)|
  |||[Łączność ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Przywracanie lub importowanie bazy danych Adventure Works z pliku [BACPAC](sql-database-import.md) z witryny [github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. Za pomocą wystąpienia zarządzanego należy zaimportować bazę danych Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty znajdujące się w tym artykule, aby korzystały z bazy danych Wide World Importers.


- Oprogramowanie związane z narzędziem Node.js dla Twojego systemu operacyjnego:

  - **System MacOS**: zainstaluj oprogramowanie Homebrew i Node.js, a następnie zainstaluj sterownik ODBC i program SQLCMD. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **System Ubuntu**: zainstaluj oprogramowanie Node.js, a następnie zainstaluj sterownik ODBC i program SQLCMD. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **System Windows**: zainstaluj oprogramowanie Chocolatey i Node.js, a następnie zainstaluj sterownik ODBC i program SQLCMD. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Otwórz stronę **Bazy danych SQL** lub **Wystąpienia zarządzane SQL**.

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**. 

## <a name="create-the-project"></a>Tworzenie projektu

Otwórz wiersz polecenia i utwórz folder o nazwie *sqltest*. Przejdź do utworzonego folderu i uruchom następujące polecenie:

  ```bash
  npm init -y
  npm install tedious@5.0.3
  npm install async@2.6.2
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
        authentication: {
            options: {
                userName: 'userName', // update me
                password: 'password' // update me
            },
            type: 'default'
        },
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

## <a name="next-steps"></a>Kolejne kroki

- [Sterownik firmy Microsoft środowiska Node.js dla programu SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Nawiązywanie połączeń i wykonywanie zapytań w systemie Windows/Linux/macOS za pomocą platformy [.NET Core](sql-database-connect-query-dotnet-core.md), programu [Visual Studio Code](sql-database-connect-query-vscode.md) lub programu [SSMS](sql-database-connect-query-ssms.md) (tylko system Windows)

- [Rozpoczynanie pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli)

- Projektowanie pierwszej bazy danych Azure SQL przy użyciu platformy [.NET](sql-database-design-first-database-csharp.md) lub programu [SSMS](sql-database-design-first-database.md)
