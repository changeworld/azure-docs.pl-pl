---
title: Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania usługi Azure Data Lake Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ciągłą integrację i ciągłe wdrażanie dla usługi Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: c069bc2a6147a021ea9bdf37e2926d5c8f33281c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145013"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania usługi Azure Data Lake Analytics

W tym dokumencie dowiesz się, jak skonfigurować potok ciągłej integracji/ciągłego Dostarczania dla zadań U-SQL i bazy danych U-SQL.

## <a name="cicd-for-u-sql-job"></a>Ciągła Integracja/ciągłe dostarczanie dla zadania U-SQL

Usługa Azure Data Lake Tools for Visual Studio zawiera typ projektu U-SQL, który pomaga organizować skryptów U-SQL. Za pomocą projektu U-SQL do zarządzania kodem języka U-SQL sprawia, że w dalszych scenariuszach ciągłej integracji/ciągłego Dostarczania łatwe.

## <a name="build-u-sql-project"></a>Kompiluj projekt U-SQL

Można skompilować projekt U-SQL za pomocą narzędzia MSBuild, przekazując odpowiednie parametry. Wykonaj poniższe kroki, aby skonfigurować proces kompilacji dla projektów języka U-SQL.

### <a name="project-migration"></a>Migracja projektu

Przed skonfigurowaniem zadania kompilacji dla projektu U-SQL, upewnij się, że używasz najnowszej wersji projekt U-SQL. Otwórz plik projektu U-SQL w edytorze i sprawdź, czy poniższe elementy importu:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Jeśli nie, dostępne są dwie opcje, aby migrować projekt:

- Opcja 1: Zmiana, stare Importuj element do przedstawionego powyżej.
- Opcja 2: Otwórz stary projekt w usłudze Azure Data Lake Tools for Visual Studio po wersji 2.3.3000.0. Stary szablon projektu zostanie automatycznie uaktualniony do najnowszej wersji. Nowy projekt utworzony po wersji 2.3.3000.0 używa nowego szablonu bezpośrednio.

### <a name="get-nuget-package"></a>Pobierz pakiet NuGet

Program MSBuild nie zapewnia wbudowaną obsługę typów projektów języka U-SQL. Aby dodać tę możliwość, musisz dodać odwołanie do rozwiązania w celu [pakietu Microsoft.Azure.DataLake.USQL.SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) dodająca usługa językowa wymagane.

Aby dodać odwołanie do pakietu NuGet, kliknij prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań i wybierz polecenie **Zarządzaj pakietami NuGet**. Lub można dodać plik o nazwie `packages.config` w folderze rozwiązania i Dodaj poniższe zawartości do niego.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Zarządzanie odwołaniami do bazy danych U-SQL

Jeśli skryptów U-SQL projektu U-SQL ma instrukcji kwerendy dla obiektów bazy danych U-SQL, na przykład wysłać zapytanie do tabeli U-SQL lub odwołanie do zestawu, należy odwoływać się do odpowiedniego projektu bazy danych U-SQL, który zawiera definicję te obiekty przed kompilowanie ten projekt U-SQL.

