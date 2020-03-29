---
title: Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla usługi Azure Data Lake Analytics
description: Dowiedz się, jak skonfigurować ciągłą integrację i ciągłe wdrażanie usługi Azure Data Lake Analytics.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60333864"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla usługi Azure Data Lake Analytics  

W tym artykule dowiesz się, jak skonfigurować potok ciągłej integracji i wdrażania (CI/CD) dla zadań U-SQL i baz danych U-SQL.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Używanie ciągłej integracji/ciągłego wdrażania dla zadań U-SQL

Narzędzia usługi Azure Data Lake Tools for Visual Studio udostępnia typ projektu U-SQL, który ułatwia organizowanie skryptów U-SQL. Za pomocą projektu U-SQL do zarządzania kodem U-SQL sprawia, że dalsze scenariusze ciągłej integracji/ciągłego wdrażania łatwe.

## <a name="build-a-u-sql-project"></a>Tworzenie projektu U-SQL

Projekt U-SQL można utworzyć za pomocą aparatu kompilacji firmy Microsoft (MSBuild) przez przekazanie odpowiednich parametrów. Wykonaj kroki opisane w tym artykule, aby skonfigurować proces kompilacji dla projektu U-SQL.

### <a name="project-migration"></a>Migracja projektu

Przed skonfigurowaniem zadania kompilacji dla projektu U-SQL upewnij się, że masz najnowszą wersję projektu U-SQL. Otwórz plik projektu U-SQL w edytorze i sprawdź, czy masz następujące elementy importu:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Jeśli nie, masz dwie opcje migracji projektu:

- Opcja 1: Zmień stary element importu na poprzedni.
- Opcja 2: Otwórz stary projekt w narzędziach usługi Azure Data Lake dla programu Visual Studio. Użyj wersji nowszej niż 2.3.3000.0. Stary szablon projektu zostanie automatycznie uaktualniony do najnowszej wersji. Nowe projekty utworzone w wersjach nowszych niż 2.3.3000.0 używają nowego szablonu.

### <a name="get-nuget"></a>Pobierz NuGet

MSBuild nie zapewnia wbudowanej obsługi projektów U-SQL. Aby uzyskać tę pomoc techniczną, należy dodać odwołanie do rozwiązania do pakietu NuGet [Microsoft.Azure.DataLake.USQL.SDK,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) który dodaje wymaganą usługę języka.

Aby dodać odwołanie do pakietu NuGet, kliknij prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań programu Visual Studio i wybierz polecenie **Zarządzaj pakietami NuGet**. Możesz też dodać plik `packages.config` o nazwie w folderze rozwiązania i umieścić w nim następującą zawartość:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Zarządzanie odwołaniami do bazy danych U-SQL

Skrypty U-SQL w projekcie U-SQL mogą mieć instrukcje kwerend dla obiektów bazy danych U-SQL. W takim przypadku należy odwołać się do odpowiedniego projektu bazy danych U-SQL, który zawiera definicję obiektów przed utworzeniem projektu U-SQL. Przykładem jest podczas kwerendy tabeli U-SQL lub odwołania do zestawu. 

