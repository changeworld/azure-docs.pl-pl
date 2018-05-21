---
title: Korzystanie z języka Java do wykonywania zapytań w bazie danych Azure SQL | Microsoft Docs
description: W tym temacie przedstawiono sposób użycia języka Java do utworzenia programu, który nawiązuje połączenie z bazą danych SQL Azure i wykonuje zapytania za pomocą instrukcji języka Transact-SQL.
services: sql-database
author: ajlam
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: java
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: andrela
ms.openlocfilehash: e19006d177777b18e1665ec5039f9c28f7e54df5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="use-java-to-query-an-azure-sql-database"></a>Korzystanie z języka Java do wykonywania zapytań w bazie danych Azure SQL

W tym przewodniku Szybki start pokazano, jak używać języka [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) w celu nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Reguła zapory poziomu serwera](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) dla publicznego adresu IP komputera, który będzie używany w tym przewodniku Szybki start.

- W systemie operacyjnym zainstalowano język Java i związane z nim oprogramowanie:

    - **System MacOS**: zainstaluj oprogramowanie Homebrew i Java, a następnie zainstaluj pakiet Maven. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).
    - **System Ubuntu**: zainstaluj pakiet Java Development Kit i zainstaluj pakiet Maven. Zobacz [kroki 1.2, 1.3 i 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).
    - **System Windows**: zainstaluj pakiet Java Development Kit i zainstaluj pakiet Maven. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).    

## <a name="sql-server-connection-information"></a>Informacje o połączeniu z serwerem SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-maven-project-and-dependencies"></a>**Utwórz projekt Maven i zależności**
1. Z poziomu terminalu utwórz nowy projekt rozwiązania Maven o nazwie **sqltest**. 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. Wprowadź **T** po wyświetleniu monitu.
3. Zmień katalog na **sqltest**, a następnie otwórz plik ***pom.xml*** w ulubionym edytorze tekstów.  Dodaj **sterownik JDBC firmy Microsoft dla programu SQL Server** do zależności projektu, używając następującego kodu:

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.4.0.jre8</version>
   </dependency>
   ```

4. Również w pliku ***pom.xml*** dodaj następujące właściwości do projektu.  W przypadku braku sekcji właściwości można ją dodać po zależnościach.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. Zapisz i zamknij plik ***pom.xml***.

## <a name="insert-code-to-query-sql-database"></a>Wstawianie kodu zapytania bazy danych SQL

1. W projekcie Maven w lokalizacji ..\sqltest\src\main\java\com\sqlsamples\App.Java powinien już znajdować się plik o nazwie ***App.java***

2. Otwórz plik, zastąp jego zawartość następującym kodem i dodaj odpowiednie wartości dla serwera, bazy danych, użytkownika i hasła.

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
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom następujące polecenia:

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. Sprawdź, czy zostało zwróconych 20 pierwszych wierszy, a następnie zamknij okno aplikacji.


## <a name="next-steps"></a>Następne kroki
- [Projektowanie pierwszej bazy danych SQL na platformie Azure](sql-database-design-first-database.md)
- [Sterownik JDBC firmy Microsoft dla programu SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Zgłaszanie problemów/zadawanie pytań](https://github.com/microsoft/mssql-jdbc/issues)

