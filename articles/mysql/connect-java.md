---
title: Połącz się przy użyciu usługi Java — usługa Azure Database for MySQL
description: Ten przewodnik Szybki start zawiera przykładowy kod Java, którego można użyć do łączenia się i wykonywania zapytań o dane z bazy danych usługi Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019
ms.topic: quickstart
ms.devlang: java
ms.date: 3/18/2020
ms.openlocfilehash: b5f1cbf2f822f350b1eeba032199676651364d84
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983827"
---
# <a name="quickstart-use-java-to-connect-to-and-query-data-in-azure-database-for-mysql"></a>Szybki start: łączenie się z danymi usługi Azure Database dla mysql za pomocą języka Java i ich wykonywanie

W tym przewodniku Szybki start można połączyć się z usługą Azure Database for MySQL przy użyciu aplikacji Java i sterownika JDBC MariaDB Connector/J. Następnie używasz instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych z platform Mac, Ubuntu Linux i Windows. 

W tym temacie przyjęto założenie, że jesteś zaznajomiony z tworzenia przy użyciu języka Java, ale jesteś nowy do pracy z usługą Azure Database dla MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Usługa Azure Database dla serwera MySQL. [Utwórz usługę Azure Database dla serwera MySQL przy użyciu witryny Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) lub [utwórz usługę Azure Database dla serwera MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.](quickstart-create-mysql-server-database-using-azure-cli.md)
- Zabezpieczenia połączeń usługi Azure Database for MySQL są skonfigurowane przy zastosowaniu otwartej zapory i ustawień połączenia SSL skonfigurowanych dla aplikacji.

## <a name="obtain-the-mariadb-connector"></a>Uzyskaj łącznik MariaDB

Uzyskaj łącznik [MariaDB/J](https://mariadb.com/kb/en/library/mariadb-connector-j/) przy użyciu jednego z następujących metod:
   - Użyj pakietu Maven [mariadb-java-client,](https://search.maven.org/search?q=a:mariadb-java-client) aby uwzględnić [zależność mariadb-java-client](https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client) w pliku POM dla projektu.
   - Pobierz sterownik JDBC [MariaDB Connector/J](https://downloads.mariadb.org/connector-java/) i dołącz plik jar JDBC (na przykład mariadb-java-client-2.4.3.jar) do ścieżki klasy aplikacji. Szczegółowe informacje na temat ścieżki klasy można znaleźć w dokumentacji środowiska, takich jak [Apache Tomcat](https://tomcat.apache.org/tomcat-7.0-doc/class-loader-howto.html) lub [Java SE](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/classpath.html)

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z usługą Azure Database for MySQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Z menu po lewej stronie w witrynie Azure portal wybierz pozycję **Wszystkie zasoby**, a następnie wyszukaj utworzony serwer (np. **mydemoserver**).
3. Wybierz nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 ![Nazwa serwera usługi Azure Database for MySQL](./media/connect-java/azure-database-mysql-server-name.png)

## <a name="connect-create-table-and-insert-data"></a>Nawiązywanie połączenia, tworzenie tabeli i wstawianie danych

Użyj poniższego kodu, aby nawiązać połączenie i załadować dane przy użyciu funkcji z instrukcją **INSERT** języka SQL. Metoda [GetConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) jest używana do nawiązania połączenia z usługą MySQL. Metody [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) i execute() są używane do przenoszenia i tworzenia tabeli. Obiekt prepareStatement jest używany do tworzenia poleceń insert, z metodami setString() i setInt() do powiązania wartości parametrów. Metoda executeUpdate() uruchamia polecenie dla każdego zestawu parametrów w celu wstawienia wartości. 

Zastąp parametry hosta, bazy danych, użytkownika i hasła wartościami, które zostały określone podczas tworzenia własnego serwera i bazy danych.

```java
import java.sql.*;
import java.util.Properties;

public class CreateTableInsertRows {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Drop previous table of same name if one exists.
                Statement statement = connection.createStatement();
                statement.execute("DROP TABLE IF EXISTS inventory;");
                System.out.println("Finished dropping table (if existed).");
    
                // Create table.
                statement.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
                System.out.println("Created table.");
                
                // Insert some data into table.
                int nRowsInserted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("INSERT INTO inventory (name, quantity) VALUES (?, ?);");
                preparedStatement.setString(1, "banana");
                preparedStatement.setInt(2, 150);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "orange");
                preparedStatement.setInt(2, 154);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "apple");
                preparedStatement.setInt(2, 100);
                nRowsInserted += preparedStatement.executeUpdate();
                System.out.println(String.format("Inserted %d row(s) of data.", nRowsInserted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
    
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}

```

## <a name="read-data"></a>Odczyt danych

Użyj poniższego kodu, aby odczytać dane za pomocą instrukcji **SELECT** języka SQL. Metoda [GetConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) jest używana do nawiązania połączenia z usługą MySQL. Metody [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) i executeQuery() są używane do łączenia i uruchamiania instrukcji select. Wyniki są przetwarzane przy użyciu obiektu ResultSet. 

Zastąp parametry hosta, bazy danych, użytkownika i hasła wartościami, które zostały określone podczas tworzenia własnego serwera i bazy danych.

```java
import java.sql.*;
import java.util.Properties;

public class ReadTable {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
    
                Statement statement = connection.createStatement();
                ResultSet results = statement.executeQuery("SELECT * from inventory;");
                while (results.next())
                {
                    String outputString = 
                        String.format(
                            "Data row = (%s, %s, %s)",
                            results.getString(1),
                            results.getString(2),
                            results.getString(3));
                    System.out.println(outputString);
                }
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database."); 
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="update-data"></a>Aktualizowanie danych

Użyj poniższego kodu, aby zmienić dane za pomocą instrukcji **UPDATE** języka SQL. Metoda [GetConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) jest używana do nawiązania połączenia z usługą MySQL. Metody [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) i executeUpdate() są używane do przygotowywania i uruchamiania instrukcji update. 

Zastąp parametry hosta, bazy danych, użytkownika i hasła wartościami, które zostały określone podczas tworzenia własnego serwera i bazy danych.

```java
import java.sql.*;
import java.util.Properties;

public class UpdateTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Modify some data in table.
                int nRowsUpdated = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?;");
                preparedStatement.setInt(1, 200);
                preparedStatement.setString(2, "banana");
                nRowsUpdated += preparedStatement.executeUpdate();
                System.out.println(String.format("Updated %d row(s) of data.", nRowsUpdated));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="delete-data"></a>Usuwanie danych

Użyj poniższego kodu, aby usunąć dane za pomocą instrukcji **DELETE** języka SQL. Metoda [GetConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) jest używana do nawiązania połączenia z usługą MySQL.  Metody [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) i executeUpdate() są używane do przygotowania i uruchomienia instrukcji usuwania. 

Zastąp parametry hosta, bazy danych, użytkownika i hasła wartościami, które zostały określone podczas tworzenia własnego serwera i bazy danych.

```java
import java.sql.*;
import java.util.Properties;

public class DeleteTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Delete some data from table.
                int nRowsDeleted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("DELETE FROM inventory WHERE name = ?;");
                preparedStatement.setString(1, "orange");
                nRowsDeleted += preparedStatement.executeUpdate();
                System.out.println(String.format("Deleted %d row(s) of data.", nRowsDeleted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych MySQL do usługi Azure Database for MySQL przy użyciu zrzutu i przywracania](concepts-migrate-dump-restore.md)
