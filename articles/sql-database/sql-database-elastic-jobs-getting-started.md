---
title: Wprowadzenie do zadań elastycznych baz danych | Dokumentacja firmy Microsoft
description: Zadania elastic database umożliwia wykonywanie skryptów T-SQL, obejmujące wiele baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 68a5bdef17077d1815b6d85e121d9bb26c2280bf
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484258"
---
# <a name="getting-started-with-elastic-database-jobs"></a>Wprowadzenie do zadań elastycznych baz danych

Zadania elastic Database (wersja zapoznawcza) dla usługi Azure SQL Database umożliwia niezawodne wykonywanie skryptów T-SQL, obejmujące wiele baz danych podczas Automatyczne ponawianie próby i ostateczną uzupełniania gwarantuje dostarczanie. Aby uzyskać więcej informacji na temat funkcji zadań elastycznej bazy danych, zobacz [zadań elastycznych](sql-database-elastic-jobs-overview.md).

W tym artykule rozszerza przykładu w [rozpoczęcie korzystania z narzędzi elastycznych baz danych](sql-database-elastic-scale-get-started.md). Po zakończeniu dowiesz się, jak tworzyć i zarządzać zadaniami, które zarządzają grupą powiązanych baz danych. Nie jest wymagane do używania narzędzia elastyczne skalowanie, aby można było korzystać z zalet zadań elastycznych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Pobierz i uruchom [wprowadzenie do przykładowej narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Tworzenie fragmentu manager mapy za pomocą przykładowej aplikacji

Tu możesz tworzyć mapowania fragmentów w postaci manager oraz kilka fragmentów, a następnie wstawiania danych na fragmenty. Jeśli masz już skonfigurować przy użyciu danych podzielonych na fragmenty w tych fragmentach, możesz pominąć poniższe kroki i przejść do następnej sekcji.

1. Kompilowanie i uruchamianie **rozpoczęcie korzystania z narzędzi elastycznych baz danych** przykładowej aplikacji. Postępuj zgodnie z instrukcjami aż do kroku 7 w sekcji [pobieranie i uruchamianie aplikacji przykładowej](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Na końcu krok 7 zostanie wyświetlony następujący wiersz polecenia:

   ![Wiersz polecenia](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. W oknie wiersza polecenia wpisz "1", a następnie naciśnij klawisz **Enter**. Tworzy Menedżera mapowań fragmentów i dodaje dwie fragmentów do serwera. Następnie wpisz "3" i naciśnij klawisz **Enter**; Powtarzaj tę akcję, cztery razy. Wstawia Przykładowe wiersze danych z fragmentów.
3. [Witryny Azure portal](https://portal.azure.com) powinny być widoczne trzy nowe bazy danych:

   ![Visual Studio potwierdzenia](./media/sql-database-elastic-query-getting-started/portal.png)

   W tym momencie możemy utworzyć kolekcję niestandardową bazę danych, która odzwierciedla wszystkich baz danych w ramach mapowania fragmentów. Dzięki temu można tworzyć i wykonywać zadania, który dodaje nową tabelę między fragmentami.

W tym miejscu będą zwykle utworzymy mapowania fragmentów w postaci docelowymi w programie **New AzureSqlJobTarget** polecenia cmdlet. Bazy danych Menedżera mapowań fragmentów musi być ustawiona jako docelowej bazy danych, a następnie mapowania określonych fragmentów jest określony jako element docelowy. Pobierzemy mógł wyliczyć wszystkich baz danych na serwerze i dodawanie baz danych do nowej kolekcji niestandardowych z wyjątkiem bazy danych master.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Tworzy kolekcję niestandardową, a następnie dodaj wszystkie bazy danych na serwerze do docelowej kolekcji niestandardowej, z wyjątkiem wzorca

   ```powershell
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Tworzenie skryptu T-SQL do wykonania w bazach danych
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Tworzenie zadania do uruchomienia skryptu w niestandardowej grupy baz danych

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Wykonywania zadania
Poniższy skrypt programu PowerShell mogą służyć do wykonywania istniejącego zadania:

Aktualizacja następującej zmiennej, aby odzwierciedlić nazwę żądanego zadania, aby zostały wykonane:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Pobiera stan wykonania pojedynczego zadania
Użyto tych samych **Get AzureSqlJobExecution** polecenia cmdlet z **właściwość IncludeChildren** parametru, aby wyświetlić stan wykonania zadania podrzędnego, a mianowicie określony stan dla każdego wykonania zadania, dla każdej bazy danych Celem tego zadania.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Wyświetlanie stanu między wiele wykonań zadania
**Get AzureSqlJobExecution** polecenie cmdlet ma wiele parametry opcjonalne, które mogą służyć do wyświetlania wielu wykonań zadań w celu filtrowany za pośrednictwem podane parametry. Poniżej przedstawiono niektóre możliwe sposoby używania Get AzureSqlJobExecution:

Pobieranie wszystkich wykonań aktywnego zadania najwyższego poziomu:

   ```
    Get-AzureSqlJobExecution
   ```

Pobieranie wszystkich wykonań zadania najwyższego poziomu, w tym liczba wykonań zadań nieaktywne:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Pobieranie wszystkich wykonań zadania podrzędnego dostarczone zadanie identyfikatora wykonywania, liczba wykonań zadań nieaktywne w tym:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Pobieranie wszystkich wykonań zadania utworzone za pomocą harmonogramu / zadanie kombinacji, w tym zadania nieaktywne:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Pobieranie wszystkich zadań przeznaczonych dla określonego mapowania fragmentów w postaci, w tym zadania nieaktywne:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Pobieranie wszystkich zadań przeznaczonych dla określonej kolekcji niestandardowych, w tym zadania nieaktywne:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Pobieranie listy zadań wykonań zadań w ramach wykonania określonego zadania:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Pobierz szczegóły wykonania zadania dla zadania:

Poniższy skrypt programu PowerShell, można wyświetlić szczegóły wykonania zadania, które jest szczególnie przydatna podczas debugowania awarii wykonywania.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Pobieranie błędy w ramach zadania zadanie wykonania
Obiekt JobTaskExecution zawiera właściwości do zarządzania cyklem życia zadania, wraz z właściwością wiadomości. Jeżeli wykonanie zadania zadania nie powiodło się, ma ustawioną właściwość cyklu *życia* i właściwości wiadomości ustawiono Wynikowy komunikat o wyjątku i jego stosu. Jeśli zadanie nie powiodło się, jest ważne, aby wyświetlić szczegóły zadania, które nie powiodła się dla danego zadania.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>Oczekiwanie na ukończenie wykonywania zadania
Poniższy skrypt programu PowerShell może służyć do czekać na zakończenie zadania zadania:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Tworzenie zasad wykonywania niestandardowych
Zadania elastic Database obsługuje tworzenie zasad wykonywania niestandardowych, które mogą być stosowane podczas uruchamiania zadania.

Zezwalaj na aktualnie zasad wykonywania do definiowania:

* Nazwa: Identyfikator zasad wykonywania programu.
* Limit czasu zadania: Łączny czas przed anulowaniem zadania przez zadania Elastic Database.
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

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Aktualizowanie zasad wykonywania niestandardowych
Aktualizuj zasady wykonywania żądanego do aktualizacji:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Anulowanie zadania

Zadania elastic Database obsługuje żądania anulowania zadania.  Zadania Elastic Database wykryje na żądanie anulowania zadania aktualnie wykonywane, próbuje zatrzymać zadanie.

Istnieją dwa różne sposoby, że zadania Elastic Database może wykonywać anulowania:

1. Trwa anulowanie aktualnie wykonywanych zadań: W przypadku wykrycia anulowania, gdy zadanie jest obecnie uruchomiony, anulowanie zostanie podjęta w ramach aktualnie wykonywanej aspekt zadania.  Na przykład: W przypadku obecnie jest wykonywana, gdy podejmowana jest próba anulowania wolno działające zapytanie ma próba anulować wykonywanie zapytania.
2. Anulowanie zadań ponawia próbę: W przypadku anulowania wykrycia przez wątek kontroli przed uruchomiono zadanie do wykonania, wątek kontroli pozwala uniknąć uruchamiania zadania i deklaruje żądania, ponieważ zostało anulowane.

W przypadku anulowania zadania jest wymagany dla zadania nadrzędnego, żądanie anulowania zostanie uznane dla zadania nadrzędnego i wszystkich jego zadań podrzędnych.

Aby przesłać żądanie anulowania, należy użyć **Stop AzureSqlJobExecution** polecenia cmdlet i ustaw **JobExecutionId** parametru.

   ```powershell
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Usuwanie zadania według nazwy i historii zadań

Zadania elastic Database obsługuje asynchroniczne usunięcie zadania. Zadanie może być oznaczony do usunięcia, a system spowoduje usunięcie zadania i jego Historia zadania po zakończeniu wszystkich wykonań zadania dla zadania. System automatycznie Anuluj wykonań aktywnych zadań.  

Zamiast tego Stop AzureSqlJobExecution musi można wywołać można anulować wykonań aktywnych zadań.

Aby wyzwolić zadanie usuwania, należy użyć **AzureSqlJob Usuń** polecenia cmdlet i ustaw **JobName** parametru.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Utwórz obiekt docelowy niestandardowej bazy danych
Obiekty docelowe w niestandardowej bazie danych można zdefiniować w zadaniach elastycznych baz danych, które mogą być używane do wykonywania bezpośrednio lub do włączenia w obrębie grupy niestandardowej bazy danych. Ponieważ **pul elastycznych** nie są jeszcze bezpośrednio obsługiwane za pośrednictwem interfejsów API programu PowerShell, możesz po prostu utworzyć niestandardową bazę danych obiekt docelowy oraz docelowy kolekcji niestandardowej bazy danych, która obejmuje wszystkie bazy danych w puli.

Ustaw następujące zmienne, aby odzwierciedlić informacje o żądanej bazy danych:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Utwórz obiekt docelowy kolekcję niestandardową bazę danych
Obiekt docelowy kolekcję niestandardową bazę danych można zdefiniować włączyć wykonywanie w wielu lokalizacjach docelowych zdefiniowanych bazy danych. Po utworzeniu grupy bazy danych, baz danych może być skojarzony z obiektem docelowym kolekcji niestandardowej.

Ustaw następujące zmienne w celu odzwierciedlenia konfiguracji docelowego żądanej kolekcji niestandardowej:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Dodawanie baz danych do docelowej kolekcji niestandardowej bazy danych
Obiekty docelowe bazy danych może być skojarzony z celami kolekcji niestandardowej bazy danych, aby utworzyć grupę baz danych. Zawsze, gdy zostanie utworzone zadanie, który jest przeznaczony dla docelowej kolekcji niestandardowej bazy danych, jest rozwinięte pod kątem baz danych skojarzonego z grupą w czasie wykonywania.

Dodaj żądaną bazy danych do określonej kolekcji niestandardowej:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Przejrzyj baz danych w docelowej kolekcji niestandardowej bazy danych
Użyj **Get AzureSqlJobTarget** polecenie cmdlet do pobierania podrzędnych baz danych w docelowej kolekcji niestandardowej bazy danych.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Tworzenie zadania do uruchomienia skryptu w docelowej kolekcji niestandardowej bazy danych
Użyj **New AzureSqlJob** polecenia cmdlet, aby utworzyć zadanie względem grupy baz danych zdefiniowane przez obiekt docelowy kolekcję niestandardową bazę danych. Zadania elastic Database rozwija zadania na wiele zadań podrzędnych, odpowiadający każdej bazy danych skojarzony z elementem docelowym kolekcji niestandardowej bazy danych i upewnij się, że skrypt zostanie wykonany w każdej bazie danych. Ponownie ważne jest, że skrypty są idempotentne, aby była odporna na liczbę ponownych prób.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Zbieranie danych w bazach danych
**Zadania elastic Database** obsługuje wykonywanie zapytania dla grupy baz danych i przesyła wyniki do tabeli określonej bazy danych. Tabela może być odpytywany w późniejszym czasie, aby zobaczyć wyniki zapytania z każdej bazy danych. Dzięki temu asynchronicznego mechanizmu, można wykonać zapytania w wielu bazach danych. Błąd przypadkach, takich jak jedna baza danych jest tymczasowo niedostępne są obsługiwane automatycznie za pomocą ponownych prób.

W tabeli z określoną lokalizacją docelową jest tworzony automatycznie, jeśli go jeszcze nie istnieje, dopasowanie schematu zestawu wyników zwracanego. Jeśli wykonanie skryptu zwraca wiele zestawów wyników, zadania Elastic Database wysyła tylko pierwszy z nich do tabeli docelowej podana.

Poniższy skrypt programu PowerShell może służyć do uruchomienia skryptu zbieranie wyników do określonej tabeli. Ten skrypt założono, że utworzono skryptu T-SQL, która wysyła pojedynczy zestaw wyników i docelowej kolekcji niestandardowej bazy danych została utworzona.

Ustaw następujące polecenie, aby odzwierciedlić żądaną skryptu, poświadczenia i element docelowy wykonywania:

   ```
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
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Tworzenie i uruchamianie zadania dla scenariuszy zbierania danych
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Utwórz harmonogram wykonywania zadania przy użyciu wyzwalacza zadania
Poniższy skrypt programu PowerShell, można utworzyć harmonogram cyklicznych. Ten skrypt używa interwał jedną minutę, ale nowy AzureSqlJobSchedule obsługuje również parametry - DayInterval, - HourInterval, - MonthInterval i - WeekInterval. Można utworzyć harmonogramy, które są wykonywane tylko raz przez przekazanie - jednorazowa.

Utwórz nowy harmonogram:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Tworzenie wyzwalacza zadania, aby zadania wykonywane zgodnie z harmonogramem czasu
Wyzwalacz zadania można zdefiniować zadania, wykonane zgodnie z harmonogramem. Poniższy skrypt programu PowerShell, można utworzyć wyzwalacza zadania.

Ustaw następujące zmienne odnoszą się do żądanego zadania i harmonogramu:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Usuwanie zaplanowanego skojarzenia można zatrzymać zadania wykonania zgodnie z harmonogramem
Aby przerwać cyklicznych wykonywania zadań za pomocą wyzwalacza zadania, można usunąć wyzwalacza zadania.
Usuwanie wyzwalacza zadania można zatrzymać zadania zostanie wykonywany zgodnie z harmonogramem przy użyciu **AzureSqlJobTrigger Usuń** polecenia cmdlet.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importuj wyniki zapytania elastycznej bazy danych do programu Excel
 Można zaimportować wyników z zapytania do pliku programu Excel.

1. Launch Excel 2013.
2. Przejdź do **danych** wstążki.
3. Kliknij przycisk **z innych źródeł** i kliknij przycisk **z programu SQL Server**.

   ![Importowania z programu Excel z innych źródeł](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. W **Kreatora połączenia danych** wpisz poświadczenia nazwy i nazwy logowania serwera. Następnie kliknij przycisk **Next** (Dalej).
5. W oknie dialogowym **wybierz bazę danych, która zawiera dane, które mają**, wybierz opcję **ElasticDBQuery** bazy danych.
6. Wybierz **klientów** tabeli w widoku listy, a następnie kliknij przycisk **dalej**. Następnie kliknij przycisk **Zakończ**.
7. W **importu danych** formularza, w obszarze **wybierz sposób wyświetlania tych danych w skoroszycie**, wybierz opcję **tabeli** i kliknij przycisk **OK**.

Wszystkie wiersze z **klientów** tabeli, przechowywane w różnych fragmentach wypełnianie arkusza programu Excel.

## <a name="next-steps"></a>Kolejne kroki
Można teraz używać funkcji danych programu Excel. Użyj parametrów połączenia z nazwą serwera, nazwa bazy danych i poświadczeń, do łączenia z narzędzi integracji danych i analizy Biznesowej w bazie danych zapytania elastycznego. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla swojego narzędzia. Zapoznaj się zapytanie elastyczne bazy danych i tabel zewnętrznych, podobnie jak każdej innej bazy danych programu SQL Server i tabel programu SQL Server, które można połączyć się z narzędziem.

### <a name="cost"></a>Koszty
Nie ma żadnych dodatkowych opłat za używanie funkcji zapytanie elastycznej bazy danych. Jednak w tej chwili ta funkcja jest dostępna tylko na Premium i krytyczne dla działania firmy baz danych i pul elastycznych jako punkt końcowy, ale fragmentami może być dowolną warstwę usług.

Aby uzyskać informacje o cenach, zobacz [SQL Database — szczegóły cennika](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
