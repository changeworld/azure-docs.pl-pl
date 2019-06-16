---
title: Monitorowanie usługi Batch za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmodyfikować aplikację usługi Azure Batch dla środowiska .NET, przy użyciu biblioteki usługi Azure Application Insights.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: c527b0b10a2b9a351b242d0858fdbe64687970a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595291"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorowania i debugowania aplikacji usługi Azure Batch dla środowiska .NET za pomocą usługi Application Insights

[Usługa Application Insights](../azure-monitor/app/app-insights-overview.md) zapewnia elegancki i zaawansowany sposób dla deweloperów do monitorowania i debugowania aplikacji wdrożonej w usłudze Azure services. Usługa Application Insights umożliwia monitorowanie liczników wydajności i wyjątków, a także Instrumentacji kodu za pomocą metryk niestandardowych i śledzenie. Integracja usługi Application Insights z aplikacji usługi Azure Batch pozwala uzyskać głęboki wgląd w zachowania i badania problemów w czasie niemal rzeczywistym.

Ten artykuł pokazuje, jak dodać i skonfigurować Biblioteka usługi Application Insights do rozwiązania usługi Azure Batch dla środowiska .NET i instrumentacji w kodzie aplikacji. Pokazuje także sposób monitorować aplikację za pośrednictwem witryny Azure portal i tworzyć niestandardowe pulpity nawigacyjne. Usługa Application Insights dotyczących pomocy technicznej w innych językach, Przyjrzyj się [dokumentacji języki, platformy i integracje](../azure-monitor/app/platforms.md).

Przykładowe języka C# rozwiązanie z kodem, która ma towarzyszyć w tym artykule jest dostępny na [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Ten przykład dodaje kod Instrumentacji usługi Application Insights do [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) przykład. Jeśli nie znasz tego przykładu, spróbuj kompilowanie i uruchamianie TopNWords najpierw. W ten sposób pomoże Ci zrozumieć podstawowy przepływ pracy usługi Batch przetwarzania zestawu danych wejściowych obiektów blob równolegle na wielu węzłach obliczeniowych. 

