---
title: Monitorowanie partii z usługą Azure Application Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i Instrumentacja aplikacji .NET usługi partia zadań Azure za pomocą biblioteki Azure Application Insights.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 9f989ada01a2ffced509b42df9e46aa001386ab6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077398"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorowanie i debugowania aplikacji .NET usługi partia zadań Azure z usługą Application Insights

[Usługa Application Insights](../application-insights/app-insights-overview.md) zapewnia elegancki i wydajny sposób deweloperom monitora i debugowania aplikacji wdrożone do usług platformy Azure. Przy użyciu usługi Application Insights liczniki Monitora wydajności i wyjątków, a także dokumentu kodu za pomocą niestandardowych metryk i śledzenia. Integrowanie usługi Application Insights z aplikacji partii zadań Azure umożliwia wgląd w głębokie zachowania i badania problemów w czasie niemal rzeczywistym.

W tym artykule pokazano, jak dodać i skonfigurować biblioteki usługi Application Insights w ramach rozwiązania Azure partiami platformy .NET i Instrumentacja kod aplikacji. Przedstawia on także sposoby monitorowania aplikacji za pośrednictwem portalu Azure i tworzenia niestandardowych pulpitów nawigacyjnych. Do usługi Application Insights obsługuje w innych językach, obejrzyj [Dokumentacja języków, platform i integracji](../application-insights/app-insights-platforms.md).

Przykładowe C# rozwiązanie z kodem towarzyszące w tym artykule jest dostępna w [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). W tym przykładzie dodaje kod Instrumentacji usługi Application Insights, aby [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) przykład. Jeśli nie znasz tego przykładu, spróbuj najpierw TopNWords kompilowania i uruchomiona. W ten sposób pomoże poznać podstawowy przepływ pracy partii przetwarzania zestawu wejściowego obiektów blob równolegle na wielu węzłach obliczeniowych. 

