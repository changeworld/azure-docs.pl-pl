---
title: Korzystanie z języka Java do wykonywania zapytań w bazie danych Azure SQL | Microsoft Docs
description: Informacje na temat sposobu użycia języka Java do utworzenia programu, który nawiązuje połączenie z bazą danych Azure SQL Database i wykonuje zapytania za pomocą instrukcji języka T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 2d9ce34d52d08b4dd38caaadfab48b7a69870e9a
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447932"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Szybki start: Korzystanie z języka Java do wykonywania zapytań w bazie danych Azure SQL Database

W tym przewodniku Szybki start przedstawiono sposób nawiązywania połączeń z bazą danych Azure SQL Database za pomocą języka [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). Następnie można użyć instrukcji języka T-SQL w celu wykonywania zapytań o dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przykład, upewnij się, że dysponujesz następującymi elementami:

- Baza danych Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  || Pojedyncza baza danych | Wystąpienie zarządzane |
  |:--- |:--- |:---|
  | Przycisk Utwórz| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [Interfejs wiersza polecenia](scripts/sql-database-create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Program PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [Program PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurowanie | [Reguła zapory IP na poziomie serwera](sql-database-server-level-firewall-rule.md)| [Łączność z maszyny wirtualnej](sql-database-managed-instance-configure-vm.md)|
  |||[Łączność ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Przywróć lub zaimportować Adventure Works z [BACPAC](sql-database-import.md) plik wchodzącej w skład [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. Za pomocą wystąpienia zarządzanego należy zaimportować bazę danych Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty znajdujące się w tym artykule, aby korzystały z bazy danych Wide World Importers.

- Zainstalowane oprogramowanie związane z językiem Java odpowiednie dla systemu operacyjnego:

  - **MacOS**: zainstaluj oprogramowanie Homebrew i środowisko Java, a następnie zainstaluj pakiet Maven. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu**: zainstaluj zestaw Java Development Kit, a następnie zainstaluj pakiet Maven. Zobacz [kroki 1.2, 1.3 i 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows**: zainstaluj środowisko Java, a następnie zainstaluj pakiet Maven. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Otwórz stronę **Bazy danych SQL** lub **Wystąpienia zarządzane SQL**.

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**. 

## <a name="create-the-project"></a>Tworzenie projektu

1. Z poziomu wiersza polecenia utwórz nowy projekt rozwiązania Maven o nazwie *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Zmień folder na *sqltest*, a następnie otwórz plik *pom.xml* w ulubionym edytorze tekstów. Dodaj **sterownik JDBC firmy Microsoft dla programu SQL Server** do zależności projektu, używając poniższego kodu.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. Również w pliku *pom.xml* dodaj następujące właściwości do projektu. W przypadku braku sekcji właściwości można ją dodać po zależnościach.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Zapisz i zamknij plik *pom.xml*.

## <a name="add-code-to-query-database"></a>Dodawanie kodu umożliwiającego wykonywanie zapytań w bazie danych

1. Projekt Maven powinien już zawierać plik o nazwie *App.java* w następującej lokalizacji:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Otwórz ten plik i zastąp jego zawartość następującym kodem. Następnie dodaj odpowiednie wartości dla swojego serwera, bazy danych, użytkownika i hasła.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > W przykładzie kodu użyto przykładowej bazy danych **AdventureWorksLT** dla usługi Azure SQL.

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom aplikację.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Sprawdź, czy zostało zwróconych 20 pierwszych wierszy, i zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Projektowanie pierwszej bazy danych SQL na platformie Azure](sql-database-design-first-database.md)  

- [Sterownik JDBC firmy Microsoft dla programu SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Zgłaszanie problemów/zadawanie pytań](https://github.com/microsoft/mssql-jdbc/issues)  