[Dowiedz się więcej na temat projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
>Projekt bazy danych U-SQL jest obecnie w publicznej wersji zapoznawczej. Jeśli masz instrukcja DROP w projekcie, kompilacja kończy się niepowodzeniem. Instrukcja DROP będą miały wkrótce.
>

### <a name="build-u-sql-project-with-msbuild-command-line"></a>Kompiluj projekt U-SQL przy użyciu wiersza polecenia programu MSBuild

Po migracji projektu i pobieranie pakietu NuGet, można wywołać standardowego wiersza polecenia programu MSBuild z dodatkowymi argumentami poniżej, aby skompilować projekt U-SQL:

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Definicja argumentów i wartości są:

* USQLSDKPath = < pakietu Nuget języka U-SQL > \build\runtime: ten parametr odwołuje się do ścieżki instalacji pakietu NuGet usługi języka U-SQL.
* USQLTargetType = scalania lub SyntaxCheck:
    * Scalanie: Tryb scalania kompiluje pliki związane z kodem, takich jak CS, PY i .r plików i inlines wynikowy biblioteka kodów zdefiniowanych przez użytkownika (np. biblioteki dll pliku binarnego, Python lub R code) do skryptu U-SQL.
    * SyntaxCheck: Tryb SyntaxCheck najpierw scala plików z kodem skryptu U-SQL, a następnie kompiluje skrypt U-SQL, aby zweryfikować swój kod.
* Ścieżka folderu DataRoot =<DataRoot path>: ścieżka folderu DataRoot jest niezbędny tylko w trybie SyntaxCheck. Podczas tworzenia skryptów w trybie SyntaxCheck, program MSBuild sprawdza, czy odwołania w skrypcie do obiektów bazy danych. Upewnij się skonfigurować zgodne środowisko lokalne, które zawiera obiekty z bazy danych U-SQL na maszynie kompilacji folderu DataRoot przed kompilacją. Można również zarządzać te zależności bazy danych przez [odwołanie do projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Należy pamiętać, że program MSBuild sprawdza tylko odwołania do obiektów bazy danych, nie pliki.
* EnableDeployment = true lub false: EnableDeployment wskazuje na to, jeśli istnieje możliwość Wdróż przywoływane bazy danych U-SQL podczas procesu kompilacji. Jeśli można odwoływać się do projektu bazy danych U-SQL i korzystać z obiektów bazy danych w skrypcie języka U-SQL, należy ustawić ten parametr na wartość true.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Ciągła Integracja z usługą Visual Studio Team Service

Oprócz wiersza polecenia klienci mogą używać również zadania kompilacji usługi Visual Studio lub programu MSBuild do kompilowania projektów języka U-SQL w programie Visual Studio Team Service. Aby skonfigurować zadanie kompilacji, upewnij się, że:

1.  Dodaj zadanie przywracania NuGet w celu uzyskania rozwiązania odwołanie do pakietu NuGet w tym `Azure.DataLake.USQL.SDK`, dzięki czemu program MSBuild można znaleźć elementy docelowe języka U-SQL. Ustaw **Zaawansowane > katalog docelowy** jako `$(Build.SourcesDirectory)/packages` Jeśli chcesz korzystać z próbki argumenty MSBuild bezpośrednio w kroku 2.

    ![Zadanie programu MSBuild CD CI Lake zestawu danych dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Zadanie Nuget CD CI Ustaw jeziora danych dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Argumenty MSBuild zestawu, na które można ustawić argumenty w zadaniu kompilacji programu Visual Studio lub programu MSBuild, takich jak poniżej, lub zmienne dla tych argumentów można zdefiniować w definicji kompilacji usługi VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

    ![Dane Lake Ustaw CI CD MSBuild zmienne dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>Dane wyjściowe kompilacji projektu U-SQL

Po uruchomieniu kompilacji, wszystkie skrypty w projekcie języka U-SQL tworzone i wyświetlona w pliku zip o nazwie `USQLProjectName.usqlpack`. Strukturę folderów w projekcie zostaną zachowane w danych wyjściowych kompilacji zip.

>[!NOTE]
>
>Kod związany z pliku dla każdego skryptu U-SQL zostanie scalona jako wbudowany instrukcję, aby dane wyjściowe kompilacji skryptu.
>

## <a name="test-u-sql-script"></a>Testowanie skryptu U-SQL

Usługa Azure Data Lake udostępnia projekty testowe dla skryptu U-SQL i C# operatory zdefiniowane przez użytkownika/UDAG/funkcji zdefiniowanej przez użytkownika:
* [Dowiedz się, jak dodać przypadki testowe dla skryptu U-SQL i rozszerzonego kodu C#](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [Dowiedz się, jak uruchamiać te przypadki testowe w Visual Studio Team Service](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>Wdrażanie zadania U-SQL

Po zweryfikowaniu kodu za pomocą kompilacji i testowania procesu, można przesłać zadań U-SQL bezpośrednio z programu Visual Studio Team Service za pośrednictwem **zadań programu Azure PowerShell**. Można także wdrożyć skrypt do usługi Azure Data Lake Store/Azure Blob Storage i [uruchamiać zaplanowane zadania za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>Przesyłanie zadań U-SQL przy użyciu programu Visual Studio Team Service

Dane wyjściowe kompilacji, za pomocą projektu U-SQL jest plikiem zip o nazwie **USQLProjectName.usqlpack** obejmuje wszystkie skrypty U-SQL w projekcie. Możesz użyć [Azure PowserShell zadania w Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) za pomocą poniższego skryptu PowserShell próbki, aby przesłać U-SQL zadania bezpośrednio z programu Visual Studio Team Service kompilacji lub potoku wydania.

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

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
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

Oprócz przesyłania zadań U-SQL bezpośrednio z programu Visual Studio Team Service, możesz również przekazać utworzonych skryptów usługi Azure Data Lake Store/Azure Blob Storage i [uruchamiać zaplanowane zadania za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Użyj [zadań programu Azure PowerShell w programie Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) za pomocą poniżej przykładowy skrypt programu PowerShell, aby przekazać U-SQL skryptów do konta usługi Azure Data Lake Store.

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
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
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
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
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

## <a name="cicd-for-u-sql-database"></a>Ciągła Integracja/ciągłe dostarczanie dla bazy danych U-SQL

Usługa Azure Data Lake Tools for Visual Studio zawiera szablon projektu bazy danych U-SQL ułatwia deweloperom tworzenie, zarządzanie i wdrażanie bazy danych U-SQL, szybkie i łatwe. [Dowiedz się więcej na temat projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

### <a name="get-nuget-package"></a>Pobierz pakiet NuGet

Program MSBuild nie zapewnia wbudowaną obsługę typów projektów bazy danych U-SQL. Aby dodać tę możliwość, musisz dodać odwołanie do rozwiązania w celu [pakietu Microsoft.Azure.DataLake.USQL.SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) dodająca usługa językowa wymagane.

Aby dodać odwołanie do pakietu NuGet, można kliknąć prawym przyciskiem myszy rozwiązanie w Eksploratorze rozwiązań i wybierz **Zarządzaj pakietami NuGet** rozwiązania, następnie wyszukaj i zainstaluj pakiet NuGet. Lub możesz dodać plik o nazwie "packages.config" w folderze rozwiązania i dodać poniżej zawartości do niego.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>Tworzenie projektu bazy danych U-SQL przy użyciu wiersza polecenia programu MSBuild

Można wywołać standardowych wiersza polecenia programu MSBuild i przekaż jako dodatkowy argument odwołania pakietu NuGet zestawu SDK U-SQL, takich jak poniżej do kompilowania projektu bazy danych U-SQL:

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argumenty `USQLSDKPath=<U-SQL Nuget package>\build\runtime` odnosi się do ścieżki instalacji pakietu NuGet usługi języka U-SQL.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Ciągła Integracja z usługą Visual Studio Team Service

Oprócz wiersza polecenia, można także używać **Visual Studio Build** lub **zadanie MSBuild** do tworzenia projektów bazy danych U-SQL w programie Visual Studio Team Service. Aby skonfigurować zadanie kompilacji, upewnij się, że:

1.  Dodaj zadanie przywracania NuGet w celu uzyskania rozwiązania odwołanie do pakietu NuGet w tym `Azure.DataLake.USQL.SDK`, dzięki czemu program MSBuild można znaleźć elementy docelowe języka U-SQL. Ustaw **Zaawansowane > katalog docelowy** jako `$(Build.SourcesDirectory)/packages` Jeśli chcesz korzystać z próbki argumenty MSBuild bezpośrednio w kroku 2.

    ![Zadanie programu MSBuild CD CI Lake zestawu danych dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Zadanie Nuget CD CI Ustaw jeziora danych dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Argumenty MSBuild zestawu, na które można ustawić argumenty w zadaniu kompilacji programu Visual Studio lub programu MSBuild, takich jak poniżej, lub zmienne dla tych argumentów można zdefiniować w definicji kompilacji usługi VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```

    ![Usługa Data Lake ustawiania zmiennych MSBuild ciągłe wdrażanie elementu konfiguracji dla projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>Wynik kompilacji projektu bazy danych U-SQL

Dane wyjściowe projektu bazy danych U-SQL jest pakiet wdrożeniowy bazy danych U-SQL, nazwę z sufiksem kompilacji `.usqldbpack`. `.usqldbpack` Pakiet jest plik zip zawiera wszystkich instrukcji DDL w jednego skryptu U-SQL w folderze DDL i wszystkich bibliotek DLL i dodatkowe pliki dla zestawów w folderze Temp.

## <a name="test-table-valued-function-and-stored-procedure"></a>Testowanie funkcji zwracającej tabelę i procedury składowanej

W tej chwili bezpośrednio Dodawanie przypadków testowych dla funkcji z wartościami przechowywanymi w tabeli i procedur składowanych nie jest obsługiwane. Obejść ten problem można utworzyć projektu U-SQL, który ma skryptów U-SQL, wywoływanie tych funkcji i zapisu przypadków testowych dla nich. Wykonaj poniższe kroki, aby skonfigurować przypadki testowe dla funkcji z wartościami przechowywanymi w tabeli i procedur składowanych, zdefiniowane w projekcie bazy danych U-SQL:

1.  Utwórz projekt U-SQL w celu badania i pisanie skryptów U-SQL podczas wywoływania funkcji z wartościami przechowywanymi w tabeli i procedur składowanych.
2.  Dodaj odwołanie do bazy danych do tego projektu U-SQL. Aby uzyskać funkcji zwracającej tabelę i definicji procedury składowanej, konieczne będzie odwoływać się do projektu bazy danych, który zawiera instrukcję DDL. [Dowiedz się więcej o odwołanie do bazy danych](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Dodaj przypadki testowe dla skryptów U-SQL, które wywołują funkcje zwracające tabele i procedury składowane. [Dowiedz się, jak dodać przypadki testowe dla skryptu U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Wdrażanie bazy danych U-SQL za pomocą usługi Visual Studio Team

`PackageDeploymentTool.exe` udostępnia programowania i interfejsów z wierszem polecenia, które ułatwiają wdrażanie package(.usqldbpack) wdrożenia bazy danych U-SQL. Zestaw SDK jest uwzględniony w [pakietu NuGet zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), lokalizowania na build/runtime/PackageDeploymentTool.exe. Za pomocą `PackageDeploymentTool.exe`, można wdrożyć bazy danych U-SQL do usługi Azure Data Lake Analytics i konto lokalne.

>[!NOTE]
>
>Polecenie programu PowerShell, obsługę wiersza i Visual Studio Team Service wersjami obsługi zadań do wdrożenia bazy danych U-SQL jest już w drodze.
>

Wykonaj poniższe kroki, aby skonfigurować zadania wdrożenia bazy danych w Visual Studio Team Service:

1. Dodaj zadanie skrypt programu PowerShell w kompilacji lub potoku wydania i wykonanie poniższego skryptu programu PowerShell. To zadanie pozwala uzyskać zależności zestawu Azure SDK `PackageDeploymentTool.exe` i `PackageDeploymentTool.exe`. Można ustawić parametry - AzureSDK i - DBDeploymentTool można załadować zależności i narzędzia wdrażania niektórych określonych folderów. Przekaż AzureSDK — ścieżka do `PackageDeploymentTool.exe` jako parametr - AzureSDKPath w kroku 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL dabatase deployment tool
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

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Dodaj **zadania wiersza polecenia** w kompilacji lub wydania, potoku i wypełnij wywołanie skryptu `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` znajduje się w folderze $DBDeploymentTool zdefiniowane. Przykładowy skrypt jest następująca: 

    * Wdrażanie lokalne bazy danych U-SQL

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Wdrażanie bazy danych U-SQL na konta usługi Azure Data Lake Analytics za pomocą trybu interakcyjnego uwierzytelniania:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Użyj secrete uwierzytelniania do wdrożenia bazy danych U-SQL do konta usługi Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Wdrażanie bazy danych U-SQL do konta usługi Azure Data Lake Analytics za pomocą uwierzytelniania certFile:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**Opis parametru PackageDeploymentTool.exe:**

**Wspólne parametry:**

|Parametr|Opis|Wartość domyślna|Wymagane|
|---------|-----------|-------------|--------|
|Pakiet|Ścieżka pakietu wdrażania bazy danych U-SQL do wdrożenia|Wartość null|true|
|Database (Baza danych)|Nazwa do wdrożenia bazy danych / lub utworzone|master|false|
|Plik dziennika|Ścieżka pliku do rejestrowania, domyślnie standard out (Konsola)|Wartość null|false|
|LogLevel|Poziom dziennika: pełne, ostrzeżenie, normalna błąd|LogLevel.Normal|false|

**Parametr wdrożenia lokalnego:**

|Parametr|Opis|Wartość domyślna|Wymagane|
|---------|-----------|-------------|--------|
|DataRoot|Ścieżka folderu lokalnego katalogu głównego danych|Wartość null|true|

**Parametr wdrożenia usługi Azure Data Lake Analytics:**

|Parametr|Opis|Wartość domyślna|Wymagane|
|---------|-----------|-------------|--------|
|Konto|Określa, wdrażania, do którego nazwy przez konto usługi Azure Data Lake Analytics|Wartość null|true|
|ResourceGroup|Nazwa grupy zasobów platformy Azure dla konta usługi Azure Data Lake Analytics|Wartość null|true|
|SubscriptionId|Identyfikator subskrypcji platformy Azure dla konta usługi Azure Data Lake Analytics|Wartość null|true|
|Dzierżawa|Nazwa dzierżawy (nazwa domeny katalogu usługi AAD, można znaleźć go na stronie zarządzania subskrypcją w witrynie Azure portal)|Wartość null|true|
|AzureSDKPath|Ścieżki wyszukiwania zestawów zależnych w zestawie Azure SDK|Wartość null|true|
|Interaktywne|Należy użyć trybu interakcyjnego uwierzytelniania lub nie|false|false|
|ClientId|Identyfikator aplikacji usługi AAD dla żadnego wymagane dla żadnego uwierzytelnianie interaktywne uwierzytelnianie interakcyjne|Wartość null|wymagane dla żadnego przeprowadzić uwierzytelnianie interakcyjne|
|Secrete|Secrete/hasło dla żadnego uwierzytelnianie interakcyjne go należy używać tylko w środowisku zaufanych secure|Wartość null|wymagane dla żadnego przeprowadzić uwierzytelnianie interakcyjne lub użyj SecreteFile|
|SecreteFile|Plik jest zapisywany secrete hasła dla żadnego przeprowadzić uwierzytelnianie interakcyjne, upewnij się utrzymać ją tylko do odczytu przez bieżącego użytkownika|Wartość null|wymagane dla żadnego przeprowadzić uwierzytelnianie interakcyjne lub użyj Secrete|
|CertFile|Plik jest zapisywany certyfikacji X.509 dla żadnego przeprowadzić uwierzytelnianie interakcyjne, domyślna ma używać klienta secrete uwierzytelniania|Wartość null|false|
|JobPrefix|Prefiks do wdrożenia bazy danych U-SQL DDL zadania|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>Następne kroki

- [Jak przetestować kod usługi Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
- [Użyj projektu bazy danych U-SQL do tworzenia bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
