---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 07/31/2019
ms.author: mathoma
ms.openlocfilehash: d19e21edd31be461ba30e8985e80c4651bbbb02d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894088"
---
W tym kroku utworzysz Azure SQL Database pojedynczą bazę danych. 

> [!IMPORTANT]
> Pamiętaj, aby skonfigurować reguły zapory w celu używania publicznego adresu IP komputera, którego używasz do wykonania tego artykułu.
>
> Aby uzyskać więcej informacji, zobacz [Tworzenie reguły zapory na poziomie bazy danych](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) lub Określanie adresu IP używanego dla reguły zapory na poziomie serwera dla komputera, zobacz [Tworzenie zapory na poziomie serwera](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę zasobów i pojedynczą bazę danych przy użyciu Azure Portal.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz *SQL Azure* w polu wyszukiwania.
2. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Aby wyświetlić dodatkowe informacje o różnych bazach danych, wybierz pozycję **Pokaż szczegóły** na kafelku **bazy danych** .
3. Wybierz pozycję **Utwórz**:

   ![Tworzenie pojedynczej bazy danych](../media/sql-database-get-started-portal/create-single-database.png)

3. Na karcie **podstawowe** w sekcji **szczegóły projektu** wpisz lub wybierz następujące wartości:

   - **Subskrypcja**: otwórz listę rozwijaną i wybierz poprawną subskrypcję, jeśli nie została wyświetlona.
   - **Grupa zasobów**: Wybierz pozycję **Utwórz nowy**, `myResourceGroup`wpisz i wybierz **przycisk OK**.

     ![Nowa baza danych SQL — Karta podstawowa](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. W sekcji **szczegóły bazy danych** wpisz lub wybierz następujące wartości:

   - **Nazwa bazy danych**: Wprowadź polecenie `mySampleDatabase`.
   - **Serwer**: Wybierz pozycję **Utwórz nowy**, wprowadź następujące wartości, a następnie wybierz pozycję **Wybierz**.
       - **Nazwa serwera**: Typ `mysqlserver`; wraz z liczbami unikatowymi.
       - **Identyfikator logowania administratora serwera**: Wpisz polecenie `azureuser`.
       - **Hasło**: Wpisz złożone hasło spełniające wymagania dotyczące haseł.
       - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej, na przykład `West US 2`.

         ![Nowy serwer](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Pamiętaj, aby zapisać identyfikator logowania administratora serwera i hasło, aby logować się do serwera i baz danych dla tego przewodnika Szybki start oraz pozostałych. Jeśli zapomnisz swój identyfikator logowania lub hasło, możesz uzyskać identyfikator logowania lub zresetować hasło na stronie **serwera SQL**. Aby otworzyć stronę **serwera SQL**, wybierz nazwę serwera na stronie **Przegląd** po utworzeniu bazy danych.

   - **Chcesz użyć elastycznej puli SQL**: Wybierz opcję **nie** .
   - **Obliczenia + magazyn**: Wybierz pozycję **Konfiguruj bazę danych**. 

     ![Szczegóły SQL Database](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Wybierz opcję **aprowizacji** i **5 rdzeń**.

     ![Obliczenia zainicjowany](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Zapoznaj się z ustawieniami **Maksymalna rdzeni wirtualnych**, **minimum rdzeni wirtualnych**, **opóźnienie AutoPause**i **Maksymalny rozmiar danych**. Zmień je zgodnie z potrzebami.
   - Zaakceptuj warunki wersji zapoznawczej, a następnie kliknij przycisk **OK**.
   - Wybierz przycisk **Zastosuj**.

5. Wybierz kartę **Ustawienia dodatkowe** . 
6. W sekcji **Źródło danych** w obszarze **Użyj istniejących danych**wybierz opcję `Sample`.

   ![Dodatkowe ustawienia bazy danych SQL](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Pamiętaj, aby wybrać dane **Przykład (AdventureWorksLT)** , co umożliwi łatwe wykonanie tego i innych przewodników Szybki start usługi Azure SQL Database korzystających z tych danych.

7. Pozostaw pozostałe wartości jako domyślne i wybierz pozycję **Przegląd + Utwórz** w dolnej części formularza.
8. Sprawdź ustawienia końcowe i wybierz pozycję **Utwórz**.

9. W formularzu **SQL Database** wybierz opcję **Utwórz**, aby wdrożyć i aprowizować grupę zasobów, serwer i bazę danych.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Utwórz grupę zasobów i pojedynczą bazę danych przy użyciu programu PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US 2"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Utwórz grupę zasobów i pojedynczą bazę danych przy użyciu polecenia AZ CLI.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

---
