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
ms.date: 09/23/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d57e350bc63a8acf7c4719572d43b4e703de019e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990930"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime w usłudze Azure Data Factory za pomocą programu PowerShell
Ten samouczek zawiera instrukcje aprowizacji środowiska Azure SSIS Integration Runtime (IR) w usłudze Azure Data Factory. Następnie możesz użyć programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS) do wdrożenia pakietów usług SQL Server Integration Services (SSIS) w tym środowisku uruchomieniowym na platformie Azure. W tym samouczku wykonasz następujące czynności:

> [!NOTE]
> W tym artykule środowisko IR Azure SSIS jest aprowizowane za pomocą programu Azure PowerShell. Aby aprowizować środowisko IR Azure SSIS za pomocą interfejsu użytkownika usługi Data Factory, zobacz [Samouczek: Tworzenie środowiska Azure SSIS Integration Runtime](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie środowiska Azure SSIS Integration Runtime
> * Uruchamianie środowiska Azure SSIS Integration Runtime
> * Wdrażanie pakietów usług SSIS
> * Przeglądanie kompletnego skryptu

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. Aby uzyskać koncepcyjne informacje dotyczące środowiska Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). 
- **Serwer usługi Azure SQL Database**. Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Ten serwer hostuje bazę danych katalogu usług SSIS (SSISDB). Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja pozwala środowisku Integration Runtime zapisywać dzienniki wykonywania SSISDB bez wykraczania poza granice regionów świadczenia usług platformy Azure. 
    - W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wyboru typu serwera baz danych do hostowania bazy danych SSISDB, zobacz [Porównanie serwera logicznego usługi SQL Database i wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Jeśli używasz bazy danych Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym do hostowania bazy danych SSISDB lub wymagasz dostępu do danych lokalnych, należy dołączyć środowisko Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR w ramach sieci wirtualnej](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Upewnij się, że ustawienie „**Zezwalaj na dostęp do usług platformy Azure**” jest **WŁĄCZONE** dla serwera bazy danych. To ustawienie nie ma zastosowania w przypadku używania bazy danych Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym do hostowania bazy danych SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL)](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Aby włączyć to ustawienie za pomocą programu PowerShell, zobacz polecenie [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
    - Do listy adresów IP klienta w ustawieniach zapory dla serwera bazy danych dodaj adres IP maszyny klienta lub zakres adresów IP, który zawiera adres IP maszyny klienta. Aby uzyskać więcej informacji, zobacz [Azure SQL Database server-level and database-level firewall rules (Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database)](../sql-database/sql-database-firewall-configure.md). 
    - Połączenie z serwerem bazy danych możesz nawiązać za pomocą uwierzytelniania SQL wraz z poświadczeniami administratora serwera lub uwierzytelniania usługi Azure Active Directory (AAD) wraz z tożsamością usługi zarządzanej usługi Azure Data Factory.  W przypadku ostatniego rozwiązania należy dodać tożsamość usługi zarządzanej usługi Data Factory do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR przy użyciu uwierzytelniania usługi AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Upewnij się, że serwer Azure SQL Database nie ma katalogu usług SSIS (baza danych SSISDB). Aprowizacja środowiska IR Azure-SSIS nie obsługuje istniejącego katalogu usług SSIS. 
- Zainstalowanie programu **Azure PowerShell**. Wykonaj instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Program PowerShell służy do uruchamiania skryptu w celu aprowizacji środowiska Azure SSIS Integration Runtime, które uruchamia pakiety SSIS w chmurze. 

> [!NOTE]
> - Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). 
> - Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Azure-SSIS Integration Runtime, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **SSIS Integration Runtime**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="launch-windows-powershell-ise"></a>Uruchamianie programu Windows PowerShell ISE
Uruchom program **Windows PowerShell ISE** przy użyciu uprawnień administracyjnych. 

## <a name="create-variables"></a>Tworzenie zmiennych
Skopiuj i wklej poniższy skrypt. Określ wartości zmiennych. Aby zapoznać się z listą obsługiwanych **warstw cenowych** dla usługi Azure SQL Database, zobacz [SQL Database resource limits (Limity zasobów usługi SQL Database)](../sql-database/sql-database-resource-limits.md).

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B". For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"
```

## <a name="validate-the-connection-to-database"></a>Weryfikowanie połączenia z bazą danych
Dodaj poniższy skrypt w celu zweryfikowania serwera usługi Azure SQL Database `<servername>.database.windows.net`. 

```powershell
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
```

Aby utworzyć bazę danych Azure SQL w ramach skryptu, zobacz poniższy przykład: 

Ustaw wartości dla zmiennych, które nie zostały jeszcze zdefiniowane. Na przykład: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="log-in-and-select-subscription"></a>Logowanie i wybieranie subskrypcji
Dodaj następujący kod do skryptu logowania i wybierz subskrypcję platformy Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

Jeśli grupa zasobów już istnieje, nie kopiuj tego kodu do skryptu. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Uruchom następujące polecenie, aby utworzyć fabrykę danych:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime
Uruchom następujące polecenie, aby utworzyć środowisko Azure-SSIS Integration Runtime, które uruchamia pakiety usług SSIS na platformie Azure: 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
  
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier
```

## <a name="start-integration-runtime"></a>Uruchamianie środowiska Integration Runtime
Uruchom następujące polecenie, aby uruchomić środowisko Azure-SSIS Integration Runtime: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Wykonanie tego polecenia trwa od **20 do 30 minut**. 

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS
Teraz użyj programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS), aby wdrożyć pakiety usług SSIS na platformie Azure. Połącz się ze swoim serwerem Azure SQL, który hostuje katalog usług SSIS (SSISDB). Nazwa serwera bazy danych Azure SQL Database ma następujący format: `<servername>.database.windows.net`. 

