---
title: Monitorowanie partii za pomocą usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak przyrządzować aplikację usługi Azure Batch .NET przy użyciu biblioteki usługi Azure Application Insights.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: b1f4fb0207d4f659861dbd3fdfd1b2d502409935
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022464"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Monitorowanie i debugowanie aplikacji platformy Azure batch .NET za pomocą usługi Application Insights

[Usługa Application Insights](../azure-monitor/app/app-insights-overview.md) zapewnia programowi microsoftowi elegancki i zaawansowany sposób monitorowania i debugowania aplikacji wdrożonych w usługach platformy Azure. Usługa Application Insights służy do monitorowania liczników wydajności i wyjątków, a także instruowania kodu za pomocą niestandardowych metryk i śledzenia. Integracja usługi Application Insights z aplikacją Usługi Azure Batch umożliwia uzyskanie szczegółowych informacji na temat zachowań i zbadanie problemów w czasie zbliżonym do rzeczywistego.

W tym artykule pokazano, jak dodać i skonfigurować bibliotekę usługi Application Insights do rozwiązania usługi Azure Batch .NET i przyrządzać kod aplikacji. Pokazuje również sposoby monitorowania aplikacji za pośrednictwem witryny Azure portal i tworzenia niestandardowych pulpitów nawigacyjnych. Aby uzyskać pomoc techniczną usługi Application Insights w innych językach, zapoznaj się z [dokumentacją języków, platform i integracji.](../azure-monitor/app/platforms.md)

Przykładowe rozwiązanie języka C# z kodem towarzyszącym temu artykułowi jest dostępne w [usłudze GitHub.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) W tym przykładzie dodaje kod instrumentacji usługi Application Insights do przykładu [TopNWords.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Jeśli nie znasz tego przykładu, spróbuj najpierw zbudować i uruchomić TopNWords. W ten sposób można zrozumieć podstawowy przepływ pracy partii przetwarzania zestawu wejściowych obiektów blob równolegle na wielu węzłach obliczeniowych. 

