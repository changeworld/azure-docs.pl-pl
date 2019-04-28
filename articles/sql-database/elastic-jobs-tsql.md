---
title: Tworzenie i zarządzanie nimi w usłudze Azure SQL zadania Elastic Database przy użyciu języka Transact-SQL (T-SQL) | Dokumentacja firmy Microsoft
description: Uruchom skrypty w wielu bazach danych przy użyciu agenta elastycznych zadań bazy danych przy użyciu języka Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 59e0e4cf82af9851dacf3ec030575ed392571331
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475817"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Użyj instrukcji języka Transact-SQL (T-SQL), aby tworzyć i zarządzać nimi zadania Elastic Database

Ten artykuł zawiera wiele przykładowymi scenariuszami, aby rozpocząć pracę z usługą zadań elastycznych, przy użyciu języka T-SQL.

W przykładach użyto [procedur składowanych](#job-stored-procedures) i [widoków](#job-views) dostępne w [ *bazy danych zadania*](sql-database-job-automation-overview.md#job-database).

Języka Transact-SQL (T-SQL) jest używany do tworzenia, konfigurowania, wykonywania i zarządzać zadaniami. Tworzenie agenta elastycznych zadań nie jest obsługiwane w języku T-SQL, dlatego najpierw należy utworzyć *agenta elastycznych zadań* korzystanie z portalu lub [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Tworzenie poświadczeń do wykonywania zadań

Poświadczenie jest używane, aby nawiązać połączenie docelowe bazy danych do wykonania skryptu. Poświadczenie musi mieć odpowiednie uprawnienia, w bazach danych, określonego przez grupy docelowej, aby pomyślnie wykonać skrypt. Korzystając z serwera i/lub członka grupy docelowej puli, wysoce zalecane jest do tworzenia głównego poświadczenie do użycia odświeżyć poświadczenia przed rozszerzenia serwera i/lub puli, w momencie wykonywania zadania. Poświadczenie o zakresie bazy danych jest tworzony w bazie danych agenta zadania. Te same poświadczenia muszą być używane do *Utwórz dane logowania* i *Utwórz użytkownika posługującego się nazwą logowania, aby udzielić uprawnień do bazy danych logowania* na docelowych baz danych.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>Tworzenie grupy docelowej (serwery)

Poniższy przykład przedstawia sposób wykonania zadania dla wszystkich baz danych na serwerze.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Wyklucz poszczególnych baz danych

Poniższy przykład przedstawia sposób wykonania zadania dla wszystkich baz danych na serwerze bazy danych SQL, z wyjątkiem bazy danych o nazwie *MappingDB*.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Tworzenie grupy docelowej (pule)

Poniższy przykład pokazuje, jak pod kątem wszystkich baz danych w co najmniej jednej puli elastycznej.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Wdrażanie nowego schematu na wiele baz danych

Poniższy przykład pokazuje, jak wdrożyć nowy schemat do wszystkich baz danych.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Zbieranie danych przy użyciu wbudowanych parametrów

W wielu scenariuszach zbierania danych może być przydatna podczas dołączania niektórych z tych skryptów zmiennych w celu końcowego przetwarzania wyników zadania.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Na przykład aby zgrupować wszystkich wyników z tych samych razem wykonywania zadań, należy użyć *$(job_execution_id)* jak pokazano w następującym poleceniu:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorowanie wydajności bazy danych

Poniższy przykład tworzy nowe zadanie do zbierania danych wydajności z wieloma bazami danych.

Domyślnie będzie wyglądać agenta zadań, aby utworzyć tabelę do przechowywania wyników zwróconych w. W wyniku logowania skojarzone z poświadczeń używanych dla poświadczenia danych wyjściowych musisz mieć wystarczające uprawnienia do wykonania tej. Jeśli chcesz ręcznie utworzyć tabelę wcześniejsze następnie musi ona mieć następujące właściwości:
1. Kolumny przy użyciu poprawnej nazwy i typy danych dla zestawu wyników.
2. Dodatkowa kolumna internal_execution_id z typu danych uniqueidentifier.
3. Indeksu nieklastrowanego na indeks o nazwie `IX_<TableName>_Internal_Execution_ID` w kolumnie internal_execution_id.

Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenia:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Wyświetl definicje zadań

Poniższy przykład pokazuje sposób wyświetlania bieżącego definicje zadań.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>Rozpocznij ad-hoc wykonywania zadania

Poniższy przykład pokazuje, jak natychmiast uruchomić zadanie.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>Harmonogram wykonywania zadania

Poniższy przykład pokazuje, jak można zaplanować zadania do wykonania w przyszłości.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitoruj stan wykonania zadania

Poniższy przykład pokazuje, jak wyświetlić szczegóły stanu wykonywania dla wszystkich zadań.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Anulowanie zadania

Poniższy przykład pokazuje, jak można anulować zadania.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>Usuń stare historię zadania

Poniższy przykład pokazuje, jak usunąć historii zadań przed określonej daty.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Usunięcie zadania i jego Historia zadania

Poniższy przykład pokazuje, jak można usunąć zadania i wszystkie powiązane historii zadań.  
Połączyć się z [ *bazy danych zadania* ](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Zadanie procedur składowanych

Poniższe procedury składowanej znajdują się w [bazy danych zadania](sql-database-job-automation-overview.md#job-database).



|Procedura składowana  |Opis  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Dodaje nowe zadanie.    |
|[sp_update_job](#sp_update_job)    |      Aktualizuje istniejące zadanie.   |
|[sp_delete_job](#sp_delete_job)     |      Usuwa istniejące zadanie.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Do dodawania kroku do zadania.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Aktualizuje krok zadania.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Usuwa krok zadania.    |
|[sp_start_job](#sp_start_job)    |  Rozpoczyna się wykonywanie zadania.       |
|[sp_stop_job](#sp_stop_job)     |     Zatrzymuje wykonywanie zadania.   |
|[sp_add_target_group](#sp_add_target_group)    |     Dodaje grupy docelowej.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Usuwa grupy docelowej.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Dodaje do grupy docelowej bazy danych lub grupy baz danych.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Usuwa docelowy element członkowski grupy z grupy docelowej.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Usuwa rekordy historii zadania.     |





### <a name="spaddjob"></a>sp_add_job

Dodaje nowe zadanie. 
  
#### <a name="syntax"></a>Składnia  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>Argumenty  

[  **\@parametr job_name =** ] "parametr job_name"  
Nazwa zadania. Nazwa musi być unikatowa i nie może zawierać procentu (%) znak. Parametr job_name jest nvarchar(128) bez wartości domyślnej.

[  **\@opis =** ] "opis"  
Opis zadania. Długość opisu jest nvarchar(512) z domyślną wartością NULL. W przypadku pominięcia opis pusty ciąg jest używany.

[  **\@włączone =** ] włączone  
Czy jest włączony harmonogram zadań. Włączone jest bit z domyślną 0 (wyłączone). Jeśli jest to 0, zadanie nie jest włączona i nie jest uruchamiane zgodnie ze swoim harmonogramem; jednak go mogą być uruchamiane ręcznie. Jeśli jest to 1, zadanie będzie uruchamiany zgodnie ze swoim harmonogramem i może być także uruchamiane ręcznie.

[  **\@schedule_interval_type =**] schedule_interval_type  
Wartość wskazuje, kiedy zadanie jest wykonywana. schedule_interval_type jest nvarchar(50) z domyślną jeden raz i może być jedną z następujących wartości:
- Raz,
- "Minutes"
- "Hours",
- "Dni"
- "Weeks",
- "Liczba miesięcy:"

[  **\@schedule_interval_count =** ] schedule_interval_count  
Liczba okresów schedule_interval_count się komunikować między każdego wykonania zadania. schedule_interval_count to typ int, z domyślną 1. Wartość musi być większa lub równa 1.

[  **\@schedule_start_time =** ] schedule_start_time  
Data zadania, które można rozpocząć wykonywania. schedule_start_time jest DATETIME2, przy użyciu domyślnego 00:00:00.0000000 0001-01-01.

[  **\@schedule_end_time =** ] schedule_end_time  
Data zadania, które można zatrzymać wykonywanie. schedule_end_time jest DATETIME2, przy użyciu domyślnego 9999-12-31 11:59:59.0000000. 

[  **\@wartość job_id =** ] wartość job_id danych wyjściowych  
Numer identyfikacyjny zadań przypisanych do zadania, jeśli utworzono pomyślnie. wartość job_id jest zmienną danych wyjściowych typu uniqueidentifier.

#### <a name="return-code-values"></a>Zwracane wartości kodu

0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
musi być uruchamiane sp_add_job baza danych agenta zadań, które zostały określone podczas tworzenia zadania agenta.
Po wykonaniu sp_add_job można dodać zadania sp_add_jobstep może służyć do dodawania kroków, które wykonują działania zadania. Zadanie drukowania numer wersji początkowej to 0, co zostanie zwiększony do 1, po dodaniu pierwszy krok.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.

### <a name="spupdatejob"></a>sp_update_job

Aktualizuje istniejące zadanie.

#### <a name="syntax"></a>Składnia

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>Argumenty
[  **\@parametr job_name =** ] "parametr job_name"  
Nazwa zadania do zaktualizowania. Parametr job_name jest nvarchar(128).

[  **\@nowa_nazwa =** ] "nowa_nazwa"  
Nowa nazwa zadania. nowa_nazwa jest nvarchar(128).

[  **\@opis =** ] "opis"  
Opis zadania. Długość opisu jest nvarchar(512).

[  **\@włączone =** ] włączone  
Określa, czy harmonogram zadań jest włączony (1) lub nie włączono (0). Włączone jest bit.

[  **\@schedule_interval_type =** ] schedule_interval_type  
Wartość wskazuje, kiedy zadanie jest wykonywana. schedule_interval_type jest nvarchar(50) i może być jedną z następujących wartości:

- Raz,
- "Minutes"
- "Hours",
- "Dni"
- "Weeks",
- "Liczba miesięcy:"

[  **\@schedule_interval_count =** ] schedule_interval_count  
Liczba okresów schedule_interval_count się komunikować między każdego wykonania zadania. schedule_interval_count to typ int, z domyślną 1. Wartość musi być większa lub równa 1.

[  **\@schedule_start_time =** ] schedule_start_time  
Data zadania, które można rozpocząć wykonywania. schedule_start_time jest DATETIME2, przy użyciu domyślnego 00:00:00.0000000 0001-01-01.

[  **\@schedule_end_time =** ] schedule_end_time  
Data zadania, które można zatrzymać wykonywanie. schedule_end_time jest DATETIME2, przy użyciu domyślnego 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Po wykonaniu sp_add_job można dodać zadania sp_add_jobstep może służyć do dodawania kroków, które wykonują działania zadania. Zadanie drukowania numer wersji początkowej to 0, co zostanie zwiększony do 1, po dodaniu pierwszy krok.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.



### <a name="spdeletejob"></a>sp_delete_job

Usuwa istniejące zadanie.

#### <a name="syntax"></a>Składnia

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenty
[  **\@parametr job_name =** ] "parametr job_name"  
Nazwa zadania do usunięcia. Parametr job_name jest nvarchar(128).

[  **\@wymusić =** ] Wymuszaj  
Określa, czy chcesz usunąć, jeśli zadanie ma żadnych wykonań w toku i anulowanie wszystkich operacji w trakcie wykonywania (1) lub zakończone niepowodzeniem, jeśli wszystkie Liczba wykonań zadań są w toku (0). bit jest wymuszone.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Historia zadań zostanie usunięta automatycznie, gdy zadanie zostanie usunięte.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.



### <a name="spaddjobstep"></a>sp_add_jobstep

Do dodawania kroku do zadania.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenty

[  **\@parametr job_name =** ] "parametr job_name"  
Nazwa zadania, do którego należy dodać krok. Parametr job_name jest nvarchar(128).

[  **\@step_id =** ] step_id  
Numer identyfikacyjny kolejny krok zadania. Numery identyfikacyjne kroku są liczone od 1 i zwiększ bez przerw. Jeśli etap istniejących już ten identyfikator, krok i wszystkie następne czynności że swój identyfikator użytkownika zwiększana tak, aby ten nowy krok mogą być wstawiane do sekwencji. Jeśli nie zostanie określony, step_id zostanie automatycznie przypisany do ostatniej sekwencji kroków. step_id to int.

[  **\@step_name =** ] step_name  
Nazwa kroku. Należy określić, z wyjątkiem pierwszym krokiem zadania, który ma domyślną nazwę "JobStep" (dla wygody). step_name jest nvarchar(128).

[  **\@command_type =** ] "command_type"  
Typ polecenia, która jest wykonywana przez ten jobstep. command_type jest nvarchar(50) o wartości domyślne języka TSql, co oznacza, że wartość @command_type parametru jest skryptem T-SQL.

Jeśli zostanie określony, wartość musi być TSql.

[  **\@command_source =** ] "command_source"  
Typ lokalizacji przechowywania polecenia. command_source jest nvarchar(50) z wartością domyślną w tekście, co oznacza, że wartość @command_source parametr jest tekst dosłowny polecenia.

Jeśli zostanie określony, wartość musi być wbudowany.

[  **\@polecenia =** ] 'command'  
Polecenie musi być prawidłową skryptu T-SQL, a następnie jest wykonywany przez ten etap zadania. polecenie jest nvarchar(max) z domyślną wartością NULL.

[  **\@credential_name =** ] "credential_name"  
Nazwa bazy danych o określonym zakresie poświadczeń przechowywanych w tej bazie danych kontroli zadania, który jest używany do łączenia do każdego z docelowymi bazami danych w grupie docelowej po wykonaniu tego kroku. credential_name jest nvarchar(128).

[  **\@target_group_name =** ] "target nazwa_grupy"  
Nazwa grupy docelowej, która zawiera docelowych baz danych, które krok zadania zostaną wykonane na. target_group_name jest nvarchar(128).

[  **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Opóźnienie przed pierwszym ponowieniem próby próby, jeśli krok zadania zakończy się niepowodzeniem przy próbie wykonania początkowej. initial_retry_interval_seconds to typ int, za pomocą wartość domyślną 1.

[  **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami będzie powiększać większa niż ta wartość, jej wynosi tej wartości zamiast tego. maximum_retry_interval_seconds to typ int, przy użyciu wartości domyślnej 120.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Mnożnik do zastosowania do opóźnienia ponawiania, jeśli wiele zadań krok wykonywania próbuje zakończyć się niepowodzeniem. Na przykład jeśli pierwsze ponowienie miał opóźnienie 5-sekundowego mnożnik wycofywania jest w wersji 2.0, następnie drugi ponawiania prób będzie mieć opóźnienie 10 sekund i trzeci ponawiania prób będzie mieć opóźnienie wynoszącego 20 sekund. retry_interval_backoff_multiplier jest prawdziwe, wartością domyślną w wersji 2.0.

[  **\@retry_attempts =** ] retry_attempts  
Liczba ponownych prób wykonania, jeśli początkowa próba nie powiedzie się. Na przykład jeśli wartość retry_attempts jest 10, nastąpi 1 początkowa próba i 10 ponownymi próbami, co daje w sumie 11 prób. W przypadku niepowodzenia końcowego ponowienia próby wykonania zadania zostanie zakończona z cyklem życia nie powiodło się. retry_attempts to typ int, za pomocą wartość domyślną równą 10.

[  **\@step_timeout_seconds =** ] step_timeout_seconds  
Maksymalna ilość czasu dozwolony krok do wykonania. Tym razem po przekroczeniu, wykonanie zadania zostanie zakończona z cyklem życia przekroczenie limitu czasu. step_timeout_seconds to typ int, wartością domyślną 43,200 sekund (12 godzin).

[  **\@output_type =** ] "output_type"  
W przeciwnym razie wartość null, typu miejsca docelowego, który ustawiony wynik pierwszego polecenia są zapisywane. output_type jest nvarchar(50) z domyślną wartością NULL.

Jeśli zostanie określony, wartość musi być baza danych SQL.

[  **\@output_credential_name =** ] "output_credential_name"  
Jeśli nie ma wartość null, nazwa bazy danych obejmuje poświadczenie, które służy do nawiązania połączenia z bazą danych wyjściowych docelowego. Należy określić w przypadku output_type jest równe baza danych SQL. output_credential_name jest nvarchar(128) z wartością domyślną wartością null.

[  **\@output_subscription_id =** ] "output_subscription_id"  
Musi mieć opis.

[  **\@output_resource_group_name =** ] "output_resource_group_name"  
Musi mieć opis.

[  **\@output_server_name =** ] "output_server_name"  
W przeciwnym razie wartość null, w pełni kwalifikowana nazwa DNS serwera, który zawiera dane wyjściowe docelowej bazy danych. Należy określić w przypadku output_type jest równe baza danych SQL. output_server_name jest nvarchar(256) z domyślną wartością NULL.

[  **\@output_database_name =** ] "output_database_name"  
W przeciwnym razie wartość null, nazwa bazy danych, który zawiera dane wyjściowe tabeli docelowej. Należy określić w przypadku output_type jest równe baza danych SQL. output_database_name jest nvarchar(128) z domyślną wartością NULL.

[  **\@output_schema_name =** ] "output_schema_name"  
W przeciwnym razie wartość null, nazwa schematu SQL, który zawiera dane wyjściowe tabeli docelowej. Jeśli output_type równa baza danych SQL, wartość domyślna to dbo. output_schema_name jest nvarchar(128).

[  **\@output_table_name =** ] "output_table_name"  
W przeciwnym razie wartość null, nazwa tabeli, który ustawiony wynik pierwszego polecenia zostaną zapisane. Jeśli tabela już nie istnieje, zostanie on utworzony zależności od schematu zwraca zestaw wyników. Należy określić w przypadku output_type jest równe baza danych SQL. output_table_name jest nvarchar(128) z wartością domyślną wartością null.

[  **\@job_version =** ] job_version danych wyjściowych  
Parametr wyjściowy, który zostanie przypisany numer wersji zadania. job_version to int.

[  **\@max_parallelism =** ] max_parallelism danych wyjściowych  
Maksymalny poziom równoległości dla puli elastycznej. Zestaw, a następnie etap zadania zostaną ograniczone do uruchomienia tylko na maksymalnego wiele baz danych w jednej puli elastycznej. Dotyczy to każdej puli elastycznej, albo bezpośrednio znajduje się w grupie docelowej lub znajduje się wewnątrz serwera, który znajduje się w grupie docelowej. max_parallelism to int.


#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Po pomyślnym zakończeniu sp_add_jobstep, bieżący numer wersji zadania jest zwiększany. Następnym razem, zadanie jest wykonywane, nowa wersja będzie używany. Jeśli zadanie jest w trakcie wykonywania, wykonanie tego nie będzie zawierać nowy krok.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:  

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.



### <a name="spupdatejobstep"></a>sp_update_jobstep

Aktualizuje krok zadania.

#### <a name="syntax"></a>Składnia

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenty
[  **\@parametr job_name =** ] "parametr job_name"  
Nazwa zadania, do której należy ten krok. Parametr job_name jest nvarchar(128).

[  **\@step_id =** ] step_id  
Numer identyfikacyjny dla etapu zadania do zmodyfikowania. Należy określić step_id albo step_name. step_id to int.

[  **\@step_name =** ] "step_name"  
Nazwa kroku do zmodyfikowania. Należy określić step_id albo step_name. step_name jest nvarchar(128).

[  **\@new_id =** ] new_id  
Nowy numer identyfikacyjny sekwencji dla etapu zadania. Numery identyfikacyjne kroku są liczone od 1 i zwiększ bez przerw. Jeśli krok kolejność została zmieniona, następnie pozostałe kroki zostaną automatycznie oznaczenia.

[  **\@nowa_nazwa =** ] "nowa_nazwa"  
Nowa nazwa kroku. nowa_nazwa jest nvarchar(128).

[  **\@command_type =** ] "command_type"  
Typ polecenia, która jest wykonywana przez ten jobstep. command_type jest nvarchar(50) o wartości domyślne języka TSql, co oznacza, że wartość @command_type parametru jest skryptem T-SQL.

Jeśli zostanie określony, wartość musi być TSql.

[  **\@command_source =** ] "command_source"  
Typ lokalizacji przechowywania polecenia. command_source jest nvarchar(50) z wartością domyślną w tekście, co oznacza, że wartość @command_source parametr jest tekst dosłowny polecenia.

Jeśli zostanie określony, wartość musi być wbudowany.

[  **\@polecenia =** ] 'command'  
Polecenia musi być prawidłową skryptu T-SQL, a następnie jest wykonywany przez ten etap zadania. polecenie jest nvarchar(max) z domyślną wartością NULL.

[  **\@credential_name =** ] "credential_name"  
Nazwa bazy danych o określonym zakresie poświadczeń przechowywanych w tej bazie danych kontroli zadania, który jest używany do łączenia do każdego z docelowymi bazami danych w grupie docelowej po wykonaniu tego kroku. credential_name jest nvarchar(128).

[  **\@target_group_name =** ] "target nazwa_grupy"  
Nazwa grupy docelowej, która zawiera docelowych baz danych, które krok zadania zostaną wykonane na. target_group_name jest nvarchar(128).

[  **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Opóźnienie przed pierwszym ponowieniem próby próby, jeśli krok zadania zakończy się niepowodzeniem przy próbie wykonania początkowej. initial_retry_interval_seconds to typ int, za pomocą wartość domyślną 1.

[  **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami będzie powiększać większa niż ta wartość, jej wynosi tej wartości zamiast tego. maximum_retry_interval_seconds to typ int, przy użyciu wartości domyślnej 120.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Mnożnik do zastosowania do opóźnienia ponawiania, jeśli wiele zadań krok wykonywania próbuje zakończyć się niepowodzeniem. Na przykład jeśli pierwsze ponowienie miał opóźnienie 5-sekundowego mnożnik wycofywania jest w wersji 2.0, następnie drugi ponawiania prób będzie mieć opóźnienie 10 sekund i trzeci ponawiania prób będzie mieć opóźnienie wynoszącego 20 sekund. retry_interval_backoff_multiplier jest prawdziwe, wartością domyślną w wersji 2.0.

[  **\@retry_attempts =** ] retry_attempts  
Liczba ponownych prób wykonania, jeśli początkowa próba nie powiedzie się. Na przykład jeśli wartość retry_attempts jest 10, nastąpi 1 początkowa próba i 10 ponownymi próbami, co daje w sumie 11 prób. W przypadku niepowodzenia końcowego ponowienia próby wykonania zadania zostanie zakończona z cyklem życia nie powiodło się. retry_attempts to typ int, za pomocą wartość domyślną równą 10.

[  **\@step_timeout_seconds =** ] step_timeout_seconds  
Maksymalna ilość czasu dozwolony krok do wykonania. Tym razem po przekroczeniu, wykonanie zadania zostanie zakończona z cyklem życia przekroczenie limitu czasu. step_timeout_seconds to typ int, wartością domyślną 43,200 sekund (12 godzin).

[  **\@output_type =** ] "output_type"  
W przeciwnym razie wartość null, typu miejsca docelowego, który ustawiony wynik pierwszego polecenia są zapisywane. Aby przywrócić wartość output_type o wartości NULL, ustaw wartość tego parametru na "(pusty ciąg znaków). output_type jest nvarchar(50) z domyślną wartością NULL.

Jeśli zostanie określony, wartość musi być baza danych SQL.

[  **\@output_credential_name =** ] "output_credential_name"  
Jeśli nie ma wartość null, nazwa bazy danych obejmuje poświadczenie, które służy do nawiązania połączenia z bazą danych wyjściowych docelowego. Należy określić w przypadku output_type jest równe baza danych SQL. Aby przywrócić wartość output_credential_name o wartości NULL, ustaw wartość tego parametru na "(pusty ciąg znaków). output_credential_name jest nvarchar(128) z wartością domyślną wartością null.

[  **\@output_server_name =** ] "output_server_name"  
W przeciwnym razie wartość null, w pełni kwalifikowana nazwa DNS serwera, który zawiera dane wyjściowe docelowej bazy danych. Należy określić w przypadku output_type jest równe baza danych SQL. Aby przywrócić wartość output_server_name o wartości NULL, ustaw wartość tego parametru na "(pusty ciąg znaków). output_server_name jest nvarchar(256) z domyślną wartością NULL.

[  **\@output_database_name =** ] "output_database_name"  
W przeciwnym razie wartość null, nazwa bazy danych, który zawiera dane wyjściowe tabeli docelowej. Należy określić w przypadku output_type jest równe baza danych SQL. Aby przywrócić wartość output_database_name o wartości NULL, ustaw wartość tego parametru na "(pusty ciąg znaków). output_database_name jest nvarchar(128) z domyślną wartością NULL.

[  **\@output_schema_name =** ] "output_schema_name"  
W przeciwnym razie wartość null, nazwa schematu SQL, który zawiera dane wyjściowe tabeli docelowej. Jeśli output_type równa baza danych SQL, wartość domyślna to dbo. Aby przywrócić wartość output_schema_name o wartości NULL, ustaw wartość tego parametru na "(pusty ciąg znaków). output_schema_name jest nvarchar(128).

[  **\@output_table_name =** ] "output_table_name"  
W przeciwnym razie wartość null, nazwa tabeli, który ustawiony wynik pierwszego polecenia zostaną zapisane. Jeśli tabela już nie istnieje, zostanie on utworzony zależności od schematu zwraca zestaw wyników. Należy określić w przypadku output_type jest równe baza danych SQL. Aby przywrócić wartość output_server_name o wartości NULL, ustaw wartość tego parametru na "(pusty ciąg znaków). output_table_name jest nvarchar(128) z wartością domyślną wartością null.

[  **\@job_version =** ] job_version danych wyjściowych  
Parametr wyjściowy, który zostanie przypisany numer wersji zadania. job_version to int.

[  **\@max_parallelism =** ] max_parallelism danych wyjściowych  
Maksymalny poziom równoległości dla puli elastycznej. Zestaw, a następnie etap zadania zostaną ograniczone do uruchomienia tylko na maksymalnego wiele baz danych w jednej puli elastycznej. Dotyczy to każdej puli elastycznej, albo bezpośrednio znajduje się w grupie docelowej lub znajduje się wewnątrz serwera, który znajduje się w grupie docelowej. Aby zresetować wartość max_parallelism na wartość null, wartość tego parametru na wartość -1. max_parallelism to int.


#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Nie ma wpływu na dowolnym wykonań w toku zadania. Po pomyślnym zakończeniu sp_update_jobstep, zadanie drukowania numer wersji jest zwiększany. Następnym razem, zadanie jest wykonywane, nowa wersja będzie używany.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie grupy mogą być edytowane atrybutów zadania, które są własnością innych użytkowników tej procedury składowanej




### <a name="spdeletejobstep"></a>sp_delete_jobstep

Usuwa krok zadania z zadania.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumenty
[  **\@parametr job_name =** ] "parametr job_name"  
Nazwa zadania, z którego ten krok zostanie usunięty. Parametr job_name jest nvarchar(128) bez wartości domyślnej.

[  **\@step_id =** ] step_id  
Numer identyfikacyjny dla etapu zadania do usunięcia. Należy określić step_id albo step_name. step_id to int.

[  **\@step_name =** ] "step_name"  
Nazwa kroku do usunięcia. Należy określić step_id albo step_name. step_name jest nvarchar(128).

[  **\@job_version =** ] job_version danych wyjściowych  
Parametr wyjściowy, który zostanie przypisany numer wersji zadania. job_version to int.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Nie ma wpływu na dowolnym wykonań w toku zadania. Po pomyślnym zakończeniu sp_update_jobstep, zadanie drukowania numer wersji jest zwiększany. Następnym razem, zadanie jest wykonywane, nowa wersja będzie używany.

Pozostałe kroki zadania zostaną automatycznie oznaczenie do wypełnienia lukę po kroku usuniętego zadania.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.






### <a name="spstartjob"></a>sp_start_job

Rozpoczyna się wykonywanie zadania.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumenty
[  **\@parametr job_name =** ] "parametr job_name"  
Nazwa zadania, z którego ten krok zostanie usunięty. Parametr job_name jest nvarchar(128) bez wartości domyślnej.

[  **\@job_execution_id =** ] job_execution_id danych wyjściowych  
Dane wyjściowe parametru, który zostanie przypisany identyfikator wykonywania zadania. job_version jest uniqueidentifier.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Brak.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.

### <a name="spstopjob"></a>sp_stop_job

Zatrzymuje wykonywanie zadania.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumenty
[  **\@job_execution_id =** ] job_execution_id  
Numer identyfikacyjny wykonywania zadań, aby zatrzymać. job_execution_id jest uniqueidentifier z domyślną wartością NULL.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Brak.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.


### <a name="spaddtargetgroup"></a>sp_add_target_group

Dodaje grupy docelowej.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumenty
[  **\@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej do utworzenia. target_group_name jest nvarchar(128) bez wartości domyślnej.

[  **\@target_group_id =** ] target_group_id dane wyjściowe w docelowej grupie numeru identyfikacyjnego przypisanych do zadania, jeśli pomyślnie utworzono. target_group_id jest zmienną typu uniqueidentifier. dane wyjściowe z domyślną wartością NULL.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Grupy docelowe umożliwiają łatwe pod kątem zadanie w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

Usuwa grupy docelowej.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumenty
[  **\@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej, można usunąć. target_group_name jest nvarchar(128) bez wartości domyślnej.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Brak.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Dodaje do grupy docelowej bazy danych lub grupy baz danych.

#### <a name="syntax"></a>Składnia

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumenty
[  **\@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej, do którego zostanie dodany element członkowski. target_group_name jest nvarchar(128) bez wartości domyślnej.

[  **\@membership_type =** ] "membership_type"  
Określa, jeśli docelowy element członkowski grupy zostaną dołączone lub wykluczone. target_group_name jest nvarchar(128) z domyślną "Include". Prawidłowe wartości dla target_group_name to "Include" lub "Exclude".

[  **\@target_type — =** ] "target_type —"  
Typ docelowej bazy danych lub kolekcji baz danych w tym wszystkich baz danych na serwerze, wszystkie bazy danych w puli elastycznej, wszystkie bazy danych w mapowania fragmentów w postaci lub poszczególnych baz danych. target_type — jest nvarchar(128) bez wartości domyślnej. Prawidłowe wartości dla target_type — są 'SqlServer', "SqlElasticPool", "Baza danych SQL" lub "SqlShardMap". 

[  **\@refresh_credential_name =** ] "refresh_credential_name"  
Nazwa serwera bazy danych SQL. refresh_credential_name jest nvarchar(128) bez wartości domyślnej.

[  **\@nazwa_serwera =** ] "nazwa_serwera"  
Nazwa serwera bazy danych SQL, która powinna być dodana do określoną grupę docelową. nazwa_serwera powinna być określona, gdy target_type — jest "SqlServer". nazwa_serwera jest nvarchar(128) bez wartości domyślnej.

[  **\@database_name =** ] "database_name"  
Nazwa bazy danych, która powinna być dodana do określoną grupę docelową. należy określić database_name po target_type — "Baza danych SQL". database_name jest nvarchar(128) bez wartości domyślnej.

[  **\@elastic_pool_name =** ] "elastic_pool_name"  
Nazwa puli elastycznej, która powinna być dodana do określoną grupę docelową. należy określić elastic_pool_name po target_type — "SqlElasticPool". elastic_pool_name jest nvarchar(128) bez wartości domyślnej.

[  **\@shard_map_name =** ] "shard_map_name"  
Nazwa puli mapy fragmentów, które powinny zostać dodane do określoną grupę docelową. należy określić elastic_pool_name po target_type — "SqlSqlShardMap". shard_map_name jest nvarchar(128) bez wartości domyślnej.

[  **\@target_id =** ] target_group_id danych wyjściowych  
Numer identyfikacyjny docelowego, które są przypisane do członka grupy docelowej, jeśli utworzono dodane do grupy docelowej. target_id jest zmienną typu uniqueidentifier. dane wyjściowe z domyślną wartością NULL.
Wartości zwracane kod 0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Dla wszystkich pojedynczych baz danych w ramach serwera usługi SQL Database lub w puli elastycznej wykonuje zadania w czasie wykonywania, gdy serwer bazy danych SQL lub puli elastycznej jest uwzględniona w grupie docelowej.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
Poniższy przykład dodaje wszystkie bazy danych w Londynie i NewYork serwerów do grupy, informacje o kliencie utrzymywania serwerów. Musisz połączyć się określona podczas tworzenia agenta zadań w tym przypadku ElasticJobs baza danych zadań.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="spdeletetargetgroupmember"></a>sp_delete_target_group_member

Usuwa docelowy element członkowski grupy z grupy docelowej.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Argumenty [ @target_group_name =] "target_group_name"  
Nazwa grupy docelowej, z którego chcesz usunąć członka grupy docelowej. target_group_name jest nvarchar(128) bez wartości domyślnej.

[ @target_id =] target_id  
 Docelowy numer identyfikacyjny przypisane do członka grupy docelowej, do usunięcia. target_id jest uniqueidentifier, z domyślną wartością NULL.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Grupy docelowe umożliwiają łatwe pod kątem zadanie w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
Poniższy przykład usuwa serwer Londyn z grupy, informacje o kliencie utrzymywania serwerów. Musisz połączyć się określona podczas tworzenia agenta zadań w tym przypadku ElasticJobs baza danych zadań.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sppurgejobhistory"></a>sp_purge_jobhistory

Usuwa rekordy historii zadania.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumenty
[  **\@parametr job_name =** ] "parametr job_name"  
Nazwa zadania, dla której chcesz usunąć rekordy historii. Parametr job_name jest nvarchar(128) z domyślną wartością NULL. Należy określić wartość job_id lub parametr job_name, ale nie może być jednocześnie określone.

[  **\@wartość job_id =** ] wartość job_id  
 Numer identyfikacyjny zadania zadania dla rekordów do usunięcia. wartość job_id jest uniqueidentifier z domyślną wartością NULL. Należy określić wartość job_id lub parametr job_name, ale nie może być jednocześnie określone.

[  **\@oldest_date =** ] oldest_date  
 Najstarszy rekord do przechowania w historii. oldest_date jest DATETIME2, domyślną wartością NULL. W przypadku oldest_date sp_purge_jobhistory powoduje usunięcie tylko rekordy, które są starsze niż określona wartość.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (Powodzenie) lub 1 (niepowodzenie) uwagi docelowych grup umożliwiają łatwe pod kątem zadanie w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Mogą ograniczyć użytkownikowi tylko można monitorować zadania, można przyznać użytkownikowi należeć następującej roli bazy danych w bazie danych agenta zadań, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról zobacz sekcję uprawnienia, w tym dokumencie. Tylko członkowie sysadmin służy tę procedurę składowaną, aby edytować atrybuty, zadań, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
Poniższy przykład dodaje wszystkie bazy danych w Londynie i NewYork serwerów do grupy, informacje o kliencie utrzymywania serwerów. Musisz połączyć się określona podczas tworzenia agenta zadań w tym przypadku ElasticJobs baza danych zadań.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Widoki zadania

Następujące widoki są dostępne w [bazy danych zadania](sql-database-job-automation-overview.md#job-database).


|Widok  |Opis  |
|---------|---------|
|[jobs_executions](#jobs_executions-view)     |  Pokazano zadań historii wykonywania.      |
|[Zadania](#jobs-view)     |   Pokazuje wszystkie zadania.      |
|[job_versions](#job_versions-view)     |   Przedstawia wszystkie wersje zadania.      |
|[etapów zadania](#jobsteps-view)     |     Przedstawia wszystkie kroki opisane w bieżącej wersji każdego zadania.    |
|[jobstep_versions](#jobstep_versions-view)     |     Przedstawia wszystkie kroki opisane we wszystkich wersjach każdego zadania.    |
|[target_groups](#target_groups-view)     |      Wyświetla wszystkie grupy docelowej.   |
|[target_group_members](#target_groups_members-view)     |   Przedstawia wszystkie elementy członkowskie wszystkich grup docelowych.      |


### <a name="jobsexecutions-view"></a>Widok jobs_executions

[zadania]. [jobs_executions]

Pokazano zadań historii wykonywania.


|Nazwa kolumny|   Typ danych   |Opis|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  Unikatowy identyfikator wystąpienia wykonywania zadania.
|**Parametr job_name**   |nvarchar(128)  |Nazwa zadania.
|**job_id** |uniqueidentifier|  Unikatowy identyfikator zadania.
|**job_version**    |int    |Wersja zadania (zaktualizowane automatycznie każdorazowo, gdy zadanie zostanie zmodyfikowane).
|**step_id**    |int|   Identyfikator unikatowy (dla tego zadania) dla etapu. Wartość NULL oznacza, że jest to wykonywania zadania nadrzędnego.
|**is_active**| bit |Wskazuje, czy informacje są aktywne lub nieaktywne. wartość 1 oznacza aktywne zadania, a wartość 0 wskazuje nieaktywne.
|**cykl życia**| nvarchar(50)|Wartość wskazująca, stan zadania: "Utworzony", "W toku", "Nie powiodło się", "Powodzenie", "Pominięto", "SucceededWithSkipped"|
|**create_time**|   datetime2(7)|   Data i godzina utworzenia zadania.
|**godzina_rozpoczęcia** |datetime2(7)|  Data i godzina zadania rozpoczął wykonywanie sekwencji. Wartość NULL, jeśli zadanie nie zostało jeszcze wykonane.
|**end_time**|  datetime2(7)    |Data i godzina zadanie zostało zakończone, wykonanie. Wartość NULL, jeśli zadanie nie zostało jeszcze wykonane lub nie zostało jeszcze ukończone.
|**current_attempts**   |int    |Liczba prób ponownego wykonania kroku. Zadanie nadrzędne będzie mieć wartość 0, liczba wykonań zadań podrzędnych będzie mieć wartość 1 lub większa oparte na zasad wykonywania programu.
|**current_attempt_start_time** |datetime2(7)|  Data i godzina zadania rozpoczął wykonywanie sekwencji. Wartość NULL oznacza, że jest to wykonywania zadania nadrzędnego.
|**last_message**   |nvarchar(max)| Komunikat historii zadania lub krok. 
|**target_type**|   nvarchar(128)   |Typ docelowej bazy danych lub kolekcji baz danych, uwzględniający wszystkie bazy danych w serwera i wszystkich baz danych w puli elastycznej bazy danych. Prawidłowe wartości dla target_type — to 'SqlServer', "SqlElasticPool" lub "Baza danych SQL". Wartość NULL oznacza, że jest to wykonywania zadania nadrzędnego.
|**target_id**  |uniqueidentifier|  Unikatowy identyfikator elementu członkowskiego grupy docelowej.  Wartość NULL oznacza, że jest to wykonywania zadania nadrzędnego.
|**target_group_name**  |nvarchar(128)  |Nazwa grupy docelowej. Wartość NULL oznacza, że jest to wykonywania zadania nadrzędnego.
|**target_server_name**|    nvarchar(256)|  Nazwa serwera bazy danych SQL, znajdujących się w grupie docelowej. Tylko należy określić, czy target_type — "SqlServer". Wartość NULL oznacza, że jest to wykonywania zadania nadrzędnego.
|**target_database_name**   |nvarchar(128)| Nazwa bazy danych znajdujących się w grupie docelowej. Określona, tylko wtedy, gdy target_type — jest "Baza danych SQL". Wartość NULL oznacza, że jest to wykonywania zadania nadrzędnego.


### <a name="jobs-view"></a>w widoku zadań

[zadania]. [zadań]

Pokazuje wszystkie zadania.

|Nazwa kolumny|   Typ danych|  Opis|
|------|------|-------|
|**Parametr job_name**|  nvarchar(128)   |Nazwa zadania.|
|**job_id**|    uniqueidentifier    |Unikatowy identyfikator zadania.|
|**job_version**    |int    |Wersja zadania (zaktualizowane automatycznie każdorazowo, gdy zadanie zostanie zmodyfikowane).|
|**description**    |nvarchar(512)| Opis zadania. bit włączone wskazuje, czy zadanie jest włączone. wartość 1 oznacza włączone zadania, a wartość 0 wskazuje wyłączone zadania.|
|**schedule_interval_type** |nvarchar(50)   |Wartość wskazująca, kiedy zadanie jest wykonywana: "Raz", "Min", "Godziny", "dni", "Tygodni", "Miesiące"
|**schedule_interval_count**|   int|    Liczba okresów schedule_interval_type się komunikować między każdego wykonania zadania.|
|**schedule_start_time**    |datetime2(7)|  Data i godzina, których ostatni Rozpoczęto wykonywanie zadania.|
|**schedule_end_time**| datetime2(7)|   Data i godzina, których ostatniego wykonania ukończone zadania.|


### <a name="jobversions-view"></a>Widok job_versions

[zadania]. [job_versions]

Przedstawia wszystkie wersje zadania.

|Nazwa kolumny|   Typ danych|  Opis|
|------|------|-------|
|**Parametr job_name**|  nvarchar(128)   |Nazwa zadania.|
|**job_id**|    uniqueidentifier    |Unikatowy identyfikator zadania.|
|**job_version**    |int    |Wersja zadania (zaktualizowane automatycznie każdorazowo, gdy zadanie zostanie zmodyfikowane).|


### <a name="jobsteps-view"></a>Widok etapów zadania

[zadania]. [etapów zadania]

Przedstawia wszystkie kroki opisane w bieżącej wersji każdego zadania.

|Nazwa kolumny    |Typ danych| Opis|
|------|------|-------|
|**Parametr job_name**   |nvarchar(128)| Nazwa zadania.|
|**job_id** |uniqueidentifier   |Unikatowy identyfikator zadania.|
|**job_version**|   int|    Wersja zadania (zaktualizowane automatycznie każdorazowo, gdy zadanie zostanie zmodyfikowane).|
|**step_id**    |int    |Identyfikator unikatowy (dla tego zadania) dla etapu.|
|**step_name**  |nvarchar(128)  |(Dla tego zadania) unikatowa wartość kroku.|
|**command_type**   |nvarchar(50)   |Typ polecenia do wykonania w kroku zadania. Dla wersji 1, wartość musi być równa się i wartość domyślna to "TSql".|
|**command_source** |nvarchar(50)|  Lokalizacja polecenia. Dla wersji 1 "Inline" jest ustawieniem domyślnym i akceptowany tylko wartości.|
|**Polecenie**|   nvarchar(max)|  Polecenia, który ma być wykonane przez zadania elastyczne za pośrednictwem command_type.|
|**credential_name**|   nvarchar(128)   |Nazwa poświadczeń o zakresie bazy danych używane do wykonywania zadania.|
|**target_group_name**| nvarchar(128)   |Nazwa grupy docelowej.|
|**target_group_id**|   uniqueidentifier|   Unikatowy identyfikator grupy docelowej.|
|**initial_retry_interval_seconds**|    int |Opóźnienie przed pierwszym ponowieniem próby. Wartość domyślna to 1.|
|**maximum_retry_interval_seconds** |int|   Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami będzie powiększać większa niż ta wartość, jej wynosi tej wartości zamiast tego. Wartość domyślna to 120.|
|**retry_interval_backoff_multiplier**  |real|  Mnożnik do zastosowania do opóźnienia ponawiania, jeśli wiele zadań krok wykonywania próbuje zakończyć się niepowodzeniem. Wartością domyślną jest w wersji 2.0.|
|**retry_attempts** |int|   Liczba ponownych prób do użycia, jeśli ta czynność zakończy się niepowodzeniem. Domyślna wartość wynosząca 10, co oznacza nie ponownych prób.|
|**step_timeout_seconds**   |int|   Ilość czasu w minutach między ponownymi próbami. Wartość domyślna to 0, co oznacza, 0-minutowych interwałach.|
|**output_type**    |nvarchar(11)|  Lokalizacja polecenia. W bieżącej wersji zapoznawczej "Inline" jest ustawieniem domyślnym i akceptowany tylko wartości.|
|**output_credential_name**|    nvarchar(128)   |Nazwa poświadczenia ma być używany do łączenia się z serwerem docelowym do przechowywania wyników zestawu.|
|**output_subscription_id**|    uniqueidentifier|   Unikatowy identyfikator subskrypcji server\database docelowego dla wyników z wykonywania zapytania.|
|**output_resource_group_name** |nvarchar(128)| Nazwa grupy zasobów zawierającej serwer docelowy.|
|**output_server_name**|    nvarchar(256)   |Nazwa serwera docelowego dla zestawu wyników.|
|**output_database_name**   |nvarchar(128)| Nazwa docelowej bazy danych dla zestawu wyników.|
|**output_schema_name** |nvarchar(max)| Nazwa schematu docelowego. Wartość domyślna to dbo, jeśli nie zostanie określony.|
|**output_table_name**| nvarchar(max)|  Nazwa tabeli do przechowywania wyników z wyników zapytania. Tabela zostanie utworzona automatycznie w oparciu o schemacie wyniki, jeśli jeszcze nie istnieje. Schemat musi być zgodna ze schematem zestawu wyników.|
|**max_parallelism**|   int|    Maksymalna liczba baz danych w jednej puli elastycznej, który krok zadania jest uruchamiane w danym momencie. Wartość domyślna to NULL, co oznacza brak limitu. |


### <a name="jobstepversions-view"></a>Widok jobstep_versions

[zadania]. [jobstep_versions]

Przedstawia wszystkie kroki opisane we wszystkich wersjach każdego zadania. Schemat jest taka sama jak [etapów zadania](#jobsteps-view).

### <a name="targetgroups-view"></a>Widok target_groups

[zadania]. [target_groups]

Wyświetla listę wszystkich grup docelowych.

|Nazwa kolumny|Typ danych| Opis|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |Nazwa grupy docelowej kolekcji baz danych. 
|**target_group_id**    |uniqueidentifier   |Unikatowy identyfikator grupy docelowej.

### <a name="targetgroupsmembers-view"></a>Widok target_groups_members

[jobs].[target_groups_members]

Przedstawia wszystkie elementy członkowskie wszystkich grup docelowych.

|Nazwa kolumny|Typ danych| Opis|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|Nazwa grupy docelowej kolekcji baz danych. |
|**target_group_id**    |uniqueidentifier   |Unikatowy identyfikator grupy docelowej.|
|**membership_type**    |int|   Określa członka grupy docelowej jest włączone czy wyłączone w docelowej grupie. Prawidłowe wartości dla target_group_name to "Include" lub "Exclude".|
|**target_type**    |nvarchar(128)| Typ docelowej bazy danych lub kolekcji baz danych, uwzględniający wszystkie bazy danych w serwera i wszystkich baz danych w puli elastycznej bazy danych. Prawidłowe wartości dla target_type — są 'SqlServer', "SqlElasticPool", "Baza danych SQL" lub "SqlShardMap".|
|**target_id**  |uniqueidentifier|  Unikatowy identyfikator elementu członkowskiego grupy docelowej.|
|**refresh_credential_name**    |nvarchar(128)  |Nazwa bazy danych o określonym zakresie poświadczenia używane do łączenia z członka grupy docelowej.|
|**subscription_id**    |uniqueidentifier|  Unikatowy identyfikator subskrypcji.|
|**resource_group_name**    |nvarchar(128)| Nazwa grupy zasobów, w której znajduje się docelowy element członkowski grupy.|
|**nazwa_serwera**    |nvarchar(128)  |Nazwa serwera bazy danych SQL, znajdujących się w grupie docelowej. Tylko należy określić, czy target_type — "SqlServer". |
|**database_name**  |nvarchar(128)  |Nazwa bazy danych znajdujących się w grupie docelowej. Określona, tylko wtedy, gdy target_type — jest "Baza danych SQL".|
|**elastic_pool_name**  |nvarchar(128)| Nazwa puli elastycznej, znajdujących się w grupie docelowej. Określona, tylko wtedy, gdy target_type — jest "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| Nazwa mapy fragmentów znajdujących się w grupie docelowej. Określona, tylko wtedy, gdy target_type — jest "SqlShardMap".|


## <a name="resources"></a>Zasoby

 - ![Ikona łącza do tematu](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "ikona łącza do tematu") [Konwencji składni języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie zadań elastycznych i zarządzanie nimi za pomocą programu PowerShell](elastic-jobs-powershell.md)
- [Autoryzacja i uprawnienia programu SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
