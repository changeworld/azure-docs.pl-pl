---
title: Użyj modułu usługi migracji bazy danych Azure w programie Microsoft Azure PowerShell do migracji serwera SQL lokalnej do bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się przeprowadzić migrację z lokalnego programu SQL Server do bazy danych SQL Azure przy użyciu programu Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/05/2018
ms.openlocfilehash: d70d39f027df1fba7934c4b752b1dd2100f2e8b9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrowanie lokalnej instalacji programu SQL Server do bazy danych SQL Azure przy użyciu programu Azure PowerShell
W tym artykule, wykonywana jest migracja **Adventureworks2012** przywrócone do lokalnego wystąpienia programu SQL Server 2016 lub nowszy z bazą danych SQL Azure za pomocą programu Microsoft Azure PowerShell bazy danych. Można migrować baz danych z lokalnego wystąpienia programu SQL Server z bazą danych SQL Azure za pomocą `AzureRM.DataMigration` moduł w programie Microsoft Azure PowerShell.

W tym artykule dowiesz się, jak:
> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Utwórz wystąpienie usługi migracji bazy danych Azure.
> * Utwórz projekt migracji w wystąpieniu usługi migracji bazy danych Azure.
> * Uruchom migrację.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać te kroki, potrzebne są:

- [SQL Server 2016 lub nowszego](https://www.microsoft.com/sql-server/sql-server-downloads) (dowolna wersja)
- Protokół TCP/IP jest domyślnie wyłączona, z instalacją programu SQL Server Express. Włącz go, wykonując [instrukcje w tym artykule](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Aby skonfigurować Twoje [zapory systemu Windows dla dostępu aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Wystąpienie bazy danych SQL Azure. Można utworzyć wystąpienia bazy danych SQL Azure, wykonując szczegółowo w artykule [tworzenie bazy danych Azure SQL w portalu Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Dane migracji Asystenta](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 lub nowszym.
- Usługa Azure bazy danych migracji wymaga utworzony przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia połączenie lokacja lokacja z serwerami lokalnymi źródła przy użyciu sieci Wirtualnej [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [ Sieć VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Ukończono ocenę migracji bazy danych i schemat lokalnymi przy użyciu Asystenta migracji danych, zgodnie z opisem w artykule [ wykonywania oceny migracji programu SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Pobierz i zainstaluj moduł AzureRM.DataMigration z bu galerii programu PowerShell przy użyciu [polecenia cmdlet programu PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Poświadczenia użyte do nawiązania połączenia źródła wystąpienia programu SQL Server musi mieć [serwera kontroli](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) uprawnienia.
- Poświadczenia użyte do nawiązania połączenia docelowego wystąpienia bazy danych SQL Azure musi mieć uprawnienia do kontroli bazy danych na docelowymi bazami danych bazy danych SQL Azure.
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji platformy Microsoft Azure
Użyj wskazówek w następującym artykule w [Zaloguj się przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) logować się do subskrypcji platformy Azure przy użyciu programu PowerShell.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, aby można było utworzyć maszynę wirtualną.

Utwórz grupę zasobów za pomocą [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) polecenia. 

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *EastUS* regionu.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Utwórz wystąpienie usługi migracji bazy danych Azure 
Można utworzyć nowego wystąpienia usługi migracji bazy danych platformy Azure przy użyciu `New-AzureRmDataMigrationService` polecenia cmdlet. Aplet polecenia oczekuje następujące wymagane parametry:
- *Nazwa grupy zasobów platformy Azure*. Można użyć [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) polecenie, aby utworzyć grupę zasobów Azure pokazano wcześniej i podać jego nazwę, jako parametr.
- *Nazwa usługi*. Ciąg, który odpowiada nazwie żądanej usługi unikatowy dla usługi migracji bazy danych Azure 
- *Lokalizacja*. Określa lokalizację usługi. Określ lokalizację centrum danych Azure, takich jak zachodnie stany USA lub Azja południowo-wschodnia
- *Sku*. Ten parametr odpowiada nazwa jednostki Sku DMS. Są obecnie obsługiwane nazwy jednostki Sku *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identyfikator podsieci wirtualnej*. Możesz użyć polecenia cmdlet [AzureRmVirtualNetworkSubnetConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) Aby utworzyć podsieć. 

Poniższy przykład tworzy usługi o nazwie *MyDMS* w grupie zasobów *MyDMSResourceGroup*, który znajduje się w *wschodnie stany USA* regionu w sieci wirtualnej o nazwie *MyVNET* i podsieć o nazwie *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji
Po utworzeniu wystąpienia usługi migracji bazy danych Azure, Utwórz projekt migracji. Projekt usługi migracji bazy danych Azure wymaga informacji o połączeniu zarówno wystąpień źródłowych i docelowych, a także listy baz danych, które chcesz przeprowadzić migrację w ramach projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Tworzenie obiektu bazy danych informacji o połączeniu dla połączeń źródłowa i docelowa
Obiekt informacje o połączeniu bazy danych można utworzyć za pomocą `New-AzureRmDmsConnInfo` polecenia cmdlet. Aplet polecenia oczekuje następujące parametry:
- *Typ*. Połączenia z bazą danych żądanego typu, na przykład SQL, Oracle lub MySQL. Użyj programu SQL dla programu SQL Server i SQL Azure.
- *Źródło danych*. Nazwa lub adres IP wystąpienia serwera SQL lub serwer usługi SQL Azure.
- *Typ*. Typ uwierzytelniania dla połączenia, który może być SqlAuthentication lub WindowsAuthentication.
- *TrustServerCertificate* parametr ustawia wartość wskazującą, czy kanał jest szyfrowany podczas pomijanie przejście łańcuch certyfikatów do sprawdzania poprawności zaufania. Wartość może być równa true lub false.

Poniższy przykład tworzy obiekt informacje o połączeniu dla źródła SQL Server o nazwie MySQLSourceServer przy użyciu uwierzytelniania programu sql 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Kolejnym przykładzie pokazano tworzenie informacji o połączeniu dla serwera bazy danych SQL Azure o nazwie MySQLAzureTarget przy użyciu uwierzytelniania programu sql

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Podaj bazy danych dla projektu migracji
Utwórz listę `AzureRmDataMigrationDatabaseInfo` obiektów, które określa projekt bazy danych w ramach migracji bazy danych Azure, który można podać jako parametr do tworzenia projektu. Polecenia cmdlet `New-AzureRmDataMigrationDatabaseInfo` może służyć do tworzenia AzureRmDataMigrationDatabaseInfo. 

Poniższy przykład tworzy `AzureRmDataMigrationDatabaseInfo` projektu dla bazy danych AdventureWorks2016 i dodaje go do listy, aby można podać jako parametr do tworzenia projektu.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Utwórz obiekt projektu
Na koniec można utworzyć projektu Azure migracja bazy danych o nazwie *MyDMSProject* znajduje się w *wschodnie stany USA* przy użyciu `New-AzureRmDataMigrationProject` i dodawanie utworzonej wcześniej połączenia źródłowe i docelowe i listy bazy danych do migracji.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji
Na koniec Utwórz i uruchom zadania migracji bazy danych Azure. Zadania migracji bazy danych Azure wymaga informacji o poświadczeniach połączenia na serwerze źródłowym i docelowym i listy tabel bazy danych do migracji oprócz informacji dostępnych już w projekcie utworzony jako warunek wstępny. 

### <a name="create-credential-parameters-for-source-and-target"></a>Utwórz poświadczenia parametry źródłowe i docelowe
Poświadczenia zabezpieczeń połączenia może zostać utworzony jako [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu. 

W poniższym przykładzie pokazano tworzenie *PSCredential* obiektów dla źródła i celu połączeń podanie hasła jako zmiennych ciągu *$sourcePassword* i *$ Hasło_docelowe*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Utwórz mapowanie tabel i wybierz parametry źródłowe i docelowe do migracji
Inny parametr potrzebne do migracji jest mapowanie tabel z źródłowego do docelowego do migracji. Utwórz słownik tabel, który zapewnia mapowanie między tabel źródłowych i docelowych do migracji. Poniższy przykład przedstawia mapowanie między tabel źródłowych i docelowych kadr schematu bazy danych AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Następnym krokiem jest wybierz źródło i cel baz danych i podaj mapowania tabeli, aby przeprowadzić migrację jako parametr przy użyciu `New-AzureRmDmsSqlServerSqlDbSelectedDB` polecenia cmdlet, jak pokazano w poniższym przykładzie:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Użyj `New-AzureRmDataMigrationTask` polecenia cmdlet, aby utworzyć i uruchomić zadanie migracji. Aplet polecenia oczekuje następujące parametry:
- *TaskType*. Typ tworzonego zadania migracji dla programu SQL Server do typu migracji SQL Azure *MigrateSqlServerSqlDb* jest oczekiwany. 
- *Nazwa grupy zasobów*. Nazwa grupy zasobów platformy Azure, w którym można utworzyć zadania.
- *ServiceName*. Azure wystąpienie usługi migracji bazy danych, w którym można utworzyć zadania.
- *ProjectName*. Nazwa projektu migracja bazy danych Azure, w którym można utworzyć zadania. 
- *TaskName*. Nazwa zadania, które ma zostać utworzony. 
- *Źródło połączenia*. Obiekt AzureRmDmsConnInfo reprezentujący połączenie ze źródłem.
- *Docelowa połączenia*. Obiekt AzureRmDmsConnInfo reprezentujący docelowy połączenia.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiekt do nawiązywania połączenia z serwera źródłowego.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiekt do nawiązywania połączenia z serwerem docelowym.
- *SelectedDatabase*. Obiekt AzureRmDmsSqlServerSqlDbSelectedDB reprezentujący mapowania bazy danych źródłowych i docelowych.

Poniższy przykład tworzy i uruchamia zadanie migracji o nazwie myDMSTask:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>Monitoruj migrację
Możesz monitorować zadanie migracji z zapytania dotyczącego właściwości Stan zadania, jak pokazano w poniższym przykładzie:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Kolejne kroki
- Przejrzyj wskazówki dotyczące migracji w programie Microsoft [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/).