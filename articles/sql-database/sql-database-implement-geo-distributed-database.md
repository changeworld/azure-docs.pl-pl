---
title: Wdrażanie rozwiązania rozproszonego geograficznie
description: Dowiedz się, jak skonfigurować bazę danych i aplikację SQL platformy Azure do pracy awaryjnej do replikowanej bazy danych i przetestować proces pracy awaryjnej.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 58d5bd4a7f3087e11056354f7534c3c9dbebca3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067283"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Samouczek: Implementowanie bazy danych rozproszonych geograficznie

Skonfiguruj bazę danych i aplikację SQL platformy Azure do pracy awaryjnej w regionie zdalnym i przetestuj plan pracy awaryjnej. Omawiane kwestie:

> [!div class="checklist"]
> - Tworzenie [grupy trybu failover](sql-database-auto-failover-group.md)
> - Uruchamianie aplikacji Java w celu wykonywania kwerend w bazie danych SQL platformy Azure
> - Testowanie pracy w trybie failover

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Aby ukończyć samouczek, upewnij się, że zainstalowano następujące elementy:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Pojedyncza baza danych w bazie danych SQL usługi Azure. Aby utworzyć jedno użycie,
  - [Portal](sql-database-single-database-get-started.md)
  - [Cli](sql-database-cli-samples.md)
  - [Powershell](sql-database-powershell-samples.md)

  > [!NOTE]
  > Samouczek używa *adventureworkslt* przykładowej bazy danych.

- Java i Maven, zobacz [Tworzenie aplikacji przy użyciu programu SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), wyróżnij język **Java** i wybierz środowisko, a następnie wykonaj kroki.

> [!IMPORTANT]
> Należy skonfigurować reguły zapory, aby używać publicznego adresu IP komputera, na którym wykonujesz kroki opisane w tym samouczku. Reguły zapory na poziomie bazy danych będą automatycznie replikowane na serwer pomocniczy.
>
> Aby uzyskać więcej informacji, zobacz [Tworzenie reguły zapory na poziomie bazy danych](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) lub określenie adresu IP używanego dla reguły zapory na poziomie serwera dla komputera, zobacz Tworzenie [zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Tworzenie grupy trybu failover

Korzystając z programu Azure PowerShell, należy utworzyć [grupy trybu failover](sql-database-auto-failover-group.md) między istniejącym serwerem SQL platformy Azure a nowym serwerem SQL platformy Azure w innym regionie. Następnie dodaj przykładową bazę danych do grupy trybu failover.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Aby utworzyć grupę trybu failover, uruchom następujący skrypt:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!IMPORTANT]
> Uruchom, `az login` aby zalogować się na platformie Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

Ustawienia replikacji geograficznej można również zmienić w witrynie Azure Portal, wybierając bazę danych, a następnie **ustawienia** > **replikacji geograficznej**.

![Ustawienia replikacji geograficznej](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Uruchamianie przykładowego projektu

1. W konsoli utwórz projekt Maven za pomocą następującego polecenia:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Wpisz **Y** i naciśnij **klawisz Enter**.

1. Zmień katalogi na nowy projekt.

   ```bash
   cd SqlDbSample
   ```

1. Za pomocą ulubionego edytora otwórz plik *pom.xml* w folderze projektu.

1. Dodaj zależność microsoft JDBC driver dla programu `dependency` SQL Server, dodając następującą sekcję. Zależność musi zostać wklejona `dependencies` w większej sekcji.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Określ wersję języka `properties` Java, `dependencies` dodając sekcję po sekcji:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Obsługa plików manifestu `build` przez `properties` dodanie sekcji po sekcji:

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

1. Zapisz i zamknij plik *pom.xml*.

1. Otwórz plik *App.java* znajdujący się w pliku .. \SqlDbSample\src\main\java\com\sqldbsamples i zastąp zawartość następującym kodem:

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

1. Zapisz i zamknij plik *App.java.*

1. W konsoli poleceń uruchom następujące polecenie:

   ```bash
   mvn package
   ```

1. Uruchom aplikację, która będzie działać przez około 1 godzinę, aż zostanie zatrzymana ręcznie, co pozwala na czas uruchamiania testu pracy awaryjnej.

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

Uruchom następujące skrypty, aby symulować pracy awaryjnej i obserwować wyniki aplikacji. Zwróć uwagę, jak niektóre wstawia i wybiera zakończy się niepowodzeniem podczas migracji bazy danych.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Rolę serwera odzyskiwania po awarii można sprawdzić podczas testu za pomocą następującego polecenia:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Aby przetestować przemieścić po awarii:

1. Rozpocznij ręczną pracę awaryjną grupy trybu failover:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rolę serwera odzyskiwania po awarii można sprawdzić podczas testu za pomocą następującego polecenia:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Aby przetestować przemieścić po awarii:

1. Rozpocznij ręczną pracę awaryjną grupy trybu failover:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano bazę danych SQL platformy Azure i aplikację do pracy awaryjnej w regionie zdalnym i przetestowano plan pracy awaryjnej. W tym samouczku omówiono:

> [!div class="checklist"]
> - Tworzenie grupy trybu failover replikacji geograficznej
> - Uruchamianie aplikacji Java w celu wykonywania kwerend w bazie danych SQL platformy Azure
> - Testowanie pracy w trybie failover

Przejdź do następnego samouczka na temat migracji przy użyciu dms.

> [!div class="nextstepaction"]
> [Migrowanie wystąpienia zarządzanego bazy danych SQL Server do usługi Azure przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md)
