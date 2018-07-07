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
ms.openlocfilehash: a55c8fd95409de4fb1ea725d234de907f79d3c7b
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889606"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania usługi Azure Data Lake Analytics

W tym dokumencie dowiesz się, jak skonfigurować potok ciągłej integracji/ciągłego Dostarczania dla zadań U-SQL i bazy danych U-SQL.

## <a name="cicd-for-u-sql-job"></a>Ciągła Integracja/ciągłe dostarczanie dla zadania U-SQL

Usługa Azure Data Lake Tools for Visual Studio zawiera typ projektu U-SQL, który pomoże Ci skrypty orgnize U-SQL. Za pomocą projektu U-SQL do zarządzania kodem języka U-SQL sprawia, że w dalszych scenariuszach ciągłej integracji/ciągłego Dostarczania łatwe.

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
  <package id="Microsoft.Azure.DataLake.USQL.SDK" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Zarządzanie odwołaniami do bazy danych U-SQL

Jeśli skryptów U-SQL projektu U-SQL ma instrukcji kwerendy dla obiektów bazy danych U-SQL, na przykład wysłać zapytanie do tabeli U-SQL lub odwołanie do zestawu, należy odwoływać się do odpowiedniego projektu bazy danych U-SQL, który zawiera definicję te obiekty przed kompilowanie ten projekt U-SQL.

