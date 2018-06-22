---
title: Utwórz zadania i zarządzać nimi Azure SQL elastycznej bazy danych przy użyciu języka Transact-SQL (T-SQL) | Dokumentacja firmy Microsoft
description: Uruchom skrypty przez wiele baz danych z agentem zadanie elastycznej bazy danych przy użyciu języka Transact-SQL (T-SQL).
services: sql-database
author: jaredmoo
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 06/14/2018
ms.author: jaredmoo
ms.openlocfilehash: fb6e4ebd635d8afa8e679ee5bb0f5646f28f887b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313339"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Użyj języka Transact-SQL (T-SQL), aby tworzyć zadania i zarządzać nimi elastycznej bazy danych

Ten artykuł zawiera wiele przykładowymi scenariuszami, aby rozpocząć pracę z usługą zadania elastyczne za pomocą T-SQL.

W przykładach użyto [procedur składowanych](#job-stored-procedures) i [widoków](#job-views) dostępne w [ *bazy danych zadania*](elastic-jobs-overview.md#job-database).

Transact-SQL (T-SQL) służy do tworzenia, konfigurowania, wykonywania, zadania i zarządzać nimi. Tworzenie agenta zadania elastyczne nie jest obsługiwane w T-SQL, dlatego najpierw należy utworzyć *agent zadania elastycznej* korzystanie z portalu lub [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Tworzenie poświadczeń do wykonywania zadań

Poświadczenie jest używany do nawiązania połączenia z docelowymi bazami danych do wykonania skryptu. Poświadczenie musi mieć odpowiednie uprawnienia, w bazach danych, określonej przez grupy docelowej, aby pomyślnie wykonać skrypt. Podczas korzystania z serwera i/lub członka grupy docelowej puli, zdecydowanie zalecane jest można utworzyć głównego poświadczenia do użycia odświeżyć poświadczeń przed ekspansja serwera i/lub puli w czasie wykonywania zadania. Poświadczenia bazy danych jest tworzony w bazie danych agenta zadania. Tych samych poświadczeń musi być używane do *Utwórz dane logowania* i *utworzyć użytkownika posługującego się nazwą logowania, aby udzielić uprawnień do bazy danych logowania* na z docelowymi bazami danych.


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

Poniższy przykład przedstawia sposób wykonywania zadania dla wszystkich baz danych na serwerze.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:


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


## <a name="exclude-a-single-database"></a>Wyklucz pojedynczej bazy danych

Poniższy przykład przedstawia sposób wykonania zadania wszystkich baz danych na serwerze, z wyjątkiem bazy danych o nazwie *MappingDB*.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group = N'ServerGroup'
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

--Excude a database target member from the server target group
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

Poniższy przykład pokazuje, jak pod kątem wszystkich baz danych w jednej lub kilku pul elastycznych.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

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


## <a name="deploy-new-schema-to-many-databases"></a>Wdrażanie nowego schematu do wielu baz danych

Poniższy przykład przedstawia sposób wdrażania nowego schematu do wszystkich baz danych.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:


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

W wielu scenariuszach zbierania danych może być przydatny niektóre z tych zmiennych skryptowych ułatwiające po przetwarzaniu wyniki zadania.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Na przykład aby pogrupować wszystkie wyniki z tego samego razem wykonania zadania, należy użyć *$(job_execution_id)* jak pokazano w poniższym poleceniu:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorowanie wydajności bazy danych

Poniższy przykład tworzy nowe zadanie do zbierania danych wydajności z wieloma bazami danych.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenia:

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
@output_credential_name=’myjobcred’,
@output_server_name=’server1.database.windows.net',
@output_database_name=’<resultsdb>',
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
@output_server_name=’server1.database.windows.net',
@output_database_name=’resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Wyświetlanie definicji zadania

Poniższy przykład przedstawia sposób wyświetlania bieżącej definicji zadania.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Rozpocznij ad hoc wykonania zadania

Poniższy przykład pokazuje, jak od razu uruchomić zadanie.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

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

Poniższy przykład przedstawia sposób tworzenia harmonogramu zadań do wykonania w przyszłości.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitoruje stan wykonania zadania

Poniższy przykład przedstawia sposób wyświetlić szczegóły stanu wykonywania wszystkich zadań.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named ‘ResultsPoolJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named ‘ResultsPoolsJob’
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
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

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

Poniższy przykład przedstawia sposób usuwania historii zadań przed określonej daty.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Usuwanie zadania i jego historii zadań

Poniższy przykład pokazuje, jak usunąć zadania i wszystkich powiązanych historii zadań.  
Połączyć się z [ *bazy danych zadania* ](elastic-jobs-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Procedury składowane zadania

Poniższe procedury składowane są w [bazy danych zadania](elastic-jobs-overview.md#job-database).



|Procedura składowana  |Opis  |
|---------|---------|
|[sp_add_job](#spaddjob)     |     Dodaje nowe zadanie.    |
|[sp_update_job ](#spupdatejob)    |      Aktualizuje istniejące zadanie.   |
|[sp_delete_job](#spdeletejob)     |      Usuwa istniejące zadanie.   |
|[sp_add_jobstep](#spaddjobstep)    |    Dodaje krok do zadania.     |
|[sp_update_jobstep](#spupdatejobstep)     |     Aktualizuje etap zadania.    |
|[sp_delete_jobstep](#spdeletejobstep)     |     Usuwa etap zadania.    |
|[sp_start_job](#spstartjob)    |  Rozpoczyna wykonywanie zadania.       |
|[sp_stop_job](#spstopjob)     |     Zatrzymuje wykonywanie zadania.   |
|[sp_add_target_group](#spaddtargetgroup)    |     Dodaje grupę docelową.    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    Usuwa grupę docelową.     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    Dodaje bazy danych lub grupę baz danych do grupy docelowej.     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     Usuwa członka grupy docelowej z grupy docelowej.    |
|[sp_purge_jobhistory ](#sppurgejobhistory)    |    Usuwa rekordy historii zadania.     |





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

[  **@job_name =** ] "job_name"  
Nazwa zadania. Nazwa musi być unikatowa i nie może zawierać znaku procentu (%). job_name jest nvarchar(128) bez wartości domyślnej.

[  **@description =** ] "opis"  
Opis zadania. Opis jest nvarchar(512) z domyślną wartością NULL. W przypadku pominięcia opis pusty ciąg jest używany.

[  **@enabled =** ] włączone  
Określa, czy włączono harmonogram zadań. Włączone jest bit z domyślną 0 (wyłączone). Jeśli jest to 0, zadanie nie jest włączone i nie jest uruchamiane zgodnie ze swoim harmonogramem; jednak go mogą być uruchamiane ręcznie. Jeśli 1, zadania, którego będzie uruchamiany zgodnie ze swoim harmonogramem i może być także uruchamiane ręcznie.

[  **@schedule_interval_type =**] schedule_interval_type  
Wartość wskazuje, kiedy zadanie ma zostać wykonana. schedule_interval_type jest nvarchar(50) z domyślną raz i może być jedną z następujących wartości:
- Raz,
- "Minutes",
- "Hours",
- "Dni"
- "Weeks",
- "Miesięcy"

[  **@schedule_interval_count =** ] schedule_interval_count  
Liczba okresów schedule_interval_count pojawiają się między każdym wykonywanie zadania. schedule_interval_count jest int z domyślną 1. Wartość musi być większa lub równa 1.

[  **@schedule_start_time =** ] schedule_start_time  
Data na zadanie (job) można rozpocząć wykonywania. schedule_start_time jest DATETIME2, przy użyciu domyślnego 00:00:00.0000000 0001-01-01.

[  **@schedule_end_time =** ] schedule_end_time  
Data w zadaniu, które można zatrzymać wykonywania. schedule_end_time jest DATETIME2, przy użyciu domyślnego 9999-12-31 11:59:59.0000000. 

[  **@job_id =** ] wartość job_id danych wyjściowych  
Numer identyfikacyjny zadania przypisane do zadania, jeśli została utworzona pomyślnie. wartość job_id jest wyjście zmiennej typu uniqueidentifier.

#### <a name="return-code-values"></a>Wartości kodów powrotnych

0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
sp_add_job musi zostać uruchomiona z bazy danych agenta zadania, które zostały określone podczas tworzenia zadania agenta.
Po wykonaniu sp_add_job Aby dodać zadanie sp_add_jobstep można dodać kroki, które wykonują działania zadania. Numer wersji początkowej zadanie jest 0, co jest zwiększany 1 po dodaniu pierwszym krokiem.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:

- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.

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
[  **@job_name =** ] "job_name"  
Nazwa zadania aktualizacji. job_name jest nvarchar(128).

[  **@new_name =** ] "nowa_nazwa"  
Nowa nazwa zadania. nowa_nazwa jest nvarchar(128).

[  **@description =** ] "opis"  
Opis zadania. Opis jest nvarchar(512).

[  **@enabled =** ] włączone  
Określa, czy harmonogram zadań jest włączone (1) lub wyłączona (0). Włączone jest bit.

[  **@schedule_interval_type=** ] schedule_interval_type  
Wartość wskazuje, kiedy zadanie ma zostać wykonana. schedule_interval_type jest nvarchar(50) i może być jedną z następujących wartości:

- Raz,
- "Minutes",
- "Hours",
- "Dni"
- "Weeks",
- "Miesięcy"

[  **@schedule_interval_count=** ] schedule_interval_count  
Liczba okresów schedule_interval_count pojawiają się między każdym wykonywanie zadania. schedule_interval_count jest int z domyślną 1. Wartość musi być większa lub równa 1.

[  **@schedule_start_time=** ] schedule_start_time  
Data na zadanie (job) można rozpocząć wykonywania. schedule_start_time jest DATETIME2, przy użyciu domyślnego 00:00:00.0000000 0001-01-01.

[  **@schedule_end_time=** ] schedule_end_time  
Data w zadaniu, które można zatrzymać wykonywania. schedule_end_time jest DATETIME2, przy użyciu domyślnego 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Po wykonaniu sp_add_job Aby dodać zadanie sp_add_jobstep można dodać kroki, które wykonują działania zadania. Numer wersji początkowej zadanie jest 0, co jest zwiększany 1 po dodaniu pierwszym krokiem.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.



### <a name="spdeletejob"></a>sp_delete_job

Usuwa istniejące zadanie.

#### <a name="syntax"></a>Składnia

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] "job_name"  
Nazwa zadania, które mają zostać usunięte. job_name jest nvarchar(128).

[  **@force =** ] wymusić  
Określa, czy usunąć, jeśli zadanie ma wszelkie wykonaniami w toku i Anuluj wszystkie trwające wykonaniami (1) lub zakończyć się niepowodzeniem, jeśli są wszystkie wykonania zadania w toku (0). bit jest Force.

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Historia zadań zostanie usunięta automatycznie, gdy zadanie zostanie usunięta.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.



### <a name="spaddjobstep"></a>sp_add_jobstep

Dodaje krok do zadania.

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

[  **@job_name =** ] "job_name"  
Nazwa zadania, do którego należy dodać krok. job_name jest nvarchar(128).

[  **@step_id =** ] step_id  
Numer identyfikacyjny sekwencji dla etapu zadania. Numery identyfikacyjne kroku są liczone od 1 i zwiększyć bez przerwy. Jeśli krok istniejących już ten identyfikator, krok i wszystkie czynności że swój identyfikator użytkownika zwiększana tak, aby ten nowy krok można wstawiać do sekwencji. Jeśli nie zostanie określony, step_id zostanie automatycznie przypisany do ostatniego w sekwencji kroki. step_id to int.

[  **@step_name =** ] step_name  
Nazwa kroku. Należy określić, z wyjątkiem pierwszym krokiem zadania, które ma domyślną nazwę etapu "zadania" (dla wygody). step_name jest nvarchar(128).

[  **@command_type =** ] "command_type"  
Typ polecenia, które jest wykonywane przez ten etap zadania. nvarchar(50) z wartością domyślną TSql, co oznacza, że wartość jest command_type @command_type parametr jest skryptu T-SQL.

Jeśli jest określony, wartość musi być TSql.

[  **@command_source =** ] "command_source"  
Typ lokalizacji przechowywania polecenia. nvarchar(50) z wartością domyślną w tekście, co oznacza, że wartość jest command_source @command_source parametr jest literałem tekst polecenia.

Jeśli jest określony, wartość musi być wbudowany.

[  **@command =** ] 'command'  
Polecenie musi być prawidłową skryptu T-SQL i następnie jest wykonywane przez ten etap zadania. polecenie jest nvarchar(max) z domyślną wartością NULL.

[  **@credential_name =** ] "credential_name"  
Nazwa bazy danych o zakresie poświadczeń przechowywanych w tym bazą danych sterowania zadaniami, które służy do łączenia do każdego z docelowymi bazami danych w grupie docelowej po wykonaniu tego kroku. credential_name jest nvarchar(128).

[  **@target_group_name =** ] "docelowy nazwa_grupy"  
Nazwa grupy docelowej, zawierający z docelowymi bazami danych, które ma zostać wykonany etap zadania. target_group_name jest nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Opóźnienie przed pierwszy ponawiania próby, jeśli krok zadanie zakończy się niepowodzeniem podczas próby wykonania początkowej. initial_retry_interval_seconds jest int, wartość domyślną 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami czy coraz większe niż ta wartość, go jest ograniczona do tej wartości zamiast tego. maximum_retry_interval_seconds jest int z wartością domyślną 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Mnożnik dotyczyć Opóźnienie ponownych prób, jeśli wykonanie kroku zadania wielu prób kończyć się niepowodzeniem. Na przykład jeśli pierwsza próba miał opóźnienie 5-sekundowego mnożnik wycofywania jest 2.0, następnie ponawiania drugi będzie miał opóźnieniem 10 sekund i trzeci ponawiania ma 20 sekund opóźnienia. retry_interval_backoff_multiplier jest prawdziwe z wartością domyślną 2.0.

[  **@retry_attempts =** ] retry_attempts  
Liczba ponownych prób wykonania, jeśli początkowa próba nie powiedzie się. Na przykład jeśli wartość retry_attempts jest 10, będą 1 początkowej próba i 10 ponowienia, podając łączną liczbę prób 11. W przypadku niepowodzenia końcowego ponowienia próby wykonywania zadania zostanie zakończona z cyklem życia nie powiodło się. retry_attempts jest int, wartość domyślną równą 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Maksymalna ilość czasu dozwolony dla kroku do wykonania. Po przekroczeniu tego czasu wykonywania zadania zostanie zakończona z cyklem życia upłynął limit czasu. step_timeout_seconds jest int z wartością domyślną 43,200 sekund (12 godzin).

[  **@output_type =** ] "output_type"  
W przeciwnym razie wartość null, typ docelowy, który zestaw wyników pierwszego polecenia są zapisywane. output_type jest nvarchar(50) z domyślną wartością NULL.

Jeśli jest określony, wartość musi być Bazadanychsql.

[  **@output_credential_name =** ] "output_credential_name"  
Jeśli nie ma wartość null, nazwa bazy danych poświadczeń o zakresie używanego do łączenia z bazą danych miejsce docelowe danych wyjściowych. Musi być określona, jeśli output_type jest równe Bazadanychsql. output_credential_name jest nvarchar(128) z wartością domyślną wartością null.

[  **@output_subscription_id =** ] "output_subscription_id"  
Musi mieć opis.

[  **@output_resource_group_name =** ] "output_resource_group_name"  
Musi mieć opis.

[  **@output_server_name =** ] "output_server_name"  
W przeciwnym razie wartość null, w pełni kwalifikowana nazwa DNS serwera, który zawiera bazę danych miejsce docelowe danych wyjściowych. Musi być określona, jeśli output_type jest równe Bazadanychsql. output_server_name jest nvarchar(256) z domyślną wartością NULL.

[  **@output_database_name =** ] "output_database_name"  
W przeciwnym razie wartość null, nazwę bazy danych, która zawiera dane wyjściowe tabeli docelowej. Musi być określona, jeśli output_type jest równe Bazadanychsql. output_database_name jest nvarchar(128) z domyślną wartością NULL.

[  **@output_schema_name =** ] "output_schema_name"  
W przeciwnym razie wartość null, nazwę schematu SQL, który zawiera dane wyjściowe tabeli docelowej. Jeśli output_type równa Bazadanychsql, wartością domyślną jest dbo. output_schema_name jest nvarchar(128).

[  **@output_table_name =** ] "output_table_name"  
W przeciwnym razie wartość null, nazwę tabeli, która zestaw wyników pierwszego polecenia zostaną zapisane. Jeśli tabela nie istnieje, zostanie on utworzony oparte na schemacie przekazujących zestawu wyników. Musi być określona, jeśli output_type jest równe Bazadanychsql. output_table_name jest nvarchar(128) z wartością domyślną wartością null.

[  **@job_version =** ] job_version danych wyjściowych  
Parametr wyjściowy, który zostanie przypisany numer wersji zadania. job_version to int.

[  **@max_parallelism =** ] max_parallelism danych wyjściowych  
Maksymalny poziom równoległości dla każdej puli elastycznej. Zestaw, a następnie etap zadania zostaną ograniczone do uruchomienia tylko na maksymalnego wielu baz danych dla każdej puli elastycznej. Dotyczy to każdej puli elastycznej, albo bezpośrednio znajduje się w grupie docelowej lub znajduje się wewnątrz serwera, który znajduje się w grupie docelowej. max_parallelism to int.


#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Jeśli sp_add_jobstep zakończy się powodzeniem, jest zwiększana zadania bieżący numer wersji. Gdy zadanie jest wykonywane, będą używane nowej wersji. Jeśli zadanie jest w trakcie wykonywania, wykonanie tego nie będzie zawierać nowy krok.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:  

- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.



### <a name="spupdatejobstep"></a>sp_update_jobstep

Aktualizuje etap zadania.

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
[  **@job_name =** ] "job_name"  
Nazwa zadania, do której należy dany krok. job_name jest nvarchar(128).

[  **@step_id =** ] step_id  
Numer identyfikacyjny etap zadania do zmodyfikowania. Należy określić step_id lub step_name. step_id to int.

[  **@step_name =** ] "step_name"  
Nazwa kroku do zmodyfikowania. Należy określić step_id lub step_name. step_name jest nvarchar(128).

[  **@new_id =** ] new_id  
Nowy numer identyfikacyjny sekwencji dla etapu zadania. Numery identyfikacyjne kroku są liczone od 1 i zwiększyć bez przerwy. Jeśli ponownie sortowane krok następnie inne czynności nie będzie automatycznie oznaczenia.

[  **@new_name =** ] "nowa_nazwa"  
Nowa nazwa kroku. nowa_nazwa jest nvarchar(128).

[  **@command_type =** ] "command_type"  
Typ polecenia, które jest wykonywane przez ten etap zadania. nvarchar(50) z wartością domyślną TSql, co oznacza, że wartość jest command_type @command_type parametr jest skryptu T-SQL.

Jeśli jest określony, wartość musi być TSql.

[  **@command_source =** ] "command_source"  
Typ lokalizacji przechowywania polecenia. nvarchar(50) z wartością domyślną w tekście, co oznacza, że wartość jest command_source @command_source parametr jest literałem tekst polecenia.

Jeśli jest określony, wartość musi być wbudowany.

[  **@command =** ] 'command'  
Polecenia musi być prawidłową skryptu T-SQL, a następnie jest wykonywane przez ten etap zadania. polecenie jest nvarchar(max) z domyślną wartością NULL.

[  **@credential_name =** ] "credential_name"  
Nazwa bazy danych o zakresie poświadczeń przechowywanych w tym bazą danych sterowania zadaniami, które służy do łączenia do każdego z docelowymi bazami danych w grupie docelowej po wykonaniu tego kroku. credential_name jest nvarchar(128).

[  **@target_group_name =** ] "docelowy nazwa_grupy"  
Nazwa grupy docelowej, zawierający z docelowymi bazami danych, które ma zostać wykonany etap zadania. target_group_name jest nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Opóźnienie przed pierwszy ponawiania próby, jeśli krok zadanie zakończy się niepowodzeniem podczas próby wykonania początkowej. initial_retry_interval_seconds jest int, wartość domyślną 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami czy coraz większe niż ta wartość, go jest ograniczona do tej wartości zamiast tego. maximum_retry_interval_seconds jest int z wartością domyślną 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Mnożnik dotyczyć Opóźnienie ponownych prób, jeśli wykonanie kroku zadania wielu prób kończyć się niepowodzeniem. Na przykład jeśli pierwsza próba miał opóźnienie 5-sekundowego mnożnik wycofywania jest 2.0, następnie ponawiania drugi będzie miał opóźnieniem 10 sekund i trzeci ponawiania ma 20 sekund opóźnienia. retry_interval_backoff_multiplier jest prawdziwe z wartością domyślną 2.0.

[  **@retry_attempts =** ] retry_attempts  
Liczba ponownych prób wykonania, jeśli początkowa próba nie powiedzie się. Na przykład jeśli wartość retry_attempts jest 10, będą 1 początkowej próba i 10 ponowienia, podając łączną liczbę prób 11. W przypadku niepowodzenia końcowego ponowienia próby wykonywania zadania zostanie zakończona z cyklem życia nie powiodło się. retry_attempts jest int, wartość domyślną równą 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Maksymalna ilość czasu dozwolony dla kroku do wykonania. Po przekroczeniu tego czasu wykonywania zadania zostanie zakończona z cyklem życia upłynął limit czasu. step_timeout_seconds jest int z wartością domyślną 43,200 sekund (12 godzin).

[  **@output_type =** ] "output_type"  
W przeciwnym razie wartość null, typ docelowy, który zestaw wyników pierwszego polecenia są zapisywane. Aby przywrócić wartość output_type wartość NULL, ustaw wartość tego parametru "(ciąg pusty). output_type jest nvarchar(50) z domyślną wartością NULL.

Jeśli jest określony, wartość musi być Bazadanychsql.

[  **@output_credential_name =** ] "output_credential_name"  
Jeśli nie ma wartość null, nazwa bazy danych poświadczeń o zakresie używanego do łączenia z bazą danych miejsce docelowe danych wyjściowych. Musi być określona, jeśli output_type jest równe Bazadanychsql. Aby przywrócić wartość output_credential_name wartość NULL, ustaw wartość tego parametru "(ciąg pusty). output_credential_name jest nvarchar(128) z wartością domyślną wartością null.

[  **@output_server_name =** ] "output_server_name"  
W przeciwnym razie wartość null, w pełni kwalifikowana nazwa DNS serwera, który zawiera bazę danych miejsce docelowe danych wyjściowych. Musi być określona, jeśli output_type jest równe Bazadanychsql. Aby przywrócić wartość output_server_name wartość NULL, ustaw wartość tego parametru "(ciąg pusty). output_server_name jest nvarchar(256) z domyślną wartością NULL.

[  **@output_database_name =** ] "output_database_name"  
W przeciwnym razie wartość null, nazwę bazy danych, która zawiera dane wyjściowe tabeli docelowej. Musi być określona, jeśli output_type jest równe Bazadanychsql. Aby przywrócić wartość output_database_name wartość NULL, ustaw wartość tego parametru "(ciąg pusty). output_database_name jest nvarchar(128) z domyślną wartością NULL.

[  **@output_schema_name =** ] "output_schema_name"  
W przeciwnym razie wartość null, nazwę schematu SQL, który zawiera dane wyjściowe tabeli docelowej. Jeśli output_type równa Bazadanychsql, wartością domyślną jest dbo. Aby przywrócić wartość output_schema_name wartość NULL, ustaw wartość tego parametru "(ciąg pusty). output_schema_name jest nvarchar(128).

[  **@output_table_name =** ] "output_table_name"  
W przeciwnym razie wartość null, nazwę tabeli, która zestaw wyników pierwszego polecenia zostaną zapisane. Jeśli tabela nie istnieje, zostanie on utworzony oparte na schemacie przekazujących zestawu wyników. Musi być określona, jeśli output_type jest równe Bazadanychsql. Aby przywrócić wartość output_server_name wartość NULL, ustaw wartość tego parametru "(ciąg pusty). output_table_name jest nvarchar(128) z wartością domyślną wartością null.

[  **@job_version =** ] job_version danych wyjściowych  
Parametr wyjściowy, który zostanie przypisany numer wersji zadania. job_version to int.

[  **@max_parallelism =** ] max_parallelism danych wyjściowych  
Maksymalny poziom równoległości dla każdej puli elastycznej. Zestaw, a następnie etap zadania zostaną ograniczone do uruchomienia tylko na maksymalnego wielu baz danych dla każdej puli elastycznej. Dotyczy to każdej puli elastycznej, albo bezpośrednio znajduje się w grupie docelowej lub znajduje się wewnątrz serwera, który znajduje się w grupie docelowej. Aby zresetować wartość max_parallelism na wartość null, wartość tego parametru wartość -1. max_parallelism to int.


#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Nie będzie to miało wpływu na wykonaniami wszystkie trwające zadania. Jeśli sp_update_jobstep zakończy się powodzeniem, numer wersji zadania jest zwiększana. Gdy zadanie jest wykonywane, będą używane nowej wersji.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:

- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie sysadmin mogą wykorzystać tę procedurę składowaną atrybuty zadania, które są własnością innych użytkowników




### <a name="spdeletejobstep"></a>sp_delete_jobstep

Usuwa etap zadania z zadania.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] "job_name"  
Nazwa zadania, w którym kroku zostaną usunięte. job_name jest nvarchar(128) bez wartości domyślnej.

[  **@step_id =** ] step_id  
Numer identyfikacyjny dla etapu zadania mają zostać usunięte. Należy określić step_id lub step_name. step_id to int.

[  **@step_name =** ] "step_name"  
Nazwa kroku do usunięcia. Należy określić step_id lub step_name. step_name jest nvarchar(128).

[  **@job_version =** ] job_version danych wyjściowych  
Parametr wyjściowy, który zostanie przypisany numer wersji zadania. job_version to int.

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Nie będzie to miało wpływu na wykonaniami wszystkie trwające zadania. Jeśli sp_update_jobstep zakończy się powodzeniem, numer wersji zadania jest zwiększana. Gdy zadanie jest wykonywane, będą używane nowej wersji.

Pozostałe kroki zadania zostaną automatycznie numerowane do wypełnienia lukę po kroku usunięto zadanie.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.






### <a name="spstartjob"></a>sp_start_job

Rozpoczyna wykonywanie zadania.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] "job_name"  
Nazwa zadania, w którym kroku zostaną usunięte. job_name jest nvarchar(128) bez wartości domyślnej.

[  **@job_execution_id =** ] job_execution_id danych wyjściowych  
Dane wyjściowe parametru, który zostanie przypisany identyfikator wykonywania zadania. job_version jest unikatowy identyfikator.

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Brak.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.

### <a name="spstopjob"></a>sp_stop_job

Zatrzymuje wykonywanie zadania.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumenty
[  **@job_execution_id =** ] job_execution_id  
Numer identyfikacyjny wykonywania zadań, aby zatrzymać. job_execution_id jest uniqueidentifier z domyślną wartością NULL.

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Brak.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.


### <a name="spaddtargetgroup"></a>sp_add_target_group

Dodaje grupę docelową.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumenty
[  **@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej do utworzenia. target_group_name jest nvarchar(128) bez wartości domyślnej.

[  **@target_group_id =** ] target_group_id danych wyjściowych docelowej grupy numer identyfikacyjny przypisany do zadania, jeśli została utworzona pomyślnie. target_group_id jest zmienną typu uniqueidentifier, dane wyjściowe z domyślną wartością NULL.

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Grupy docelowe zapewnić łatwą metodę docelową zadanie w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

Usuwa grupę docelową.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumenty
[  **@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej, można usunąć. target_group_name jest nvarchar(128) bez wartości domyślnej.

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Brak.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Dodaje bazy danych lub grupę baz danych do grupy docelowej.

#### <a name="syntax"></a>Składnia

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] ‘membership_type’ ]   
        [ , [ @target_type = ] ‘target_type’ ]   
        [ , [ @refresh_credential_name = ] ‘refresh_credential_name’ ]   
        [ , [ @server_name = ] ‘server_name’ ]   
        [ , [ @database_name = ] ‘database_name’ ]   
        [ , [ @elastic_pool_name = ] ‘elastic_pool_name’ ]   
        [ , [ @shard_map_name = ] ‘shard_map_name’ ]   
        [ , [ @target_id = ] ‘target_id’ OUTPUT ]
```

#### <a name="arguments"></a>Argumenty
[  **@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej, do którego zostanie dodany element członkowski. target_group_name jest nvarchar(128) bez wartości domyślnej.

[  **@membership_type =** ] "membership_type"  
Określa, w przypadku uwzględnione lub wykluczone członka grupy docelowej. target_group_name jest nvarchar(128) z domyślną "Dołącz". Prawidłowe wartości target_group_name to "Include" lub "Wyklucz".

[  **@target_type =** ] "target_type"  
Typ docelowej bazy danych lub kolekcji baz danych w tym wszystkie bazy danych na serwerze, wszystkie bazy danych w puli elastycznej, wszystkie bazy danych na mapie niezależnego fragmentu lub poszczególne bazy danych. target_type jest nvarchar(128) bez wartości domyślnej. Prawidłowe wartości target_type to 'SqlServer', "SqlElasticPool", "Bazadanychsql" lub "SqlShardMap". 

[  **@refresh_credential_name =** ] "refresh_credential_name"  
Nazwa serwera logicznego. refresh_credential_name jest nvarchar(128) bez wartości domyślnej.

[  **@server_name =** ] "nazwa_serwera"  
Nazwa serwera logicznego, która powinna zostać dodana do określoną grupę docelową. nazwa_serwera powinna być określona, gdy target_type jest 'SqlServer'. nazwa_serwera jest nvarchar(128) bez wartości domyślnej.

[  **@database_name =** ] 'nazwa_bazy_danych'  
Nazwa bazy danych, które powinny zostać dodane do określoną grupę docelową. nazwa_bazy_danych należy określić podczas target_type jest "Bazadanychsql". nazwa_bazy_danych jest nvarchar(128) bez wartości domyślnej.

[  **@elastic_pool_name =** ] "elastic_pool_name"  
Nazwa puli elastycznej, które powinny zostać dodane do określoną grupę docelową. elastic_pool_name powinna być określona, gdy target_type jest "SqlElasticPool". elastic_pool_name jest nvarchar(128) bez wartości domyślnej.

[  **@shard_map_name =** ] "shard_map_name"  
Nazwa puli mapy niezależnego fragmentu, które powinny zostać dodane do określoną grupę docelową. elastic_pool_name powinna być określona, gdy target_type jest "SqlSqlShardMap". shard_map_name jest nvarchar(128) bez wartości domyślnej.

[  **@target_id =** ] target_group_id danych wyjściowych  
Numer identyfikacyjny docelowy przypisane do członka grupy docelowej, jeśli utworzono dodane do grupy docelowej. target_id jest zmienną typu uniqueidentifier, dane wyjściowe z domyślną wartością NULL.
Zwraca kod wartości 0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Dla wszystkich baz danych w ramach serwera wykonuje zadania lub elastycznej puli w czasie wykonywania, gdy serwer logiczny lub puli elastycznej jest uwzględniona w grupie docelowej.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
W następującym przykładzie dodano wszystkich baz danych w Londynie i NowyJork serwerów do grupy serwerów Obsługa klienta informacje. Należy nawiązać określona podczas tworzenia agent zadania w tym przypadku ElasticJobs baza danych zadania.

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

Usuwa członka grupy docelowej z grupy docelowej.

#### <a name="syntax"></a>Składnia


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] ‘target_id’]
```



Argumenty [ @target_group_name =] "target_group_name"  
Nazwa grupy docelowej, z którego chcesz usunąć element członkowski grupy docelowej. target_group_name jest nvarchar(128) bez wartości domyślnej.

[ @target_id =] target_id  
 Numer identyfikacyjny docelowy przypisany do elementu członkowskiego grupy docelowej do usunięcia. target_id jest uniqueidentifier z domyślną wartością NULL.

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Grupy docelowe zapewnić łatwą metodę docelową zadanie w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
Poniższy przykład umożliwia usunięcie serwera Londynie z grupy serwerów Obsługa klienta informacje. Należy nawiązać określona podczas tworzenia agent zadania w tym przypadku ElasticJobs baza danych zadania.

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
[  **@job_name =** ] "job_name"  
Nazwa zadania, dla której chcesz usunąć rekordy historii. job_name jest nvarchar(128) z domyślną wartością NULL. Należy określić wartość job_id lub job_name, ale nie może być jednocześnie określone.

[  **@job_id =** ] wartość job_id  
 Numer identyfikacyjny zadania zadania dla rekordy do usunięcia. wartość job_id jest uniqueidentifier z domyślną wartością NULL. Należy określić wartość job_id lub job_name, ale nie może być jednocześnie określone.

[  **@oldest_date =** ] oldest_date  
 Rekord najstarsze do zachowania w historii. oldest_date jest DATETIME2, z domyślną wartością NULL. W przypadku oldest_date sp_purge_jobhistory tylko usunięcie rekordów, które są starsze niż określona wartość.

#### <a name="return-code-values"></a>Wartości kodów powrotnych
0 (Powodzenie) lub 1 (niepowodzenie) uwagi docelowych grup umożliwiają łatwe pod kątem zadań w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ogranicz użytkownikowi dokładnie mieć możliwość monitorowania zadań, można przyznać użytkownikowi następujące roli bazy danych w bazie danych agenta zadania, które zostały określone podczas tworzenia zadania agenta:
- jobs_reader

Aby uzyskać więcej informacji o uprawnieniach tych ról zobacz sekcję uprawnień w tym dokumencie. Tylko członkowie grupy można użyć tej procedury składowanej atrybuty zadania, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
W następującym przykładzie dodano wszystkich baz danych w Londynie i NowyJork serwerów do grupy serwerów Obsługa klienta informacje. Należy nawiązać określona podczas tworzenia agent zadania w tym przypadku ElasticJobs baza danych zadania.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Widoki zadania

Dostępne są następujące widoki [bazy danych zadania](elastic-jobs-overview.md#job-database).


|Widok  |Opis  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  Pokazuje zadania historii wykonywania.      |
|[Zadania](#jobs-view)     |   Wyświetla wszystkie zadania.      |
|[job_versions](#jobversions-view)     |   Przedstawia wszystkie wersje zadania.      |
|[najpierw](#jobsteps-view)     |     Przedstawia wszystkie kroki w bieżącej wersji każdego zadania.    |
|[jobstep_versions](#jobstepversions-view)     |     Przedstawia wszystkie kroki we wszystkich wersjach każdego zadania.    |
|[target_groups](#targetgroups-view)     |      Wyświetla wszystkie grupy docelowej.   |
|[target_group_members](#targetgroups-view)     |   Zawiera wszystkie elementy członkowskie wszystkich grup docelowych.      |


### <a name="jobsexecutions-view"></a>Widok jobs_executions

[zadania]. [jobs_executions]

Pokazuje zadania historii wykonywania.


|Nazwa kolumny|   Typ danych   |Opis|
|---------|---------|---------|
|**job_execution_id**   |Unikatowy identyfikator|  Unikatowy identyfikator wystąpienia wykonywania zadania.
|**job_name**   |Nvarchar(128)  |Nazwa zadania.
|**wartość job_id** |Unikatowy identyfikator|  Unikatowy identyfikator zadania.
|**job_version**    |int    |Wersja zadania (automatycznie aktualizowane zawsze, gdy zadanie zostanie zmodyfikowana).
|**step_id**    |int|   Identyfikator unikatowy (dla tego zadania) kroku. Wartość NULL oznacza, że jest wykonanie zadania nadrzędnego.
|**is_active**| bitowe |Wskazuje, czy informacje są aktywne lub nieaktywne. wartość 1 oznacza aktywnych zadań, a wartość 0 wskazuje nieaktywne.
|**Cykl życia**| nvarchar(50)|Wartość wskazująca, stan zadania: "Utworzony", "W toku", "Nie powiodło się", "Powiodło się", "Pominięto", "SucceededWithSkipped"|
|**create_time**|   datetime2(7)|   Data i godzina utworzenia zadania.
|**godzina_rozpoczęcia** |datetime2(7)|  Data i godzina wykonania zadanie zostało uruchomione. Wartość NULL, jeśli zadanie nie zostało wykonane.
|**end_time**|  datetime2(7)    |Data i godzina zadanie zakończone wykonywania. Wartość NULL, jeśli zadanie nie zostało wykonane lub nie został jeszcze ukończył wykonywanie sekwencji.
|**current_attempts**   |int    |Liczba przypadków została podjęta ponowna próba wykonania tego kroku. Zadaniem nadrzędnym będzie równa 0, wykonania zadania podrzędne będzie mieć wartość 1 lub większą na podstawie wykonywania zasad.
|**current_attempt_start_time** |datetime2(7)|  Data i godzina wykonania zadanie zostało uruchomione. Wartość NULL oznacza, że jest wykonanie zadania nadrzędnego.
|**last_message**   |nvarchar(max)| Komunikat historię zadania lub kroku. 
|**target_type**|   Nvarchar(128)   |Typ docelowej bazy danych lub kolekcji baz danych w tym wszystkie bazy danych serwera, wszystkie bazy danych w puli elastycznej lub bazy danych. Prawidłowe wartości target_type to 'SqlServer', "SqlElasticPool" lub "Bazadanychsql". Wartość NULL oznacza, że jest wykonanie zadania nadrzędnego.
|**target_id**  |Unikatowy identyfikator|  Unikatowy identyfikator elementu członkowskiego grupy docelowej.  Wartość NULL oznacza, że jest wykonanie zadania nadrzędnego.
|**target_group_name**  |Nvarchar(128)  |Nazwa grupy docelowej. Wartość NULL oznacza, że jest wykonanie zadania nadrzędnego.
|**nazwa_serwera_docelowego**|    nvarchar(256)|  Nazwa serwera logicznego znajdujących się w grupie docelowej. Określona tylko, jeśli target_type jest 'SqlServer'. Wartość NULL oznacza, że jest wykonanie zadania nadrzędnego.
|**target_database_name**   |Nvarchar(128)| Nazwa bazy danych znajdujących się w grupie docelowej. Określona, tylko gdy target_type jest "Bazadanychsql". Wartość NULL oznacza, że jest wykonanie zadania nadrzędnego.


### <a name="jobs-view"></a>Zadania widoku

[zadania]. [zadań]

Wyświetla wszystkie zadania.

|Nazwa kolumny|   Typ danych|  Opis|
|------|------|-------|
|**job_name**|  Nvarchar(128)   |Nazwa zadania.|
|**wartość job_id**|    Unikatowy identyfikator    |Unikatowy identyfikator zadania.|
|**job_version**    |int    |Wersja zadania (automatycznie aktualizowane zawsze, gdy zadanie zostanie zmodyfikowana).|
|**Opis elementu**    |nvarchar(512)| Opis elementu zadania. włączone bit wskazuje, czy zadanie jest włączone. wartość 1 oznacza włączonych zadań, a wartość 0 wskazuje wyłączonego zadania.|
|**schedule_interval_type** |nvarchar(50)   |Wartość wskazująca, kiedy zadanie ma zostać wykonana: "Raz", "Min", "Godziny", "dni", "Tygodni", "Miesiące"
|**schedule_interval_count**|   int|    Liczba okresów schedule_interval_type pojawiają się między każdym wykonywanie zadania.|
|**schedule_start_time**    |datetime2(7)|  Data i godzina ostatniego wykonania uruchomiono to zadanie.|
|**schedule_end_time**| datetime2(7)|   Data i godzina ostatniego wykonania ukończone to zadanie.|


### <a name="jobversions-view"></a>Widok job_versions

[zadania]. [job_verions]

Przedstawia wszystkie wersje zadania.

|Nazwa kolumny|   Typ danych|  Opis|
|------|------|-------|
|**job_name**|  Nvarchar(128)   |Nazwa zadania.|
|**wartość job_id**|    Unikatowy identyfikator    |Unikatowy identyfikator zadania.|
|**job_version**    |int    |Wersja zadania (automatycznie aktualizowane zawsze, gdy zadanie zostanie zmodyfikowana).|


### <a name="jobsteps-view"></a>najpierw widoku

[zadania]. [najpierw]

Przedstawia wszystkie kroki w bieżącej wersji każdego zadania.

|Nazwa kolumny    |Typ danych| Opis|
|------|------|-------|
|**job_name**   |Nvarchar(128)| Nazwa zadania.|
|**wartość job_id** |Unikatowy identyfikator   |Unikatowy identyfikator zadania.|
|**job_version**|   int|    Wersja zadania (automatycznie aktualizowane zawsze, gdy zadanie zostanie zmodyfikowana).|
|**step_id**    |int    |Identyfikator unikatowy (dla tego zadania) kroku.|
|**step_name**  |Nvarchar(128)  |(Dla tego zadania) Nazwa kroku.|
|**command_type**   |nvarchar(50)   |Typ polecenia do wykonania w kroku zadania. Dla v1, wartość musi być równa się, a wartość domyślna to "TSql".|
|**command_source** |nvarchar(50)|  Lokalizacja polecenia. Dla v1 "Inline" jest domyślna i akceptowany tylko wartości.|
|**polecenie**|   nvarchar(max)|  Polecenia ma być wykonane przez zadania elastyczne za pośrednictwem command_type.|
|**credential_name**|   Nvarchar(128)   |Nazwa poświadczeń o zakresie bazy danych używane do realizacji zadania.|
|**target_group_name**| Nvarchar(128)   |Nazwa grupy docelowej.|
|**target_group_id**|   Unikatowy identyfikator|   Unikatowy identyfikator grupy docelowej.|
|**initial_retry_interval_seconds**|    int |Opóźnienie przed pierwszym ponowienia próby. Wartość domyślna to 1.|
|**maximum_retry_interval_seconds** |int|   Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami czy coraz większe niż ta wartość, go jest ograniczona do tej wartości zamiast tego. Wartość domyślna to 120.|
|**retry_interval_backoff_multiplier**  |rzeczywiste|  Mnożnik dotyczyć Opóźnienie ponownych prób, jeśli wykonanie kroku zadania wielu prób kończyć się niepowodzeniem. Wartość domyślna to 2.0.|
|**retry_attempts** |int|   Liczbę ponownych prób do użycia, jeśli ten krok nie powiedzie się. Domyślny: 10, co oznacza nie ponownych prób.|
|**step_timeout_seconds**   |int|   Ilość czasu w minutach, między ponownymi próbami. Wartość domyślna to 0, co oznacza, 0-minutowych interwałach.|
|**output_type**    |nvarchar(11)|  Lokalizacja polecenia. W bieżącej wersji zapoznawczej "Inline" jest domyślna i akceptowany tylko wartości.|
|**output_credential_name**|    Nvarchar(128)   |Nazwa poświadczenia, które będą służyć do podłączenia do serwera docelowego do przechowywania wyników zestawu.|
|**output_subscription_id**|    Unikatowy identyfikator|   Unikatowy identyfikator subskrypcji server\database docelowy dla wykonywania zapytania zestaw wyników.|
|**output_resource_group_name** |Nvarchar(128)| Nazwa grupy zasobów, gdzie znajduje się serwer docelowy.|
|**output_server_name**|    nvarchar(256)   |Nazwa serwera docelowego dla zestawu wyników.|
|**output_database_name**   |Nvarchar(128)| Nazwa docelowej bazy danych dla zestawu wyników.|
|**output_schema_name** |nvarchar(max)| Nazwa schematu docelowego. Wartość domyślna to dbo, jeśli nie zostanie określony.|
|**output_table_name**| nvarchar(max)|  Nazwa tabeli do przechowywania wyników ustawiony na podstawie wyników zapytania. Tabela zostanie utworzona automatycznie w oparciu o schemacie wyniki, jeśli jeszcze nie istnieje. Schemat musi być zgodna ze schematem zestawu wyników.|
|**max_parallelism**|   int|    Maksymalna liczba baz danych na puli elastycznej, które etap zadania będą uruchamiane naraz. Wartość domyślna to NULL, co oznacza brak limitu. |


### <a name="jobstepversions-view"></a>Widok jobstep_versions

[zadania]. [jobstep_versions]

Przedstawia wszystkie kroki we wszystkich wersjach każdego zadania. Schemat jest taki sam jak [najpierw](#jobsteps-view).

### <a name="targetgroups-view"></a>Widok target_groups

[zadania]. [target_groups]

Wyświetla listę wszystkich grup docelowych.

|Nazwa kolumny|Typ danych| Opis|
|-----|-----|-----|
|**target_group_name**| Nvarchar(128)   |Nazwa grupy docelowej kolekcji baz danych. 
|**target_group_id**    |Unikatowy identyfikator   |Unikatowy identyfikator grupy docelowej.

### <a name="targetgroupsmembers-view"></a>Widok target_groups_members

[zadania]. [target_groups_members]

Zawiera wszystkie elementy członkowskie wszystkich grup docelowych.

|Nazwa kolumny|Typ danych| Opis|
|-----|-----|-----|
|**target_group_name**  |nvarchar (128|Nazwa grupy docelowej kolekcji baz danych. |
|**target_group_id**    |Unikatowy identyfikator   |Unikatowy identyfikator grupy docelowej.|
|**membership_type**    |int|   Określa, czy członka grupy docelowej jest włączone lub wyłączone w docelowej grupie. Prawidłowe wartości target_group_name to "Include" lub "Wyklucz".|
|**target_type**    |Nvarchar(128)| Typ docelowej bazy danych lub kolekcji baz danych w tym wszystkie bazy danych serwera, wszystkie bazy danych w puli elastycznej lub bazy danych. Prawidłowe wartości target_type to 'SqlServer', "SqlElasticPool", "Bazadanychsql" lub "SqlShardMap".|
|**target_id**  |Unikatowy identyfikator|  Unikatowy identyfikator elementu członkowskiego grupy docelowej.|
|**refresh_credential_name**    |Nvarchar(128)  |Nazwa bazy danych o zakresie poświadczenia używane do łączenia się członka grupy docelowej.|
|**IDENTYFIKATOR_SUBSKRYPCJI**    |Unikatowy identyfikator|  Unikatowy identyfikator subskrypcji.|
|**resource_group_name**    |Nvarchar(128)| Nazwa grupy zasobów, w której znajduje się członka grupy docelowej.|
|**nazwa_serwera**    |Nvarchar(128)  |Nazwa serwera logicznego znajdujących się w grupie docelowej. Określona tylko, jeśli target_type jest 'SqlServer'. |
|**nazwa_bazy_danych**  |Nvarchar(128)  |Nazwa bazy danych znajdujących się w grupie docelowej. Określona, tylko gdy target_type jest "Bazadanychsql".|
|**elastic_pool_name**  |Nvarchar(128)| Nazwa puli elastycznej znajdujących się w grupie docelowej. Określona, tylko gdy target_type jest "SqlElasticPool".|
|**shard_map_name** |Nvarchar(128)| Nazwa mapy niezależnego fragmentu znajdujących się w grupie docelowej. Określona, tylko gdy target_type jest "SqlShardMap".|


## <a name="resources"></a>Zasoby

 - ![Ikona łącza do tematu](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "ikona łącza do tematu") [konwencje składni języka Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  


## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie i zarządzanie nimi zadania elastyczne przy użyciu programu PowerShell](elastic-jobs-powershell.md)
- [Autoryzacja i uprawnień programu SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  