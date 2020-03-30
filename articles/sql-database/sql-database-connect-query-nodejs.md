---
title: Używanie pliku Node.js do wykonywania kwerend bazy danych
description: Sposób użycia narzędzia Node.js do utworzenia programu, który nawiązuje połączenie z bazą danych Azure SQL Database i wykonuje zapytania za pomocą instrukcji języka T-SQL.
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
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768604"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Szybki start: korzystanie z narzędzia Node.js do wykonywania zapytań w bazie danych Azure SQL Database

W tym przewodniku Szybki start można użyć Node.js do łączenia się z bazą danych SQL platformy Azure i używać instrukcji T-SQL do wykonywania zapytań o dane.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Baza danych SQL platformy Azure](sql-database-single-database-get-started.md)
- Oprogramowanie powiązane z [node.js](https://nodejs.org)

  # <a name="macos"></a>[Macos](#tab/macos)

  Zainstaluj homebrew i node.js, a następnie zainstaluj sterownik ODBC i SQLCMD, wykonując kroki **1.2** i **1.3** w [aplikacji Create Node.js przy użyciu programu SQL Server w systemie macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Zainstaluj Node.js, a następnie zainstaluj sterownik ODBC i SQLCMD, wykonując kroki **1.2** i **1.3** w [aplikacji Create Node.js przy użyciu programu SQL Server na Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Zainstaluj Chocolatey i Node.js, a następnie zainstaluj sterownik ODBC i SQLCMD, wykonując kroki **1.2** i **1.3** w [aplikacji Create Node.js przy użyciu programu SQL Server w systemie Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Skrypty w tym artykule są zapisywane w celu użycia bazy danych **Adventure Works.**

> [!NOTE]
> Opcjonalnie można wybrać opcję użycia wystąpienia zarządzanego sql platformy Azure.
>
> Aby utworzyć i skonfigurować, należy użyć [usługi Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)lub [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), a następnie skonfigurować łączność [lokacji](sql-database-managed-instance-configure-p2s.md) lub [maszyny Wirtualnej.](sql-database-managed-instance-configure-vm.md)
>
> Aby załadować dane, zobacz [przywracanie za pomocą bacpac](sql-database-import.md) z plikiem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) lub zobacz [przywracanie bazy danych Szerokich importerów światowych](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Przejdź do **bazy danych SQL** lub strony wystąpienia zarządzane **sql.**

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**. 

## <a name="create-the-project"></a>Tworzenie projektu

Otwórz wiersz polecenia i utwórz folder o nazwie *sqltest*. Otwórz utworzony folder i uruchom następujące polecenie:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Dodawanie kodu umożliwiającego wykonywanie zapytań w bazie danych

1. W swoim ulubionym edytorze tekstów utwórz nowy plik o nazwie *sqltest.js*.

1. Zastąp jego zawartość poniższym kodem. Następnie dodaj odpowiednie wartości dla swojego serwera, bazy danych, użytkownika i hasła.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
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

- Projektowanie pierwszej bazy danych Azure SQL Database przy użyciu platformy [.NET](sql-database-design-first-database-csharp.md) lub programu [SSMS](sql-database-design-first-database.md)
