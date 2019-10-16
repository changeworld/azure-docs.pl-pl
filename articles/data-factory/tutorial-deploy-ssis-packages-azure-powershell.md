---
title: Aprowizowanie środowiska Azure-SSIS Integration Runtime za pomocą programu PowerShell | Microsoft Docs
description: Dowiedz się, jak aprowizować środowisko Azure-SSIS Integration Runtime w usłudze Azure Data Factory za pomocą programu PowerShell w celu wdrażania i uruchamiania pakietów SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7a4ae2198653ea8adab136caef0f812019efd998
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326108"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime w usłudze Azure Data Factory za pomocą programu PowerShell

Ten samouczek zawiera kroki umożliwiające zainicjowanie obsługi Integration Runtime (IR) na platformie SQL Server Integration Services Azure w Azure Data Factory (ADF). Azure-SSIS IR obsługuje uruchamianie pakietów wdrożonych w katalogu usług SSIS (SSISDB) hostowanych przez Azure SQL Database Server/wystąpienie zarządzane (model wdrażania projektu) i te wdrożone w systemach plików/udziałach plików/Azure Files (model wdrażania pakietu). Po zainicjowaniu Azure-SSIS IR można używać znanych narzędzi, takich jak SQL Server narzędzia do obsługi danych (SSDT)/SQL Server Management Studio (SSMS) i narzędzi wiersza polecenia, takich jak `dtinstall` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4, aby wdrożyć i uruchomić pakiety na platformie Azure. W tym samouczku wykonasz następujące czynności:

> [!NOTE]
> W tym artykule zainicjowano Azure-SSIS IR przy użyciu Azure PowerShell. Aby użyć aplikacji Azure Portal/ADF do aprowizacji Azure-SSIS IR, zobacz [Samouczek: Inicjowanie obsługi Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie środowiska Azure SSIS Integration Runtime
> * Uruchamianie środowiska Azure SSIS Integration Runtime
> * Przeglądanie kompletnego skryptu
> * Wdrażanie pakietów usług SSIS

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. Aby uzyskać koncepcyjne informacje dotyczące środowiska Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Serwer Azure SQL Database (opcjonalnie)** . Jeśli nie masz jeszcze serwera bazy danych, utwórz go w Azure Portal przed rozpoczęciem pracy. ADF utworzy SSISDB na tym serwerze bazy danych. Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja umożliwia wykonywanie dzienników wykonywania zapisu środowiska Integration Runtime w usłudze SSISDB bez przekroczenia regionów świadczenia usługi Azure. 
    - W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie Azure SQL Database pojedynczej bazy danych, elastycznej puli i wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). Jeśli używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym w sieci wirtualnej, aby hostować SSISDB lub wymagać dostępu do danych lokalnych, musisz dołączyć Azure-SSIS IR do sieci wirtualnej, a następnie zapoznaj się z tematem [tworzenie Azure-SSIS IR w wirtualnej Sieć](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. Nie dotyczy to sytuacji, gdy do hostowania SSISDB jest używany serwer Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz polecenie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, na listę adres IP klienta w ustawieniach zapory dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Można nawiązać połączenie z serwerem bazy danych przy użyciu uwierzytelniania SQL z poświadczeniami administratora serwera lub uwierzytelnianiem Azure Active Directory (AAD) z tożsamością zarządzaną dla Twojego ADF.  W przypadku drugiego rozwiązania musisz dodać tożsamość zarządzaną usługi Azure Data Factory do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR przy użyciu uwierzytelniania usługi AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Upewnij się, że serwer bazy danych nie ma już SSISDB. Inicjowanie obsługi Azure-SSIS IR nie obsługuje korzystania z istniejącej SSISDB.
- Zainstalowanie programu **Azure PowerShell**. Postępuj zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-Az-ps), jeśli chcesz uruchomić skrypt programu PowerShell w celu aprowizacji Azure-SSIS IR.

> [!NOTE]
> - Aby zapoznać się z listą regionów świadczenia usługi Azure, w których obecnie są dostępne ADF i Azure-SSIS IR, zapoznaj się z tematem obsługa systemu APD i usług [SSIS IR](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all) 

