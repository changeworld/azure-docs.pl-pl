---
title: Implementacja rozproszonego geograficznie rozwiązania usługi Azure SQL Database | Microsoft Docs
description: Dowiedz się, jak skonfigurować usługę Azure SQL Database i aplikację pod kątem przechodzenia w tryb failover do zreplikowanej bazy danych i jak testować tryb failover.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 2508d43e876a7e463d68eed1b1ca93ddf0d1e9d1
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913348"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Samouczek: Implementowanie rozproszonej geograficznie bazy danych

W ramach tego samouczka usługa Azure SQL Database i aplikacja zostaną skonfigurowane pod kątem przechodzenia w tryb failover do zdalnego regionu, a następnie zostanie przetestowany plan trybu failover. Omawiane kwestie: 

> [!div class="checklist"]
> * Tworzenie użytkowników bazy danych i udzielanie im uprawnień
> * Konfigurowanie reguły zapory na poziomie bazy danych
> * Tworzenie [grupy trybu failover replikacji geograficznej](sql-database-geo-replication-overview.md)
> * Tworzenie i kompilowanie aplikacji w języku Java na potrzeby odpytywania usługi Azure SQL Database
> * Wykonywanie próbnego odzyskiwania po awarii

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowano najnowszą wersję programu [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs). 
- Zainstalowano usługę Azure SQL Database. W tym samouczku jest używana przykładowa baza danych AdventureWorksLT o nazwie **mySampleDatabase** z jednego z następujących przewodników Szybki start:

   - [Tworzenie bazy danych — portal](sql-database-get-started-portal.md)
   - [Tworzenie bazy danych — interfejs wiersza polecenia](sql-database-cli-samples.md)
   - [Tworzenie bazy danych — PowerShell](sql-database-powershell-samples.md)

