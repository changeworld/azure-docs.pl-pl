---
title: Implementowanie rozproszonej geograficznie rozwiązanie bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, skonfiguruj bazę danych Azure SQL i aplikacji dla trybu failover do zreplikowanej bazy danych oraz testowanie trybu failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6022c016b83ffe1362db4d826a5ee4397afd4128
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57844147"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Samouczek: Implementowanie rozproszonej geograficznie bazy danych

Konfigurowanie usługi Azure SQL database i aplikacji dla trybu failover do zdalnego regionu i przetestuj plan trybu failover. Omawiane kwestie:

> [!div class="checklist"]
> - Utwórz [grupy trybu failover](sql-database-auto-failover-group.md)
> - Uruchamianie aplikacji języka Java do wykonywania zapytań usługi Azure SQL database
> - Testowanie pracy w trybie failover

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Do ukończenia tego samouczka, upewnij się, że zainstalowano następujące elementy:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Baza danych Azure SQL Database. Aby utworzyć jedno użycie
  - [Portal](sql-database-single-database-get-started.md)
  - [Interfejs wiersza polecenia](sql-database-cli-samples.md)
  - [Program PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > W tym samouczku użyto *AdventureWorksLT* przykładowej bazy danych.

- Java i Maven, zobacz [kompilacji aplikacji za pomocą programu SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), wyróżnij **Java** i wybierz środowisko, a następnie postępuj zgodnie z instrukcjami.

> [!IMPORTANT]
> Pamiętaj skonfigurować reguły zapory do Użyj publicznego adresu IP komputera, na którym wykonujesz kroki opisane w tym samouczku. Bazy danych na poziomie zapory, które zasady są automatycznie replikowane do serwera pomocniczego.
>
> Aby uzyskać informacje, zobacz [Tworzenie reguły zapory na poziomie bazy danych](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) lub, aby określić adres IP używany dla reguły zapory na poziomie serwera dla komputera adresem [utworzyć zaporę na poziomie serwera](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Utwórz grupę trybu failover

Przy użyciu programu Azure PowerShell utworzyć [grupy trybu failover](sql-database-auto-failover-group.md) między istniejącym serwerze Azure SQL i nowym serwerze Azure SQL w innym regionie. Następnie dodaj przykładową bazę danych do grupy trybu failover.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Aby utworzyć grupy trybu failover, uruchom następujący skrypt:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Ustawienia replikacji geograficznej można także zmienić w witrynie Azure portal po wybraniu bazy danych, a następnie **ustawienia** > **Geografickou Replikaci**.

![Ustawienia replikacji geograficznej](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Uruchamianie przykładowego projektu

1. W konsoli Utwórz projekt narzędzia Maven za pomocą następującego polecenia:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Typ **Y** i naciśnij klawisz **Enter**.

1. Zmień katalogi do nowego projektu.

   ```bash
   cd SqlDbSample
   ```

1. Za pomocą ulubionego edytora, otwórz *pom.xml* pliku w folderze projektu.

1. Dodaj sterownik JDBC firmy Microsoft dla programu SQL Server zależności przez dodanie poniższego `dependency` sekcji. Zależności należy wkleić w większej `dependencies` sekcji.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Określ wersję języka Java, dodając `properties` sekcji po `dependencies` sekcji:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Obsługi plików manifestu, dodając `build` sekcji po `properties` sekcji:

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

1. Zapisz i Zamknij *pom.xml* pliku.

1. Otwórz *App.java* plik znajdujący się w.. \SqlDbSample\src\main\java\com\sqldbsamples i zastąp jego zawartość następującym kodem:

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

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

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
      // Query the data previously inserted into the primary database from the geo replicated database
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
      // Query the high water mark id stored in the table to be able to make unique inserts
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

1. Zapisz i Zamknij *App.java* pliku.

1. W konsoli poleceń uruchom następujące polecenie:

   ```bash
   mvn package
   ```

1. Uruchom aplikację, który będzie uruchamiany na godzinę, aż do zatrzymania się ręcznie, zezwolenie na czas, aby uruchomić test trybu failover.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Testowanie pracy w trybie failover

Uruchom następujące skrypty w celu symulowania przejścia w tryb failover i obserwuj wyniki aplikacji. Zwróć uwagę, jak niektóre wstawia i wybiera zakończy się niepowodzeniem podczas migracji bazy danych.

Możesz również sprawdzić rolę serwera odzyskiwania po awarii podczas testu przy użyciu następującego polecenia:

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Aby przetestować tryb failover:

1. Rozpocznij ręczne przełączenie grupy trybu failover:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Przywróć grupę trybu failover na serwer podstawowy:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został skonfigurowany, Azure SQL database i aplikacji dla trybu failover do zdalnego regionu i przetestowany plan trybu failover. W tym samouczku omówiono:

> [!div class="checklist"]
> - Tworzenie grupy trybu failover replikacji geograficznej
> - Uruchamianie aplikacji języka Java do wykonywania zapytań usługi Azure SQL database
> - Testowanie pracy w trybie failover

Przejdź do następnego samouczka, dotyczące migracji przy użyciu usługi DMS.

> [!div class="nextstepaction"]
> [Migrowanie programu SQL Server do wystąpienia zarządzanego bazy danych Azure SQL przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md)
