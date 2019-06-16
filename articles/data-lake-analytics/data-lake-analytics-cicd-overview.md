---
title: Jak skonfigurować potok CI/CD dla usługi Azure Data Lake Analytics
description: Dowiedz się, jak skonfigurować ciągłą integrację i ciągłe wdrażanie dla usługi Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: b035be727df2dfecb613da79681affd740c69bec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60333864"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak skonfigurować potok CI/CD dla usługi Azure Data Lake Analytics  

W tym artykule dowiesz się, jak skonfigurować ciągłej integracji i potoku wdrażania (CI/CD) na potrzeby zadań U-SQL i bazy danych U-SQL.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Ciągła Integracja/ciągłe dostarczanie na użytek zadań U-SQL

Usługa Azure Data Lake Tools for Visual Studio zawiera typ projektu U-SQL, który pomaga organizować skryptów U-SQL. Za pomocą projektu U-SQL do zarządzania kodem języka U-SQL ułatwia dalszych scenariuszach ciągłej integracji/ciągłego wdrażania.

## <a name="build-a-u-sql-project"></a>Kompiluj projekt U-SQL

Projekt U-SQL mogą być tworzone za pomocą aparatu Microsoft Build Engine (MSBuild), przekazując odpowiednie parametry. Wykonaj kroki opisane w tym artykule, aby skonfigurować proces kompilacji dla projektu U-SQL.

### <a name="project-migration"></a>Migracja projektu

Przed skonfigurowaniem zadania kompilacji dla projektu U-SQL, upewnij się, że masz najnowszą wersję projektu U-SQL. Otwórz plik projektu U-SQL w edytorze i należy sprawdzić, czy je importować elementy:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Jeśli nie, dostępne są dwie opcje, aby migrować projekt:

- Opcja 1: Zmień stary element import na poprzednim.
- Opcja 2: Otwórz starego projektu w usłudze Azure Data Lake Tools dla programu Visual Studio. Użyj wersji nowszej niż 2.3.3000.0. Stary szablon projektu zostanie automatycznie uaktualniony do najnowszej wersji. Nowe projekty utworzone za pomocą wersji nowszej niż 2.3.3000.0 nowy szablon.

### <a name="get-nuget"></a>Pobierz NuGet

Program MSBuild nie zapewnia wbudowaną obsługę dla projektów języka U-SQL. Aby uzyskać tę obsługę, musisz dodać odwołanie do rozwiązania w celu [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) pakietu NuGet, który dodaje usługę wymagany język.

Aby dodać odwołanie do pakietu NuGet, kliknij prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań w usłudze Visual Studio, a następnie wybierz **Zarządzaj pakietami NuGet**. Lub można dodać plik o nazwie `packages.config` w folderze rozwiązania i umieść następujące zawartości do niego:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Zarządzanie odwołaniami do bazy danych U-SQL

Skryptów U-SQL projektu U-SQL może zawierać instrukcji kwerendy dla obiektów bazy danych U-SQL. W takim przypadku należy odwoływać się do odpowiedniego projektu bazy danych U-SQL, który zawiera definicji obiektów, przed utworzeniem projektu U-SQL. Na przykład sytuacja wysłać zapytanie do tabeli U-SQL, lub odwołanie do zestawu. 