[Dowiedz się więcej na temat projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

### <a name="build-u-sql-project-with-msbuild-command-line"></a>Kompiluj projekt U-SQL przy użyciu wiersza polecenia programu MSBuild

Po migracji projektu i pobieranie pakietu NuGet, można wywołać standardowego wiersza polecenia programu MSBuild z dodatkowymi argumentami poniżej, aby skompilować projekt U-SQL:

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder
``` 

Definicja argumentów i wartości są:

* USQLSDKPath = < pakietu Nuget języka U-SQL > \build\runtime: ten parametr odwołuje się do ścieżki instalacji pakietu NuGet usługi języka U-SQL.
* USQLTargetType = scalania lub SyntaxCheck:
    * Scalanie: Tryb scalania kompiluje pliki związane z kodem, takich jak CS, PY i .r plików i inlines wynikowy biblioteka kodów zdefiniowanych przez użytkownika (np. biblioteki dll pliku binarnego, Python lub R code) do skryptu U-SQL.
    * SyntaxCheck: Tryb SyntaxCheck najpierw scala plików z kodem skryptu U-SQL, a następnie kompiluje skrypt U-SQL, aby zweryfikować swój kod.
* Ścieżka folderu DataRoot =<DataRoot path>: ścieżka folderu DataRoot jest niezbędny tylko w trybie SyntaxCheck. Podczas tworzenia skryptów w trybie SyntaxCheck, program MSBuild sprawdza, czy odwołania w skrypcie do obiektów bazy danych. Upewnij się skonfigurować zgodne środowisko lokalne, które zawiera obiekty z bazy danych U-SQL na maszynie kompilacji folderu DataRoot przed kompilacją. Można również zarządzać te zależności bazy danych przez [odwołanie do projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Należy pamiętać, że program MSBuild sprawdza tylko odwołania do obiektów bazy danych, nie pliki.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Ciągła Integracja z usługą Visual Studio Team Service

Oprócz wiersza polecenia klienci mogą używać również zadania kompilacji usługi Visual Studio lub programu MSBuild do kompilowania projektów języka U-SQL w programie Visual Studio Team Service. Aby skonfigurować zadanie kompilacji, upewnij się, że:

1.  Dodaj zadanie przywracania NuGet w celu uzyskania rozwiązania odwołanie do pakietu NuGet w tym `Azure.DataLake.USQL.SDK`, dzięki czemu program MSBuild można znaleźć elementy docelowe języka U-SQL. 

    ![Zadanie programu MSBuild CD CI Lake zestawu danych dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

2.  Argumenty MSBuild zestawu, na które można ustawić argumenty w zadaniu kompilacji programu Visual Studio lub programu MSBuild, takich jak poniżej, lub zmienne dla tych argumentów można zdefiniować w definicji kompilacji usługi VSTS.

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/<your project name>/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.1019-preview/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory)
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
param(
    [Parameter(Mandatory=$true)][string]$AnalyticsAccountName, #ADLA account name
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, #Root folder (e.g. artifacts root folder)
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

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
        # [System.IO.Compression.ZipFile]::ExtractToDirectory($USQLPackfile, $UnzipOutput, 0)
        # $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
        # Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue | Where-Object {$_.DirectoryName -match $subFolder}

    return $USQLFiles
}

Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."
    # submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "($usqlFiles.IndexOf())Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $AnalyticsAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        LogJobInformation $jobToSubmit
        
        Write-Output "waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $AnalyticsAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
        
        # ProcessResult $jobResult
    }
}

Function ProcessResult($jobResult)
{
    if ($jobResult.Result -eq "Failed")
    {
        Write-Error "Job Failed. Job Id: $($jobResult.JobId), Job Name: $($jobResult.Name), Log: $($jobResult.LogFolder)"
    }
    else
    {
        Write-Output "Job Succeeded. Job Id: $($jobResult.JobId), Job Name: $($jobResult.Name)"
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

    Write-Output "Starting USQL script deployment..."

    # Submit ADLA jobs with usql scripts in given sub-folder.
    # Order is important here. Scripts with least dependency goes first followed 
    # by scripts which more dependencies.
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Wdrażanie zadań U-SQL przy użyciu usługi Azure Data Factory

Oprócz przesyłania zadań U-SQL bezpośrednio z programu Visual Studio Team Service, możesz również przekazać utworzonych skryptów usługi Azure Data Lake Store/Azure Blob Storage i [uruchamiać zaplanowane zadania za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Użyj [zadań programu Azure PowerShell w programie Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) za pomocą poniżej przykładowy skrypt programu PowerShell, aby przekazać U-SQL skryptów do konta usługi Azure Data Lake Store.

```powershell
param(
    [Parameter(Mandatory=$true)][string]$ADLSName, #ADLA account name
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot #Root folder (e.g. artifacts root folder)
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading DLL files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles
    Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $usqlScripts.FullName -Destination "/ScriptResource2/$usqlScripts" -Force -Recurse
    # $files = @(get-childitem $usqlScripts -recurse)
    # foreach($file in $files)
    # {
    #    Write-Host "Uploading file: $($file.Name)"
    #    Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/ScriptResource/$file" -Force -Recurse
    # }
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

    return $UnzipOutput
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

1.  Dodaj zadanie przywracania NuGet w celu uzyskania rozwiązania odwołanie do pakietu NuGet w tym `Azure.DataLake.USQL.SDK`, dzięki czemu program MSBuild można znaleźć elementy docelowe języka U-SQL. 

    ![Zadanie MSBuild CD CI zestaw Data Lake dla projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

2.  Argumenty MSBuild zestawu, na które można ustawić argumenty w zadaniu kompilacji programu Visual Studio lub programu MSBuild, takich jak poniżej, lub zmienne dla tych argumentów można zdefiniować w definicji kompilacji usługi VSTS.

```
/p:USQLSDKPath=$(Build.SourcesDirectory)/<your project name>/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.1019-preview/build/runtime
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

1. Dodaj zadanie skrypt programu PowerShell w kompilacji lub potoku wydania i wykonanie poniższego skryptu programu PowerShell. To zadanie pozwala uzyskać zależności zestawu Azure SDK `PackageDeploymentTool.exe`. Można ustawić parametru - outputfolder załadować te zależności, aby niektóre określonego folderu. Ścieżka tego folderu do przekazania `PackageDeploymentTool.exe` w kroku 2. 

    ```powershell
    param (
        [string]$outputfolder = "RequiredDll",
        [string]$workingfolder = ""
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

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force

    echo "Copy required DLLs to output folder..."

    mkdir $outputfolder -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $outputfolder
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $outputfolder
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $outputfolder
    ```

2. Dodaj **zadania wiersza polecenia** w kompilacji lub wydania, potoku i wypełnij wywołanie skryptu `PackageDeploymentTool.exe`. Przykładowy skrypt jest następująca: 

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