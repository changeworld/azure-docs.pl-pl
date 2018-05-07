---
title: Zarządzanie Azure Data Lake Analytics przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak zarządzać kont usługi Data Lake Analytics, źródła danych, zadania i elementów katalogu. '
services: data-lake-analytics
documentationcenter: ''
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 96360eabefcbbdf36ef3bd83b0c6de45c1a6f3cc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Dowiedz się, jak zarządzanie kontami, źródłami danych, zadania i elementy katalogu przy użyciu programu Azure PowerShell usługi Azure Data Lake Analytics. 

## <a name="prerequisites"></a>Wymagania wstępne

Podczas tworzenia konta usługi Data Lake Analytics, musisz wiedzieć:

* **Identyfikator subskrypcji**: identyfikator subskrypcji Azure, pod którym znajduje się konto usługi Data Lake Analytics.
* **Grupa zasobów**: Nazwa grupy zasobów platformy Azure, która zawiera konto usługi Data Lake Analytics.
* **Nazwa konta usługi Data Lake Analytics**: Nazwa konta musi zawierać tylko małe litery i cyfry.
* **Domyślne konto usługi Data Lake Store**: każde konto usługi Data Lake Analytics ma domyślne konto usługi Data Lake Store. Te konta muszą znajdować się w tej samej lokalizacji.
* **Lokalizacja**: Lokalizacja konta usługi Data Lake Analytics, takie jak "Wschodnie stany USA 2" lub inne obsługiwane lokalizacje. Obsługiwane lokalizacje będą widoczne na naszych [cennikiem](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

We fragmentach kodu programu PowerShell w ramach tego samouczka do przechowywania tych informacji są używane następujące zmienne

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Logowanie się

Zaloguj się za pomocą identyfikatora subskrypcji.

```powershell
Connect-AzureRmAccount -SubscriptionId $subId
```

Zaloguj się za pomocą nazwę subskrypcji.

```
Connect-AzureRmAccount -SubscriptionName $subname 
```

`Connect-AzureRmAccount` Polecenia cmdlet zawsze wyświetla monit o podanie poświadczeń. Można uniknąć monitowania za pomocą następujących poleceń cmdlet:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="manage-accounts"></a>Zarządzanie kontami

### <a name="create-a-data-lake-analytics-account"></a>Tworzenie konta Data Lake Analytics

Jeśli nie masz jeszcze [grupy zasobów](../azure-resource-manager/resource-group-overview.md#resource-groups) do użycia, utworzyć. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Każde konto usługi Data Lake Analytics wymaga domyślnego konta usługi Data Lake Store używanego do przechowywania dzienników. Można ponownie użyć istniejącego konta lub utworzyć konto. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Gdy grupa zasobów i konto magazynu usługi Data Lake Store będą dostępne, utwórz konto usługi Data Lake Analytics.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-acount-information"></a>Uzyskiwanie informacji konto

Uzyskiwanie szczegółowych informacji o koncie.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Sprawdź istnienie określonego konta usługi Data Lake Analytics. Polecenie cmdlet zwraca albo `$true` lub `$false`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Sprawdź istnienie określonego konta usługi Data Lake Store. Polecenie cmdlet zwraca albo `$true` lub `$false`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="list-accounts"></a>Wyświetlanie listy kont

Lista Data Lake Analytics kont w ramach bieżącej subskrypcji.

```powershell
Get-AdlAnalyticsAccount
```

Lista Data Lake Analytics kont w ramach określonej grupy zasobów.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="manage-data-sources"></a>Zarządzaj źródłami danych
Azure Data Lake Analytics obsługuje obecnie następujących źródeł danych:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Podczas tworzenia konta usługi Analytics należy wyznaczyć konto usługi Data Lake Store jako domyślnego źródła danych. Domyślne konto usługi Data Lake Store jest używany do przechowywania metadanych i zadanie inspekcji z dziennikami zadań. Po utworzeniu konta usługi Data Lake Analytics można dodać dodatkowe konta usługi Data Lake Store i/lub konta magazynu. 

### <a name="find-the-default-data-lake-store-account"></a>Znajdź domyślnego konta usługi Data Lake Store

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Domyślne konto usługi Data Lake Store można znaleźć przez filtrowanie listy źródeł danych chronionych przez `IsDefault` właściwości:

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
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Przesyłanie zadań U-SQL

### <a name="submit-a-string-as-a-u-sql-script"></a>Przedstawia ciąg jako skrypt U-SQL

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

### <a name="submit-a-file-as-a-u-sql-script"></a>Przesyłanie pliku jako skrypt U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Lista zadania konta

### <a name="list-all-the-jobs-in-the-account"></a>Wyświetl listę wszystkich zadań w ramach konta. 

Dane wyjściowe obejmują obecnie uruchomione oraz niedawno ukończone zadania.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Listę zadań pierwszych N

Domyślnie lista zadań jest sortowane na przedstawia czas. Dlatego ostatnio przesłanych zadania występować jako pierwszy. Domyślnie konto ADLA pamięta zadania na okres 180 dni, ale polecenia cmdlet Get-AdlJob domyślnie zwraca tylko 500 pierwszych. Użyj - Top parametru do listy określoną liczbę zadań.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-based-on-the-value-of-job-property"></a>Lista zadań na podstawie wartości właściwości zadania

Przy użyciu `-State` parametru. Możesz połączyć ze sobą następujące wartości:

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

Użyj `-Result` parametr, aby wykryć, czy Zakończono zadania zakończone pomyślnie. Ma ona następujące wartości:

* Anulowano
* Niepowodzenie
* Brak
* Powodzenie

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

`-Submitter` Parametr pomagają określić, kto przesłać zadania.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

`-SubmittedAfter` Jest przydatne w filtrowanie zakresu czasu.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Analizowanie historii zadań

Korzystanie z programu Azure PowerShell do analizowania Historia zadań, które zostały uruchomione w usłudze Data Lake analytics to technika zaawansowanych. Aby uzyskać wgląd w użycie i koszt, można użyć go. Dowiedz się więcej analizując [repozytorium przykładowej Analiza historii zadań](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Pobieranie informacji o potoki i powtórzeń

Użyj `Get-AdlJobPipeline` polecenia cmdlet, aby zobaczyć informacje o potoku wcześniej zgłoszonych zadania.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Użyj `Get-AdlJobRecurrence` polecenia cmdlet, aby zobaczyć informacje o cyklu dla poprzednio przesłać zadania.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>Pobierz informacje o zadaniu

### <a name="get-job-status"></a>Pobierz stan zadania

Uzyskaj stan określonego zadania.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Sprawdź dane wyjściowe zadania

Zadanie zostało zakończone, aby sprawdzić, czy plik wyjściowy istnieje przez wyświetlanie listy plików w folderze.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Zarządzanie uruchomionych zadań

### <a name="cancel-a-job"></a>Anulowanie zadania

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Poczekaj na zakończenie zadania

Zamiast powtarzające się `Get-AdlAnalyticsJob` zakończenie zadania można użyć `Wait-AdlJob` polecenia cmdlet, aby czekać na zakończenie zadania.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Zarządzanie zasadami obliczeń

### <a name="list-existing-compute-policies"></a>Wyświetl listę istniejących zasad obliczeń

`Get-AdlAnalyticsComputePolicy` Polecenie cmdlet pobiera informacje o zasadach obliczeniowe dla konta usługi Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Tworzenie zasad obliczeń

`New-AdlAnalyticsComputePolicy` Polecenie cmdlet tworzy nową zasadę obliczeniowe dla konta usługi Data Lake Analytics. W tym przykładzie AUs maksymalna, dostępna do określonego użytkownika do 50, a także priorytet zadania minimalna 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Sprawdź, czy plik istnieje.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Przekazywanie i pobieranie

Przekaż plik.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Przekaż rekursywnie całego folderu.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Pobierz plik.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Pobierz rekursywnie całego folderu.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Jeśli proces przekazywania i pobierania zostanie przerwany, możesz spróbować wznowić proces, uruchamiając polecenie cmdlet ponownie z ``-Resume`` flagi.

## <a name="manage-catalog-items"></a>Zarządzanie elementów katalogu

Katalogu U-SQL jest używana do struktury danych i kod, aby mogły być udostępniane przez skrypty U-SQL. Katalog umożliwia najwyższym możliwym poziomie wydajności z danymi w usłudze Azure Data Lake. Więcej informacji znajduje się w temacie [Używanie katalogu U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Elementy listy w katalogu U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

Wyświetl listę wszystkich zestawów w wszystkich baz danych na koncie ADLA.

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

### <a name="get-details-about-a-catalog-item"></a>Uzyskiwanie szczegółowych informacji o elemencie katalogu

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>Utwórz poświadczenia w katalogu

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

### <a name="get-basic-information-about-an-adla-account"></a>Uzyskać podstawowe informacje o koncie ADLA

Podana nazwa konta, poniższy kod pobiera podstawowe informacje o koncie

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="manage-firewall-rules"></a>Zarządzaj regułami zapory

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

### <a name="change-a-firewall-rule"></a>Zmień reguły zapory

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Usuwanie reguły zapory

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Zezwalaj na adresy IP platformy Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Praca z platformy Azure

### <a name="get-details-of-azurerm-errors"></a>Uzyskiwanie szczegółowych informacji o błędach AzureRm

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Sprawdź, czy są uruchomione jako administrator

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Znajdowanie identyfikatora dzierżawcy

Na podstawie nazwy subskrypcji:

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

Z identyfikatorem subskrypcji:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

Z adresu domeny, takie jak "contoso.com"


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Wyświetl listę wszystkich subskrypcji i dzierżawców identyfikatorów

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Utwórz konto usługi Data Lake Analytics przy użyciu szablonu

Umożliwia także szablonem grupy zasobów platformy Azure przy użyciu poniższego polecenia: [Utwórz konto usługi Data Lake Analytics przy użyciu szablonu](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)


## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Wprowadzenie do usługi Data Lake Analytics przy użyciu [portalu Azure](data-lake-analytics-get-started-portal.md) | [programu Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [2.0 interfejsu wiersza polecenia](data-lake-analytics-get-started-cli2.md)
* Zarządzanie usługą Azure Data Lake Analytics przy użyciu [portalu Azure](data-lake-analytics-manage-use-portal.md) | [programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [interfejsu wiersza polecenia](data-lake-analytics-manage-use-cli.md) 
