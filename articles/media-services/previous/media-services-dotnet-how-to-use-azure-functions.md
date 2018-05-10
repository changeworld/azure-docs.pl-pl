---
title: Tworzenie funkcji platformy Azure z programem Media Services
description: W tym temacie pokazano, jak rozpocząć tworzenie usługi Azure Functions w usłudze Media Services przy użyciu portalu Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 31a12d43ba71f1a0eacbb12887b047f2fafe3b53
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="develop-azure-functions-with-media-services"></a>Tworzenie funkcji platformy Azure z programem Media Services

W tym artykule przedstawiono, jak rozpocząć pracę z tworzeniem usługi Azure Functions, używanego przez usługi Media Services. Funkcja Azure zdefiniowane w tym artykule monitoruje kontenera konta magazynu o nazwie **wejściowych** dla nowych plików MP4. Gdy plik zostanie upuszczony do kontenera magazynu, wyzwalacza obiektu blob wykonuje funkcji. Aby przejrzeć usługę Azure functions, zobacz [omówienie](../../azure-functions/functions-overview.md) i innych tematach w **usługę Azure functions** sekcji.

Jeśli chcesz Eksploruj i wdrożyć istniejącej usługi Azure Functions, używanego przez usługi Azure Media Services, zapoznaj się [Media Services usługi Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). To repozytorium zawiera przykłady, które za pomocą usługi Media Services można wyświetlić przepływów pracy związanych z wprowadzania zawartości bezpośrednio z magazynu obiektów blob, kodowanie i zapisywania zawartości z powrotem do magazynu obiektów blob. Zawiera również przykłady monitorować powiadomienia zadania za pomocą elementów Webhook i kolejek Azure. Można również zaprojektować funkcji w oparciu o przykłady w [Media Services usługi Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) repozytorium. Aby wdrożyć funkcje, naciśnij klawisz **wdrażanie na platformie Azure** przycisku.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed utworzeniem pierwszej funkcji musisz mieć aktywne konto platformy Azure. Jeśli nie masz jeszcze konta platformy Azure, [dostępne są konta bezpłatne](https://azure.microsoft.com/free/).
- Jeśli zamierzasz utworzyć usługi Azure Functions, wykonywać działania na Twoim koncie usługi Azure Media Services (AMS) lub nasłuchiwania zdarzeń wysłanych przez usługę Media Services, należy utworzyć konta usługi AMS zgodnie z opisem [tutaj](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. Przejdź do [witryny Azure Portal](http://portal.azure.com) i zaloguj się przy użyciu konta Azure.
2. Tworzenie aplikacji funkcji, zgodnie z opisem [tutaj](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Konto magazynu, którą można określić w **StorageConnection** zmiennej środowiskowej (patrz następny krok) powinna być w tym samym regionie co aplikacja.

## <a name="configure-function-app-settings"></a>Konfiguruj ustawienia aplikacji — funkcja

Podczas tworzenia usługi Media Services funkcje, warto dodać zmiennych środowiskowych, które będą używane w całej funkcji. Aby skonfigurować ustawienia aplikacji, kliknij łącze Konfiguruj ustawienia aplikacji. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania ustawień aplikacji funkcji platformy Azure](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funkcja zdefiniowana w tym artykule przyjęto założenie, że masz następujące zmienne środowiskowe w ustawieniach aplikacji:

**AMSAADTenantDomain**: punktu końcowego dzierżawcy usługi Azure AD. Aby uzyskać więcej informacji na temat podłączania do interfejsu API usług AMS, zobacz [to](media-services-use-aad-auth-to-access-ams-api.md) artykułu.

**AMSRESTAPIEndpoint**: identyfikator URI, który reprezentuje punkt końcowy interfejsu API REST. 

**AMSClientId**: identyfikator klienta aplikacji usługi Azure AD

**AMSClientSecret**: klucz tajny klienta aplikacji usługi Azure AD.

**StorageConnection**: połączenia z magazynem konta skojarzonego z kontem usługi Media Services. Ta wartość jest używana w **function.json** pliku i **run.csx** pliku (opisanych poniżej).

## <a name="create-a-function"></a>Tworzenie funkcji

Po wdrożeniu aplikacji funkcji można znaleźć wśród **usługi aplikacji** usługi Azure Functions.

1. Wybierz aplikację funkcji, a następnie kliknij przycisk **nową funkcję**.
2. Wybierz **C#** języka i **przetwarzania danych** scenariusza.
3. Wybierz **BlobTrigger** szablonu. Ta funkcja jest zawsze wyzwalane, gdy obiekt blob jest przekazywany do **wejściowych** kontenera. **Wejściowych** nazwa została określona w **ścieżki**, w następnym kroku.

    ![plików](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Po wybraniu **BlobTrigger**, niektóre formanty więcej wyświetlane na stronie.

    ![plików](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Kliknij przycisk **Utwórz**. 

## <a name="files"></a>Pliki

Funkcja Azure jest skojarzony z plików kodu i innych plików, które zostały opisane w tej sekcji. Korzystając z portalu Azure można utworzyć funkcji **function.json** i **run.csx** są tworzone automatycznie. Należy dodać lub Przekaż **project.json** pliku. Pozostałej części tej sekcji zawiera krótki opis każdego pliku i przedstawiono ich definicje.

![plików](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Plik function.json definiuje, powiązania funkcji i innych ustawień konfiguracyjnych. Środowisko uruchomieniowe korzysta z tego pliku, aby określić zdarzenia do monitorowania oraz sposób przekazywania danych do i zwracać dane z wykonywania funkcji. Aby uzyskać więcej informacji, zobacz [Azure functions powiązania protokołu HTTP i webhook](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Ustaw **wyłączone** właściwości **true** aby zapobiec wykonywana przez funkcję. 

Zastąp zawartość pliku function.json następującym kodem:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

Plik project.json zawiera zależności. Oto przykład **project.json** plik zawierający wymagane pakiety platformy .NET usługi Azure Media Services z pakietu Nuget. Należy pamiętać, że numery wersji Zmień z najnowszymi aktualizacjami pakietów, dlatego należy upewnić się, najnowsze wersje. 

Dodaj następującą definicję do pliku project.json. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

To jest kod C# dla funkcji.  Funkcja zdefiniowana poniżej monitorów kontenera konta magazynu o nazwie **wejściowych** (tzn. jak określono w ścieżce) dla nowych plików MP4. Gdy plik zostanie upuszczony do kontenera magazynu, wyzwalacza obiektu blob wykonuje funkcji.
    
W przykładzie zdefiniowano w tej sekcji pokazano 

1. jak pozyskiwania zasób do konta usługi Media Services (przez kopiowanie obiektu blob do elementu zawartości AMS) i 
2. w jaki sposób można przesłać zadania kodowania, która używa Media Encoder Standard "Adaptacyjne przesyłanie strumieniowe" wstępnie.

W tym scenariuszu rzeczywistych prawdopodobnie chcesz śledzić postęp zadania, a następnie opublikować z zakodowanym elementem zawartości. Aby uzyskać więcej informacji, zobacz [elementów Webhook Azure używana do monitorowania usługi Media Services zadania powiadomienia](media-services-dotnet-check-job-progress-with-webhooks.md). Aby uzyskać więcej przykładów, zobacz [Media Services usługi Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Zastąp zawartość pliku run.csx następującym kodem: po zakończeniu definiowania funkcji kliknij **Zapisz i uruchom**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Testowanie funkcji

Aby przetestować funkcję, należy przekazać plik MP4 do **wejściowych** kontenera konta magazynu, który określono w parametrach połączenia.  

1. Wybierz konto magazynu, który określono w **StorageConnection** zmiennej środowiskowej.
2. Kliknij przycisk **obiekty BLOB**.
3. Kliknij przycisk **+ kontener**. Nazwa kontenera **wejściowych**.
4. Naciśnij klawisz **przekazać** i przejdź do pliku plik MP4, który chcesz przekazać.

>[!NOTE]
> Podczas korzystania z wyzwalacza obiektu blob w planie zużycia, może istnieć maksymalnie 10-minutowych opóźnienia w przetwarzaniu nowe obiekty BLOB po aplikacji funkcji przeszedł bezczynności. Po uruchomieniu aplikacji funkcja obiekty BLOB są przetwarzane natychmiast. Aby uzyskać więcej informacji, zobacz [obiektu Blob magazynu wyzwalaczy i powiązań](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob#blob-storage-triggers-and-bindings).

## <a name="next-steps"></a>Kolejne kroki

W tym momencie można przystąpić do rozpocząć tworzenie aplikacji usługi Media Services. 
 
Bardziej szczegółowe informacje i kompletnych rozwiązań/przykłady korzystania z usługi Azure Functions i Logic Apps usłudze Azure Media Services do tworzenia przepływów pracy niestandardowe tworzenie zawartości, zobacz [Media Services .NET funkcje integracji przykładem w witrynie GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Zobacz też [elementów Webhook Azure używana do monitorowania usługi Media Services zadania powiadomienia z platformą .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