Zobacz następujące artykuły wchodzące w skład usług SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Wdrażanie, uruchamianie i monitorowanie pakietu usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Łączenie z wykazem usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Planowanie wykonywania pakietów na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Pełny skrypt
Skrypt programu PowerShell w tej sekcji konfiguruje wystąpienie środowiska Azure SSIS Integration Runtime w chmurze, w której działają pakiety usług SSIS. Po pomyślnym uruchomieniu tego skryptu możesz wdrażać i uruchamiać pakiety usług SSIS w chmurze Microsoft Azure z bazą danych SSISDB hostowaną w usłudze Azure SQL Database.

1. Uruchom środowisko Windows PowerShell Integrated Scripting Environment (ISE).
2. W środowisku ISE uruchom następujące polecenie w wierszu polecenia.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Skopiuj skrypt programu PowerShell w tej sekcji i wklej go do środowiska ISE.
4. Podaj odpowiednie wartości dla wszystkich parametrów na początku skryptu.
5. Uruchom skrypt. Wykonanie polecenia `Start-AzureRmDataFactoryV2IntegrationRuntime` w pobliżu końca skryptu zajmuje od **20 do 30 minut**.

> [!NOTE]
> - Skrypt łączy się z serwerem bazy danych Azure SQL Database w celu przygotowania bazy danych wykazu usług SSIS (SSISDB).

> - Podczas aprowizowania wystąpienia środowiska Azure-SSIS IR są instalowane również pakiety Azure Feature Pack for SSIS i Access Redistributable. Te składniki zapewniają łączność z plikami programów Excel i Access oraz z różnymi źródłami danych platformy Azure (oprócz źródeł danych obsługiwanych przez wbudowane składniki). Możesz też zainstalować dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

Aby zapoznać się z listą obsługiwanych **warstw cenowych** dla usługi Azure SQL Database, zobacz [SQL Database resource limits (Limity zasobów usługi SQL Database)](../sql-database/sql-database-resource-limits.md). 

Aby uzyskać listę regionów obsługiwanych przez usługę Azure Data Factory w wersji 2 i środowisko Azure SSIS Integration Runtime, zobacz [Dostępność produktów według regionów](https://azure.microsoft.com/regions/services/). Rozwiń pozycję **Dane + analiza**, aby wyświetlić usługi **Data Factory V2** i **SSIS Integration Runtime**.

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B". For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"

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

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
    
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
    
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-virtual-network"></a>Dołączanie środowiska Azure SSIS IR do sieci wirtualnej
Jeśli używasz bazy danych Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym, które dołącza do sieci wirtualnej hostującej bazę danych SSISDB, należy również dołączyć środowisko Azure-SSIS do tej samej sieci wirtualnej. Usługa Azure Data Factory umożliwia dołączenie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).

Aby uzyskać pełen skrypt służący do tworzenia środowiska Azure-SSIS Integration Runtime dołączanego do sieci wirtualnej, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorowanie środowiska Azure-SSIS IR
W poniższych artykułach można znaleźć szczegółowe informacje o monitorowaniu środowiska Azure-SSIS IR i zarządzania nim. 

- [Monitor an Azure-SSIS integration runtime (Monitorowanie środowiska Azure-SSIS Integration Runtime)](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Manage an Azure-SSIS integration runtime (Zarządzanie środowiskiem Azure-SSIS Integration Runtime)](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie środowiska Azure SSIS Integration Runtime
> * Uruchamianie środowiska Azure SSIS Integration Runtime
> * Wdrażanie pakietów usług SSIS
> * Przeglądanie kompletnego skryptu

Przejdź do następującego samouczka, aby dowiedzieć się więcej o kopiowaniu danych lokalnych do chmury: 

> [!div class="nextstepaction"]
>[Kopiowanie danych w chmurze](tutorial-copy-data-dot-net.md)
