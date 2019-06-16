---
title: Tworzenie usługi Azure Functions z usługą Media Services
description: W tym temacie przedstawiono sposób rozpoczęcia tworzenia usługi Azure Functions z usługą Media Services, za pomocą witryny Azure portal.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 618acae10b874eb5ebd5b6da7fe081368528dbd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217508"
---
# <a name="develop-azure-functions-with-media-services"></a>Tworzenie usługi Azure Functions z usługą Media Services

W tym artykule pokazano, jak rozpocząć tworzenie usługi Azure Functions, używanego przez usługi Media Services. Funkcja platformy Azure zdefiniowany w tym artykule monitoruje kontenera konta magazynu o nazwie **wejściowych** dla nowych plików MP4. Gdy plik zostanie upuszczony do kontenera magazynu, obiekt blob wyzwalacza wykonuje tę funkcję. Aby zapoznać się z usługi Azure functions, zobacz [Przegląd](../../azure-functions/functions-overview.md) i innych tematów w **usługi Azure functions** sekcji.

Jeśli chcesz eksplorować i wdrażanie istniejącej usługi Azure Functions, używanego przez usługi Azure Media Services, zapoznaj się z [multimediów usługi Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). To repozytorium zawiera przykłady z zastosowaniem usługi Media Services do wyświetlenia przepływów pracy związanych z wprowadzane zawartości bezpośrednio z magazynu obiektów blob, kodowanie i zapisywanie zawartości z powrotem do magazynu obiektów blob. Zawiera także przykłady sposobu monitorowania powiadomień dotyczących zadań przy użyciu elementów Webhook i kolejek systemu Azure. Możesz również tworzyć na podstawie przykładów w funkcji [multimediów usługi Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) repozytorium. Aby wdrożyć funkcji, naciśnij klawisz **Wdróż na platformie Azure** przycisku.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed utworzeniem pierwszej funkcji musisz mieć aktywne konto platformy Azure. Jeśli nie masz jeszcze konta platformy Azure, [dostępne są konta bezpłatne](https://azure.microsoft.com/free/).
- Jeśli zamierzasz utworzyć usługi Azure Functions, wykonywać działania na Twoim koncie usługi Azure Media Services (AMS) lub nasłuchiwać zdarzeń wysłanych przez usługę Media Services, należy utworzyć konto usługi AMS zgodnie z opisem [tutaj](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. Przejdź do [witryny Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta Azure.
2. Tworzenie aplikacji funkcji, zgodnie z opisem [tutaj](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Konto magazynu, którą określasz w **StorageConnection** zmienna środowiskowa (patrz następny krok) powinna być w tym samym regionie co aplikacja.

## <a name="configure-function-app-settings"></a>Konfigurowanie ustawień aplikacji funkcji

Podczas tworzenia funkcji usługi Media Services, warto dodać zmienne środowiskowe, które będą używane w całej funkcji. Aby skonfigurować ustawienia aplikacji, kliknij łącze Konfiguruj ustawienia aplikacji. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania ustawień aplikacji funkcji platformy Azure](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funkcja zdefiniowana w tym artykule przyjęto założenie, że masz następujące zmienne środowiskowe w ustawieniach Twojej aplikacji:

**AMSAADTenantDomain**: Punkt końcowy dzierżawy usługi Azure AD. Aby uzyskać więcej informacji na temat nawiązywania połączenia z interfejsem API usługi AMS zobacz [to](media-services-use-aad-auth-to-access-ams-api.md) artykułu.

**AMSRESTAPIEndpoint**:  Identyfikator URI, który reprezentuje punkt końcowy interfejsu API REST. 

**AMSClientId**: Identyfikatora klienta aplikacji w usłudze Azure AD

**AMSClientSecret**: Usługa Azure AD klucz tajny klienta aplikacji.

**StorageConnection**: połączenie magazynu na koncie skojarzonym z kontem usługi Media Services. Ta wartość jest używana w **function.json** pliku i **run.csx** pliku (opisanych poniżej).

## <a name="create-a-function"></a>Tworzenie funkcji

Po wdrożeniu aplikacji funkcji można znaleźć wśród **App Services** usługi Azure Functions.

1. Wybierz aplikację funkcji, a następnie kliknij przycisk **nową funkcję**.
2. Wybierz **C#** języka i **przetwarzania danych** scenariusza.
3. Wybierz **BlobTrigger** szablonu. Ta funkcja jest wyzwalane, gdy obiekt blob zostanie przekazany do **wejściowych** kontenera. **Wejściowych** nazwa została określona w **ścieżki**, w następnym kroku.

    ![files](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Po wybraniu **BlobTrigger**, niektóre dodatkowe formanty są wyświetlane na stronie.

    ![files](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Kliknij pozycję **Utwórz**. 

## <a name="files"></a>Pliki

Funkcji platformy Azure jest skojarzony z plików kodu i innych plików, które są opisane w tej sekcji. Podczas tworzenia funkcji za pomocą witryny Azure portal **function.json** i **run.csx** są tworzone automatycznie. Należy dodać lub przekazać **project.json** pliku. Pozostała część tej sekcji zawiera krótki opis każdego pliku i wyświetla ich definicje.

![files](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Plik function.json definiuje powiązań funkcji i innych ustawień konfiguracyjnych. Środowisko wykonawcze używa tego pliku, aby określić zdarzenia do monitorowania oraz sposób przekazywania danych do i zwrócić dane z wykonywania funkcji. Aby uzyskać więcej informacji, zobacz [powiązania protokołu HTTP i elementu webhook usługi Azure functions](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Ustaw **wyłączone** właściwości **true** aby zapobiec wykonywane przez funkcję. 

Zastąp zawartość istniejącego pliku function.json następującym kodem:

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

Plik project.json zawiera zależności. Oto przykład **project.json** pliku, który zawiera wymagane pakiety platformy .NET usługi Azure Media Services z pakietów Nuget. Pamiętaj, że numery wersji zmiany z najnowszymi aktualizacjami do pakietów, dlatego należy sprawdzić najnowsze wersje. 

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

Jest to C# kodu funkcji.  Funkcja zdefiniowana poniżej monitorów kontenera konta magazynu o nazwie **wejściowych** (jest jak określono w ścieżce) dla nowych plików MP4. Gdy plik zostanie upuszczony do kontenera magazynu, obiekt blob wyzwalacza wykonuje tę funkcję.
    
W przykładzie zdefiniowane w tej sekcji pokazano 

1. jak pozyskiwanie zasobów do konta usługi Media Services (przez kopiowanie obiektu blob do elementu zawartości usługi AMS) i 
2. w jaki sposób można przesłać zadania kodowania, który używa usługi Media Encoder Standard firmy "Adaptacyjnego przesyłania strumieniowego" ustawienie wstępne.

W realnym scenariuszu najprawdopodobniej chcesz śledzić postęp zadania, a następnie opublikować z zakodowanym elementem zawartości. Aby uzyskać więcej informacji, zobacz [do monitorowania powiadomień dotyczących zadania usługi Media Services, elementy Webhook usługi Azure użyj](media-services-dotnet-check-job-progress-with-webhooks.md). Aby uzyskać więcej przykładów, zobacz [multimediów usługi Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Zastąp zawartość pliku run.csx poniższym kodem: Po zakończeniu definiowania funkcji kliknij **Zapisz i uruchom**.

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

1. Wybierz konto magazynu, który określiłeś w **StorageConnection** zmiennej środowiskowej.
2. Kliknij przycisk **obiektów blob**.
3. Kliknij przycisk **+ kontener**. Nazwa kontenera **wejściowych**.
4. Naciśnij klawisz **przekazywanie** i przejdź do pliku MP4, który chcesz przekazać.

>[!NOTE]
> Podczas korzystania z wyzwalacz obiektu blob w ramach planu zużycie, może istnieć maksymalnie 10 minut opóźnienia w przetwarzaniu nowe obiekty BLOB, po aplikacji funkcji przeszedł bezczynności. Po uruchomieniu aplikacji funkcji, obiekty BLOB są przetwarzane od razu. Aby uzyskać więcej informacji, zobacz [obiektu Blob magazynu wyzwalaczy i powiązań](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>Kolejne kroki

W tym momencie możesz przystąpić do rozpoczęcia tworzenia aplikacji usługi Media Services. 
 
Aby uzyskać więcej szczegółów i kompletnych rozwiązań/przykłady użycia usługi Azure Functions i Logic Apps za pomocą usługi Azure Media Services do tworzenia przepływów pracy tworzenia zawartości niestandardowych, zobacz [przykład platformy .NET usługi Media integracji funkcji w witrynie GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Zobacz też [do monitorowania powiadomień dotyczących zadań usługi Media Services przy użyciu platformy .NET, elementy Webhook usługi Azure użyj](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