> [!TIP]
> Alternatywnie należy skonfigurować rozwiązanie batch do wyświetlania danych usługi Application Insights, takich jak liczniki wydajności maszyn wirtualnych w Eksploratorze wsadowym. [Wytwórcy partii](https://github.com/Azure/BatchExplorer) to bezpłatne, bogate, funkcjonalne, autonomiczne narzędzie klienta ułatwiające tworzenie, debugowanie i monitorowanie aplikacji usługi Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows. Zobacz [repozytorium wsadowych,](https://github.com/Azure/batch-insights) aby uzyskać szybkie kroki, aby włączyć dane usługi Application Insights w Eksploratorze wsadowym. 
>

## <a name="prerequisites"></a>Wymagania wstępne
* [Visual Studio 2017 lub nowsze](https://www.visualstudio.com/vs)

* [Konto wsadowe i połączone konto magazynu](batch-account-create-portal.md)

* [Zasób usługi Application Insights](../azure-monitor/app/create-new-resource.md )
  
   * Użyj portalu Azure, aby utworzyć *zasób*usługi Application Insights . Wybierz *General* **typ aplikacji**ogólnej .

   * Skopiuj [klucz instrumentacji](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) z portalu. Jest to wymagane w dalszej części tego artykułu.
  
  > [!NOTE]
  > Za dane przechowywane w usłudze Application Insights może zostać [naliczona opłata.](https://azure.microsoft.com/pricing/details/application-insights/) Obejmuje to dane diagnostyczne i monitorowania omówione w tym artykule.
  > 

## <a name="add-application-insights-to-your-project"></a>Dodawanie usługi Application Insights do projektu

**Pakiet Microsoft.ApplicationInsights.WindowsServer** NuGet i jego zależności są wymagane dla projektu. Dodaj lub przywróć je do projektu aplikacji. Aby zainstalować pakiet, `Install-Package` użyj polecenia lub Menedżera pakietów NuGet.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Odwołaj się do aplikacji z aplikacji .NET przy użyciu obszaru nazw **Microsoft.ApplicationInsights.**

## <a name="instrument-your-code"></a>Instrumentuj swój kod

Aby przyrządzywać kod, rozwiązanie musi utworzyć aplikację Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). W przykładzie TelemetryClient ładuje swoją konfigurację z [pliku ApplicationInsights.config.](../azure-monitor/app/configuration-with-applicationinsights-config.md) Należy zaktualizować ApplicationInsights.config w następujących projektach za pomocą klucza instrumentacji usługi Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask i TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Dodaj również klawisz instrumentacji w pliku TopNWords.cs.

W przykładzie w TopNWords.cs używa następujących [wywołań instrumentacji](../azure-monitor/app/api-custom-events-metrics.md) z interfejsu API usługi Application Insights:
* `TrackMetric()`- Śledzi, jak długo, średnio, węzeł obliczeniowy trwa do pobrania wymaganego pliku tekstowego.
* `TrackTrace()`- Dodaje wywołania debugowania do kodu.
* `TrackEvent()`- Śledzi ciekawe wydarzenia do uchwycenia.

W tym przykładzie celowo pomija obsługi wyjątków. Zamiast tego usługa Application Insights automatycznie zgłasza nieobsługiwane wyjątki, co znacznie poprawia działanie debugowania. 

Poniższy fragment kodu ilustruje sposób korzystania z tych metod.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Pomocnik inicjatora inicjowania telemetrii usługi Azure Batch
Podczas raportowania danych telemetrycznych dla danego serwera i wystąpienia usługa Application Insights używa roli maszyny Wirtualnej platformy Azure i nazwy maszyny Wirtualnej dla wartości domyślnych. W kontekście usługi Azure Batch w przykładzie pokazano, jak zamiast tego używać nazwy puli i nazwy węzła obliczeniowego. Użyj [inicjatora telemetrii,](../azure-monitor/app/api-filtering-sampling.md#add-properties) aby zastąpić wartości domyślne. 

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

Aby włączyć inicjatora telemetrii, plik ApplicationInsights.config w projekcie TopNWordsSample zawiera następujące elementy:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Aktualizowanie zadania i zadań w celu uwzględnienia plików binarnych usługi Application Insights

Aby usługa Application Insights działała poprawnie w węzłach obliczeniowych, upewnij się, że pliki binarne są poprawnie umieszczone. Dodaj wymagane pliki binarne do kolekcji plików zasobów zadania, aby zostały pobrane w momencie wykonywania zadania. Poniższe fragmenty kodu są podobne do kodu w Job.cs.

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

Następnie należy utworzyć pliki przemieszczania, które są używane przez zadanie.
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

Metoda `FileToStage` jest funkcją pomocnika w przykładzie kodu, który umożliwia łatwe przekazywanie pliku z dysku lokalnego do obiektu blob usługi Azure Storage. Każdy plik jest później pobierany do węzła obliczeniowego i odwołuje się do zadania.

Na koniec dodaj zadania do zadania i dołącz niezbędne pliki binarne usługi Application Insights.
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

Teraz, gdy zostało skonfigurowane zadanie i zadania do korzystania z usługi Application Insights, uruchom przykładowe zadanie w puli. Przejdź do witryny Azure Portal i otwórz zasób usługi Application Insights, który został zainicjowany. Po aprowizowaniu puli należy rozpocząć wyświetlanie danych płynących i coraz rejestrowane. Dalsza część tego artykułu dotyczy tylko kilku funkcji usługi Application Insights, ale możesz zapoznać się z pełnym zestawem funkcji.

### <a name="view-live-stream-data"></a>Wyświetlanie danych strumienia na żywo

Aby wyświetlić dzienniki śledzenia w zasobie aplikacji Insights, kliknij pozycję **Live Stream**. Poniższy zrzut ekranu pokazuje, jak wyświetlić dane na żywo pochodzące z węzłów obliczeniowych w puli, na przykład użycie procesora CPU na węzeł obliczeniowy.

![Dane węzła obliczeniowego na żywo strumień](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Wyświetlanie dzienników śledzenia

Aby wyświetlić dzienniki śledzenia w zasobie aplikacji Insights, kliknij przycisk **Wyszukaj**. W tym widoku jest wyświetlana lista danych diagnostycznych przechwyconych przez aplikację Application Insights, w tym ślady, zdarzenia i wyjątki. 

Poniższy zrzut ekranu pokazuje, jak pojedynczy ślad dla zadania jest rejestrowany, a później wyszukiwane do celów debugowania.

![Obraz dzienników śledzenia](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Wyświetlanie nieobsługiwało się wyjątków

Poniższe zrzuty ekranu pokazują, jak usługa Application Insights rejestruje wyjątki generowane z aplikacji. W takim przypadku w ciągu kilku sekund od zgłoszenia wyjątku przez aplikację można przejść do określonego wyjątku i zdiagnozować problem.

![Nieobsługiwane wyjątki](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Mierzenie czasu pobierania obiektów blob

Metryki niestandardowe są również cennym narzędziem w portalu. Na przykład można wyświetlić średni czas potrzebny każdemu węzłowi obliczeniowemu na pobranie wymaganego pliku tekstowego, który był przetwarzany.

Aby utworzyć przykładowy wykres:
1. W zasobie usługi Application Insights kliknij pozycję >  **Eksplorator metryk****Dodaj wykres**.
2. Kliknij **pozycję Edytuj** na dodanym wykresie.
2. Zaktualizuj szczegóły wykresu w następujący sposób:
   * Ustaw **typ wykresu** na **Siatka**.
   * Ustaw **agregację** na **średnią**.
   * Ustaw **grupowanie według** **identyfikatora nodeid**.
   * W **obszarze Metryki**wybierz pozycję **Pobieranie niestandardowego** > **obiektu blob w kilka sekund**.
   * Dostosuj **paletę kolorów** wyświetlacza do wyboru. 

![Czas pobierania obiektów blob na węzeł](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Ciągłe monitorowanie węzłów obliczeniowych

Być może zauważyłeś, że wszystkie metryki, w tym liczniki wydajności, są rejestrowane tylko wtedy, gdy zadania są uruchomione. To zachowanie jest przydatne, ponieważ ogranicza ilość danych, które usługa Application Insights loguje. Istnieją jednak przypadki, w których zawsze chcesz monitorować węzły obliczeniowe. Na przykład mogą one być uruchomione pracy w tle, który nie jest zaplanowany za pośrednictwem usługi Batch. W takim przypadku należy skonfigurować proces monitorowania, aby uruchomić przez cały okres eksploatacji węzła obliczeniowego. 

Jednym ze sposobów osiągnięcia tego zachowania jest zdyskwisuj proces, który ładuje bibliotekę usługi Application Insights i działa w tle. W przykładzie zadanie uruchamiania ładuje pliki binarne na komputerze i utrzymuje proces uruchomiony przez czas nieokreślony. Skonfiguruj plik konfiguracyjny usługi Application Insights dla tego procesu, aby emitować dodatkowe dane, które Cię interesują, takie jak liczniki wydajności.

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
> Aby zwiększyć łatwość zarządzania rozwiązaniem, można połączyć zestaw w [pakiecie aplikacji](./batch-application-packages.md). Następnie, aby automatycznie wdrożyć pakiet aplikacji do pul, dodaj odwołanie do pakietu aplikacji do konfiguracji puli.
>

## <a name="throttle-and-sample-data"></a>Przepustnicy i przykładowe dane 

Ze względu na dużą skalę charakter aplikacji usługi Azure Batch uruchomionych w produkcji, można ograniczyć ilość danych zebranych przez usługę Application Insights do zarządzania kosztami. Zobacz [próbkowanie w usłudze Application Insights](../azure-monitor/app/sampling.md) dla niektórych mechanizmów, aby to osiągnąć.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [usłudze Application Insights](../azure-monitor/app/app-insights-overview.md).

* Aby uzyskać pomoc techniczną usługi Application Insights w innych językach, zapoznaj się z [dokumentacją języków, platform i integracji.](../azure-monitor/app/platforms.md)