## <a name="launch-windows-powershell-ise"></a>Uruchamianie programu Windows PowerShell ISE

Uruchom program **Windows PowerShell ISE** przy użyciu uprawnień administracyjnych. 

## <a name="create-variables"></a>Tworzenie zmiennych

Skopiuj i wklej następujący skrypt — Określ wartości dla zmiennych. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Logowanie i wybieranie subskrypcji

Dodaj następujący kod skryptu, aby się zalogować, i wybierz subskrypcję platformy Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Weryfikowanie połączenia z serwerem bazy danych

Dodaj następujący skrypt, aby zweryfikować serwer Azure SQL Database. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Aby utworzyć Azure SQL Database w ramach skryptu, zobacz następujący przykład: 

Ustaw wartości dla zmiennych, które nie zostały jeszcze zdefiniowane. Na przykład: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Jeśli grupa zasobów już istnieje, nie Kopiuj tego kodu do skryptu. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Uruchom następujące polecenie, aby utworzyć fabrykę danych:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime

Uruchom następujące polecenia, aby utworzyć środowisko Azure-SSIS Integration Runtime, które uruchamia pakiety usług SSIS na platformie Azure.

Jeśli nie używasz SSISDB, możesz pominąć parametry CatalogServerEndpoint, CatalogPricingTier i CatalogAdminCredential.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Uruchamianie środowiska Integration Runtime

Uruchom następujące polecenia, aby uruchomić środowisko Azure-SSIS Integration Runtime.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Bez czasu instalacji niestandardowej ten proces powinien zostać zakończony w ciągu 5 minut.
>
> Jeśli używasz SSISDB, usługa ADF będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. 
> 
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel/Access i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które są już obsługiwane przez wbudowane składniki. Możesz również zainstalować dodatkowe składniki, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Pełny skrypt

Skrypt programu PowerShell w tej sekcji służy do konfigurowania wystąpienia Azure-SSIS IR, na którym są uruchamiane pakiety usług SSIS. Po pomyślnym uruchomieniu tego skryptu możesz wdrażać i uruchamiać pakiety usług SSIS na platformie Azure.

1. Uruchom środowisko Windows PowerShell Integrated Scripting Environment (ISE).
2. W środowisku ISE uruchom następujące polecenie w wierszu polecenia.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Skopiuj skrypt programu PowerShell w tej sekcji i wklej go do środowiska ISE.
4. Podaj odpowiednie wartości dla wszystkich parametrów na początku skryptu.
5. Uruchom skrypt. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorowanie środowiska Azure-SSIS IR

W poniższych artykułach można znaleźć szczegółowe informacje o monitorowaniu środowiska Azure-SSIS IR i zarządzania nim. 

- [Monitoruj Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Zarządzaj Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, możesz wdrożyć w nim pakiety i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi SSDT/SSMS, które łączą się z serwerem bazy danych za pośrednictwem swojego punktu końcowego serwera. W przypadku Azure SQL Database serwera/wystąpienia zarządzanego z publicznym punktem końcowym format punktu końcowego serwera jest odpowiednio `<server name>.database.windows.net` @ no__t-1 @ no__t-2. Jeśli nie korzystasz z programu SSISDB, możesz wdrożyć pakiety w systemach plików/udziałach plików/Azure Files i uruchamiać je na Azure-SSIS IR przy użyciu `dtinstall` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 narzędzi wiersza polecenia. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). W obu przypadkach wdrożone pakiety można również uruchomić na Azure-SSIS IR przy użyciu działania wykonywania pakietu SSIS w potokach ADF, zobacz [wywołania wykonywania pakietu SSIS jako pierwszej klasy działania ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Zobacz również następujące artykuły z dokumentacji usług SSIS: 

- [Wdrażanie, uruchamianie i monitorowanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Zaplanuj wykonywanie pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie środowiska Azure SSIS Integration Runtime
> * Uruchamianie środowiska Azure SSIS Integration Runtime
> * Przeglądanie kompletnego skryptu
> * Wdrażanie pakietów usług SSIS

Aby dowiedzieć się więcej o dostosowywaniu środowiska Azure-SSIS Integration Runtime, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
>[Customize Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) (Dostosowywanie środowiska Azure-SSIS IR)