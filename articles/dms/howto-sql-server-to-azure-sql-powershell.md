---
title: 'Powershell: Migrowanie programu SQL Server do bazy danych SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzać migrację z lokalnego programu SQL Server do bazy danych SQL Azure przy użyciu programu Azure PowerShell z usługą migracji bazy danych azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: f63f79402b457017257f1762c6ddc7e04c0ee1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650694"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>Migrowanie lokalnego programu SQL Server do usługi Azure SQL Database przy użyciu programu Azure PowerShell

W tym artykule można przeprowadzić migrację bazy danych **Adventureworks2012** przywrócone do lokalnego wystąpienia programu SQL Server 2016 lub wyższej do bazy danych SQL Azure przy użyciu programu Microsoft Azure PowerShell. Bazy danych można migrować z lokalnego wystąpienia programu SQL Server `Az.DataMigration` do bazy danych SQL Azure przy użyciu modułu w programie Microsoft Azure PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Utwórz grupę zasobów.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Utwórz projekt migracji w wystąpieniu usługi migracji bazy danych platformy Azure.
> * Uruchamianie migracji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące kroki, potrzebujesz:

* [SQL Server 2016 lub wyższy](https://www.microsoft.com/sql-server/sql-server-downloads) (dowolna wersja)
* Aby włączyć protokół TCP/IP, który jest domyślnie wyłączony z instalacją PROGRAMU SQL Server Express. Włącz protokół TCP/IP, wykonując artykuł [Włącz lub wyłącz protokół sieciowy serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Aby skonfigurować [Zaporę systemu Windows w celu uzyskania dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Wystąpienie usługi Azure SQL Database. Wystąpienie usługi Azure SQL Database można utworzyć, postępając zgodnie ze szczegółami w artykule [Tworzenie bazy danych SQL platformy Azure w portalu Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)
* [Asystent migracji danych](https://www.microsoft.com/download/details.aspx?id=53595) w wersji 3.3 lub nowszej.
* Aby utworzyć sieć wirtualną platformy Microsoft Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia usłudze migracji bazy danych platformy Azure łączność lokacja-lokacja z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Aby zakończyć ocenę lokalnej bazy danych i migracji schematu przy użyciu Asystenta migracji danych, jak opisano w artykule [Wykonywanie oceny migracji programu SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
* Aby pobrać i zainstalować moduł Az.DataMigration z Galerii programu PowerShell przy użyciu [polecenia cmdlet programu Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); należy otworzyć okno polecenia programu PowerShell przy użyciu uruchom jako administrator.
* Aby upewnić się, że poświadczenia używane do łączenia się ze źródłowym wystąpieniem programu SQL Server mają uprawnienie [SERWER CONTROL SERVER.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Aby upewnić się, że poświadczenia używane do łączenia się z docelowym wystąpieniem bazy danych SQL platformy Azure ma uprawnienie BAZA DANYCH CONTROL na docelowej bazie danych usługi Azure SQL Database.
* Subskrypcja platformy Azure. Jeśli go nie masz, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji platformy Microsoft Azure

Skorzystaj ze wskazówek w artykule [Zaloguj się za pomocą programu Azure PowerShell,](https://docs.microsoft.com/powershell/azure/authenticate-azureps) aby zalogować się do subskrypcji platformy Azure przy użyciu programu PowerShell.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów przed utworzeniem maszyny wirtualnej.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w regionie *EastUS.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi migracji bazy danych platformy Azure

Można utworzyć nowe wystąpienie usługi migracji `New-AzDataMigrationService` bazy danych platformy Azure przy użyciu polecenia cmdlet. To polecenie cmdlet oczekuje następujących wymaganych parametrów:

* *Nazwa grupy zasobów platformy Azure*. Za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) można utworzyć grupę zasobów platformy Azure, jak pokazano wcześniej i podać jej nazwę jako parametr.
* *Nazwa usługi*. Ciąg odpowiadający żądanej unikatowej nazwie usługi dla usługi migracji bazy danych Azure 
* *Lokalizacja*. Określa lokalizację usługi. Określanie lokalizacji centrum danych platformy Azure, takiej jak Zachodnie stany USA lub Azja Południowo-Wschodnia
* *Sku*. Ten parametr odpowiada nazwie Sku DMS. Aktualnie obsługiwana nazwa Sku jest *GeneralPurpose_4vCores*.
* *Identyfikator podsieci wirtualnej*. Do utworzenia podsieci można użyć polecenia cmdlet [New-AzVirtualNetworkSubnetConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 

Poniższy przykład tworzy usługę o nazwie *MyDMS* w grupie zasobów *MyDMSResourceGroup* znajdującej się w regionie *Wschodnie stany USA* przy użyciu sieci wirtualnej o nazwie *MyVNET* i podsieci o nazwie *MySubnet*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi migracji bazy danych platformy Azure utwórz projekt migracji. Projekt usługi migracji bazy danych Azure wymaga informacji o połączeniu dla wystąpień źródłowych i docelowych, a także listy baz danych, które mają zostać migrowane w ramach projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Tworzenie obiektu Informacje o połączeniu bazy danych dla połączeń źródłowych i docelowych

Obiekt Informacje o połączeniu bazy danych `New-AzDmsConnInfo` można utworzyć przy użyciu polecenia cmdlet. To polecenie cmdlet oczekuje następujących parametrów:

* *ServerType*. Typ żądanego połączenia z bazą danych, na przykład SQL, Oracle lub MySQL. Użyj sql dla sql server i sql sql.
* *Źródło danych*. Nazwa lub adres IP wystąpienia programu SQL Server lub bazy danych SQL platformy Azure.
* *AuthType*. Typ uwierzytelniania dla połączenia, który może być sqlauthentication lub WindowsAuthentication.
* *Parametr TrustServerCertificate* ustawia wartość wskazującą, czy kanał jest szyfrowany podczas pomijania przechodzenia łańcucha certyfikatów w celu sprawdzenia poprawności zaufania. Wartość może być true lub false.

W poniższym przykładzie utworzy się obiekt Informacje o połączeniu dla źródłowego programu SQL Server o nazwie MySourceSQLServer przy użyciu uwierzytelniania sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

W następnym przykładzie pokazano tworzenie informacji o połączeniu dla serwera bazy danych SQL platformy Azure o nazwie SQLAzureTarget przy użyciu uwierzytelniania sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Dostarczanie baz danych dla projektu migracji

Utwórz listę `AzDataMigrationDatabaseInfo` obiektów, która określa bazy danych w ramach projektu migracji bazy danych platformy Azure, które mogą być dostarczane jako parametr do tworzenia projektu. Polecenie cmdlet `New-AzDataMigrationDatabaseInfo` może służyć do tworzenia AzDataMigrationDatabaseInfo. 

Poniższy przykład `AzDataMigrationDatabaseInfo` tworzy projekt dla **adventureworks2016** bazy danych i dodaje go do listy, które mają być dostarczane jako parametr do tworzenia projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Tworzenie obiektu projektu

Na koniec można utworzyć projekt migracji bazy danych platformy Azure o `New-AzDataMigrationProject` nazwie *MyDMSProject* znajduje się we *wschodnich stanach ZJEDNOCZONYCH* przy użyciu i dodawania wcześniej utworzonych połączeń źródłowych i docelowych oraz listy baz danych do migracji.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
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

Na koniec utwórz i uruchom zadanie migracji bazy danych platformy Azure. Zadanie migracji bazy danych platformy Azure wymaga informacji o poświadczeniach połączenia dla źródła i obiektu docelowego oraz listy tabel bazy danych do migracji oprócz informacji już dostarczonych wraz z projektem utworzonym jako warunek wstępny.

### <a name="create-credential-parameters-for-source-and-target"></a>Tworzenie parametrów poświadczeń dla źródła i obiektu docelowego

Poświadczenia zabezpieczeń połączenia można utworzyć jako obiekt [PSCredential.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

Poniższy przykład przedstawia tworzenie *obiektów PSCredential* dla połączeń źródłowych i docelowych, zapewniających hasła jako zmienne ciągu *$sourcePassword* i *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Tworzenie mapy tabeli i wybieranie parametrów źródłowych i docelowych dla migracji

Innym parametrem potrzebnym do migracji jest mapowanie tabel ze źródła do obiektu docelowego do migracji. Utwórz słownik tabel, który udostępnia mapowanie między tabelami źródłowymi i docelowymi dla migracji. Poniższy przykład ilustruje mapowanie między tabelami źródłowymi i docelowymi Schemat zasobów ludzkich dla bazy danych AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Następnym krokiem jest wybranie źródłowych i docelowych baz danych oraz zapewnienie `New-AzDmsSelectedDB` mapowania tabel do migracji jako parametru przy użyciu polecenia cmdlet, jak pokazano w poniższym przykładzie:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Tworzenie zadania migracji i uruchamianie go

Użyj `New-AzDataMigrationTask` polecenia cmdlet, aby utworzyć i rozpocząć zadanie migracji. To polecenie cmdlet oczekuje następujących parametrów:

* *Typ zadania*. Typ zadania migracji do utworzenia dla programu SQL Server do usługi Azure SQL Database typ migracji *MigrateSqlServerSqlDb* jest oczekiwany. 
* *Nazwa grupy zasobów*. Nazwa grupy zasobów platformy Azure, w której ma być utworzone zadanie.
* *Nazwa serwisu*. Usługa migracji bazy danych platformy Azure wystąpienie, w którym można utworzyć zadanie.
* *ProjectName*. Nazwa projektu usługi migracji bazy danych azure, w którym można utworzyć zadanie. 
* *Nazwa zadania*. Nazwa zadania, które ma zostać utworzone. 
* *ŹródłoZłączenie*. Obiekt AzDmsConnInfo reprezentujący źródłowe połączenie programu SQL Server.
* *TargetConnection*. Obiekt AzDmsConnInfo reprezentujący docelowe połączenie bazy danych SQL.
* *ŹródłoCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu do łączenia się z serwerem źródłowym.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu do łączenia się z serwerem docelowym.
* *SelectedDatabase*. AzDataMigrationSelectedDB obiekt reprezentujący mapowanie źródłowej i docelowej bazy danych.
* *SchemaValidation*. (opcjonalnie, parametr przełącznika) Po migracji wykonuje porównanie informacji o schemacie między źródłem a obiektem docelowym.
* *DataIntegrityValidation*. (opcjonalnie, parametr przełącznika) Po migracji wykonuje sprawdzanie poprawności integralności danych oparte na podstawie sumy kontrolnej między źródłem a obiektem docelowym.
* *QueryAnalysisValidation*. (opcjonalnie, parametr przełącznika) Po migracji wykonuje szybką i inteligentną analizę zapytań, pobierając zapytania ze źródłowej bazy danych i wykonując je w docelowych.

Poniższy przykład tworzy i uruchamia zadanie migracji o nazwie myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

Poniższy przykład tworzy i uruchamia to samo zadanie migracji, jak powyżej, ale również wykonuje wszystkie trzy sprawdzanie poprawności:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Monitorowanie migracji

Zadanie migracji można monitorować, wykonując kwerendę właściwości stanu zadania, jak pokazano w poniższym przykładzie:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Usuwanie wystąpienia DMS

Po zakończeniu migracji można usunąć wystąpienie usługi Azure DMS:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Następny krok

* Zapoznaj się ze wskazówkami dotyczącymi migracji w [Przewodniku migracji bazy danych firmy](https://datamigration.microsoft.com/)Microsoft .
