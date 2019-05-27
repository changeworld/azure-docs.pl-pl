---
title: Programowe monitorowanie usługi Azure data factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować potok w fabryce danych za pomocą różnych software development Kit (SDK).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 035e12da67d28e8e3fb46ac295717dd6b579922c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66167057"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programowe monitorowanie usługi Azure data factory
W tym artykule opisano sposób monitorowania potoku w fabryce danych przy użyciu różnych software development Kit (SDK). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Zakres danych

Fabryka danych przechowuje tylko po danych uruchomieniom 45 dni. Podczas wysyłania zapytania programowo uzyskać dane dotyczące uruchomienia potoku fabryki danych — na przykład za pomocą polecenia PowerShell `Get-AzDataFactoryV2PipelineRun` — istnieją maksymalna dat dla opcjonalnego `LastUpdatedAfter` i `LastUpdatedBefore` parametrów. Ale jeśli zapytania dla danych przez ostatni rok, na przykład, zapytanie nie zwraca błędu, ale tylko zwraca potoku wykonywania dane z ostatnich 45 dni.

Jeśli chcesz zachować uruchomieniom danych przez ponad 45 dni, należy skonfigurować własne rejestrowania diagnostycznego z [usługi Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Aby uzyskać szczegółowy przewodnik tworzenia i monitorowanie potoku przy użyciu zestawu .NET SDK, zobacz [utworzyć fabrykę danych i potoku przy użyciu platformy .NET](quickstart-create-data-factory-dot-net.md).

1. Dodaj następujący kod, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

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

2. Dodaj następujący kod do tego pobiera szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

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

Aby uzyskać pełną dokumentację zestawu SDK platformy .NET, zobacz [odwołanie do zestawu SDK .NET usługi Data Factory](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Aby uzyskać szczegółowy przewodnik tworzenia i monitorowanie potoku przy użyciu zestawu SDK języka Python, zobacz [utworzyć fabrykę danych i potoku przy użyciu języka Python](quickstart-create-data-factory-python.md).

Aby monitorować uruchomienie potoku, Dodaj następujący kod:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Aby uzyskać pełną dokumentację zestawu SDK języka Python, zobacz [odwołanie do zestawu SDK Python usługi Data Factory](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>Interfejs API REST
Aby uzyskać szczegółowy przewodnik tworzenia i monitorowanie potoku przy użyciu interfejsu API REST, zobacz [utworzyć fabrykę danych i potoku przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md).
 
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

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Aby uzyskać pełną dokumentację interfejsu API REST, zobacz [odwołanie do interfejsu API REST usługi fabryka danych](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Aby uzyskać szczegółowy przewodnik tworzenia i monitorowanie potoku przy użyciu programu PowerShell, zobacz [utworzyć fabrykę danych i potoku przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md).

1. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

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
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Aby uzyskać pełną dokumentację poleceń cmdlet programu PowerShell, zobacz [Dokumentacja poleceń cmdlet programu PowerShell do fabryki danych](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Kolejne kroki
Zobacz [monitorowania potoków przy użyciu usługi Azure Monitor](monitor-using-azure-monitor.md) artykuł, aby dowiedzieć się więcej o korzystaniu z usługi Azure Monitor do monitorowania potoków usługi Data Factory. 

