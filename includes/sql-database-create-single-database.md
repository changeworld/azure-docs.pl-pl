---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: eff121cfaf4473607110de4553a9bb8021990caf
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444684"
---
W tym kroku utworzysz grupę zasobów i Azure SQL Database pojedynczą bazę danych. 

> [!IMPORTANT]
> Należy pamiętać o skonfigurowaniu reguł zapory do korzystania z publicznego adresu IP komputera, na którym wykonywane są kroki opisane w tym artykule. Reguły zapory na poziomie bazy danych zostaną automatycznie zreplikowane na serwerze pomocniczym.
>
> Aby uzyskać więcej informacji, zobacz [Tworzenie reguły zapory na poziomie bazy danych](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) lub Określanie adresu IP używanego dla reguły zapory na poziomie serwera dla komputera, zobacz [Tworzenie zapory na poziomie serwera](../articles/sql-database/sql-database-server-level-firewall-rule.md).  

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Utwórz grupę zasobów i pojedynczą bazę danych przy użyciu Azure Portal. 

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **bazy danych** , a następnie wybierz pozycję **SQL Database** , aby otworzyć stronę **Tworzenie SQL Database** .

   ![Tworzenie pojedynczej bazy danych](../articles/sql-database/media/sql-database-get-started-portal/create-database-1.png)

3. Na karcie **podstawowe** w sekcji **szczegóły projektu** wpisz lub wybierz następujące wartości:

   - **Subskrypcja**: otwórz listę rozwijaną i wybierz poprawną subskrypcję, jeśli nie została wyświetlona.
   - **Grupa zasobów**: Wybierz pozycję **Utwórz nowy**, `myResourceGroup`wpisz i wybierz **przycisk OK**.

     ![Nowa baza danych SQL — Karta podstawowa](../articles/sql-database/media/sql-database-get-started-portal/new-sql-database-basics.png)

4. W sekcji **szczegóły bazy danych** wpisz lub wybierz następujące wartości:

   - **Nazwa bazy danych**: Wprowadź polecenie `mySampleDatabase`.
   - **Serwer**: Wybierz pozycję **Utwórz nową** i wprowadź następujące wartości, a następnie wybierz pozycję **Wybierz**.
       - **Nazwa serwera**: Typ `mysqlserver`; wraz z liczbami unikatowymi.
       - **Identyfikator logowania administratora serwera**: Wpisz polecenie `azureuser`.
       - **Hasło**: Wpisz złożone hasło spełniające wymagania dotyczące haseł.
       - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej, na przykład `West US 2`.

         ![Nowy serwer](../articles/sql-database/media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Pamiętaj, aby zapisać identyfikator logowania administratora serwera i hasło, aby logować się do serwera i baz danych dla tego przewodnika Szybki start oraz pozostałych. Jeśli zapomnisz swój identyfikator logowania lub hasło, możesz uzyskać identyfikator logowania lub zresetować hasło na stronie **serwera SQL**. Aby otworzyć stronę **serwera SQL**, wybierz nazwę serwera na stronie **Przegląd** po utworzeniu bazy danych.

        ![Szczegóły SQL Database](../articles/sql-database/media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Chcesz użyć elastycznej puli SQL**: Wybierz opcję **nie** .
   - **Obliczenia + magazyn**: Wybierz pozycję **Konfiguruj bazę danych** i w tym przewodniku Szybki Start wybierz pozycję **Opcje zakupu oparte na rdzeń wirtualny**

     ![Opcje zakupu oparte na rdzeniach wirtualnych](../articles/sql-database/media/sql-database-get-started-portal/create-database-vcore.png)

   - Wybierz opcję **aprowizacji** i **obliczenia**.

     ![warstwa obliczeń bezserwerowych](../articles/sql-database/media/sql-database-get-started-portal/create-database-serverless.png)

   - Zapoznaj się z ustawieniami **maksymalnych rdzeni wirtualnych**, **min rdzeni wirtualnych**, **opóźnieniem AutoPause**i maksymalnym rozmiarem **danych**. Zmień je zgodnie z potrzebami.
   - Zaakceptuj warunki wersji zapoznawczej, a następnie kliknij przycisk **OK**.
   - Wybierz przycisk **Zastosuj**.

5. Wybierz kartę **Ustawienia dodatkowe** . 
6. W sekcji **Źródło danych** w obszarze **Użyj istniejących danych**wybierz opcję `Sample`. 

   ![Dodatkowe ustawienia bazy danych SQL](../articles/sql-database/media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Pamiętaj, aby wybrać dane **Przykład (AdventureWorksLT)** , co umożliwi łatwe wykonanie tego i innych przewodników Szybki start usługi Azure SQL Database korzystających z tych danych.

7. Pozostaw pozostałe wartości jako domyślne i wybierz pozycję **Przegląd + Utwórz** w dolnej części formularza.
8. Sprawdź ustawienia końcowe i wybierz pozycję **Utwórz**.

9. W formularzu **SQL Database** wybierz opcję **Utwórz**, aby wdrożyć i aprowizować grupę zasobów, serwer i bazę danych.

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](updated-for-az.md)]

Utwórz grupę zasobów i pojedynczą bazę danych przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   $SubscriptionId = '<Your Azure subscription ID>'
   $ResourceGroupName = "myResourceGroup" # to randomize: "myResourceGroup-$(Get-Random)"
   $Location = "westus2"
   $AdminLogin = "azureuser"
   $Password = "ChangeYourAdminPassword1"
   $ServerName = "mysqlserver" # to randomize: "mysqlserver-$(Get-Random)"
   $DatabaseName = "mySampleDatabase"
   
   
   # The ip address range that you want to allow to access your server 
   #(leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"
   
   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   
   # Create a server with a system wide unique server name
   $server = New-AzSqlServer -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -Location $Location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AdminLogin, $(ConvertTo-SecureString -String $Password -AsPlainText -Force))
   
   # Create a server firewall rule that allows access from the specified IP range
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   
   # Create General Purpose Gen4 database with 1 vCore
   $database = New-AzSqlDatabase  -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -DatabaseName $DatabaseName `
      -Edition GeneralPurpose `
      -VCore 1 `
      -ComputeGeneration Gen4  `
      -MinimumCapacity 1 `
      -SampleName "AdventureWorksLT"
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Utwórz grupę zasobów i pojedynczą bazę danych przy użyciu polecenia AZ CLI. 


   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   export subscriptionID=<Your Subscription ID>
   export ResourceGroupName=myResourceGroup # to randomize: myResourceGroup-$RANDOM
   export Location=WestUS2
   export AdminLogin=azureuser
   export Password=ChangeYourAdminPassword1
   export ServerName="mysqlserver" # to randomize: mysqlserver-$RANDOM
   export DatabaseName=mySampleDatabase
   
   # The ip address range that you want to allow to access your DB. 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   export startip=0.0.0.0
   export endip=0.0.0.0
  
   # Connect to Azure
   az login

   $ Set subscription ID
   az account set --subscription $SubscriptionID
   
   # Create a resource group
   az group create \
      --name $ResourceGroupName \
      --location $Location
   
   # Create a logical server in the resource group
   az sql server create \
      --name $ServerName \
      --resource-group $ResourceGroupName \
      --location $Location  \
      --admin-user $AdminLogin\
      --admin-password $Password
   
   # Configure a firewall rule for the server
   az sql server firewall-rule create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a database in the server 
   az sql db create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      --name $DatabaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen4 \
      --capacity 1 \
   ```

---