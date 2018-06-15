---
title: Tworzenie i zarządzanie nimi przy użyciu programu PowerShell zadania elastyczne | Dokumentacja firmy Microsoft
description: Umożliwia Zarządzanie pulami bazy danych SQL Azure PowerShell
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: d1869dd689ef090978c3835e0a16fec82dfb5c05
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648690"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Tworzenie i zarządzanie nimi zadania elastycznej bazy danych SQL przy użyciu programu PowerShell (wersja zapoznawcza)

Interfejsy API środowiska PowerShell dla **zadania elastycznej bazy danych** (w wersji zapoznawczej), umożliwiają definiowanie grupę baz danych, które wykona skryptów. W tym artykule przedstawiono sposób tworzenia i zarządzania nimi **zadania elastycznej bazy danych** przy użyciu poleceń cmdlet programu PowerShell. Zobacz [omówienie zadania elastyczne](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Bezpłatnej wersji próbnej, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Zestaw baz danych utworzonych przy użyciu narzędzi elastycznej bazy danych. Zobacz [wprowadzenie do narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).
* **Zadania elastyczne bazy danych** pakietu programu PowerShell: zobacz [zadania instalowania elastycznej bazy danych](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Wybierz subskrypcję platformy Azure
Aby wybrać subskrypcję należy identyfikator subskrypcji (**- SubscriptionId**) lub nazwę subskrypcji (**— Nazwa subskrypcji**). Jeśli masz wiele subskrypcji możesz uruchomić **Get-AzureRmSubscription** polecenia cmdlet i skopiuj ustawić informacji o subskrypcji żądanego wyniku. Po uzyskaniu informacji o subskrypcji, uruchom następujące polecenia można ustawić jako domyślny, a mianowicie docelowych do tworzenia i zarządzania zadaniami tej subskrypcji:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

[PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) jest zalecane w przypadku użycia w celu opracowywania i wykonywanie skryptów programu PowerShell dla zadania elastycznej bazy danych.

## <a name="elastic-database-jobs-objects"></a>Obiekty zadania elastycznej bazy danych
W poniższej tabeli wymieniono się wszystkie typy obiektów z **zadania elastycznej bazy danych** wraz z jego opis i odpowiednich interfejsów API programu PowerShell.

<table style="width:100%">
  <tr>
    <th>Typ obiektu</th>
    <th>Opis</th>
    <th>Powiązanych interfejsów API programu PowerShell</th>
  </tr>
  <tr>
    <td>Poświadczenie</td>
    <td>Nazwa użytkownika i hasło do użycia podczas nawiązywania połączenia bazy danych dla wykonywania skryptów lub aplikacji DACPACs. <p>Hasło jest szyfrowane przed wysłaniem do i przechowywania w bazie danych zadania elastyczne bazy danych.  Hasło jest odszyfrowywany przez usługę zadania elastyczne bazy danych za pomocą poświadczeń utworzone i załadowane w skrypcie instalacji.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Skrypt</td>
    <td>Skrypt języka Transact-SQL do użycia dla wykonania dla baz danych.  Skrypt należy utworzone za idempotentności, ponieważ usługa ponowi wykonanie skryptu po awarii.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplikacji warstwy danych </a> pakietu, które mają być stosowane dla baz danych.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Docelowej bazy danych</td>
    <td>Nazwa bazy danych i serwera wskazujące bazę danych SQL Azure.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Identyfikator niezależnego fragmentu mapy docelowego</td>
    <td>Kombinacja docelowej bazy danych i poświadczeń ma być używany do określenia informacji przechowywanych w mapie niezależnego fragmentu elastycznej bazy danych.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Docelowy kolekcji niestandardowej</td>
    <td>Zdefiniowane grupy baz danych do zbiorczo użycia dla wykonania.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Docelowy podrzędnej kolekcji niestandardowej</td>
    <td>Obiekt docelowy bazy danych, do którego odwołuje się z kolekcji niestandardowej.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Zadanie</td>
    <td>
    <p>Definicja parametrów zadania, które mogą być używane do wyzwolenia wykonywania lub do zrealizowania planu.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Wykonanie zadania</td>
    <td>
    <p>Kontener zadania niezbędne do spełnienia wykonywania skryptu albo stosowania DACPAC do obiektu docelowego przy użyciu poświadczeń dla połączenia z bazą danych z błędami obsługiwane zgodnie z zasadami wykonywania.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Wykonywanie zadań zadania</td>
    <td>
    <p>Pojedynczą jednostkę pracy do wykonania zadania.</p>
    <p>Jeśli zadanie nie będzie mógł pomyślnie wykonać, będą rejestrowane Wynikowy komunikat o wyjątku i nowe zadanie zgodne, zostanie utworzona i wykonywane zgodnie z zasadami wykonywania określonej.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Zasady wykonywania zadania</td>
    <td>
    <p>Formanty zadań limitów czasu wykonywania, ponownych prób, ograniczenia i odstępach czasu między kolejnymi próbami.</p>
    <p>Zadania elastyczne bazy danych zawiera domyślne zasady wykonywania zadania, które zasadniczo nieskończone ponowne próby niepowodzeń zadań zadania z wykładniczego wycofywania odstępach czasu między kolejnymi próbami.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Harmonogram</td>
    <td>
    <p>Czas na podstawie specyfikacji wykonywania odbywać się w przedziale reoccurring lub w danym momencie.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Wyzwala zadanie</td>
    <td>
    <p>Mapowanie między zadania i harmonogramu wykonywania wyzwalacza zadania zgodnie z harmonogramem.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Obsługiwane zadania elastycznej bazy danych grupy typów
To zadanie wykonuje skrypty języka Transact-SQL (T-SQL) lub aplikacji DACPACs między grupą baz danych. Po przesłaniu zadania do wykonania między grupą baz danych przez zadanie "rozszerza" do zadania podrzędne gdzie pełniących żądanego wykonanie jednej bazy danych w grupie. 

Istnieją dwa typy grup, które możesz utworzyć: 

* [Mapa niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md) grupy: przesyłanej zadania docelową mapę niezależnego fragmentu zadanie odpytuje mapy niezależnego fragmentu określenie jego bieżącego zestawu odłamków, a następnie zadania dla każdego niezależnego fragmentu tworzy podrzędnego, na mapie niezależnego fragmentu.
* Niestandardowe grupy zbierania: niestandardowy zdefiniowany zestaw baz danych. Gdy zadanie jest przeznaczony dla kolekcji niestandardowych, tworzy podrzędnych zadania dla każdej bazy danych obecnie w kolekcji niestandardowej.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Aby ustawić elastycznej bazy danych zadania połączeń
Połączenie musi być ustawiona na zadania *bazy danych kontroli* przed użyciem zadania interfejsów API. Uruchomienie tego polecenia cmdlet wyzwala okno poświadczeń przeskoczyć do góry żądania nazwy użytkownika i hasła utworzonego podczas instalacji zadania elastycznej bazy danych. Wszystkie przykłady w tym temacie założono, że pierwsza została już wykonana.

Otwórz połączenie zadania elastycznej bazy danych:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Zaszyfrowane poświadczenia w ramach zadania elastycznej bazy danych
Poświadczenia bazy danych można wstawiać do zadania *bazy danych kontroli* jego hasłem, szyfrowane. Należy zapisać poświadczenia na potrzeby uruchomienia zadania do wykonania w późniejszym czasie (przy użyciu harmonogramy zadań).

Szyfrowanie działa za pośrednictwem utworzone jako część skrypt instalacji certyfikatu. Skrypt instalacji tworzy i wysyła certyfikat do usługi w chmurze Azure do odszyfrowywania przechowywane hasła szyfrowane. Usługi w chmurze Azure później przechowuje klucz publiczny w ramach zadania *bazy danych kontroli* co pozwala interfejsu API programu PowerShell lub Azure interfejs portalu do zaszyfrowania podanego hasła bez konieczności lokalnie instalacji certyfikatu .

Hasła poświadczenia są szyfrowane i bezpieczne od użytkowników z dostępem tylko do odczytu do obiektów zadania elastycznej bazy danych. Ale istnieje możliwość, że złośliwy użytkownik z dostępem do odczytu i zapisu do obiektów zadania elastyczne bazy danych można wyodrębnić hasła. Poświadczenia są przeznaczone do ponownie wykorzystać w odniesieniu do wykonania zadania. Poświadczenia są przekazywane do docelowej bazy danych podczas ustanawiania połączenia. Obecnie nie istnieją żadne ograniczenia dotyczące z docelowymi bazami danych używane dla każdego poświadczenia, złośliwy użytkownik może dodać element docelowy bazy danych dla bazy danych pod kontrolą złośliwy użytkownik. Użytkownik może następnie uruchomić zadanie przeznaczonych dla tej bazy danych, aby uzyskać poświadczenia: hasło.

Najlepsze rozwiązania dotyczące zadania elastycznej bazy danych obejmują:

* Ogranicz użycie interfejsów API do zaufanych osób.
* Poświadczenia ma co najmniej uprawnienia niezbędne do wykonania zadanie.  Więcej informacji, można wyświetlić w ramach tego [autoryzacji i uprawnienia](https://msdn.microsoft.com/library/bb669084.aspx) artykuł w witrynie MSDN programu SQL Server.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Aby utworzyć zaszyfrowany poświadczeń do wykonywania zadań dla baz danych
Aby utworzyć nowe poświadczenie zaszyfrowane, [ **polecenia cmdlet Get-Credential** ](https://technet.microsoft.com/library/hh849815.aspx) monit o podanie nazwy użytkownika i hasło, które mogą zostać przekazane do [ **polecenia cmdlet New-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Aby zaktualizować poświadczenia
Po zmianie hasła, użyj [ **polecenia cmdlet Set-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) i ustaw **CredentialName** parametru.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Aby określić obiekt docelowy mapy niezależnego fragmentu elastycznej bazy danych
Do wykonania zadania wszystkich baz danych w zestawie niezależnego fragmentu (utworzone za pomocą [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md)), użyć mapy niezależnego fragmentu jako element docelowy bazy danych. W tym przykładzie wymaga podzielonej aplikacji utworzony za pomocą biblioteki klienta elastycznej bazy danych. Zobacz [wprowadzenie próbki narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

Bazy danych Menedżera mapy niezależnego fragmentu musi być ustawiona jako docelowej bazy danych, a opcja mapy określonych niezależnego fragmentu musi być określona jako miejsce docelowe.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Tworzenie skryptu T-SQL do wykonania dla baz danych
Podczas tworzenia skryptów T-SQL do wykonania, zdecydowanie zaleca się tworzenie [idempotentności](https://en.wikipedia.org/wiki/Idempotence) i odporne na błędy. Zadania elastyczne bazy danych ponowi wykonanie skryptu zawsze, gdy wykonanie napotka błąd, niezależnie od klasyfikacji błędu.

Użyj [ **polecenia cmdlet New-AzureSqlJobContent** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) Aby utworzyć i zapisać skrypt do wykonania i zestaw **- ContentName** i **- CommandText** Parametry.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Utwórz nowy skrypt z pliku
Jeśli skryptu T-SQL jest zdefiniowany w pliku, służy do importowania skrypt:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Aby zaktualizować skryptu T-SQL do wykonania dla baz danych
Ten skrypt programu PowerShell aktualizuje tekst polecenia T-SQL dla istniejącego skryptu.

Ustaw następujące zmienne, aby odzwierciedlić definicji żądaną skryptu należy ustawić wartość:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Aby zaktualizować definicję do istniejącego skryptu
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Aby utworzyć zadanie do wykonania skryptu w mapie niezależnego fragmentu
Ten skrypt programu PowerShell uruchamia zadania do wykonania skryptu w każdej niezależnego fragmentu w mapie niezależnego fragmentu elastycznego skalowania.

Ustaw następujące zmienne, aby odzwierciedlić żądaną skryptu i obiekt docelowy:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Do wykonania zadania
Ten skrypt programu PowerShell jest wykonywany istniejącego zadania:

Aktualizacja następującej zmiennej w celu odzwierciedlenia nazwa żądanego zadania zostały wykonane:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Aby pobrać stan realizacji pojedyncze zadanie
Użyj [ **polecenia cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) i ustaw **JobExecutionId** parametr, aby wyświetlić stan wykonywania zadania.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Używać tego samego **Get-AzureSqlJobExecution** polecenia cmdlet z **właściwość IncludeChildren** parametr, aby wyświetlić stan wykonania zadania podrzędne, czyli określonym stanie dla każdego wykonywania zadania dla każdej bazy danych Celem tego zadania.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Aby wyświetlić stan między wieloma wykonania zadania
[ **Polecenia cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) ma wiele parametry opcjonalne, które mogą służyć do wyświetlania wielu wykonania zadania, filtrować za pomocą podanych parametrów. Poniżej przedstawiono niektóre możliwe sposoby używania Get-AzureSqlJobExecution:

Pobierz wszystkie aktywne najwyższego poziomu zadanie wykonaniami:

    Get-AzureSqlJobExecution

Pobierz wszystkie wykonaniami najwyższego poziomu zadania, w tym wykonaniami nieaktywnego zadania:

    Get-AzureSqlJobExecution -IncludeInactive

Pobierz wszystkie wykonania zadania podrzędne identyfikatora wykonywania podane zadanie wykonaniami nieaktywnego zadania w tym:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Pobierz wszystkie wykonania zadania utworzone za pomocą harmonogramu / zadania kombinacji, w tym nieaktywnych zadań:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Pobieranie wszystkich zadań przeznaczonych dla określonej niezależnych mapy, w tym nieaktywnych zadań:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Pobieranie wszystkich zadań przeznaczonych dla określonej kolekcji niestandardowych, łącznie z nieaktywnych zadań:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Pobieranie listy zadanie wykonania zadania w ramach wykonania określonego zadania:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Pobieranie szczegółów wykonywania zadań zadania:

Poniższy skrypt programu PowerShell może służyć do wyświetlania szczegółów zadania wykonywania zadania, które jest szczególnie przydatne w przypadku debugowania awarii wykonywania.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Można pobrać błędów w ramach zadania wykonania zadania
**Obiektu JobTaskExecution** zawiera właściwość dla cyklu życia zadania wraz z właściwością wiadomości. Jeżeli wykonanie zadania zadania nie powiodło się, zostanie ustawiona właściwość cyklu życia do i będzie można ustawić właściwości wiadomości Wynikowy komunikat o wyjątku i jego stosu. Jeśli zadanie nie powiodło się, jest ważne wyświetlić szczegóły zadania, których nie powiodła się dla danego zadania.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Oczekiwanie na ukończenie wykonywania zadania
Poniższy skrypt programu PowerShell można czekać na ukończenie zadania zadania:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Tworzenie zasad wykonywania niestandardowych
Zadania elastyczne bazy danych obsługuje tworzenie zasad wykonywania niestandardowych, które mogą być stosowane podczas uruchamiania zadania.

Zasady wykonywania obecnie umożliwiają definiowanie:

* Nazwa: Identyfikator zasad wykonywania.
* Limit czasu zadania: Całkowity czas przed zadania zostaną anulowane przez zadania elastyczne bazy danych.
* Początkowa interwału ponawiania prób: Interwał oczekiwania przed pierwszym ponów próbę.
* Maksymalny interwał ponawiania: Zakończenia interwałów ponawiania do użycia.
* Ponów próbę współczynnik wycofywania interwał: Współczynnik używane do obliczania następnego interwał między ponownymi próbami.  Używana jest następująca formuła: (początkowej interwał ponawiania próby) * Math.Pow — ((interwał wycofywania współczynnik) (liczba prób) - 2). 
* Maksymalna liczba prób: Maksymalną liczbę ponownych prób do wykonania w ramach danego zadania.

Domyślne zasady wykonywania korzysta z następujących wartości:

* Nazwa: Domyślne zasady wykonywania
* Limit czasu zadania: 1 tydzień
* Interwał ponawiania prób początkowej: 100 milisekund
* Maksymalny interwał ponawiania: 30 minut
* Spróbuj ponownie współczynnik interwał: 2
* Maksymalna liczba prób: 2 147 483 647

Tworzenie zasad wykonywania żądanego:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Aktualizacja zasad wykonywania niestandardowych
Aktualizacja zasad wykonywania żądanej aktualizacji:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Anulowanie zadania
Zadania elastyczne bazy danych obsługuje żądania anulowania zadań.  Jeśli zadania elastyczne bazy danych wykryje żądanie anulowania aktualnie wykonywanego zadania, spróbuje zatrzymać zadanie.

Istnieją dwa różne sposoby, że zadania elastyczne bazy danych można wykonać anulowania:

1. Anuluj aktualnie wykonywanych zadań: wykrycie anulowania aktualnie uruchomionej zadania anulowania będą podejmowane w aspekcie aktualnie wykonywanego zadania.  Na przykład: w przypadku długo działające kwerendy obecnie wykonywana jest próba anulowania, nastąpi próba anulować wykonywanie zapytania.
2. Anulowanie zadań ponownych prób: w przypadku anulowania wykrycia przez wątek kontroli przed uruchomieniu zadania do wykonania, wątku formant będzie uniknąć uruchamiania zadania i zadeklarować żądania jako anulowane.

Jeśli żądanie anulowania zadań dla zadania nadrzędnego, żądanie anulowania będą honorowane dla zadania nadrzędnego i wszystkich jego zadań podrzędnych.

Aby przesłać żądanie anulowania, użyj [ **polecenie cmdlet Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) i ustaw **JobExecutionId** parametru.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Aby usunąć zadania i Historia zadania asynchronicznego
Zadania elastyczne bazy danych obsługuje asynchroniczne usuwania. Zadanie może być oznaczony do usunięcia, a system spowoduje usunięcie zadania i jego historii zadań po zakończeniu wszystkich wykonania zadań dla zadania. System nie zostanie automatycznie anulowania wykonaniami aktywnego zadania.  

Wywołanie [ **Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) anulować wykonaniami aktywnego zadania.

Aby wyzwolić usunięcia zadania, należy użyć [ **polecenia cmdlet Remove-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) i ustaw **JobName** parametru.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Aby utworzyć cel w niestandardowej bazie danych
Można określić cele w niestandardowej bazie danych bezpośrednie wykonywanie lub dołączania w ramach grupy niestandardowej bazie danych. Na przykład ponieważ **pule elastyczne** są nie jest jeszcze bezpośrednio obsługiwane przy użyciu interfejsów API środowiska PowerShell, można utworzyć obiekt docelowy w niestandardowej bazie danych oraz miejsca docelowego do kolekcji niestandardowej bazy danych, które obejmuje wszystkie bazy danych w puli.

Ustaw następujące zmienne, aby odzwierciedlić informacje o żądanej bazy danych:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Aby utworzyć cel kolekcji niestandardowej bazy danych
Użyj [ **AzureSqlJobTarget nowy** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) polecenia cmdlet, aby zdefiniować cel kolekcji niestandardowej bazy danych, aby umożliwiają wykonanie między wiele elementów docelowych określonych w bazie danych. Po utworzeniu grupy bazy danych może być skojarzony z elementem docelowym kolekcji niestandardowej bazy danych.

Ustaw następujące zmienne w celu uwzględnienia konfiguracji docelowej żądanej kolekcji niestandardowych:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Aby dodać bazy danych do docelowej kolekcji niestandardowej bazy danych
Aby dodać bazy danych do użycia określonej kolekcji niestandardowych [ **AzureSqlJobChildTarget Dodaj** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) polecenia cmdlet.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Przejrzyj baz danych w docelowej kolekcji niestandardowej bazy danych
Użyj [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) polecenia cmdlet, aby pobrać podrzędnej bazy danych w docelowej kolekcji niestandardowej bazie danych. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Utwórz zadanie do wykonania skryptu w docelowej kolekcji niestandardowej bazy danych
Użyj [ **AzureSqlJob nowy** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) polecenia cmdlet można utworzyć zadania w odniesieniu do grupy baz danych, wynika z docelowej kolekcji niestandardowej bazy danych. Zadania elastyczne bazy danych będzie rozszerzyć zadanie do wielu zadań podrzędnych, odpowiadający każdej bazy danych skojarzony z elementem docelowym kolekcji niestandardowej bazy danych i upewnij się, że skrypt zostanie wykonany w każdej bazie danych. Ponownie ważne jest, że skrypty są idempotentności pozwala uzyskać odporność na ponownych prób.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Zbieranie danych dla baz danych
Zadania umożliwia wykonywanie zapytań przez grupę baz danych i wysyłać wyniki do określonej tabeli. Tabela może być badana po fakcie, aby zobaczyć wyniki zapytania w każdej bazie danych. Zapewnia to metodę asynchroniczną, można wykonać kwerendy przez wiele baz danych. Nieudanych prób są obsługiwane automatycznie za pomocą ponownych prób.

Jeśli jeszcze nie istnieje w określonej lokalizacji docelowej tabeli zostanie automatycznie utworzona. Nowa tabela zgodny schemat zestaw wyników zwrócony. Jeśli skrypt zwraca wiele zestawów wyników, zadania elastycznej bazy danych wysyła pierwszy do tabeli docelowej.

Poniższy skrypt programu PowerShell wykonuje skrypt i zbiera jej wyników do określonej tabeli. Ten skrypt zakłada, że skryptu T-SQL została utworzona który wyprowadza pojedynczy zestaw wyników i utworzono element docelowy kolekcji niestandardowej bazie danych.

Ten skrypt używa [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) polecenia cmdlet. Ustaw parametry skryptu, poświadczeń i wykonywanie obiekt docelowy:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Aby utworzyć i uruchomić zadanie scenariusze zbierania danych
Ten skrypt używa [ **Start AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) polecenia cmdlet.

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Aby zaplanować zadanie wykonywania wyzwalacza
Poniższy skrypt programu PowerShell, można utworzyć za pomocą harmonogramu cyklicznego. Ten skrypt używa przedział minuty, ale [ **AzureSqlJobSchedule nowy** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) obsługuje również parametry - DayInterval, - HourInterval i - MonthInterval, - WeekInterval. Można utworzyć harmonogramy, które są wykonywane tylko raz przekazywanie przez - jednorazowe.

Utwórz nowy harmonogram:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Do wyzwalania zadania wykonywane zgodnie z harmonogramem czasu
Wyzwalacz zadania można definiować zadania wykonywane zgodnie z harmonogramem. Poniższy skrypt programu PowerShell, można utworzyć wyzwalacza zadania.

Użyj [AzureSqlJobTrigger nowy](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) i ustaw następujące zmienne odpowiadają żądanego zadania i harmonogramu:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Aby usunąć skojarzenie zaplanowane zatrzymania zadania wykonywanie zgodnie z harmonogramem
Aby przerwać pojawiał wykonywania zadań za pomocą wyzwalacza zadania, można usunąć wyzwalacza zadania. Usuń wyzwalacz zadania zatrzymania zadania z wykonywana zgodnie z harmonogramem przy użyciu [ **polecenia cmdlet Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Pobrać wyzwalaczy zadania powiązane z harmonogramu
Poniższy skrypt programu PowerShell mogą być używane do wyświetlić wyzwalacze zadania zarejestrowany z harmonogramem określonym czasie.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Można pobrać zadania wyzwalaczy powiązany z zadaniem
Użyj [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) do wyświetlenia harmonogramy zawierający zarejestrowane zadania.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Aby utworzyć aplikację warstwy danych (DACPAC) do wykonania dla baz danych
Aby utworzyć DACPAC, zobacz [aplikacji warstwy danych](https://msdn.microsoft.com/library/ee210546.aspx). Aby wdrożyć DACPAC, należy użyć [polecenia cmdlet New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). DACPAC muszą być dostępne dla usługi. Zaleca się wysłanie utworzony DACPAC do magazynu Azure i utworzyć [sygnatura dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) dla DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Do aktualizacji aplikacji warstwy danych (DACPAC) do wykonania dla baz danych
Istniejących DACPACs zarejestrowanych w ramach zadania elastyczne bazy danych może zostać zaktualizowana, aby wskazywały nowy identyfikator URI. Użyj [ **polecenia cmdlet Set-AzureSqlJobContentDefinition** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) można zaktualizować identyfikatora URI DACPAC na istniejącym zarejestrowany DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Aby utworzyć zadanie, aby zastosować aplikacji warstwy danych (DACPAC) dla baz danych
Po utworzeniu DACPAC w ramach zadania elastyczne bazy danych można utworzyć zadania do stosowania DACPAC na grupę baz danych. Poniższy skrypt programu PowerShell można utworzyć zadania DACPAC przez niestandardowy zbiór baz danych:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
