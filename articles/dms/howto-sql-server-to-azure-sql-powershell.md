---
title: Użyj modułu usługi Azure Database Migration Service w systemie Microsoft Azure PowerShell do migracji programu SQL Server w środowisku lokalnym do bazy danych SQL Azure | Dokumentacja firmy Microsoft
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
ms.date: 08/02/2018
ms.openlocfilehash: 9b182b0efad16f74c21b04712143b70071943c1e
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412556"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrowanie lokalnego programu SQL Server do bazy danych SQL Azure przy użyciu programu Azure PowerShell
W tym artykule, wykonywana jest migracja **Adventureworks2012** bazy danych przywróconej do lokalnego wystąpienia programu SQL Server 2016 lub nowszym do usługi Azure SQL Database przy użyciu programu Microsoft Azure PowerShell. Można migrować bazy danych z lokalnego wystąpienia programu SQL Server do usługi Azure SQL Database przy użyciu `AzureRM.DataMigration` modułu w programie Microsoft Azure PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji w wystąpieniu usługi Azure Database Migration Service.
> * Uruchom migrację.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać te kroki, potrzebne są:

- [SQL Server 2016 lub nowszego](https://www.microsoft.com/sql-server/sql-server-downloads) (w każdej wersji)
- Protokół TCP/IP jest domyślnie wyłączona, za pomocą instalacji programu SQL Server Express. Włącz go, wykonując [zgodnie z instrukcjami w tym artykule](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Aby skonfigurować swoje [Windows zapory dla dostępu aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Wystąpienie usługi Azure SQL Database. Można utworzyć wystąpienie usługi Azure SQL Database, postępując zgodnie z szczegółowo w artykule [utworzyć bazę danych Azure SQL database w witrynie Azure portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 lub nowszej.
- Azure Database Migration Service wymaga sieci Wirtualnej utworzonej przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [ Sieć VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Ukończono ocenę migracji bazy danych i schemat w środowisku lokalnym przy użyciu Data Migration Assistant, zgodnie z opisem w artykule [ wykonywania oceny migracji programu SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Pobierz i zainstaluj moduł AzureRM.DataMigration z galerii programu PowerShell przy użyciu [polecenia cmdlet Install-Module programu PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Poświadczenia używane do połączenia z wystąpieniem programu SQL Server muszą mieć [serwera kontroli](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) uprawnień.
- Poświadczenia używane do łączenia z wystąpienia bazy danych SQL Azure docelowego musi mieć uprawnienie Kontrola bazy danych na docelowych baz danych Azure SQL Database.
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji usługi Microsoft Azure
Użyj instrukcjami w artykule [Zaloguj się przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) zalogować się do subskrypcji platformy Azure przy użyciu programu PowerShell.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, aby można było utworzyć maszynę wirtualną.

Utwórz grupę zasobów za pomocą [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) polecenia. 

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *EastUS* regionu.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service 
Można utworzyć nowego wystąpienia usługi Azure Database Migration Service przy użyciu `New-AzureRmDataMigrationService` polecenia cmdlet. To polecenie cmdlet oczekuje następujących wymaganych parametrów:
- *Nazwa grupy zasobów platformy Azure*. Możesz użyć [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) polecenie, aby utworzyć grupę zasobów platformy Azure, jak przedstawiono wcześniej, a następnie podaj jej nazwę jako parametr.
- *Nazwa usługi*. Ciąg, który odnosi się do żądanego unikatową nazwę usługi dla usługi Azure Database Migration Service 
- *Lokalizacja*. Określa lokalizację usługi. Określ lokalizację centrum danych platformy Azure, takich jak zachodnie stany USA lub Azja południowo-wschodnia
- *Jednostka SKU*. Ten parametr odnosi się do nazwy jednostek Sku usługi DMS. Obecnie obsługiwane nazwy jednostek Sku są *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identyfikator podsieci wirtualnej*. Można użyć polecenia cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) utworzyć podsieć. 

Poniższy przykład tworzy usługi o nazwie *MyDMS* w grupie zasobów *MyDMSResourceGroup*, który znajduje się w *wschodnie stany USA* regionem przy użyciu sieci wirtualnej o nazwie *MyVNET* i podsieci o nazwie *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Utwórz projekt migracji
Po utworzeniu wystąpienia usługi Azure Database Migration Service, Utwórz projekt migracji. Projekt usługi Azure Database Migration Service wymaga informacji o połączeniu dla zarówno wystąpień źródłowym i docelowym, a także wykaz baz danych, które chcesz przeprowadzić migrację w ramach projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Utwórz obiekt informacje o połączeniu z bazą danych dla połączeń źródłowe i docelowe
Można utworzyć obiekt informacje o połączeniu z bazą danych przy użyciu `New-AzureRmDmsConnInfo` polecenia cmdlet. To polecenie cmdlet oczekuje, że następujące parametry:
- *Wartością servertype ustawioną*. Połączenia z bazą danych żądanego typu, na przykład SQL, Oracle lub MySQL. Użyj programu SQL dla programu SQL Server i SQL Azure.
- *Źródło danych*. Nazwa lub adres IP wystąpienia SQL lub serwer usługi SQL Azure.
- *Wartość AuthType*. Typ uwierzytelniania dla połączenia, który może być SqlAuthentication lub WindowsAuthentication.
- *TrustServerCertificate* parametr ustawia wartość wskazującą, czy kanał jest szyfrowany podczas pomijanie zalet łańcucha certyfikatów w celu zweryfikowania relacji zaufania. Wartość może być prawdziwe lub fałszywe.

Poniższy przykład tworzy obiekt informacje o połączeniu dla źródła programu SQL Server o nazwie MySQLSourceServer przy użyciu uwierzytelniania sql 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Kolejnym przykładzie pokazano tworzenie informacje o połączeniu dla serwera bazy danych SQL Azure o nazwie MySQLAzureTarget przy użyciu uwierzytelniania sql

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Podaj baz danych dla projektu migracji
Utwórz listę `AzureRmDataMigrationDatabaseInfo` obiektów, które określa baz danych w ramach usługi Azure Database Migration projekt, który można podać jako parametr do tworzenia projektu. Polecenia cmdlet `New-AzureRmDataMigrationDatabaseInfo` może służyć do tworzenia AzureRmDataMigrationDatabaseInfo. 

Poniższy przykład tworzy `AzureRmDataMigrationDatabaseInfo` projektu bazy danych AdventureWorks2016 i dodaje go do listy można podać jako parametr do utworzenia projektu.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Utwórz obiekt projektu
Na koniec możesz utworzyć projekt migracji bazy danych Azure o nazwie *MyDMSProject* na terenie *wschodnie stany USA* przy użyciu `New-AzureRmDataMigrationProject` i dodawanie utworzonej wcześniej połączenia źródłowe i docelowe i listy bazy danych do migracji.

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
Na koniec Utwórz i uruchom zadanie migracji bazy danych Azure. Zadanie migracji bazy danych Azure wymaga informacji o poświadczeniach połączenia dla źródła i docelowych i listy tabel bazy danych do zmigrowania oprócz tych informacji, które już są dostarczane z projektu utworzonego jako warunek wstępny. 

### <a name="create-credential-parameters-for-source-and-target"></a>Tworzenie poświadczeń parametry źródłowe i docelowe
Poświadczenia zabezpieczeń połączenia mogą być tworzone jako [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu. 

W poniższym przykładzie pokazano tworzenie *PSCredential* obiektów dla połączeń elementy źródłowe i docelowe, zapewniając hasła jako zmiennych ciągu *$sourcePassword* i *$ Hasło_docelowe*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Utwórz mapę tabeli i wybierz parametry źródłowe i docelowe do migracji
Inny parametr związanych z migracją jest mapowanie tabel ze źródła do docelowego do migracji. Utwórz słownik tabel, który zapewnia mapowanie między źródłowym i docelowym tabel dla migracji. W poniższym przykładzie pokazano mapowanie między tabelami źródłowe i docelowe zasoby ludzkie schematu dla bazy danych AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Następnym krokiem jest wybierz źródłowych i docelowych baz danych i podaj Mapowanie tabeli migracji jako parametr za pomocą `New-AzureRmDmsSqlServerSqlDbSelectedDB` polecenia cmdlet, jak pokazano w poniższym przykładzie:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Użyj `New-AzureRmDataMigrationTask` polecenia cmdlet, aby utworzyć i uruchomić zadanie migracji. To polecenie cmdlet oczekuje, że następujące parametry:
- *TaskType*. Typ tworzonego zadania migracji dla programu SQL Server do typu migracji SQL Azure *MigrateSqlServerSqlDb* oczekuje. 
- *Nazwa grupy zasobów*. Nazwa grupy zasobów platformy Azure, w której chcesz utworzyć zadanie.
- *ServiceName*. Wystąpienie usługi Azure Database Migration Service w której chcesz utworzyć zadanie.
- *ProjectName*. Nazwa projektu migracji bazy danych Azure, w której chcesz utworzyć zadanie. 
- *TaskName*. Nazwa zadania, które ma zostać utworzony. 
- *Połączenie źródła*. Obiekt AzureRmDmsConnInfo reprezentującą połączenie ze źródłem.
- *Docelowy połączenia*. Obiekt AzureRmDmsConnInfo reprezentującą połączenie docelowe.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu do łączenia się z serwera źródłowego.
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

## <a name="monitor-the-migration"></a>Monitorowanie migracji
Możesz monitorować zadania migracji, uruchamianie, badając właściwość stanu zadania, jak pokazano w poniższym przykładzie:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Kolejne kroki
- Przejrzyj wskazówki dotyczące migracji w programie Microsoft [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/).