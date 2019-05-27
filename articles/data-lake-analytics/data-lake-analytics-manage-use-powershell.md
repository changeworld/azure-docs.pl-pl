---
title: Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell
description: W tym artykule opisano, jak użyć programu Azure PowerShell do zarządzania kont usługi Data Lake Analytics, źródła danych, użytkowników i zadań.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 4273828c9c2bdb75fcbc1de45da55c5a03dd615f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66156431"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kontami usługi Azure Data Lake Analytics, źródła danych, użytkowników i zadań za pomocą programu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Za pomocą programu PowerShell z usługą Data Lake Analytics, Zbierz następujących rodzajów informacji: 

* **Identyfikator subskrypcji**: Identyfikator subskrypcji platformy Azure, który zawiera Twoje konto usługi Data Lake Analytics.
* **Grupa zasobów**: Nazwa grupy zasobów platformy Azure, który zawiera Twoje konto usługi Data Lake Analytics.
* **Nazwa konta usługi Data Lake Analytics**: Nazwa konta usługi Data Lake Analytics.
* **Nazwa konta domyślnego Data Lake Store**: Każde konto usługi Data Lake Analytics ma domyślne konto Data Lake Store.
* **Lokalizacja**: Lokalizacja konta usługi Data Lake Analytics, takie jak "Wschodnie stany USA 2" lub inne obsługiwane lokalizacje.

We fragmentach kodu programu PowerShell w ramach tego samouczka do przechowywania tych informacji są używane następujące zmienne

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

### <a name="log-in-using-interactive-user-authentication"></a>Zaloguj się przy użyciu uwierzytelniania użytkownika interaktywnego

Zaloguj się przy użyciu Identyfikatora subskrypcji lub według nazwy subskrypcji

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Zapisywanie kontekstu uwierzytelniania