- Zidentyfikowano metodę wykonywania skryptów SQL względem bazy danych. Możesz użyć jednego z następujących narzędzi do obsługi zapytań:
   - Edytor zapytań w [witrynie Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji na temat używania edytora zapytań w witrynie Azure Portal, zobacz [Nawiązywanie połączenia i odpytywanie za pomocą edytora zapytań](sql-database-get-started-portal.md#query-the-sql-database).
   - Najnowsza wersja programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), który jest zintegrowanym środowiskiem do zarządzania dowolną infrastrukturą SQL — od programu SQL Server po usługę SQL Database dla systemu Microsoft Windows.
   - Najnowsza wersja programu [Visual Studio Code](https://code.visualstudio.com/docs), który jest graficznym edytorem kodu dla systemów Linux, macOS i Windows obsługującym rozszerzenia, w tym [rozszerzenie mssql](https://aka.ms/mssql-marketplace) na potrzeby wysyłania zapytań do programu Microsoft SQL Server oraz usług Azure SQL Database i SQL Data Warehouse. Aby uzyskać więcej informacji na temat użycia tego narzędzia pod kątem usługi Azure SQL Database, zobacz [Nawiązywanie połączenia i odpytywanie za pomocą programu VS Code](sql-database-connect-query-vscode.md). 

## <a name="create-database-users-and-grant-permissions"></a>Tworzenie użytkowników bazy danych i udzielanie uprawnień

Nawiąż połączenie z bazą danych i utwórz konta użytkowników przy użyciu jednego z następujących narzędzi do obsługi zapytań:

- Edytor zapytań w witrynie Azure Portal
- SQL Server Management Studio
- Visual Studio Code

Te konta użytkowników są automatycznie replikowane na serwer pomocniczy (i synchronizowane). Aby można było użyć programu SQL Server Management Studio lub Visual Studio Code, może być konieczne skonfigurowanie reguły zapory w przypadku łączenia się z klienta pod adresem, dla którego nie skonfigurowano jeszcze zapory. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie reguły zapory na poziomie serwera](sql-database-get-started-portal-firewall.md).

- W oknie zapytania wykonaj następujące zapytanie, aby utworzyć dwa konta użytkownika w bazie danych. Ten skrypt umożliwia udzielenie uprawnień **db_owner** dla konta **app_admin** oraz uprawnień **SELECT** i **UPDATE** dla konta **app_user**. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Tworzenie zapory na poziomie bazy danych

Utwórz [regułę zapory na poziomie bazy danych](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) dla bazy danych SQL. Ta reguła zapory na poziomie bazy danych jest automatycznie replikowana na serwer pomocniczy utworzony w tym samouczku. Dla uproszczenia (na potrzeby tego samouczka) użyj publicznego adresu IP komputera, na którym są wykonywane kroki opisane w tym samouczku. Aby określić adres IP używany na potrzeby reguły zapory na poziomie serwera dla bieżącego komputera, zobacz [Tworzenie zapory na poziomie serwera](sql-database-get-started-portal-firewall.md).  

- W otwartym oknie zapytania zastąp poprzednie zapytanie następującym zapytaniem, zamieniając adresy IP na adresy IP odpowiednie dla używanego środowiska.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Tworzenie grupy automatycznego trybu failover aktywnej replikacji geograficznej 

Przy użyciu programu Azure PowerShell utwórz [grupę automatycznego trybu failover aktywnej replikacji geograficznej](sql-database-geo-replication-overview.md) między istniejącym serwerem usługi Azure SQL i nowym pustym serwerem usługi Azure SQL w regionie platformy Azure. Następnie dodaj przykładową bazę danych do grupy trybu failover.

> [!IMPORTANT]
> Te polecenia cmdlet wymagają programu Azure PowerShell 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Wypełnij zmienne dla skryptów programu PowerShell przy użyciu wartości dotyczących istniejącego serwera i przykładowej bazy danych oraz określ globalnie unikatową nazwę grupy trybu failover.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Utwórz pusty serwer zapasowy w regionie trybu failover.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Utwórz grupę trybu failover między dwoma serwerami.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. Dodaj bazę danych do grupy trybu failover.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Instalowanie oprogramowania Java

W krokach w tej sekcji założono, że wiesz już, jak opracowywać zawartość za pomocą platformy Java, i dopiero zaczynasz pracę z usługą Azure SQL Database. 

### <a name="mac-os"></a>**Mac OS**
Otwórz terminal i przejdź do katalogu, w którym planujesz utworzyć projekt języka Java. Zainstaluj rozwiązania **brew** i **Maven** przez wprowadzenie następujących poleceń: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Aby uzyskać szczegółowe instrukcje dotyczące instalowania i konfigurowania środowisk Java i Maven, przejdź do artykułu [Tworzenie aplikacji za pomocą programu SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), wybierz pozycję **Java** i pozycję **MacOS**, a następnie wykonaj szczegółowe instrukcje dotyczące konfigurowania środowisk Java i Maven w krokach 1.2 i 1.3.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Otwórz terminal i przejdź do katalogu, w którym planujesz utworzyć projekt języka Java. Zainstaluj rozwiązanie **Maven** przez wprowadzenie następujących poleceń:

```bash
sudo apt-get install maven
```

Aby uzyskać szczegółowe instrukcje dotyczące instalowania i konfigurowania środowisk Java i Maven, przejdź do artykułu [Tworzenie aplikacji za pomocą programu SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), wybierz pozycję **Java** i pozycję **Ubuntu**, a następnie wykonaj szczegółowe instrukcje dotyczące konfigurowania środowisk Java i Maven w krokach 1.2, 1.3 i 1.4.

### <a name="windows"></a>**Windows**
Zainstaluj rozwiązanie [Maven](https://maven.apache.org/download.cgi) za pomocą oficjalnego instalatora. Narzędzie Maven ułatwia zarządzanie zależnościami oraz kompilowanie, testowanie i uruchamianie projektu języka Java. Aby uzyskać szczegółowe instrukcje dotyczące instalowania i konfigurowania środowisk Java i Maven, przejdź do artykułu [Tworzenie aplikacji za pomocą programu SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), wybierz pozycję **Java** i pozycję Windows, a następnie wykonaj szczegółowe instrukcje dotyczące konfigurowania środowisk Java i Maven w krokach 1.2 i 1.3.

## <a name="create-sqldbsample-project"></a>Tworzenie projektu SqlDbSample

1. W konsoli poleceń (na przykład Bash) utwórz projekt narzędzia Maven. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Wpisz znak **Y** i naciśnij klawisz **Enter**.
3. Przejdź do katalogu nowo utworzonego projektu.

   ```bash
   cd SqlDbSamples
   ```

4. Za pomocą dowolnego edytora otwórz plik pom.xml w folderze projektu. 

5. Dodaj sterownik JDBC firmy Microsoft dla programu SQL Server do projektu narzędzia Maven, otwierając wybrany edytor tekstu, a następnie kopiując i wklejając następujące wiersze do pliku pom.xml. Nie zastępuj istniejących wartości wstępnie określonych w pliku. Zależność od sterownika JDBC należy wkleić w większej sekcji „dependencies”.   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Określ wersję języka Java na potrzeby kompilowania projektu, dodając następującą sekcję „properties” do pliku pom.xml po sekcji „dependencies”. 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Dodaj następującą sekcję „build” do pliku pom.xml po sekcji „properties” na potrzeby obsługi plików manifestu w plikach jar.       

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```
8. Zapisz i zamknij plik pom.xml.
9. Otwórz plik App.java (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) i zastąp jego zawartość następującą zawartością. Zastąp nazwę grupy trybu failover nazwą Twojej grupy trybu failover. Jeśli zmieniono wartości dla nazwy bazy danych, użytkownika lub hasła, zmień także te wartości.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. Zapisz i zamknij plik App.java.

## <a name="compile-and-run-the-sqldbsample-project"></a>Kompilowanie i uruchamianie projektu SqlDbSample

1. W konsoli poleceń wykonaj następujące polecenie.

   ```bash
   mvn package
   ```
2. Po zakończeniu uruchom następujące polecenie, aby uruchomić aplikację (działa ona przez około godzinę, chyba że zostanie zatrzymana ręcznie):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Wykonanie próbnego odzyskiwania po awarii

1. Wywołaj ręczne przełączenie grupy trybu failover w tryb failover. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Obserwuj wyniki aplikacji podczas pracy w trybie failover. Niektóre operacje wstawiania nie powiodą się podczas odświeżania pamięci podręcznej usługi DNS.     

3. Dowiedz się, jaką rolę pełni serwer odzyskiwania po awarii.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Powrót po awarii.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Obserwuj wyniki aplikacji podczas powrotu po awarii. Niektóre operacje wstawiania nie powiodą się podczas odświeżania pamięci podręcznej usługi DNS.     

6. Dowiedz się, jaką rolę pełni serwer odzyskiwania po awarii.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Kolejne kroki

W ramach tego samouczka przedstawiono konfigurowanie usługi Azure SQL Database i aplikacji pod kątem przechodzenia w tryb failover do zdalnego regionu, a następnie przetestowano plan trybu failover.  W tym samouczku omówiono: 

> [!div class="checklist"]
> * Tworzenie użytkowników bazy danych i udzielanie im uprawnień
> * Konfigurowanie reguły zapory na poziomie bazy danych
> * Tworzenie grupy trybu failover replikacji geograficznej
> * Tworzenie i kompilowanie aplikacji w języku Java na potrzeby odpytywania usługi Azure SQL Database
> * Wykonywanie próbnego odzyskiwania po awarii

Przejdź do następnego samouczka, aby migrować program SQL Server do bazy danych wystąpienia zarządzanego Azure SQL przy użyciu usługi DMS.

> [!div class="nextstepaction"]
>[Migracja programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md)