Dowiedz się więcej o [projekt bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>Instrukcja DROP może spowodować problem usunięcia awarii. Aby włączyć instrukcja DROP, musisz jawnie określić argumenty MSBuild. **AllowDropStatement** spowoduje włączenie bez danych powiązanych wykonać operacji usuwania, jak usunąć zestaw i upuść funkcja zwracająca tabelę. **AllowDataDropStatement** umożliwi wykonać operacji usuwania, takie jak listy table i schema listy powiązane dane. Należy włączyć AllowDropStatement przed użyciem AllowDataDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Tworzenie projektu U-SQL przy użyciu wiersza polecenia programu MSBuild

Najpierw należy migrować projekt i pobrać pakiet NuGet. Następnie wywołaj standardowe wierszu polecenia programu MSBuild z następującymi argumentami dodatkowe do kompilowania projektu U-SQL: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Definicja argumentów i wartości są następujące:

* **USQLSDKPath =\<pakietu Nuget języka U-SQL > \build\runtime**. Ten parametr odwołuje się do ścieżki instalacji pakietu NuGet usługi języka U-SQL.
* **USQLTargetType = scalania lub SyntaxCheck**:
    * **Scal**. Tryb scalania kompiluje pliki związane z kodem. Należą do nich **.cs**, **.py**, i **.r** plików. Jego inlines wynikowej biblioteki zdefiniowanych przez użytkownika kodu do skryptu U-SQL. Przykłady to plik binarny biblioteki dll, Python lub R kodu.
    * **SyntaxCheck**. Tryb SyntaxCheck najpierw scala plików z kodem skryptu U-SQL. Następnie kompiluje skrypt U-SQL, aby zweryfikować swój kod.
* **Ścieżka folderu DataRoot =\<ścieżka folderu DataRoot >** . Ścieżka folderu DataRoot jest wymagane tylko dla trybu SyntaxCheck. Podczas tworzenia skryptów w trybie SyntaxCheck, program MSBuild sprawdza, czy odwołania do obiektów bazy danych w skrypcie. Przed kompilacją, należy skonfigurować zgodne środowisko lokalne, które zawiera obiekty z bazy danych U-SQL w folderze DataRoot maszynie kompilacji. Można również zarządzać te zależności bazy danych przez [odwołanie do projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Program MSBuild sprawdza tylko odwołania do obiektu bazy danych, nie pliki.
* **EnableDeployment = true** lub **false**. EnableDeployment wskazuje, czy zezwolił na Wdróż przywoływane bazy danych U-SQL podczas procesu kompilacji. Jeśli można odwoływać się do projektu bazy danych U-SQL i korzystać z obiektów bazy danych w skrypcie języka U-SQL, należy nadać parametrowi **true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Ciągła integracja za pośrednictwem potoków usługi Azure

Oprócz wiersza polecenia umożliwia także Visual Studio Build lub zadanie programu MSBuild do kompilowania projektów języka U-SQL w potokach platformy Azure. Aby skonfigurować potok kompilacji, upewnij się, że dodawanie dwóch zadań w potoku kompilacji: zadanie przywracania NuGet i zadanie programu MSBuild.

![Zadanie programu MSBuild dla projektów języka U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Dodaj zadanie przywracania NuGet w celu uzyskania odwołanie do rozwiązania pakiet NuGet, który zawiera `Azure.DataLake.USQL.SDK`, dzięki czemu program MSBuild można znaleźć elementy docelowe języka U-SQL. Ustaw **zaawansowane** > **katalog docelowy** do `$(Build.SourcesDirectory)/packages` Jeśli chcesz korzystać z próbki argumenty MSBuild bezpośrednio w kroku 2.

    ![Zadanie przywracania NuGet dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Ustaw argumenty MSBuild w narzędziach kompilacji Visual Studio lub w zadaniu programu MSBuild, jak pokazano w poniższym przykładzie. Lub można zdefiniować zmienne dla tych argumentów w potoku kompilacji potoki usługi Azure.

    ![Definiowanie zmiennych MSBuild ciągłej integracji/ciągłego Dostarczania dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>Dane wyjściowe kompilacji projektu U-SQL

Po uruchomieniu kompilacji, wszystkie skrypty w projekcie języka U-SQL tworzone i dane wyjściowe do pliku zip o nazwie `USQLProjectName.usqlpack`. Strukturę folderów w projekcie jest przechowywana w danych wyjściowych kompilacji zip.

> [!NOTE]
>
> Plików z kodem dla każdego skryptu U-SQL, zostaną scalone jako instrukcję wbudowanego w danych wyjściowych kompilacji skryptu.
>

## <a name="test-u-sql-scripts"></a>Testowanie skryptów U-SQL

Usługa Azure Data Lake udostępnia projektów testowych dla skryptów U-SQL i C# operatory zdefiniowane przez użytkownika/UDAG/funkcji zdefiniowanej przez użytkownika:
* Dowiedz się, jak [dodać przypadki testowe dla skryptów U-SQL i rozszerzonego kodu C#](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Dowiedz się, jak [uruchamiania przypadków testowych w potokach Azure](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Wdrażanie zadania U-SQL

Po zweryfikowaniu kodu w procesie kompilacji i testowania, można przesłać zadań U-SQL bezpośrednio z poziomu potoków usługi Azure za pomocą zadań programu Azure PowerShell. Można także wdrożyć skrypt do usługi Azure Data Lake Store lub Azure Blob storage i [uruchamiać zaplanowane zadania za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Przesyłanie zadań U-SQL za pomocą potoków usługi Azure

Dane wyjściowe kompilacji, za pomocą projektu U-SQL jest plikiem zip o nazwie **USQLProjectName.usqlpack**. Plik zip zawiera wszystkie skrypty U-SQL w projekcie. Możesz użyć [zadań programu Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) w potokach z następującego przykładowego skryptu programu PowerShell do przesyłania zadań U-SQL bezpośrednio z poziomu potoków usługi Azure.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Wdrażanie zadań U-SQL przy użyciu usługi Azure Data Factory

Można przesyłać zadania U-SQL bezpośrednio z poziomu potoków usługi Azure. Lub możesz przekazać utworzonych skryptów do usługi Azure Data Lake Store lub Azure Blob storage i [uruchamiać zaplanowane zadania za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Użyj [zadań programu Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) w potokach platformy Azure za pomocą następującego przykładowego skryptu programu PowerShell do przekazania skryptów U-SQL do konta usługi Azure Data Lake Store:

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>Ciągła Integracja/ciągłe dostarczanie dla bazy danych U-SQL

Usługa Azure Data Lake Tools for Visual Studio udostępnia szablony projektów bazy danych U-SQL, które ułatwiają tworzenie, zarządzanie i wdrażaj bazy danych U-SQL. Dowiedz się więcej o [projekt bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

### <a name="get-the-nuget-package"></a>Pobieranie pakietu NuGet

Program MSBuild nie zapewnia wbudowaną obsługę dla projektów bazy danych U-SQL. Aby uzyskać tę możliwość, musisz dodać odwołanie do rozwiązania w celu [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) pakietu NuGet, który dodaje usługę wymagany język.

Aby dodać odwołanie do pakietu NuGet, kliknij prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań w usłudze Visual Studio. Wybierz **Zarządzaj pakietami NuGet**. Następnie wyszukaj i zainstaluj pakiet NuGet. Lub można dodać plik o nazwie **packages.config** w folderze rozwiązania i umieść następujące zawartości do niego:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Tworzenie projektu bazy danych przy użyciu wiersza polecenia MSBuild U-SQL

Aby skompilować projekt bazy danych U-SQL, należy wywołać standardowego wiersza polecenia programu MSBuild i przekazać odwołanie do pakietu NuGet zestawu SDK U-SQL jako dodatkowy argument. Zobacz poniższy przykład: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argument `USQLSDKPath=<U-SQL Nuget package>\build\runtime` odnosi się do ścieżki instalacji pakietu NuGet usługi języka U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Ciągła Integracja z potokiem, Azure

Oprócz wiersza polecenia można użyć Visual Studio Build lub zadanie programu MSBuild do kompilowania projektów bazy danych U-SQL w potokach platformy Azure. Aby skonfigurować zadanie kompilacji, upewnij się dodać dwa zadania w potoku kompilacji: zadanie przywracania NuGet i zadanie programu MSBuild.

   ![Zadanie MSBuild ciągłej integracji/ciągłego Dostarczania dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1. Dodaj zadanie przywracania NuGet w celu uzyskania odwołanie do rozwiązania pakiet NuGet, który zawiera `Azure.DataLake.USQL.SDK`, dzięki czemu program MSBuild można znaleźć elementy docelowe języka U-SQL. Ustaw **zaawansowane** > **katalog docelowy** do `$(Build.SourcesDirectory)/packages` Jeśli chcesz korzystać z próbki argumenty MSBuild bezpośrednio w kroku 2.

   ![Zadanie NuGet ciągłej integracji/ciągłego Dostarczania dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Ustaw argumenty MSBuild w narzędziach kompilacji Visual Studio lub w zadaniu programu MSBuild, jak pokazano w poniższym przykładzie. Lub można zdefiniować zmienne dla tych argumentów w potoku kompilacji potoki usługi Azure.

   ![Definiowanie zmiennych MSBuild ciągłej integracji/ciągłego Dostarczania dla projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

   ```
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```
 
### <a name="u-sql-database-project-build-output"></a>Wynik kompilacji projektu bazy danych U-SQL

Dane wyjściowe projektu bazy danych U-SQL jest pakiet wdrażania bazy danych U-SQL, nazwę z sufiksem kompilacji `.usqldbpack`. `.usqldbpack` Pakiet jest plik zip, który zawiera wszystkie instrukcje języka DDL w jednego skryptu U-SQL w folderze DDL. Zawiera wszystkie **dll debuggle** i dodatkowe pliki dla zestawu w folderze temp.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testowanie funkcji zwracającej tabelę i procedur składowanych

Dodawanie przypadków testowych dla funkcji z wartościami przechowywanymi w tabeli i procedur składowanych, bezpośrednio nie jest obecnie obsługiwane. Jako obejście można utworzyć projekt U-SQL, który ma skryptów U-SQL, które wywołują te funkcje i zapisują przypadków testowych dla nich. Wykonaj poniższe kroki, aby skonfigurować przypadki testowe dla funkcji z wartościami przechowywanymi w tabeli i procedur składowanych, zdefiniowane w projekcie bazy danych U-SQL:

1.  Utwórz projekt U-SQL do celów testowych i pisanie skryptów U-SQL podczas wywoływania funkcji z wartościami przechowywanymi w tabeli i procedur składowanych.
2.  Dodaj odwołanie do bazy danych do projektu U-SQL. Aby uzyskać funkcji zwracającej tabelę i definicji procedury składowanej, należy odwoływać się do projektu bazy danych, który zawiera instrukcję DDL. Dowiedz się więcej o [bazy danych odwołania](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Dodaj przypadki testowe dla skryptów U-SQL, które wywołują funkcje zwracające tabele i procedury składowane. Dowiedz się, jak [Dodaj przypadki testowe dla skryptów U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Wdrażanie bazy danych U-SQL, za pomocą potoków usługi Azure

`PackageDeploymentTool.exe` zapewnia programowania i interfejsów z wierszem polecenia, które pomagają wdrażać pakiety wdrażania bazy danych U-SQL, **.usqldbpack**. Zestaw SDK jest uwzględniony w [pakietu NuGet zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)znajdujący się w folderze **build/runtime/PackageDeploymentTool.exe**. Za pomocą `PackageDeploymentTool.exe`, można wdrożyć bazy danych U-SQL do usługi Azure Data Lake Analytics i kont lokalnych.

> [!NOTE]
>
> Obsługę wiersza polecenia programu PowerShell i potoków Azure zwolnienie zadania Pomoc techniczna dotycząca wdrażania bazy danych U-SQL jest obecnie w stanie oczekiwania.
>

Wykonaj poniższe kroki, aby skonfigurować zadania wdrożenia bazy danych w potokach platformy Azure:

1. Dodaj zadanie skrypt programu PowerShell w kompilacji lub potoku wydania i uruchom następujący skrypt programu PowerShell. To zadanie pozwala uzyskać zależności zestawu Azure SDK `PackageDeploymentTool.exe` i `PackageDeploymentTool.exe`. Możesz ustawić **- AzureSDK** i **- DBDeploymentTool** parametrów można załadować zależności i narzędzia do wdrażania do określonych folderów. Przekaż **- AzureSDK** ścieżkę do `PackageDeploymentTool.exe` jako **- AzureSDKPath** parametru w kroku 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Dodaj **zadania wiersza polecenia** w potoku kompilacji lub wydania i wypełnienie w skrypcie, wywołując `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` znajduje się w obszarze zdefiniowane **$DBDeploymentTool** folderu. Przykładowy skrypt jest następująca: 

    * Wdróż bazę U-SQL lokalnie:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Wdrażanie bazy danych U-SQL z kontem usługi Azure Data Lake Analytics, należy użyć trybu interakcyjnego uwierzytelniania:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Użyj **secrete** uwierzytelniania wdrażanie bazy danych U-SQL z kontem usługi Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Użyj **certFile** uwierzytelniania wdrażanie bazy danych U-SQL z kontem usługi Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Opisy parametrów PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Wspólne parametry

| Parametr | Opis | Wartość domyślna | Wymagane |
|---------|-----------|-------------|--------|
|Pakiet|Ścieżka pakietu wdrożeniowego bazy danych U-SQL do wdrożenia.|Wartość null|true|
|Database (Baza danych)|Nazwa bazy danych, które mają być wdrożone lub utworzony.|master|false|
|LogFile|Ścieżka pliku do rejestrowania. Domyślnie standard out (Konsola).|Wartość null|false|
|LogLevel|Poziom dziennika: Pełne, normalny, Warning lub Error.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parametr dla wdrożenia lokalnego

|Parametr|Opis|Wartość domyślna|Wymagane|
|---------|-----------|-------------|--------|
|DataRoot|Ścieżka folderu głównego danych lokalnych.|Wartość null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametry w celu wdrożenia usługi Azure Data Lake Analytics

|Parametr|Opis|Wartość domyślna|Wymagane|
|---------|-----------|-------------|--------|
|Konto|Określa, która konta Azure Data Lake Analytics, aby wdrożyć według nazwy konta.|Wartość null|true|
|ResourceGroup|Nazwa grupy zasobów platformy Azure dla konta usługi Azure Data Lake Analytics.|Wartość null|true|
|SubscriptionId|Identyfikator subskrypcji platformy Azure dla konta usługi Azure Data Lake Analytics.|Wartość null|true|
|Dzierżawa|Nazwa dzierżawy jest nazwą domeny usługi Azure Active Directory (Azure AD). Znaleźć go na stronie zarządzania subskrypcją w witrynie Azure portal.|Wartość null|true|
|AzureSDKPath|Ścieżki wyszukiwania zestawów zależnych w zestawie SDK platformy Azure.|Wartość null|true|
|Interaktywne|Czy należy użyć trybu interakcyjnego uwierzytelniania.|false|false|
|ClientId|Identyfikator aplikacji usługi Azure AD jest wymagana dla nieinterakcyjnych authentication.|Wartość null|Wymagane dla nieinterakcyjnych authentication.|
|Secrete|Secrete lub hasła dla nieinterakcyjnych authentication. Należy używać tylko w środowisku zaufaną i bezpieczną.|Wartość null|Wymagane dla nieinterakcyjnych authentication; w przeciwnym razie użyj SecreteFile.|
|SecreteFile|Plik jest zapisywany secrete lub hasła dla nieinterakcyjnych authentication. Upewnij się, że nadal można odczytać tylko przez bieżącego użytkownika.|Wartość null|Wymagane dla nieinterakcyjnych authentication; w przeciwnym razie użyj Secrete.|
|CertFile|Plik jest zapisywany certyfikaty X.509 związane z uwierzytelnianiem nieinterakcyjnym. Wartość domyślna to korzystanie z klienta secrete uwierzytelniania.|Wartość null|false|
| JobPrefix | Prefiks do wdrożenia bazy danych zadania U-SQL DDL. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>Kolejne kroki

- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Uruchom skrypt U-SQL na maszynie lokalnej](data-lake-analytics-data-lake-tools-local-run.md).
- [Użyj języka U-SQL bazy danych projektu do tworzenia bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).