`Connect-AzAccount` Polecenia cmdlet zawsze wyświetla monit o poświadczenia. Aby uniknąć monitowania za pomocą następujących poleceń cmdlet:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Zaloguj się przy użyciu podmiotu zabezpieczeń tożsamości usługi (SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Zarządzanie kontami


### <a name="list-accounts"></a>Wyświetlanie listy kont

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Tworzenie konta

Każde konto usługi Data Lake Analytics wymaga domyślnego konta usługi Data Lake Store używanego do przechowywania dzienników. Można użyć istniejącego konta lub utworzyć konto. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Uzyskaj informacje o koncie

Uzyskaj szczegółowe informacje o koncie.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Sprawdź, czy istnieje konto

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych
Usługa Azure Data Lake Analytics obsługuje obecnie następujące źródła danych:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Każde konto usługi Data Lake Analytics ma domyślne konto Data Lake Store. Domyślnego konta Data Lake Store służy do przechowywania dzienników inspekcji metadanych i zadania zadanie. 

### <a name="find-the-default-data-lake-store-account"></a>Znajdowanie domyślnego konta Data Lake Store

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Filtrowanie listy źródeł danych, można znaleźć domyślnego konta Data Lake Store `IsDefault` właściwości:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Dodawanie źródła danych

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Lista źródeł danych

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Przesyłanie zadań U-SQL

### <a name="submit-a-string-as-a-u-sql-job"></a>Prześlij ciąg jako zadania U-SQL

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Prześlij plik jako zadania U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Lista zadań

Dane wyjściowe obejmują obecnie uruchomione oraz niedawno ukończone zadania.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Lista zadań pierwszych N

Domyślnie lista zadań jest sortowana na czas zgłoszenia. Dlatego ostatnio przesłanych zadań występować jako pierwszy. Domyślnie konto ADLA zadania są przechowywane przez 180 dni, ale polecenie cmdlet Get-AdlJob domyślnie zwraca tylko pierwsze 500. Użyj - najważniejsze parametru do listy o określoną liczbę zadań.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Lista zadań według stanu zadania

Za pomocą `-State` parametru. Możesz połączyć każdą z tych wartości:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Lista zadań według wyniku zadania

Użyj `-Result` parametru, aby wykryć, czy Zakończono zadań została ukończona pomyślnie. Ma ona następujące wartości:

* Anulowana
* Błąd
* Brak
* Udane

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Lista zadań, osoba przesyłająca zadanie

`-Submitter` Parametr pomaga zidentyfikować, kto przesłane zadanie.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Lista zadań według czasu przesyłania

`-SubmittedAfter` Przydaje się w filtrowanie zakresu czasu.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Pobierz stan zadania

Uzyskaj stan określonego zadania.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>Anulowanie zadania

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Poczekaj na zakończenie zadania

Zamiast powtarzania `Get-AdlAnalyticsJob` do momentu zakończenia zadania, można użyć `Wait-AdlJob` polecenia cmdlet, aby czekać na zakończenie zadania.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analizowanie danych historii zadań

Analizowanie historii zadań uruchamianych w usłudze Data Lake analytics przy użyciu programu Azure PowerShell jest zaawansowanych technik. Można użyć go, aby uzyskać szczegółowe informacje dotyczące użycia i kosztów. Dowiedz się więcej, analizując [Analiza historii zadania przykładowego repozytorium](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Lista zadań potoki i cykle

Użyj `Get-AdlJobPipeline` polecenia cmdlet, aby wyświetlić informacje o potoku wcześniej przesłanych zadań.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Użyj `Get-AdlJobRecurrence` polecenia cmdlet, aby wyświetlić informacje o cyklu wcześniej przesłanych zadań.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Zarządzanie zasadami obliczeń

### <a name="list-existing-compute-policies"></a>Wyświetlić listę istniejących zasad obliczeń

`Get-AdlAnalyticsComputePolicy` Polecenie cmdlet pobiera informacje o zasadach obliczeniowych na koncie usługi Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Tworzenie zasad obliczeń

`New-AdlAnalyticsComputePolicy` Polecenie cmdlet tworzy nową zasadę obliczeniowych na koncie usługi Data Lake Analytics. W tym przykładzie Ustawia maksymalną liczbę równoległości dostępne do określonego użytkownika z 50 i priorytet minimalny zadania do 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Zarządzanie plikami

### <a name="check-for-the-existence-of-a-file"></a>Sprawdź, czy plik istnieje.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Przekazywanie i pobieranie

Przekaż plik.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Przekaż rekursywnie cały folder.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Pobierz plik.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Pobierz rekursywnie cały folder.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Jeśli proces przekazywania lub pobierania zostanie przerwany, można spróbować wznowić proces, uruchamiając polecenie cmdlet ponownie, używając ``-Resume`` flagi.

## <a name="manage-the-u-sql-catalog"></a>Zarządzanie wykazem U-SQL

Wykaz U-SQL jest używana do struktury danych i kodu, dzięki czemu mogą być współużytkowane przez skrypty U-SQL. Wykaz umożliwia najwyższym możliwym poziomie wydajności z danymi w usłudze Azure Data Lake. Więcej informacji znajduje się w temacie [Używanie katalogu U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Elementy listy w wykazie U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Lista wszystkich katalogu zestawów języka U-SQL

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Szczegółowe informacje o elemencie katalogu

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Store poświadczenia w katalogu

W bazie danych U-SQL Utwórz obiekt poświadczeń dla bazy danych hostowanej na platformie Azure. Obecnie U-SQL poświadczenia są jedynym typem elementu katalogu, który można utworzyć za pomocą programu PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Zarządzanie regułami zapory

### <a name="list-firewall-rules"></a>Lista reguł zapory

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Dodaj regułę zapory

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Modyfikowanie reguły zapory

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Usuń regułę zapory

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Zezwól na adresy IP platformy Azure

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Praca z platformą Azure

### <a name="get-error-details"></a>Pobierz szczegóły błędu

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Sprawdź, czy jest uruchomiona jako Administrator na komputerze Windows

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Znajdowanie identyfikatora dzierżawy

Na podstawie nazwy subskrypcji:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Z identyfikator subskrypcji:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Z adresu domeny, taką jak "contoso.com"

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Wyświetl wszystkie swoje subskrypcje i identyfikatory dzierżawy

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Tworzenie konta usługi Data Lake Analytics przy użyciu szablonu

Można również użyć szablonu grupy zasobów platformy Azure, korzystając z poniższego przykładu: [Tworzenie konta usługi Data Lake Analytics przy użyciu szablonu](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu [witryny Azure portal](data-lake-analytics-get-started-portal.md) | [programu Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [wiersza polecenia platformy Azure](data-lake-analytics-get-started-cli.md)
* Zarządzanie przy użyciu usługi Azure Data Lake Analytics [witryny Azure portal](data-lake-analytics-manage-use-portal.md) | [programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [interfejsu wiersza polecenia](data-lake-analytics-manage-use-cli.md) 
