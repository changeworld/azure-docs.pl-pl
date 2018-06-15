---
title: Wprowadzenie zadania elastycznej bazy danych | Dokumentacja firmy Microsoft
description: Zadania elastycznej bazy danych umożliwia wykonywanie skryptów T-SQL, które obejmują wiele baz danych.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4f12c3353ca4949b3c1c031420ec5a0b8fdb2dbf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649156"
---
# <a name="getting-started-with-elastic-database-jobs"></a>Wprowadzenie zadania elastycznej bazy danych
Baza danych zadania elastyczne (wersja zapoznawcza) w bazie danych SQL Azure umożliwia niezawodnie wykonywanie skryptów T-SQL, obejmującej wiele baz danych podczas Automatyczne ponawianie próby i zapewnienia gwarancji ostatecznego zakończenia. Aby uzyskać więcej informacji na temat funkcji zadania elastycznej bazy danych, zobacz [zadania elastyczne](sql-database-elastic-jobs-overview.md).

W tym artykule rozszerza próbki w [wprowadzenie do korzystania z narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md). Po zakończeniu zostanie przedstawiony sposób tworzenia i zarządzania zadaniami, które zarządzają grupy powiązanych baz danych. Nie jest to wymagane, aby można było skorzystać z zalet elastycznej zadań za pomocą narzędzi elastycznego skalowania.

