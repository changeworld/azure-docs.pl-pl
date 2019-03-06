---
title: Tworzenie i zarządzanie nimi przy użyciu programu PowerShell zadań elastycznych | Dokumentacja firmy Microsoft
description: Program PowerShell umożliwia Zarządzanie pulami usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: powershell
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: dc9b58402ed0fcc48d60b51d30f891ffcadddbe0
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315626"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Tworzenie i zarządzanie nimi zadania elastyczne bazy danych SQL przy użyciu programu PowerShell (wersja zapoznawcza)

Interfejsy API programu PowerShell dla **zadania Elastic Database** (w wersji zapoznawczej) pozwalają zdefiniować grupy baz danych, względem których będą wykonywane skryptów. W tym artykule pokazano, jak tworzyć i zarządzać nimi **zadania Elastic Database** przy użyciu poleceń cmdlet programu PowerShell. Zobacz [Przegląd zadań elastycznej](sql-database-elastic-jobs-overview.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Bezpłatnej wersji próbnej, zobacz [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Zestaw baz danych utworzonych za pomocą narzędzi elastycznych baz danych. Zobacz [Rozpocznij pracę z narzędziami elastycznej bazy danych](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).
* **Zadania elastic Database** pakietami programu PowerShell: Zobacz [zadania instalowania elastycznych baz danych](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Wybierz swoją subskrypcję platformy Azure
Aby wybrać subskrypcję należy Twojego identyfikatora subskrypcji (**- SubscriptionId**) lub Nazwa subskrypcji (**- SubscriptionName**). Jeśli masz wiele subskrypcji możesz uruchomić **Get AzSubscription** polecenia cmdlet i skopiować informacje o odpowiedniej subskrypcji, z wyniku ustawione. Po uzyskaniu informacji o subskrypcji, uruchom następujące polecenie cmdlet można ustawić tej subskrypcji jako wartość domyślna, czyli cel dla tworzenia zadania i zarządzać nimi:

    Select-AzSubscription -SubscriptionId {SubscriptionID}

[PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) jest zalecane w przypadku użycia do tworzenia i wykonywanie skryptów programu PowerShell dla zadania elastycznych baz danych.

## <a name="elastic-database-jobs-objects"></a>Obiekty zadań z elastycznej bazy danych
W poniższej tabeli wymieniono się wszystkie typy obiektów z **zadania Elastic Database** wraz z jego opis i odpowiednich interfejsów API programu PowerShell.

<table style="width:100%">
  <tr>
    <th>Typ obiektu</th>
    <th>Opis</th>
    <th>Powiązanych interfejsów API programu PowerShell</th>
  </tr>
  <tr>
    <td>Poświadczenie</td>
    <td>Nazwa użytkownika i hasło do użycia podczas łączenia się z bazami danych, wykonywania skryptów lub aplikacji DACPACs. <p>Hasło jest szyfrowane przed wysłaniem do i przechowywania w bazie danych zadania Elastic Database.  Hasło jest odszyfrowywany przez usługę zadań elastycznych baz danych za pomocą poświadczeń utworzone i przekazane do skryptu instalacji.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Skrypt</td>
    <td>Skrypt języka Transact-SQL ma być używany do wykonania w bazach danych.  Skrypt powinien tworzone jako idempotentne, ponieważ usługa ponowi próbę wykonania skryptu po awarii.
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
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplikacja warstwy danych </a> pakietu, które mają być stosowane w bazach danych.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Docelowej bazy danych</td>
    <td>Nazwa bazy danych i serwera wskazujący usługi Azure SQL Database.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Docelowy mapy fragmentów</td>
    <td>Kombinacja docelowej bazy danych i poświadczeń ma być używany do określenia informacji przechowywanych w ramach mapowania fragmentów w elastycznych baz danych.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Docelowy kolekcji niestandardowej</td>
    <td>Zdefiniowanej grupy baz danych, aby zbiorczo użycia dla wykonania.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Custom Collection Child Target</td>
    <td>Obiekt docelowy bazy danych, który jest wywoływany przez kolekcję niestandardową.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Zadanie</td>
    <td>
    <p>Definicja parametry zadania, który może służyć do wyzwolenia wykonywania lub do spełnienia harmonogramu.</p>
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
    <p>Kontener zadań, które są niezbędne do spełnienia stosowania pliku DACPAC do obiektu docelowego przy użyciu poświadczeń dla połączenia z bazą danych z błędami lub wykonywanie skryptu obsługiwane zgodnie z zasadami wykonywania.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Wykonania zadania</td>
    <td>
    <p>Pojedyncza jednostka pracy do wykonania zadania.</p>
    <p>Jeśli zadanie nie będzie mógł pomyślnie wykonać, będą rejestrowane Wynikowy komunikat o wyjątku i nowe zadanie dopasowania zostanie utworzona i wykonywane zgodnie z zasadami wykonywania określonej.</p></p>
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
    <p>Formanty zadań przekroczeń limitu czasu wykonywania, ograniczenia ponownych prób i interwałów między kolejnymi próbami.</p>
    <p>Zadania elastic Database zawiera domyślne zasady wykonywania zadania, które spowodować zasadniczo nieskończonej ponownych prób wykonania zadania niepowodzeń zadań z wykorzystaniem wykładniczego wycofywania interwałów między kolejnymi próbami.</p>
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
    <p>Czas na podstawie specyfikacji do wykonania, wyznacz cyklicznych interwał lub na jeden raz.</p>
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
    <p>Mapowanie między zadaniem i harmonogram, aby wyzwolić wykonywanie zadania zgodnie z harmonogramem.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Obsługiwane zadania Elastic Database grupować typy
Zadanie wykonuje skrypty języka Transact-SQL (T-SQL) lub aplikacji DACPACs między grupą baz danych. Po przesłaniu zadania do wykonania w grupie baz danych zadania "rozwija" do zadania podrzędne, w których każdy wykonuje żądane wykonywanie kodu na poszczególnych baz danych w grupie. 

Istnieją dwa typy grup, które można utworzyć: 

* [Mapowanie fragmentów w postaci](sql-database-elastic-scale-shard-map-management.md) grupy: Po przesłaniu zadania pod kątem mapowania fragmentów w postaci zadania wykonuje kwerendę mapowania fragmentów, aby ustalić swój bieżący zestaw fragmentów, a następnie tworzy podrzędne zadania dla każdego fragmentu w ramach mapowania fragmentów.
* Niestandardowe grupy kolekcji: Niestandardowy zestaw określonych baz danych. Gdy zadanie jest przeznaczony dla niestandardowej kolekcji, tworzy podrzędne zadania dla każdej bazy danych obecnie w kolekcji niestandardowej.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Aby ustawić elastycznej bazy danych zadania połączeń
Połączenie musi być ustawiona na zadania *bazy danych kontroli* przed przy użyciu zadań interfejsów API. Uruchomienie tego polecenia cmdlet wyzwala okno poświadczeń przeskoczyć do góry żądania nazwy użytkownika i hasło utworzone podczas instalacji zadań Elastic Database. Wszystkie przykłady w tym temacie założono, że pierwszym kroku została już wykonana.

Otwórz połączenie do zadań elastycznych baz danych:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Zaszyfrowane poświadczenia w ramach zadań elastycznej bazy danych
Poświadczenia bazy danych mogą być wstawiane do zadań *bazy danych kontroli* przy użyciu swojego hasła szyfrowane. Jest to niezbędne do przechowywania poświadczeń, aby umożliwić zadań do wykonania w późniejszym czasie (przy użyciu harmonogramy zadań).

Szyfrowanie działa za pośrednictwem certyfikatu, utworzony jako część skryptu instalacji. Skrypt instalacji tworzy i przekazywanie certyfikatu do usługi w chmurze platformy Azure do odszyfrowywania przechowywane zaszyfrowane hasła. Usługa firmy Microsoft w chmurze później przechowuje klucz publiczny w ramach zadania *bazy danych kontroli* umożliwiająca interfejsu portalu, interfejsu API programu PowerShell lub Azure do zaszyfrowania podanego hasła bez konieczności lokalnie zainstalowania certyfikatu .

Hasła poświadczeń są szyfrowane i bezpieczne od użytkowników z dostępem tylko do odczytu do obiektów zadań elastycznej bazy danych. Ale istnieje możliwość, że złośliwy użytkownik z uprawnieniami do odczytu i zapisu zadania Elastic Database obiektów można wyodrębnić hasła. Poświadczenia są przeznaczone do można używać we wszystkich wykonań zadań. Poświadczenia są przekazywane do docelowych baz danych podczas ustanawiania połączenia. Obecnie nie istnieją żadne ograniczenia dotyczące docelowymi bazami danych, używane dla każdego poświadczenia, złośliwy użytkownik może dodać docelowej bazy danych dla bazy danych pod kontrolą złośliwy użytkownik. Użytkownika można następnie uruchomić zadanie przeznaczonych dla tej bazy danych w celu uzyskania poświadczenia: hasło.

Najlepsze rozwiązania dotyczące zadań elastycznej bazy danych obejmują:

* Ogranicz użycie interfejsów API do tych zaufanych.
* Poświadczenia powinny zawierać co najmniej uprawnienia niezbędne do wykonania zadanie.  Więcej informacji są widoczne w ramach tej [autoryzacja i uprawnienia](https://msdn.microsoft.com/library/bb669084.aspx) programu SQL Server w witrynie MSDN w artykule.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Aby utworzyć zaszyfrowanych poświadczeń w celu wykonania zadania w bazach danych
Aby utworzyć nowe poświadczenie zaszyfrowanych [ **polecenia cmdlet Get-Credential** ](/powershell/module/microsoft.powershell.security/get-credential) wyświetla monit o podanie nazwy użytkownika i hasło, które mogą być przekazywane do [ **polecenia cmdlet New-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Aby zaktualizować poświadczenia
Po zmianie hasła, użyj [ **polecenia cmdlet Set-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) i ustaw **CredentialName** parametru.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Do zdefiniowania obiektu docelowego mapy fragmentów elastycznej bazy danych
Do wykonania zadania dla wszystkich baz danych w zestawie fragmentów (utworzone za pomocą [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)), użyj mapowania fragmentów w postaci jako docelowej bazy danych. W tym przykładzie wymaga aplikacji podzielonej na fragmenty, utworzony za pomocą biblioteki klienckiej Elastic Database. Zobacz [wprowadzenie do przykładowej narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

Bazy danych Menedżera mapowań fragmentów musi być ustawiona jako docelowej bazy danych, a opcja mapy określonego fragmentu musi być określona jako element docelowy.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Tworzenie skryptu T-SQL do wykonania w bazach danych
Podczas tworzenia skryptów T-SQL do wykonania, zalecane jest aby budować je jako [idempotentne](https://en.wikipedia.org/wiki/Idempotence) i odporna na awarie. Zadania elastic Database ponowi próbę wykonania skryptu, zawsze wtedy, gdy wykonanie napotka błąd, niezależnie od klasyfikacji awarii.

Użyj [ **polecenia cmdlet New-AzureSqlJobContent** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) Aby utworzyć i zapisać skrypt dla zestawu i wykonywanie **- ContentName** i **- CommandText** Parametry.

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
Jeśli nie zdefiniowano skryptu T-SQL w pliku, umożliwia to zaimportuj skrypt:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Aby zaktualizować skryptu T-SQL do wykonania w bazach danych
Ten skrypt programu PowerShell aktualizuje tekst polecenia T-SQL dla istniejącego skryptu.

Ustaw następujące zmienne, aby odzwierciedlić definicji żądaną skryptu do skonfigurowania:

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

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Aby utworzyć zadanie do uruchomienia skryptu w mapowania fragmentów w postaci
Ten skrypt programu PowerShell uruchamia zadanie do wykonania skryptu w poszczególnych fragmentach z elastycznym skalowaniem mapy fragmentów.

Ustaw następujące zmienne, aby odzwierciedlić żądaną skryptu i docelowy:

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
Ten skrypt programu PowerShell wykonuje istniejącego zadania:

Aktualizacja następującej zmiennej, aby odzwierciedlić nazwę żądanego zadania, aby zostały wykonane:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Aby pobrać stan wykonania pojedynczego zadania
Użyj [ **polecenia cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) i ustaw **JobExecutionId** parametru, aby wyświetlić stan wykonywania zadania.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Użyto tych samych **Get AzureSqlJobExecution** polecenia cmdlet z **właściwość IncludeChildren** parametru, aby wyświetlić stan wykonania zadania podrzędnego, a mianowicie określony stan dla każdego wykonania zadania, dla każdej bazy danych Celem tego zadania.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Aby wyświetlić stan między wiele wykonań zadania
[ **Polecenia cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) ma wiele parametry opcjonalne, które mogą służyć do wyświetlania wielu wykonań zadań w celu filtrowany za pośrednictwem podane parametry. Poniżej przedstawiono niektóre możliwe sposoby używania Get AzureSqlJobExecution:

Pobieranie wszystkich wykonań zadań najwyższego poziomu:

    Get-AzureSqlJobExecution

Pobieranie wszystkich wykonań najwyższego poziomu zadania, w tym liczba wykonań zadań nieaktywne:

    Get-AzureSqlJobExecution -IncludeInactive

Pobieranie wszystkich wykonań zadania podrzędnego dostarczone zadanie identyfikatora wykonywania, liczba wykonań zadań nieaktywne w tym:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Pobieranie wszystkich wykonań zadania utworzone za pomocą harmonogramu / zadanie kombinacji, w tym zadania nieaktywne:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Pobieranie wszystkich zadań przeznaczonych dla określonego mapowania fragmentów w postaci, w tym zadania nieaktywne:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Pobieranie wszystkich zadań przeznaczonych dla określonej kolekcji niestandardowych, w tym zadania nieaktywne:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Pobieranie listy zadań wykonań zadań w ramach wykonania określonego zadania:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Pobierz szczegóły wykonania zadania dla zadania:

Poniższy skrypt programu PowerShell, można wyświetlić szczegóły wykonania zadania, które jest szczególnie przydatna podczas debugowania awarii wykonywania.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Aby pobrać błędy w ramach zadania zadanie wykonania
**Obiektu JobTaskExecution** zawiera właściwość do zarządzania cyklem życia zadania, wraz z właściwością wiadomości. Jeżeli wykonanie zadania zadania nie powiodło się, zostanie ustawiona właściwość cyklu życia do i będzie można ustawić właściwości wiadomości Wynikowy komunikat o wyjątku i jego stosu. Jeśli zadanie nie powiodło się, jest ważne, aby wyświetlić szczegóły zadania, które nie powiodła się dla danego zadania.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Aby czekać na ukończenie wykonywania zadania
Poniższy skrypt programu PowerShell może służyć do czekać na zakończenie zadania zadania:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Tworzenie zasad wykonywania niestandardowych
Zadania elastic Database obsługuje tworzenie zasad wykonywania niestandardowych, które mogą być stosowane podczas uruchamiania zadania.

Zezwalaj na aktualnie zasad wykonywania do definiowania:

* Nazwa: Identyfikator zasad wykonywania programu.
* Limit czasu zadania: Całkowity czas, zanim zadanie zostanie anulowane przez zadania Elastic Database.
* Interwał ponawiania początkowa: Interwał oczekiwania przed pierwszym ponowieniem próby.
* Interwał maksymalną liczbę ponownych prób: Limit interwałów ponawiania do użycia.
* Ponów próbę interwał wycofywania współczynnik: Współczynnik używany do obliczania kolejny odstęp czasu między kolejnymi próbami.  Używana jest następująca formuła: (Początkowej interwał ponawiania) * Math.Pow — ((interwał wycofywania współczynnik) (liczba ponownych prób) - 2). 
* Maksymalna liczba prób: Maksymalna liczba ponownych prób do wykonania w ramach danego zadania.

Domyślne zasady wykonywania korzysta z następujących wartości:

* Nazwa: Domyślne zasady wykonywania
* Limit czasu zadania: 1 tydzień
* Interwał ponawiania początkowa:  100 MS
* Interwał maksymalną liczbę ponownych prób: 30 minut
* Ponów próbę współczynnik interwału: 2
* Maksymalna liczba prób: 2,147,483,647

Utwórz zasady wykonywania żądanego:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Aktualizowanie zasad wykonywania niestandardowych
Aktualizuj zasady wykonywania żądanego do aktualizacji:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Anulowanie zadania
Zadania elastic Database obsługuje żądania anulowania zadań.  Jeśli zadania Elastic Database wykryje na żądanie anulowania zadania aktualnie wykonywane, podejmie próbę zatrzymania zadania.

Istnieją dwa różne sposoby, że zadania Elastic Database może wykonywać anulowania:

1. Anuluj aktualnie wykonywanych zadań: W przypadku wykrycia anulowania, gdy zadanie jest obecnie uruchomiony, anulowanie zostanie podjęta w ramach aktualnie wykonywanej aspekt zadania.  Na przykład: W przypadku obecnie jest wykonywana, gdy podejmowana jest próba anulowania wolno działające zapytanie, nastąpi próba anulować wykonywanie zapytania.
2. Trwa anulowanie ponownych prób wykonania zadania: W przypadku anulowania wykrycia przez wątek kontroli przed uruchomiono zadanie do wykonania, wątek kontroli uniknąć natychmiastowego generowania zadania i zadeklarować żądania, ponieważ zostało anulowane.

W przypadku anulowania zadania jest wymagany dla zadania nadrzędnego, dla zadania nadrzędnego i wszystkie jego zadania podrzędne będą honorowane żądanie anulowania.

Aby przesłać żądanie anulowania, należy użyć [ **polecenia cmdlet Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) i ustaw **JobExecutionId** parametru.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Aby usunąć zadanie i historię zadania asynchronicznego
Zadania elastic Database obsługuje asynchroniczne usunięcie zadania. Zadanie może być oznaczony do usunięcia, a system spowoduje usunięcie zadania i jego Historia zadania, po zakończeniu wszystkich wykonań zadania dla zadania. System nie będzie automatycznie anulowania wykonań aktywnych zadań.  

Wywoływanie [ **Stop AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) anulować wykonań aktywnych zadań.

Aby wyzwolić zadanie usuwania, należy użyć [ **polecenia cmdlet Remove-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) i ustaw **JobName** parametru.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Aby utworzyć cel niestandardowej bazy danych
Można zdefiniować cele niestandardowej bazy danych, wykonanie bezpośrednich lub dołączania w ramach grupy niestandardowej bazy danych. Na przykład ponieważ **pul elastycznych** są nie jest jeszcze bezpośrednio obsługiwany przy użyciu interfejsów API programu PowerShell, można utworzyć niestandardową bazę danych obiekt docelowy oraz docelowy kolekcji niestandardowej bazy danych, która obejmuje wszystkie bazy danych w puli.

Ustaw następujące zmienne, aby odzwierciedlić informacje o żądanej bazy danych:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Aby utworzyć cel kolekcji niestandardowej bazy danych
Użyj [ **New AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) polecenia cmdlet, aby zdefiniować cel kolekcji niestandardowej bazy danych, aby włączyć wykonywanie w wielu lokalizacjach docelowych zdefiniowanych bazy danych. Po utworzeniu grupy bazy danych, baz danych może być skojarzony z elementem docelowym kolekcji niestandardowej.

Ustaw następujące zmienne w celu odzwierciedlenia konfiguracji docelowego żądanej kolekcji niestandardowej:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Aby dodać bazy danych do docelowej kolekcji niestandardowej bazy danych
Aby dodać bazę danych do użycia określonych kolekcji niestandardowej [ **AzureSqlJobChildTarget Dodaj** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) polecenia cmdlet.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Przejrzyj baz danych w docelowej kolekcji niestandardowej bazy danych
Użyj [ **Get AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) polecenie cmdlet do pobierania podrzędnych baz danych w docelowej kolekcji niestandardowej bazy danych. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Tworzenie zadania do uruchomienia skryptu w docelowej kolekcji niestandardowej bazy danych
Użyj [ **New AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) polecenia cmdlet, aby utworzyć zadanie względem grupy baz danych zdefiniowane przez obiekt docelowy kolekcję niestandardową bazę danych. Zadania elastic Database będzie rozwiń zadania na wiele zadań podrzędnych, odpowiadający każdej bazy danych skojarzony z elementem docelowym kolekcji niestandardowej bazy danych i upewnij się, że skrypt zostanie wykonany w każdej bazie danych. Ponownie ważne jest, że skrypty są idempotentne, aby była odporna na liczbę ponownych prób.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Zbieranie danych w bazach danych
Zadanie służy do wykonywania zapytania w grupie baz danych i wysyłać wyniki do określonej tabeli. Tabela może być odpytywany w późniejszym czasie, aby zobaczyć wyniki zapytania z każdej bazy danych. Zapewnia to metoda asynchroniczna, aby wykonać zapytanie w wielu bazach danych. Nieudane próby są obsługiwane automatycznie za pomocą ponownych prób.

W określonej lokalizacji docelowej tabeli zostanie utworzony automatycznie, jeśli jeszcze nie istnieje. Nowa tabela pasuje do schematu zestawu wyników zwracanego. Jeśli skrypt zwraca wiele zestawów wyników, zadania elastycznych baz danych będzie wysyłać tylko pierwszy do tabeli docelowej.

Poniższy skrypt programu PowerShell jest wykonywany skrypt i zbiera wyniki do określonej tabeli. Ten skrypt założono, że skryptu T-SQL została utworzona która generuje pojedynczy zestaw wyników i docelowej kolekcji niestandardowej bazy danych została utworzona.

Ten skrypt używa [ **Get AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) polecenia cmdlet. Ustaw parametry skryptu, poświadczenia i element docelowy wykonywania:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Aby utworzyć i uruchomić zadanie dla scenariuszy zbierania danych
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
Poniższy skrypt programu PowerShell, można utworzyć harmonogram cykliczny. Ten skrypt używa-minutowego interwału, ale [ **AzureSqlJobSchedule nowy** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) obsługuje również parametry - DayInterval, - HourInterval, - MonthInterval i - WeekInterval. Można utworzyć harmonogramy, które są wykonywane tylko raz przez przekazanie - jednorazowa.

Utwórz nowy harmonogram:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Aby wyzwolić zadanie wykonywane zgodnie z harmonogramem czasu
Wyzwalacz zadania można zdefiniować zadania, wykonane zgodnie z harmonogramem. Poniższy skrypt programu PowerShell, można utworzyć wyzwalacza zadania.

Użyj [New AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) i ustaw następujące zmienne odnoszą się do żądanego zadania i harmonogramu:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Aby usunąć zaplanowane skojarzenie zatrzymania zadania wykonania zgodnie z harmonogramem
Aby przerwać cyklicznych wykonywania zadań za pomocą wyzwalacza zadania, można usunąć wyzwalacza zadania. Usuwanie wyzwalacza zadania można zatrzymać zadania zostanie wykonywany zgodnie z harmonogramem przy użyciu [ **polecenia cmdlet Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Pobieranie wyzwalaczy zadania powiązane z harmonogramu
Poniższy skrypt programu PowerShell może służyć do wyświetlania wyzwalaczy zadania zarejestrowany z harmonogramem określonym czasie.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Można pobrać zadania wyzwalaczy powiązany z zadania
Użyj [Get AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) do wyświetlić harmonogramy zawierający zarejestrowanych zadania.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Do tworzenia aplikacji warstwy danych (DACPAC) do wykonania w bazach danych
Aby utworzyć pliku DACPAC, zobacz [aplikacji warstwy danych](https://msdn.microsoft.com/library/ee210546.aspx). Aby wdrożyć pliku DACPAC, użyj [polecenia cmdlet New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). DACPAC muszą być dostępne w usłudze. Zalecane jest przekazywanie utworzonego pliku DACPAC do usługi Azure Storage i tworzenie [sygnatura dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) dla pakietu DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Do aktualizacji aplikacji warstwy danych (DACPAC) do wykonania w bazach danych
Istniejące DACPACs zarejestrowanych w ramach zadania Elastic Database może zostać zaktualizowana wskaż nowych identyfikatorów URI. Użyj [ **polecenia cmdlet Set-AzureSqlJobContentDefinition** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) można zaktualizować identyfikatora URI pliku DACPAC na istniejącym zarejestrowany plik DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Aby utworzyć zadanie do zastosowania aplikacji warstwy danych (DACPAC) w bazach danych
Po utworzeniu pliku DACPAC w ramach zadania Elastic Database można tworzyć zadania do stosowania pakietu DACPAC dla grupy baz danych. Poniższy skrypt programu PowerShell może służyć do tworzenia zadania pakietu DACPAC w kolekcji niestandardowej bazy danych:

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