Dowiedz się więcej o [projekcie bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>Instrukcja DROP może spowodować problem z usunięciem wypadku. Aby włączyć drop instrukcji, należy jawnie określić argumenty MSBuild. **AllowDropStatement** włączy operację DROP niezwiązaną z danymi, taką jak funkcja wartościowania zestawu upuszczania i tabeli upuszczania. **AllowDataDropStatement** włączy operację DROP związane z danymi, takie jak tabela upuszczania i schemat upuszczania. Musisz włączyć AllowDropStatement przed użyciem AllowDataDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Tworzenie projektu U-SQL za pomocą wiersza polecenia MSBuild

Najpierw migruj projekt i pobierz pakiet NuGet. Następnie wywołaj standardowy wiersz polecenia MSBuild z następującymi dodatkowymi argumentami, aby utworzyć projekt U-SQL: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Definicja argumentów i wartości są następujące:

* **USQLSDKPath=\<pakiet U-SQL Nuget>\build\runtime**. Ten parametr odnosi się do ścieżki instalacji pakietu NuGet dla usługi języka U-SQL.
* **USQLTargetType=Scalanie lub SkładniaCheck:**
    * **Scalanie**. Tryb scalania kompiluje pliki połączone z kodem. Przykładami są pliki **.cs**, **.py**i **.r.** Zawiera ona wytłumiewającą bibliotekę kodu zdefiniowaną przez użytkownika w skrypcie U-SQL. Przykładami są dll binarny, Python lub R kodu.
    * **SyntaxCheck**. Tryb SyntaxCheck najpierw scala pliki załączone do kodu do skryptu U-SQL. Następnie kompiluje skrypt U-SQL, aby sprawdzić poprawność kodu.
* **DataRoot=\<Ścieżka DataRoot>**. DataRoot jest potrzebny tylko w trybie SyntaxCheck. Podczas tworzenia skryptu w trybie SyntaxCheck, MSBuild sprawdza odwołania do obiektów bazy danych w skrypcie. Przed utworzeniem należy skonfigurować pasujące środowisko lokalne, które zawiera obiekty, do których istnieje odwołanie z bazy danych U-SQL w folderze DataRoot komputera kompilacji. Można również zarządzać tymi zależnościami bazy danych, [odwołując się do projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild sprawdza tylko odwołania do obiektów bazy danych, a nie pliki.
* **EnableDeployment=true** lub **false**. EnableDeployment wskazuje, czy jest dozwolone wdrażanie baz danych U-SQL, do których istnieje odwołanie podczas procesu kompilacji. Jeśli odwołujesz się do projektu bazy danych U-SQL i zużywasz obiekty bazy danych w skrypcie U-SQL, ustaw ten parametr na **true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Ciągła integracja za pośrednictwem potoków platformy Azure

Oprócz wiersza polecenia można również użyć visual studio kompilacji lub zadania MSBuild do tworzenia projektów U-SQL w potokach platformy Azure. Aby skonfigurować potok kompilacji, upewnij się, aby dodać dwa zadania w potoku kompilacji: zadanie przywracania NuGet i zadanie MSBuild.

![Zadanie MSBuild dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Dodaj zadanie przywracania NuGet, aby uzyskać pakiet NuGet, do którego odwołuje się rozwiązanie, który zawiera `Azure.DataLake.USQL.SDK`, aby program MSBuild mógł znaleźć obiekty docelowe języka U-SQL. Ustaw **katalog zaawansowanego** > **miejsca docelowego,** `$(Build.SourcesDirectory)/packages` jeśli chcesz użyć przykładu argumentów MSBuild bezpośrednio w kroku 2.

    ![Zadanie przywracania NuGet dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Ustaw argumenty MSBuild w narzędziach kompilacji programu Visual Studio lub w zadaniu MSBuild, jak pokazano w poniższym przykładzie. Lub można zdefiniować zmienne dla tych argumentów w potoku kompilacji usługi Azure Potoki.

    ![Definiowanie zmiennych CIĄGŁEGO/CD MSBuild dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>Wyniki kompilacji projektu U-SQL

Po uruchomieniu kompilacji wszystkie skrypty w projekcie U-SQL są budowane `USQLProjectName.usqlpack`i wyprowadzane do pliku zip o nazwie . Struktura folderów w projekcie jest przechowywana w spakowane dane wyjściowe kompilacji.

> [!NOTE]
>
> Pliki związane z kodem dla każdego skryptu U-SQL zostaną scalone jako instrukcja wbudowana do danych wyjściowych kompilacji skryptu.
>

## <a name="test-u-sql-scripts"></a>Testowanie skryptów U-SQL

Usługa Azure Data Lake udostępnia projekty testowe dla skryptów U-SQL i języka C# UDO/UDAG/UDF:
* Dowiedz się, jak [dodać przypadki testowe dla skryptów U-SQL i rozszerzonego kodu C#.](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* Dowiedz się, jak [uruchamiać przypadki testowe w usłudze Azure Pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Wdrażanie zadania U-SQL

Po zweryfikowaniu kodu za pośrednictwem procesu kompilacji i testowania, można przesłać zadania U-SQL bezpośrednio z potoków platformy Azure za pośrednictwem zadania programu Azure PowerShell. Skrypt można również wdrożyć w magazynie usługi Azure Data Lake Store lub Azure Blob store i [uruchomić zaplanowane zadania za pośrednictwem usługi Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Przesyłanie zadań U-SQL za pośrednictwem potoków platformy Azure

Dane wyjściowe kompilacji projektu U-SQL to plik zip o nazwie **USQLProjectName.usqlpack**. Plik zip zawiera wszystkie skrypty U-SQL w projekcie. Za pomocą [zadania programu Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) w potokach z następującym przykładowym skryptem programu PowerShell można przesłać zadania U-SQL bezpośrednio z usługi Azure Pipelines.

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

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Wdrażanie zadań U-SQL za pośrednictwem usługi Azure Data Factory

Zadania U-SQL można przesyłać bezpośrednio z usługi Azure Pipelines. Możesz też przekazać skondygnowane skrypty do magazynu usługi Azure Data Lake Store lub Azure Blob store i [uruchomić zaplanowane zadania za pośrednictwem usługi Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

Użyj [zadania programu Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) w potokach platformy Azure z następującym przykładowym skryptem programu PowerShell, aby przekazać skrypty U-SQL do konta usługi Azure Data Lake Store:

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

## <a name="cicd-for-a-u-sql-database"></a>Ciągła integracja/cd bazy danych U-SQL

Narzędzia usługi Azure Data Lake Tools for Visual Studio udostępnia szablony projektów bazy danych U-SQL, które ułatwiają tworzenie baz danych U-SQL, zarządzanie nimi i wdrażanie ich. Dowiedz się więcej o [projekcie bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

### <a name="get-the-nuget-package"></a>Pobierz pakiet NuGet

MSBuild nie zapewnia wbudowanej obsługi projektów bazy danych U-SQL. Aby uzyskać tę możliwość, należy dodać odwołanie do rozwiązania do pakietu NuGet [Microsoft.Azure.DataLake.USQL.SDK,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) który dodaje wymaganą usługę języka.

Aby dodać odwołanie do pakietu NuGet, kliknij prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań programu Visual Studio. Wybierz **pozycję Zarządzaj pakietami NuGet**. Następnie wyszukaj i zainstaluj pakiet NuGet. Możesz też dodać plik o nazwie **packages.config** w folderze rozwiązania i umieścić w nim następującą zawartość:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Tworzenie projektu bazy danych U-SQL za pomocą wiersza polecenia MSBuild

Aby utworzyć projekt bazy danych U-SQL, wywołaj standardowy wiersz polecenia MSBuild i przekaż odwołanie do pakietu NuGet SDK U-SQL jako dodatkowy argument. Zobacz poniższy przykład: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argument `USQLSDKPath=<U-SQL Nuget package>\build\runtime` odnosi się do ścieżki instalacji pakietu NuGet dla usługi języka U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Ciągła integracja z użyciem usługi Azure Pipelines

Oprócz wiersza polecenia można użyć visual studio kompilacji lub zadania MSBuild do tworzenia projektów bazy danych U-SQL w potokach platformy Azure. Aby skonfigurować zadanie kompilacji, upewnij się, aby dodać dwa zadania w potoku kompilacji: zadanie przywracania NuGet i zadanie MSBuild.

   ![Zadanie CI/CD MSBuild dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1. Dodaj zadanie przywracania NuGet, aby uzyskać pakiet NuGet, do którego odwołuje się rozwiązanie, który zawiera `Azure.DataLake.USQL.SDK`, aby program MSBuild mógł znaleźć obiekty docelowe języka U-SQL. Ustaw **katalog zaawansowanego** > **miejsca docelowego,** `$(Build.SourcesDirectory)/packages` jeśli chcesz użyć przykładu argumentów MSBuild bezpośrednio w kroku 2.

   ![Zadanie ci/cd NuGet dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Ustaw argumenty MSBuild w narzędziach kompilacji programu Visual Studio lub w zadaniu MSBuild, jak pokazano w poniższym przykładzie. Lub można zdefiniować zmienne dla tych argumentów w potoku kompilacji usługi Azure Potoki.

   ![Definiowanie zmiennych CI/CD MSBuild dla projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

   ```
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```
 
### <a name="u-sql-database-project-build-output"></a>Dane wyjściowe kompilacji projektu bazy danych U-SQL

Dane wyjściowe kompilacji dla projektu bazy danych U-SQL to pakiet wdrażania `.usqldbpack`bazy danych U-SQL o nazwie z sufiksem . Pakiet `.usqldbpack` jest plikiem zip, który zawiera wszystkie instrukcje DDL w jednym skrypcie U-SQL w folderze DDL. Zawiera wszystkie **pliki dll** i dodatkowe pliki do montażu w folderze tymczasowym.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testowanie funkcji i procedur przechowywanych w tabeli

Dodawanie przypadków testowych dla funkcji wycenianych w tabeli i procedur przechowywanych bezpośrednio nie jest obecnie obsługiwane. Aby obejść ten problem, można utworzyć projekt U-SQL, który ma skrypty U-SQL, które wywołują te funkcje i zapisują dla nich przypadki testowe. Aby skonfigurować przypadki testowe dla funkcji wycenianych w tabeli i procedur przechowywanych zdefiniowanych w projekcie bazy danych U-SQL, należy wykonać następujące kroki:

1.  Utwórz projekt U-SQL do celów testowych i napisz skrypty U-SQL wywołując funkcje wyceniane w tabeli i procedury przechowywane.
2.  Dodaj odwołanie do bazy danych do projektu U-SQL. Aby uzyskać funkcję wycenioną tabelą i definicję procedury składowanej, należy odwołać się do projektu bazy danych, który zawiera instrukcję DDL. Dowiedz się więcej o [odwołaniach do bazy danych](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Dodaj przypadki testowe dla skryptów U-SQL, które wywołują funkcje wyceniane w tabeli i procedury przechowywane. Dowiedz się, jak [dodawać przypadki testowe dla skryptów U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Wdrażanie bazy danych U-SQL za pośrednictwem potoków platformy Azure

`PackageDeploymentTool.exe`udostępnia interfejsy programowania i wiersza polecenia, które pomagają we wdrażaniu pakietów wdrażania bazy danych U-SQL, **.usqldbpack**. Pakiet SDK znajduje się w [pakiecie NuGet zestawu U-SQL SDK,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)znajdującym się pod adresem **build/runtime/PackageDeploymentTool.exe**. Za `PackageDeploymentTool.exe`pomocą programu można wdrożyć bazy danych U-SQL zarówno usługi Azure Data Lake Analytics, jak i kont lokalnych.

> [!NOTE]
>
> Obsługa wiersza polecenia programu PowerShell i obsługa zadań wydania usługi Azure Pipelines dla wdrożenia bazy danych U-SQL jest obecnie w toku.
>

Aby skonfigurować zadanie wdrażania bazy danych w usłudze Azure Pipelines, należy wykonać następujące kroki:

1. Dodaj zadanie skryptu programu PowerShell w potoku kompilacji lub wydania i wykonaj następujący skrypt programu PowerShell. To zadanie pomaga uzyskać zależności zestawów `PackageDeploymentTool.exe` `PackageDeploymentTool.exe`SDK platformy Azure dla i . Można ustawić parametry **-AzureSDK** i **-DBDeploymentTool,** aby załadować zależności i narzędzie wdrażania do określonych folderów. Przekaż ścieżkę **-AzureSDK** jako `PackageDeploymentTool.exe` **parametr -AzureSDKPath** w kroku 2. 

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

2. Dodaj **zadanie wiersza polecenia** w potoku kompilacji lub `PackageDeploymentTool.exe`wydania i wypełnij skrypt, wywołując program . `PackageDeploymentTool.exe`znajduje się w folderze zdefiniowanego **$DBDeploymentTool.** Przykładowy skrypt jest następujący: 

    * Wdrażanie bazy danych U-SQL lokalnie:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Użyj trybu uwierzytelniania interaktywnego, aby wdrożyć bazę danych U-SQL na koncie usługi Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Użyj uwierzytelniania tajnego, aby wdrożyć bazę danych U-SQL na **koncie** usługi Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Użyj **uwierzytelniania certFile,** aby wdrożyć bazę danych U-SQL na koncie usługi Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Opisy parametrów PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Wspólne parametry

| Parametr | Opis | Wartość domyślna | Wymagany |
|---------|-----------|-------------|--------|
|Pakiet|Ścieżka pakietu wdrażania bazy danych U-SQL do wdrożenia.|wartość null|true|
|baza danych|Nazwa bazy danych, która ma zostać wdrożona lub utworzona.|master|false|
|Logfile|Ścieżka pliku do rejestrowania. Domyślnie wyjście standardowe (konsola).|wartość null|false|
|LogLevel|Poziom dziennika: pełny, normalny, ostrzeżenie lub błąd.|LogLevel.Normal (LogLevel.Normal)|false|

#### <a name="parameter-for-local-deployment"></a>Parametr dla wdrożenia lokalnego

|Parametr|Opis|Wartość domyślna|Wymagany|
|---------|-----------|-------------|--------|
|DataRoot|Ścieżka lokalnego folderu głównego danych.|wartość null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametry wdrożenia usługi Azure Data Lake Analytics

|Parametr|Opis|Wartość domyślna|Wymagany|
|---------|-----------|-------------|--------|
|Konto|Określa, które konto usługi Azure Data Lake Analytics ma być wdrażane według nazwy konta.|wartość null|true|
|ResourceGroup|Nazwa grupy zasobów platformy Azure dla konta usługi Azure Data Lake Analytics.|wartość null|true|
|SubscriptionId|Identyfikator subskrypcji platformy Azure dla konta usługi Azure Data Lake Analytics.|wartość null|true|
|Dzierżawa|Nazwa dzierżawy to nazwa domeny usługi Azure Active Directory (Azure AD). Znajdź go na stronie zarządzania subskrypcją w witrynie Azure portal.|wartość null|true|
|AzureSDKPath|Ścieżka do wyszukiwania zestawów zależnych w usłudze Azure SDK.|wartość null|true|
|Interaktywne|Określa, czy do uwierzytelniania ma być używany tryb interaktywny.|false|false|
|ClientId|Identyfikator aplikacji usługi Azure AD wymagany do uwierzytelniania nieinterakcyjnego.|wartość null|Wymagane do uwierzytelniania nieinterakcyjnego.|
|Wydzielanie|Funkcja tajnego lub hasła do uwierzytelniania nieinterakcyjnego. Powinien być używany tylko w zaufanym i bezpiecznym środowisku.|wartość null|Wymagane do uwierzytelniania nieinterakcyjnego lub użyć programu SecreteFile.|
|Plik tajnego|Plik zapisuje klucz tajny lub hasło do uwierzytelniania nieinterakcyjnego. Upewnij się, że jest czytelny tylko dla bieżącego użytkownika.|wartość null|Wymagane do uwierzytelniania nieinterakcyjnego lub użyć funkcji Secrete.|
|Certfile|Plik zapisuje certyfikat X.509 dla uwierzytelniania nieinterakcyjnego. Domyślnie jest użycie uwierzytelniania tajnego klienta.|wartość null|false|
| Poprawka do zadania | Prefiks do wdrażania bazy danych zadania DDL U-SQL. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>Następne kroki

- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md).
- [Programowanie bazy danych U-SQL za pomocą projektu bazy danych U-SQL.](data-lake-analytics-data-lake-tools-develop-usql-database.md)