> [!TIP]
> Jako alternatywę skonfiguruj rozwiązania usługi Batch, aby wyświetlić dane usługi Application Insights, takich jak liczniki wydajności maszyn wirtualnych w Eksploratorze usługi Batch. [Batch Explorer](https://github.com/Azure/BatchExplorer) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje usługi Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows. Zobacz [insights partii repozytorium](https://github.com/Azure/batch-insights) Szybkie kroki umożliwiające dane usługi Application Insights w Eksploratorze usługi Batch. 
>

## <a name="prerequisites"></a>Wymagania wstępne
* [Program Visual Studio 2017 lub nowszego](https://www.visualstudio.com/vs)

* [Konto usługi Batch i połączone konto magazynu](batch-account-create-portal.md)

* [Zasób usługi Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Użyj witryny Azure portal do utworzenia usługi Application Insights *zasobów*. Wybierz *ogólne* **typ aplikacji**.

   * Kopiuj [klucz Instrumentacji](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) z poziomu portalu. Jest wymagany w dalszej części tego artykułu.
  
  > [!NOTE]
  > Może być [naliczane](https://azure.microsoft.com/pricing/details/application-insights/) dla danych przechowywanych w usłudze Application Insights. Obejmuje to diagnostyki i monitorowania danych omówionych w tym artykule.
  > 

## <a name="add-application-insights-to-your-project"></a>Dodaj usługę Application Insights do swojego projektu

**Microsoft.ApplicationInsights.WindowsServer** pakietu NuGet oraz jego zależności są wymagane dla projektu. Dodaj lub przywrócić je do projektu aplikacji. Aby zainstalować pakiet, należy użyć `Install-Package` polecenia lub Menedżer pakietów NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Odwoływać się do usługi Application Insights z aplikacji .NET za pomocą **Microsoft.ApplicationInsights** przestrzeni nazw.

## <a name="instrument-your-code"></a>Instrumentować swój kod

Aby instrumentować swój kod, rozwiązanie musi utworzyć usługi Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). W tym przykładzie TelemetryClient ładuje konfigurację z [plik ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) pliku. Pamiętaj zaktualizować plik ApplicationInsights.config w następujących projektach przy użyciu klucza Instrumentacji usługi Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask and TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Ponadto plik TopNWords.cs dodać klucza instrumentacji.

W przykładzie w TopNWords.cs użyto następujących [wywołania Instrumentacji](../azure-monitor/app/api-custom-events-metrics.md) z interfejsu API usługi Application Insights:
* `TrackMetric()` -Śledzi, jak długo — średnio o węzła obliczeniowego Trwa pobieranie pliku wymaganego tekstu.
* `TrackTrace()` -Dodaje debugowania wywołania w kodzie.
* `TrackEvent()` -Interesujących zdarzeń do przechwytywania ścieżki.

W tym przykładzie celowo powoduje, że obsługa wyjątków. Zamiast tego usługa Application Insights automatycznie raporty nieobsłużone wyjątki, które znacznie poprawia środowisko debugowania. 

Poniższy fragment kodu ilustruje sposób użycia tych metod.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Usługa Azure pomocnika inicjatora telemetrii usługi Batch
Raportowania danych telemetrycznych dla danego serwera i wystąpienia, Application Insights używa nazwy roli maszyny Wirtualnej platformy Azure i maszyn wirtualnych dla wartości domyślnych. W kontekście usługi Azure Batch w przykładzie pokazano sposób użycia nazwy puli i zamiast tego obliczenia nazwy węzła. Użyj [inicjatora telemetrii](../azure-monitor/app/api-filtering-sampling.md#add-properties) przesłonić wartości domyślne. 

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

Aby włączyć inicjatora telemetrii, plik ApplicationInsights.config w projekcie TopNWordsSample obejmuje następujące funkcje:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualizacja zadania i zadań podrzędnych, aby uwzględnić pliki binarne usługi Application Insights

Aby działać poprawnie na węzły obliczeniowe usługi Application Insights upewnij się, że pliki binarne są prawidłowo umieszczone. Dodaj wymagane pliki binarne do kolekcji pliki zasobów zadania podrzędnego, tak, aby pobrać one pobierane w czasie, który wykonuje zadanie. Poniższe fragmenty kodu są podobne do kodu w Job.cs.

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

Następnie należy utworzyć tymczasowe pliki, które są używane przez zadanie.
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

`FileToStage` Metodą jest funkcji pomocnika, która w przykładowym kodzie, która pozwala na łatwe przekazywania pliku z dysku lokalnego do obiektu blob usługi Azure Storage. Każdy plik jest później pobrane do węzła obliczeniowego i odwołuje się zadania.

Na koniec Dodawanie podzadań do zadania i zawierają niezbędne pliki binarne usługi Application Insights.
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

## <a name="view-data-in-the-azure-portal"></a>Wyświetlanie danych w witrynie Azure portal

Teraz, gdy skonfigurowano zadań i zadań podrzędnych, aby używać usługi Application Insights, należy uruchomić zadanie przykładzie w puli. Przejdź do witryny Azure portal, a następnie otwórz zasób usługi Application Insights, aprowizowana przez Ciebie. Po aprowizacji puli należy rozpocząć przepływu danych i uzyskiwanie rejestrowane. W pozostałej części tego artykułu styka się tylko kilka funkcji usługi Application Insights, ale możesz eksplorować pełny zestaw funkcji.

### <a name="view-live-stream-data"></a>Wyświetl dane transmisji strumieniowej na żywo

Aby wyświetlić dzienniki śledzenia w swoim zasobie aplikacji szczegółowe informacje, kliknij **Live Stream**. Poniższy zrzut ekranu przedstawia sposób wyświetlania danych na żywo pochodzące z węzłów obliczeniowych w puli, na przykład użycie procesora CPU na węźle obliczeniowym.

![Dane węzła obliczeniowego strumienia na żywo](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Wyświetl dzienniki śledzenia

Aby wyświetlić dzienniki śledzenia w swoim zasobie aplikacji szczegółowe informacje, kliknij **wyszukiwania**. Ten widok przedstawia listę danych diagnostycznych przechwycone przez usługę Application Insights, w tym dane śledzenia, zdarzeń i wyjątków. 

Poniższy zrzut ekranu przedstawia, jak pojedyncze śledzenie zadania jest rejestrowany i później zapytań na potrzeby debugowania.

![Obraz dzienniki śledzenia](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Wyświetl nieobsłużonych wyjątków

Poniższych zrzutach ekranu przedstawiono, jak usługa Application Insights dzienniki wyjątków zgłaszanych przez aplikację. W takim przypadku w ciągu kilku sekund aplikacji wywołującej wyjątek, możesz przejść do określonego wyjątku i zdiagnozowania problemu.

![Nieobsługiwane wyjątki](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Czas pobierania obiektów blob miary

Metryki niestandardowe są również przydatnym narzędziem w portalu. Na przykład można wyświetlić Średni czas, jaki zajęło każdy węzeł obliczeniowy, aby pobrać plik wymaganego tekstu, który przetwarza je.

Aby utworzyć wykres przykładowy:
1. W zasobie usługi Application Insights, kliknij przycisk **Eksploratora metryk** > **Dodaj wykres**.
2. Kliknij przycisk **Edytuj** na wykresie, który został dodany.
2. Zaktualizuj szczegóły wykresu w następujący sposób:
   * Ustaw **typ wykresu** do **siatki**.
   * Ustaw **agregacji** do **średni**.
   * Ustaw **Grupuj według** do **NodeId**.
   * W **metryki**, wybierz opcję **niestandardowe** > **pobierania obiektów Blob w ciągu kilku sekund**.
   * Dostosuj wyświetlanie **palety kolorów** z wybranymi. 

![Czas pobierania obiektów blob na węzeł](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Monitorowanie ciągłe węzłów obliczeniowych

Być może zauważono, że wszystkich metryk, liczników wydajności, w tym tylko są rejestrowane, gdy są uruchomione zadania. To zachowanie jest przydatne, ponieważ ogranicza ilość danych, która rejestruje w usłudze Application Insights. Jeśli chcesz zawsze monitorowanie węzłów obliczeniowych istnieją jednak przypadki. Na przykład gdzie mogą być uruchamiane pracę w tle, który nie jest zaplanowane za pomocą usługi Batch. W tym przypadku skonfigurować proces monitorowania do uruchomienia przez cały okres istnienia węzła obliczeniowego. 

Jest jednym ze sposobów osiągnięcia tego zachowania spustit novou kopii procesu, który ładuje Biblioteka usługi Application Insights i działa w tle. W tym przykładzie zadanie podrzędne uruchamiania ładuje pliki binarne na maszynie i utrzymuje proces uruchomiony na czas nieokreślony. Skonfiguruj plik konfiguracji usługi Application Insights dla tego procesu emitować dodatkowe dane, których interesuje Cię, takich jak liczniki wydajności.

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
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
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
> Aby zwiększyć możliwości rozwiązania, można powiązać zestawu w [pakiet aplikacji](./batch-application-packages.md). Następnie aby automatycznie wdrożyć pakiet aplikacji pulach, należy dodać odwołanie do pakietu aplikacji do konfiguracji puli.
>

## <a name="throttle-and-sample-data"></a>Ograniczenie przepustowości i przykładowe dane 

Ze względu na charakter na dużą skalę aplikacji usługi Azure Batch, które działają w środowisku produkcyjnym możesz chcieć ograniczyć ilość danych zebranych przez usługę Application Insights, aby zarządzać kosztami. Zobacz [próbkowanie w usłudze Application Insights](../azure-monitor/app/sampling.md) niektórych mechanizmów można to osiągnąć.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [usługi Application Insights](../azure-monitor/app/app-insights-overview.md).

* Usługa Application Insights dotyczących pomocy technicznej w innych językach, Przyjrzyj się [dokumentacji języki, platformy i integracje](../azure-monitor/app/platforms.md).


