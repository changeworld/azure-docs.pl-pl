---
title: Programowo monitorować fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować potok w fabryce danych przy użyciu różnych software development Kit (SDK).
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 5b7b473bdc411e11d6d1c90c63c4f05c6dac3280
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619606"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programowo monitorować fabryki danych Azure
W tym artykule opisano, jak monitorować potok w fabryce danych przy użyciu różnych software development Kit (SDK). 

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [monitorowanie i zarządzanie nimi potoków w fabryce danych version1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="data-range"></a>Zakres danych

Fabryka danych przechowuje dane tylko potoku Uruchom danych 45 dni. Można pobrać programowo dla danych dotyczących uruchamia potoku fabryki danych — na przykład przy użyciu polecenia programu PowerShell `Get-AzureRmDataFactoryV2PipelineRun` -Brak maksymalnej dat dla opcjonalnego `LastUpdatedAfter` i `LastUpdatedBefore` parametrów. Ale jeśli zapytania dotyczące danych przez ostatni rok, na przykład zapytanie nie zwraca błąd, ale zwraca tylko potoku wykonywania dane z ostatnich 45 dni.

Jeśli chcesz utrwalić potoku Uruchom danych przez więcej niż 45 dni, skonfigurować własne rejestrowania diagnostycznego z [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Aby uzyskać pełny Przewodnik tworzenia i monitorowania potoku przy użyciu zestawu .NET SDK, zobacz [tworzenie fabryki danych i przy użyciu platformy .NET w potoku](quickstart-create-data-factory-dot-net.md).

1. Dodaj następujący kod, aby stale sprawdzić stan potoku uruchomić do momentu zakończenia kopiowania danych.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Dodaj następujący kod do tego pobiera kopię uruchamiania działania szczegółowe informacje, na przykład rozmiar danych odczytana/zapisana.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Pełna dokumentacja zestawu .NET SDK, zobacz [odwołania do zestawu SDK .NET fabryki danych](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Aby uzyskać pełny Przewodnik tworzenia i monitorowania przy użyciu zestawu SDK Python potoku, zobacz [tworzenie fabryki danych i potoku używany język Python](quickstart-create-data-factory-python.md).

Do monitorowania Uruchom potoku, Dodaj następujący kod:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Pełna dokumentacja zestawu SDK Python, zobacz [odwołania do zestawu SDK Python fabryki danych](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>Interfejs API REST
Aby uzyskać pełny Przewodnik tworzenia i monitorowania potoku przy użyciu interfejsu API REST, zobacz [tworzenie fabryki danych i potoku przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md).
 
1. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Uruchom następujący skrypt, aby pobrać szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Pełna dokumentacja interfejsu API REST, zobacz [dokumentacji interfejsu API REST fabryki danych](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Aby uzyskać pełny Przewodnik tworzenia i monitorowania potoku przy użyciu programu PowerShell, zobacz [tworzenie fabryki danych i przy użyciu programu PowerShell w potoku](quickstart-create-data-factory-powershell.md).

1. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Uruchom następujący skrypt, aby pobrać szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Pełna dokumentacja poleceń cmdlet programu PowerShell, zobacz [dokumentacji poleceń cmdlet programu PowerShell fabryki danych](/powershell/module/azurerm.datafactoryv2/?view=azurermps-4.4.1).

## <a name="next-steps"></a>Kolejne kroki
Zobacz [Monitor potoków za pomocą monitora Azure](monitor-using-azure-monitor.md) artykułu, aby dowiedzieć się więcej o Monitorze Azure do monitorowania potoki fabryki danych. 

