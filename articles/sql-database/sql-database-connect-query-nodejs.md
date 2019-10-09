---
title: 'Szybki Start: Używanie środowiska Node. js do wykonywania zapytań Azure SQL Database'
description: Jak użyć środowiska Node. js do utworzenia programu, który nawiązuje połączenie z bazą danych SQL Azure i wykonuje zapytania za pomocą instrukcji języka T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 4d9c3954aea5fe64439190a2a0886fd8300c4cf9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178047"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Szybki Start: Używanie środowiska Node. js do wykonywania zapytań w bazie danych Azure SQL

Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z usługą Azure SQL Database przy użyciu środowiska [Node. js](https://nodejs.org) . Następnie można użyć instrukcji T-SQL do wykonywania zapytań dotyczących danych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przykład, upewnij się, że masz następujące wymagania wstępne:

- Baza danych SQL Azure. Możesz użyć jednego z tych przewodników Szybki Start, aby utworzyć i skonfigurować bazę danych w Azure SQL Database:

  || Pojedyncza baza danych | Wystąpienie zarządzane |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [Interfejs wiersza polecenia](scripts/sql-database-create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Narzędzia](scripts/sql-database-create-and-configure-database-powershell.md) | [Narzędzia](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfiguracja | [Reguła zapory adresów IP na poziomie serwera](sql-database-server-level-firewall-rule.md)| [Łączność z maszyny wirtualnej](sql-database-managed-instance-configure-vm.md)|
  |||[Łączność z lokacji](sql-database-managed-instance-configure-p2s.md)
  |Ładowanie danych|Firma Adventure Works załadowana za szybki start|[Przywracanie szerokiej międzynarodowej importera](sql-database-managed-instance-get-started-restore.md)
  |||Przywróć lub zaimportuj Adventure Works z pliku [BACPAC](sql-database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty w tym artykule są przeznaczone do korzystania z bazy danych firmy Adventure Works. W przypadku wystąpienia zarządzanego należy zaimportować bazę danych firmy Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty w tym artykule, aby użyć bazy danych Wide World Imports.


- Oprogramowanie dotyczące środowiska Node. js dla systemu operacyjnego:

  - **MacOS**, zainstaluj oprogramowania Homebrew i Node. js, a następnie zainstaluj sterownik ODBC i narzędzie sqlcmd. Zobacz [krok 1,2 i 1,3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**, zainstaluj program Node. js, a następnie zainstaluj sterownik ODBC i narzędzie sqlcmd. Zobacz [krok 1,2 i 1,3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**, zainstaluj czekoladę i Node. js, a następnie zainstaluj sterownik ODBC i narzędzie sqlcmd. Zobacz [krok 1,2 i 1,3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-sql-server-connection-information"></a>Pobierz informacje o połączeniu z programem SQL Server

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych Azure SQL. Do nadchodzących procedur potrzebna jest w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **bazy danych SQL** lub **wystąpienia zarządzane SQL** .

3. Na stronie **Przegląd** zapoznaj się z w pełni kwalifikowaną nazwą serwera obok **nazwy serwera** dla pojedynczej bazy danych lub w pełni kwalifikowana nazwa serwera obok **hosta** dla wystąpienia zarządzanego. Aby skopiować nazwę serwera lub nazwę hosta, umieść kursor nad nim i wybierz ikonę **kopiowania** . 

## <a name="create-the-project"></a>Utwórz projekt

Otwórz wiersz polecenia i Utwórz folder o nazwie *SqlTest*. Otwórz utworzony folder i uruchom następujące polecenie:

  ```bash
  npm init -y
  npm install tedious@5.0.3
  npm install async@2.6.2
  ```

## <a name="add-code-to-query-database"></a>Dodawanie kodu do bazy danych zapytań

1. W ulubionym edytorze tekstów Utwórz nowy plik *SqlTest. js*.

1. Zastąp jego zawartość następującym kodem. Następnie Dodaj odpowiednie wartości dla serwera, bazy danych, użytkownika i hasła.

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
> Przykład kodu używa przykładowej bazy danych **AdventureWorksLT** dla usługi Azure SQL.

## <a name="run-the-code"></a>Uruchom kod

1. W wierszu polecenia Uruchom program.

    ```bash
    node sqltest.js
    ```

1. Sprawdź, czy 20 najważniejszych wierszy jest zwracanych i Zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Sterownik Microsoft Node. js dla SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Łączenie i wykonywanie zapytań w systemie Windows/Linux/macOS za pomocą [platformy .NET Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md)lub [SSMS](sql-database-connect-query-ssms.md) (tylko system Windows)

- [Rozpoczynanie pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli)

- Projektowanie pierwszej bazy danych Azure SQL Database przy użyciu [platformy .NET lub programu](sql-database-design-first-database-csharp.md) [SSMS](sql-database-design-first-database.md)