## <a name="prerequisites"></a>Wymagania wstępne
* [Visual Studio IDE](https://www.visualstudio.com/vs) (Visual Studio 2015 lub nowsza wersja)

* [Konto usługi partia zadań i połączonym koncie magazynu](batch-account-create-portal.md)

* [Zasób usługi Application Insights](../application-insights/app-insights-create-new-resource.md)
  
   * Użyj portalu Azure, aby utworzyć usługę Application Insights *zasobów*. Wybierz *ogólne* **typu aplikacji**.

   * Kopiuj [klucza Instrumentacji](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key) z portalu. Jest wymagany w dalszej części tego artykułu.
  
  > [!NOTE]
  > Może być [obciążona](https://azure.microsoft.com/pricing/details/application-insights/) dla danych przechowywanych w usłudze Application Insights. Obejmuje to dane diagnostyczne i dane omówione w tym artykule monitorowania.
  > 

## <a name="add-application-insights-to-your-project"></a>Dodaj usługę Application Insights do swojego projektu

**Microsoft.ApplicationInsights.WindowsServer** pakietu NuGet i jego zależności są wymagane dla projektu. Dodaj lub przywrócić je do projektu aplikacji. Aby zainstalować pakiet, należy użyć `Install-Package` polecenia lub Menedżera pakietów NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Odwołania usługi Application Insights z poziomu aplikacji .NET przy użyciu **Microsoft.ApplicationInsights** przestrzeni nazw.

## <a name="instrument-your-code"></a>Instrumentacja kodu

Aby Instrumentacja kodu, rozwiązanie musi utworzyć usługi Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). W tym przykładzie TelemetryClient ładuje konfigurację z [ApplicationInsights.config](../application-insights/app-insights-configuration-with-applicationinsights-config.md) pliku. Pamiętaj zaktualizować ApplicationInsights.config klucz Instrumentacji usługi Application Insights w następujących projektach: Microsoft.Azure.Batch.Samples.TelemetryStartTask i TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Ponadto plik TopNWords.cs dodać klucza instrumentacji.

Przykład TopNWords.cs są używane następujące [wywołania Instrumentacji](../application-insights/app-insights-api-custom-events-metrics.md) z aplikacji interfejsu API Insights:
* `TrackMetric()` -Śledzi, jak długo, średnio węźle obliczeń przyjmuje się pobieranie pliku wymaganego tekstu.
* `TrackTrace()` -Dodaje debugowania wywołania do kodu.
* `TrackEvent()` -Śledzi interesujące zdarzenia do przechwycenia.

W tym przykładzie powoduje celowo, obsługa wyjątków. Zamiast tego usługi Application Insights automatycznie zgłasza nieobsługiwanych wyjątków, co znacznie zwiększa działanie debugowania. 

Poniższy fragment kodu przedstawia sposób użycia tych metod.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure pomocnika inicjatora telemetrii usługi partia zadań
Podczas raportowania dane telemetryczne dla danego serwera i wystąpienia, usługi Application Insights o nazwie roli maszyny Wirtualnej platformy Azure i maszyny Wirtualnej do wartości domyślnych. W kontekście partii zadań Azure przykładzie pokazano, jak nazwa puli i zamiast tego obliczeniowe nazwa węzła. Użyj [inicjatora telemetrii](../application-insights/app-insights-api-filtering-sampling.md#add-properties) Aby zastąpić wartości domyślne. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Aby włączyć inicjatora telemetrii, pliku ApplicationInsights.config w projekcie TopNWordsSample obejmuje następujące funkcje:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Zaktualizuj zadań i zadań, aby uwzględnić pliki binarne usługi Application Insights

Aby Application Insights, aby działać poprawnie na węzłów obliczeniowych upewnij się, że pliki binarne znajdują się poprawnie. Dodaj wymagane pliki binarne do kolekcji plików zasobów danego zadania, aby pobrać one pobierane w czasie, który wykonuje zadanie. Poniższe fragmenty kodu są podobne do kodu w Job.cs.

Najpierw utwórz statyczną listę plików usługi Application Insights do przekazania.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Następnie należy utworzyć pliki tymczasowe, które są używane przez zadanie.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

`FileToStage` Metody jest funkcją pomocnika w przykładowym kodzie, który umożliwia łatwe przekazywanie pliku z dysku lokalnego do obiektu blob magazynu Azure. Każdy z plików jest później pobrana węźle obliczeń i odwołuje się zadania.

Na koniec należy dodać zadania do zadania i obejmują niezbędne pliki binarne usługi Application Insights.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Wyświetlanie danych w portalu Azure

Teraz, gdy skonfigurowano zadań i zadań w celu użycia usługi Application Insights, należy uruchomić zadanie przykładzie w puli. Przejdź do portalu Azure i otwórz zasobu usługi Application Insights, które zostanie udostępnione. Po zainicjowaniu obsługi puli, należy rozpocząć przepływu danych i uzyskiwanie rejestrowane. Dalszej części tego artykułu dotyka tylko kilka funkcji usługi Application Insights, ale możesz zająć się Eksploruj pełny zestaw funkcji.

### <a name="view-live-stream-data"></a>Wyświetlanie danych strumień na żywo

Aby wyświetlić dzienniki śledzenia w zasobie Insights aplikacji, kliknij przycisk **strumień na żywo**. Poniższy zrzut ekranu przedstawia sposób wyświetlania danych na żywo pochodzące z węzłów obliczeniowych w puli, na przykład użycie procesora CPU na węźle obliczeń.

![Dane węzła obliczeń strumień na żywo](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Wyświetl dzienniki śledzenia

Aby wyświetlić dzienniki śledzenia w zasobie Insights aplikacji, kliknij przycisk **wyszukiwania**. Ten widok przedstawia listę danych diagnostycznych przechwycone przez usługę Application Insights w tym śladów, zdarzeń i wyjątki. 

Poniższy zrzut ekranu pokazuje, jak pojedyncze śledzenie zadania jest rejestrowane i później zbadać na potrzeby debugowania.

![Obraz dzienniki śledzenia](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Wyświetlić nieobsługiwanych wyjątków

Poniższe zrzuty ekranu pokazuje, jak usługi Application Insights dzienniki wyjątków zgłaszanych przez aplikację. W takim przypadku w ciągu kilku sekund z aplikacji wywołującej wyjątek, można przejść do określonego wyjątku i zdiagnozować problem.

![Nieobsługiwane wyjątki](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Czas pobierania obiektu blob miary

Metryki niestandardowe są przydatnym narzędziem w portalu. Na przykład można wyświetlić Średni czas, jaki zajęło każdy węzeł obliczeniowy, aby pobrać plik wymagany tekst, który go przetwarza.

Aby utworzyć wykres przykładowy:
1. W zasobu usługi Application Insights, kliknij przycisk **Eksploratora metryk** > **Dodaj wykres**.
2. Kliknij przycisk **Edytuj** na wykresie, która została dodana.
2. Aktualizuj szczegóły wykresu w następujący sposób:
   * Ustaw **typ wykresu** do **siatki**.
   * Ustaw **agregacji** do **średni**.
   * Ustaw **Grupuj według** do **ID. węzła**.
   * W **metryki**, wybierz pozycję **niestandardowy** > **pobieranie obiektu Blob w sekundach**.
   * Dostosuj wyświetlanie **paletę kolorów** do wyboru. 

![Czas pobierania obiektu blob na węzeł](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Monitor stale węzły obliczeniowe

Można zauważyć, że wszystkie metryki, łącznie z liczników wydajności, tylko są rejestrowane, gdy są uruchomione zadania. To zachowanie jest przydatne, ponieważ ogranicza ilość danych, które dzienniki usługi Application Insights. Gdy zawsze chcesz monitorować węzły obliczeniowe istnieją jednak przypadki. Na przykład one może być uruchomiona Praca w tle, który nie jest zaplanowane za pośrednictwem usługi partia zadań. W takim przypadku Konfigurowanie monitorowania procesu do uruchomienia przez cały okres istnienia węźle obliczeń. 

Jest jednym ze sposobów osiągnięcia tego zachowania można zduplikować procesu, który ładuje biblioteki usługi Application Insights i działa w tle. W tym przykładzie zadania uruchamiania ładuje dane binarne na maszynie i przechowuje procesu uruchomionego w nieskończoność. Konfigurowanie pliku konfiguracji usługi Application Insights dla tego procesu, aby Emituj dodatkowe dane, które są zainteresowani, takie jak liczniki wydajności.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "small",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Aby zwiększyć możliwości zarządzania rozwiązania, można powiązać zestawu w [pakiet aplikacji](./batch-application-packages.md). Następnie aby automatycznie wdrożyć pakiet aplikacji z pul, należy dodać odwołanie do pakietu aplikacji konfiguracji puli.
>

## <a name="throttle-and-sample-data"></a>Ograniczenia i przykładowe dane 

Ze względu na dużą skalę charakteru aplikacji partii zadań Azure działających w środowisku produkcyjnym warto ograniczyć ilość danych zbieranych przez usługę Application Insights, aby zarządzać kosztami. Zobacz [próbkowania w usłudze Application Insights](../application-insights/app-insights-sampling.md) dla niektórych mechanizmów można to osiągnąć.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-overview.md).

* Do usługi Application Insights obsługuje w innych językach, obejrzyj [Dokumentacja języków, platform i integracji](../application-insights/app-insights-platforms.md).


