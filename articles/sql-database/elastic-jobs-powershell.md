---
title: Tworzenie agenta zadań elastycznych bazy danych Azure SQL Database za pomocą programu PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć agenta zadań elastycznych za pomocą programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: eb5066185f9301450a68276dd4b2ce2123231b34
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476071"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Tworzenie agenta zadań elastycznych za pomocą programu PowerShell

[Zadania elastyczne](sql-database-job-automation-overview.md#elastic-database-jobs) umożliwiają uruchamianie jednego lub większej liczby skryptów języka Transact-SQL (T-SQL) równolegle w wielu bazach danych.

W ramach tego samouczka poznasz kroki wymagane do uruchomienia zapytania w wielu bazach danych:

> [!div class="checklist"]
> * Tworzenie agenta zadań elastycznych
> * Tworzenie poświadczeń zadań, aby umożliwić wykonywanie przez zadania skryptów na ich elementach docelowych
> * Definiowanie elementów docelowych (serwerów, puli elastycznych, baz danych, map fragmentów), względem których ma być uruchamiane zadanie
> * Tworzenie poświadczeń o zakresie bazy danych w docelowych bazach danych, aby umożliwić agentowi łączenie i wykonywanie zadań
> * Tworzenie zadania
> * Dodawanie kroków zadania do zadania
> * Rozpoczynanie wykonywania zadania
> * Monitorowanie zadania

## <a name="prerequisites"></a>Wymagania wstępne

Uaktualnioną wersją zadania elastycznych baz danych ma nowy zestaw poleceń cmdlet programu PowerShell do użycia podczas migracji. Te nowe polecenia cmdlet przenieść wszystkie poświadczenia istniejącego zadania jest przeznaczony dla (w tym baz danych, serwery, kolekcje niestandardowe), wyzwalaczy zadań, harmonogramów zadań, zawartość zadania i zadań za pośrednictwem nowego agenta elastycznych zadań.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Zainstaluj najnowsze polecenia cmdlet zadań elastycznych

Jeśli nie masz jeszcze subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

Zainstaluj **Az.Sql** 1.1.1-preview modułu, aby uzyskać najnowsze polecenia cmdlet zadania elastycznego. Uruchom następujące polecenia w programie PowerShell z dostępem administracyjnym.

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places Az.Sql preview cmdlets side by side with existing Az.Sql version
Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease

# Import the Az.Sql module
Import-Module Az.Sql -RequiredVersion 1.1.1

# Confirm if module successfully imported - if the imported version is 1.1.1, then continue
Get-Module Az.Sql
```

- Oprócz **Az.Sql** 1.1.1-preview modułu, ten samouczek wymaga również *sqlserver* modułu programu PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Install SQL Server PowerShell module (Instalowanie modułu usługi SQL Server dla programu PowerShell)](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module).


## <a name="create-required-resources"></a>Tworzenie wymaganych zasobów

Do utworzenia agenta zadań elastycznych wymagana jest baza danych (S0 lub wyższego poziomu) używana jako [baza danych zadań](sql-database-job-automation-overview.md#job-database). 

*Poniższy skrypt tworzy nową grupę zasobów, serwer i bazę danych, która będzie używana jako baza danych zadań. Poniższy skrypt tworzy również drugi serwer z 2 pustymi bazami danych, względem których będą wykonywane zadania.*

Dla zadań elastycznych nie ma określonych wymagań dotyczących nazewnictwa, można więc zastosować dowolne, o ile są one zgodne z [wymaganiami platformy Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

```powershell
# Sign in to your Azure account
Connect-AzAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>Włączanie wersji zapoznawczej zadań elastycznych w subskrypcji

Użycie elastycznych zadań, należy zarejestrować funkcji w subskrypcji platformy Azure, uruchamiając następujące polecenie. Uruchom to polecenie raz dla subskrypcji, w którym zamierzasz aprowizacji agenta elastycznych zadań. Subskrypcje, które zawierają tylko baz danych, które są cele zadania nie muszą być zarejestrowane.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>Tworzenie agenta zadań elastycznych

Agent zadań elastycznych jest zasobem platformy Azure służącym do tworzenia i uruchamiania zadań oraz do zarządzania nimi. Agent wykonuje zadania na podstawie harmonogramu lub jako zadania jednorazowe.

**New AzSqlElasticJobAgent** polecenie cmdlet wymaga usługi Azure SQL database, aby już istnieje, więc *ResourceGroupName*, *ServerName*, i  *DatabaseName* parametry muszą wskazujących do istniejących zasobów.

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>Tworzenie poświadczeń zadań, aby umożliwić wykonywanie przez zadania skryptów na ich elementach docelowych

Za pomocą poświadczeń o zakresie bazy danych zadania łączą się z docelowymi bazami danych określonymi przez grupę docelową w momencie wykonania. Te poświadczenia o zakresie bazy danych są również używane do łączenia się z bazą danych master w celu wyliczenia wszystkich baz danych na serwerze lub w puli elastycznej, gdy któryś z tych elementów jest używany jako typ elementu członkowskiego grupy docelowej.

Poświadczenia o zakresie bazy danych należy utworzyć w bazie danych zadań.  
Dla wszystkich docelowych baz danych muszą istnieć poświadczenia o uprawnieniach wystarczających do pomyślnego wykonania zadania.

![Poświadczenia zadań elastycznych](media/elastic-jobs-overview/job-credentials.png)

Oprócz poświadczeń w obrazie zwróć uwagę na dodanie poleceń *GRANT* w poniższym skrypcie. Te uprawnienia są wymagane dla skryptu, który wybrano dla tego przykładowego zadania. Ponieważ przykład tworzy nową tabelę w docelowych bazach danych, każda docelowa baza danych wymaga odpowiednich uprawnień do pomyślnego uruchomienia.

Aby utworzyć wymagane poświadczenia zadania (w bazie danych zadań), uruchom następujący skrypt:

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>Definiowanie docelowych baz danych, względem których ma być uruchamiane zadanie

[Grupa docelowa](sql-database-job-automation-overview.md#target-group) definiuje zestaw zawierający co najmniej jedną bazę danych, względem której będzie wykonywane zadanie. 

Poniższy fragment kodu tworzy dwie grupy docelowe: *ServerGroup* i *ServerGroupExcludingDb2*. Grupa *ServerGroup* dotyczy wszystkich baz danych, które istnieją na serwerze w czasie wykonywania, a grupa *ServerGroupExcludingDb2* — wszystkich baz danych na serwerze z wyjątkiem *TargetDb2*:

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>Tworzenie zadania

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>Tworzenie kroku zadania

W tym przykładzie zdefiniowano dwa kroki zadania dotyczące jego uruchamiania. W pierwszym kroku zadania (*step1*) zostaje utworzona nowa tabela (*Step1Table*) w każdej bazie danych w grupie docelowej *ServerGroup*. W drugim kroku zadania (*step2*) zostaje utworzona nowa tabela (*Step2Table*) w każdej bazie danych z wyjątkiem *TargetDb2*, ponieważ [zdefiniowana wcześniej](#define-the-target-databases-you-want-to-run-the-job-against) grupa docelowa jej nie zawiera.

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>Uruchamianie zadania

Aby natychmiast uruchomić zadanie, uruchom następujące polecenie:

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzSqlElasticJob
$JobExecution
```

Po pomyślnym ukończeniu powinny być widoczne dwie nowe tabele w bazie danych TargetDb1 i tylko jedna nowa tabela w bazie danych TargetDb2:


   ![weryfikacja nowych tabel w programie SSMS](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>Monitorowanie stanu wykonania zadania

Poniższe fragmenty kodu pobierają szczegóły wykonania zadania:

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

## <a name="schedule-the-job-to-run-later"></a>Planowanie późniejszego uruchomienia zadania

Aby zaplanować uruchomienie zadania w określonym czasie, uruchom następujące polecenie:

```powershell
# Run every hour starting from now
$Job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń zasoby platformy Azure utworzone w tym samouczku, usuwając grupę zasobów.

> [!TIP]
> Jeśli zamierzasz kontynuować pracę z tymi zadaniami, nie usuwaj zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuowania pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego artykułu.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został uruchomiony skrypt języka Transact-SQL na zestawie baz danych.  Przedstawiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie agenta zadań elastycznych
> * Tworzenie poświadczeń zadań, aby umożliwić wykonywanie przez zadania skryptów na ich elementach docelowych
> * Definiowanie elementów docelowych (serwerów, puli elastycznych, baz danych, map fragmentów), względem których ma być uruchamiane zadanie
> * Tworzenie poświadczeń o zakresie bazy danych w docelowych bazach danych, aby umożliwić agentowi łączenie i wykonywanie zadań
> * Tworzenie zadania
> * Dodawanie kroku zadania do zadania
> * Rozpoczynanie wykonywania zadania
> * Monitorowanie zadania

> [!div class="nextstepaction"]
>[Zarządzanie zadaniami elastycznymi za pomocą języka Transact-SQL](elastic-jobs-tsql.md)