## <a name="prerequisites"></a>Wymagania wstępne
Pobierz i uruchom [wprowadzenie próbki narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Utwórz identyfikator niezależnego fragmentu mapy manager za pomocą przykładowej aplikacji
W tym miejscu tworzenia mapy niezależnego fragmentu manager oraz kilka fragmentów, następuje wstawiania danych do fragmentów. Jeśli masz już skonfigurowano w nich danych podzielonej odłamków, możesz pominąć następujące kroki i przejść do następnej sekcji.

1. Tworzenie i uruchamianie **wprowadzenie do korzystania z narzędzi elastycznej bazy danych** przykładowej aplikacji. Postępuj zgodnie z instrukcjami aż do kroku 7 w sekcji [pobieranie i uruchamianie aplikacji przykładowej](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Po zakończeniu kroku 7 zostanie wyświetlony następujący wiersz polecenia:

   ![Wiersz polecenia](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. W oknie wiersza polecenia wpisz "1" i naciśnij klawisz **Enter**. Tworzy identyfikator niezależnego fragmentu menedżera map i dodaje dwa niezależne do serwera. Następnie wpisz "3" i naciśnij klawisz **Enter**; Powtarzaj tę akcję, cztery razy. Wstawia Przykładowe wiersze danych z fragmentów.
3. [Portalu Azure](https://portal.azure.com) powinny być widoczne trzech nowych baz danych:

   ![Visual Studio potwierdzenia](./media/sql-database-elastic-query-getting-started/portal.png)

   W tym momencie utworzymy kolekcji niestandardowej bazy danych, które odzwierciedla wszystkie bazy danych na mapie niezależnego fragmentu. Pozwala tworzyć i wykonywać zadania, który dodaje nową tabelę między odłamków.

W tym miejscu będą zazwyczaj utworzymy mapy niezależnego fragmentu docelowych przy użyciu **AzureSqlJobTarget nowy** polecenia cmdlet. Bazy danych Menedżera mapy niezależnego fragmentu musi być ustawiona jako docelowej bazy danych, a następnie mapy określonych niezależnego fragmentu jest określony jako element docelowy. Zamierzamy wylicza wszystkie bazy danych na serwerze i dodać bazy danych do nowej kolekcji niestandardowych z wyjątkiem bazy danych master.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Utworzenie kolekcji niestandardowych i dodać wszystkie bazy danych na serwerze docelowym kolekcji niestandardowych z wyjątkiem wzorca.
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
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
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Tworzenie skryptu T-SQL do wykonania dla baz danych
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

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Utwórz zadanie można wykonać skryptu na niestandardową grupę baz danych

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Wykonanie zadania
Poniższy skrypt programu PowerShell może służyć do wykonywania istniejącego zadania:

Aktualizacja następującej zmiennej w celu odzwierciedlenia nazwa żądanego zadania zostały wykonane:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Pobiera stan wykonywania pojedyncze zadanie
Używać tego samego **Get-AzureSqlJobExecution** polecenia cmdlet z **właściwość IncludeChildren** parametr, aby wyświetlić stan wykonania zadania podrzędne, czyli określonym stanie dla każdego wykonywania zadania dla każdej bazy danych Celem tego zadania.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Wyświetl stan całej wielu wykonania zadania
**Get AzureSqlJobExecution** polecenie cmdlet ma wiele parametry opcjonalne, które mogą służyć do wyświetlania wielu wykonania zadania, filtrować za pomocą podanych parametrów. Poniżej przedstawiono niektóre możliwe sposoby używania Get-AzureSqlJobExecution:

Pobierz wszystkie wykonaniami aktywnego zadania najwyższego poziomu:

   ```
    Get-AzureSqlJobExecution
   ```

Pobierz wszystkie wykonaniami zadania najwyższego poziomu, w tym wykonaniami nieaktywnego zadania:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Pobierz wszystkie wykonania zadania podrzędne identyfikatora wykonywania podane zadanie wykonaniami nieaktywnego zadania w tym:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Pobierz wszystkie wykonania zadania utworzone za pomocą harmonogramu / zadania kombinacji, w tym nieaktywnych zadań:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Pobieranie wszystkich zadań przeznaczonych dla określonej niezależnych mapy, w tym nieaktywnych zadań:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Pobieranie wszystkich zadań przeznaczonych dla określonej kolekcji niestandardowych, łącznie z nieaktywnych zadań:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Pobieranie listy zadanie wykonania zadania w ramach wykonania określonego zadania:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Pobieranie szczegółów wykonywania zadań zadania:

Poniższy skrypt programu PowerShell może służyć do wyświetlania szczegółów zadania wykonywania zadania, które jest szczególnie przydatne w przypadku debugowania awarii wykonywania.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Pobrać błędów w ramach zadania wykonania zadania
Obiekt JobTaskExecution zawiera właściwość dla cyklu życia zadania wraz z właściwością wiadomości. Jeżeli wykonanie zadania zadania nie powiodło się, ma ustawioną właściwość cyklu *życia* i właściwości wiadomości ustawiono Wynikowy komunikat o wyjątku i jego stosu. Jeśli zadanie nie powiodło się, jest ważne wyświetlić szczegóły zadania, których nie powiodła się dla danego zadania.

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
Poniższy skrypt programu PowerShell można czekać na ukończenie zadania zadania:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Tworzenie zasad wykonywania niestandardowych
Zadania elastyczne bazy danych obsługuje tworzenie zasad wykonywania niestandardowych, które mogą być stosowane podczas uruchamiania zadania.

Zasady wykonywania obecnie umożliwiają definiowanie:

* Nazwa: Identyfikator zasad wykonywania.
* Limit czasu zadania: Całkowity czas przed anulowaniem zadania przez zadania elastyczne bazy danych.
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

### <a name="update-a-custom-execution-policy"></a>Aktualizacja zasad wykonywania niestandardowych
Aktualizacja zasad wykonywania żądanej aktualizacji:

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
Zadania elastyczne bazy danych obsługuje żądania anulowania zadań.  Jeśli zadania elastyczne bazy danych wykryje żądanie anulowania aktualnie wykonywanego zadania, podejmie próbę zatrzymania zadania.

Istnieją dwa różne sposoby, że zadania elastyczne bazy danych można wykonać anulowania:

1. Anulowanie aktualnie wykonywanych zadań: wykrycie anulowania aktualnie uruchomionej zadania anulowania nastąpiła w aspekcie aktualnie wykonywanego zadania.  Na przykład: w przypadku długo działające kwerendy obecnie wykonywana jest próba anulowania jest próba anulować wykonywanie zapytania.
2. Anulowanie zadań ponownych prób: W przypadku anulowania wykrycia przez wątek kontroli przed uruchomieniu zadania do wykonania, wątek kontroli pozwala uniknąć uruchamiania zadania i zadeklarować żądania, jak anulowane.

Jeśli żądanie anulowania zadań dla zadania nadrzędnego, żądanie anulowania jest honorowane dla zadania nadrzędnego i wszystkich jego zadań podrzędnych.

Aby przesłać żądanie anulowania, użyj **Stop-AzureSqlJobExecution** polecenia cmdlet i ustaw **JobExecutionId** parametru.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Usuwanie zadania według nazwy i historii zadań
Zadania elastyczne bazy danych obsługuje asynchroniczne usuwania. Zadanie może być oznaczony do usunięcia i system usuwa zadanie, a cała Historia zadań, po zakończeniu wszystkich wykonania zadań dla zadania. System nie automatycznie anulowania wykonaniami aktywnego zadania.  

Zamiast tego Stop-AzureSqlJobExecution muszą być wywoływane anulować wykonaniami aktywnego zadania.

Aby wyzwolić usunięcia zadania, należy użyć **AzureSqlJob Usuń** polecenia cmdlet i ustaw **JobName** parametru.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Tworzenie obiektu docelowego w niestandardowej bazie danych
Obiekty docelowe w niestandardowej bazie danych można zdefiniować w zadania elastycznej bazy danych, które mogą być używane do wykonywania bezpośrednio lub dołączania w ramach grupy niestandardowej bazy danych. Ponieważ **pule elastyczne** nie są jeszcze bezpośrednio obsługiwane za pośrednictwem interfejsów API środowiska PowerShell, możesz po prostu utworzyć obiekt docelowy w niestandardowej bazie danych oraz miejsca docelowego do kolekcji niestandardowej bazy danych, które obejmuje wszystkie bazy danych w puli.

Ustaw następujące zmienne, aby odzwierciedlić informacje o żądanej bazy danych:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Utwórz docelowy kolekcji niestandardowej bazy danych
Umożliwia wykonanie przez wiele obiektów docelowych określonych bazy danych można zdefiniować docelowej kolekcji niestandardowej bazy danych. Po utworzeniu grupy bazy danych, bazy danych może być skojarzony z docelowym kolekcji niestandardowej.

Ustaw następujące zmienne w celu uwzględnienia konfiguracji docelowej żądanej kolekcji niestandardowych:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Dodawanie baz danych do docelowej kolekcji niestandardowej bazy danych
Obiekty docelowe bazy danych mogą być skojarzone z obiektami docelowymi kolekcji niestandardowej bazy danych, aby utworzyć grupę baz danych. Zawsze, gdy tworzone jest zadanie cel kolekcji niestandardowej bazy danych, jest rozwinięta pod kątem bazy danych powiązanych z grupą w czasie wykonywania.

Dodaj odpowiednie bazy danych do określonej kolekcji niestandardowych:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Przejrzyj baz danych w docelowej kolekcji niestandardowej bazy danych
Użyj **Get-AzureSqlJobTarget** polecenia cmdlet, aby pobrać podrzędnej bazy danych w docelowej kolekcji niestandardowej bazie danych.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Utwórz zadanie do wykonania skryptu w docelowej kolekcji niestandardowej bazy danych
Użyj **AzureSqlJob nowy** polecenia cmdlet można utworzyć zadania w odniesieniu do grupy baz danych, wynika z docelowej kolekcji niestandardowej bazy danych. Zadania elastyczne bazy danych jest rozwijana zadanie do wielu zadań podrzędnych, odpowiadający każdej bazy danych skojarzony z elementem docelowym kolekcji niestandardowej bazy danych i upewnij się, że skrypt zostanie wykonany w każdej bazie danych. Ponownie ważne jest, że skrypty są idempotentności pozwala uzyskać odporność na ponownych prób.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Zbieranie danych dla baz danych
**Zadania elastyczne bazy danych** obsługuje wykonywanie zapytania na grupę baz danych i wysyła wyników do określonej bazy danych tabeli. Tabela może być badana po fakcie, aby zobaczyć wyniki zapytania w każdej bazie danych. Zapewnia to asynchronicznego mechanizmu mógł wykonać zapytania przez wiele baz danych. Błąd przypadkach, takich jak jedna baza danych jest tymczasowo niedostępne są obsługiwane automatycznie za pomocą ponownych prób.

W określonej lokalizacji docelowej tabeli jest tworzony automatycznie, jeśli go jeszcze nie istnieje, dopasowania schematu zestaw wyników zwrócony. Jeśli wykonanie skrypt zwraca wiele zestawów wyników, zadania elastycznej bazy danych wysyła tylko pierwsza z nich do tabeli docelowej podana.

Poniższy skrypt programu PowerShell może służyć do uruchomienia skryptu zbieranie jej wyników do określonej tabeli. Ten skrypt założono, że utworzono skryptu T-SQL, który generuje pojedynczy zestaw wyników i docelowej kolekcji niestandardowej bazie danych została utworzona.

Ustaw następujące polecenie, aby odzwierciedlać żądaną skryptu, poświadczeń i wykonanie docelowego:

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

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Utwórz harmonogram wykonywania zadania przy użyciu wyzwalacz zadania
Poniższy skrypt programu PowerShell można utworzyć reoccurring harmonogramu. Ten skrypt używa interwał jedną minutę, ale nowy AzureSqlJobSchedule obsługuje również parametry - DayInterval, - HourInterval i - MonthInterval, - WeekInterval. Można utworzyć harmonogramy, które są wykonywane tylko raz przekazywanie przez - jednorazowe.

Utwórz nowy harmonogram:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Utwórz wyzwalacz zadania zostały wykonane na harmonogram zadania
Wyzwalacz zadania można definiować zadania wykonywane zgodnie z harmonogramem. Poniższy skrypt programu PowerShell, można utworzyć wyzwalacza zadania.

Ustaw następujące zmienne odpowiadają żądanego zadania i harmonogramu:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Usuń skojarzenie zaplanowane zatrzymania zadania wykonywanie zgodnie z harmonogramem
Aby przerwać pojawiał wykonywania zadań za pomocą wyzwalacza zadania, można usunąć wyzwalacza zadania.
Usuń wyzwalacz zadania zatrzymania zadania z wykonywana zgodnie z harmonogramem przy użyciu **AzureSqlJobTrigger Usuń** polecenia cmdlet.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importuj wyniki zapytania elastycznej bazy danych do programu Excel
 Możesz zaimportować wyników zapytania do pliku programu Excel.

1. Launch Excel 2013.
2. Przejdź do **danych** wstążki.
3. Kliknij przycisk **z innych źródeł** i kliknij przycisk **z programu SQL Server**.

   ![Importowania z programu Excel z innych źródeł](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. W **Kreator połączenia danych** wpisz poświadczenia, a nazwa logowania serwera. Następnie kliknij przycisk **Next** (Dalej).
5. W oknie dialogowym **wybierz bazę danych, która zawiera dane, które mają**, wybierz pozycję **ElasticDBQuery** bazy danych.
6. Wybierz **klientów** tabeli w widoku listy, a następnie kliknij przycisk **dalej**. Następnie kliknij przycisk **Zakończ**.
7. W **i zaimportuj dane** formularza, w obszarze **wybierz sposób wyświetlania tych danych w skoroszycie**, wybierz pozycję **tabeli** i kliknij przycisk **OK**.

Wszystkie wiersze z **klientów** tabeli, przechowywane w różnych odłamków wypełnić arkuszu programu Excel.

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz użyć funkcji danych programu Excel. Umożliwiają parametry połączenia z nazwą serwera, nazwa bazy danych i poświadczeń nawiązanie narzędzi integracji danych i BI kwerendy elastycznej bazy danych. Upewnij się, czy program SQL Server jest obsługiwana jako źródło danych dla własnych narzędzi. Zobacz kwerendy elastycznej bazy danych i tabele zewnętrzne, podobnie jak wszystkie inne bazy danych programu SQL Server i tabel programu SQL Server, które można połączyć się z narzędziem.

### <a name="cost"></a>Koszty
Brak dodatkowe opłaty za pomocą funkcji zapytania elastycznej bazy danych. Jednak w tej chwili ta funkcja jest dostępna tylko w warstwie Premium i biznesowe krytyczne (wersja zapoznawcza) baz danych i pul elastycznych jako punkt końcowy, ale odłamków może być dowolnym warstwy usługi.

Aby uzyskać informacje o cenach zobacz [szczegóły cennika bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
