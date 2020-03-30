---
title: Tworzenie funkcji platformy Azure za pomocą usług multimedialnych
description: W tym temacie pokazano, jak rozpocząć tworzenie usługi Azure Functions za pomocą usługi Media Services przy użyciu witryny Azure portal.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251169"
---
# <a name="develop-azure-functions-with-media-services"></a>Tworzenie funkcji platformy Azure za pomocą usług multimedialnych

W tym artykule pokazano, jak rozpocząć tworzenie usług Azure Functions korzystających z usługi Media Services. Funkcja platformy Azure zdefiniowana w tym artykule monitoruje kontener konta magazynu o nazwie **dane wejściowe** dla nowych plików MP4. Po upuszczeniu pliku do kontenera magazynu wyzwalacz obiektu blob wykonuje funkcję. Aby przejrzeć funkcje platformy Azure, zobacz [Omówienie](../../azure-functions/functions-overview.md) i inne tematy w sekcji **funkcje platformy Azure.**

Jeśli chcesz eksplorować i wdrażać istniejące usługi Azure Functions korzystające z usługi Azure Media Services, zapoznaj się z [programem Media Services Azure Functions.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) To repozytorium zawiera przykłady, które używają usługi Media Services do pokazywania przepływów pracy związanych z pozyskiwania zawartości bezpośrednio z magazynu obiektów blob, kodowania i zapisywania zawartości z powrotem do magazynu obiektów blob. Zawiera również przykłady sposobu monitorowania powiadomień o zadaniach za pośrednictwem elementów WebHook i kolejek platformy Azure. Można również opracować funkcje na podstawie przykładów w repozytorium [usługi Media Services usługi Azure Functions.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) Aby wdrożyć te funkcje, naciśnij przycisk Wdrażanie na **platformie Azure.**

## <a name="prerequisites"></a>Wymagania wstępne

- Przed utworzeniem pierwszej funkcji musisz mieć aktywne konto platformy Azure. Jeśli nie masz jeszcze konta platformy Azure, [dostępne są konta bezpłatne](https://azure.microsoft.com/free/).
- Jeśli zamierzasz utworzyć usługę Azure Functions, która wykonuje akcje na koncie usługi Azure Media Services (AMS) lub nasłuchuje zdarzeń wysyłanych przez usługę Media Services, należy utworzyć konto USŁUGI AMS, zgodnie z opisem [poniżej](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. Przejdź do [witryny Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta Azure.
2. Utwórz aplikację funkcyjną w sposób opisany [w tym miejscu](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Konto magazynu określone w zmiennej środowiskowej **StorageConnection** (zobacz następny krok) powinno znajdować się w tym samym regionie co aplikacja.

## <a name="configure-function-app-settings"></a>Konfigurowanie ustawień aplikacji funkcji

Podczas tworzenia funkcji usługi Media Services, jest przydatne do dodawania zmiennych środowiskowych, które będą używane w całej funkcji. Aby skonfigurować ustawienia aplikacji, kliknij łącze Konfiguruj ustawienia aplikacji. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować ustawienia aplikacji usługi Azure Function](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funkcja zdefiniowana w tym artykule zakłada, że w ustawieniach aplikacji są dostępne następujące zmienne środowiskowe:

**AMSAADTenantDomain:** Punkt końcowy dzierżawy usługi Azure AD. Aby uzyskać więcej informacji na temat łączenia się z interfejsem API usługi AMS, zobacz [ten](media-services-use-aad-auth-to-access-ams-api.md) artykuł.

**AMSRESTAPIEndpoint**: URI reprezentujący punkt końcowy interfejsu API REST. 

**IDENTYFIKATOR AMSClientId:** Identyfikator klienta aplikacji usługi Azure AD.

**AMSClientSecret:** Klucz tajny klienta aplikacji usługi Azure AD.

**StorageConnection**: połączenie magazynu konta skojarzonego z kontem usługi Media Services. Ta wartość jest używana w pliku **function.json** i pliku **run.csx** (opisanym poniżej).

## <a name="create-a-function"></a>Tworzenie funkcji

Po wdrożeniu aplikacji funkcji można ją znaleźć wśród **usług App Services** usługi Azure Functions.

1. Wybierz aplikację funkcji i kliknij pozycję **Nowa funkcja**.
2. Wybierz język **C#** i scenariusz **przetwarzania danych.**
3. Wybierz szablon **BlobTrigger.** Ta funkcja jest wyzwalana za każdym razem, gdy obiekt blob jest przekazywał do kontenera **wejściowego.** Nazwa **wejściowa** jest określona w **ścieżce**, w następnym kroku.

    ![files](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Po **wybraniu blobTrigger,** niektóre więcej formantów pojawiają się na stronie.

    ![files](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Kliknij przycisk **Utwórz**. 

## <a name="files"></a>Pliki

Funkcja platformy Azure jest skojarzona z plikami kodu i innymi plikami, które są opisane w tej sekcji. Podczas tworzenia funkcji za pomocą portalu **Azure, function.json** i **run.csx** są tworzone dla Ciebie. Musisz dodać lub przesłać plik **project.json.** W dalszej części tej sekcji przedstawiono krótkie wyjaśnienie każdego pliku i przedstawiono ich definicje.

![files](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Plik function.json definiuje powiązania funkcji i inne ustawienia konfiguracji. Środowisko wykonawcze używa tego pliku do określenia zdarzeń do monitorowania i jak przekazywać dane i zwracać dane z wykonywania funkcji. Aby uzyskać więcej informacji, zobacz [funkcje platformy Azure HTTP i powiązania elementu webhook](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Ustaw **właściwość disabled** na **true,** aby zapobiec wykonaniu funkcji. 

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

Plik project.json zawiera zależności. Oto przykład pliku **project.json,** który zawiera wymagane pakiety usługi .NET Azure Media Services firmy Nuget. Należy zauważyć, że numery wersji zmieniają się wraz z najnowszymi aktualizacjami pakietów, dlatego należy potwierdzić najnowsze wersje. 

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
    
### <a name="runcsx"></a>plik run.csx

Jest to kod języka C# dla funkcji.  Funkcja zdefiniowana poniżej monitoruje kontener konta magazynu o nazwie **input** (czyli to, co zostało określone w ścieżce) dla nowych plików MP4. Po upuszczeniu pliku do kontenera magazynu wyzwalacz obiektu blob wykonuje funkcję.
    
Przykład zdefiniowany w tej sekcji pokazuje, że 

1. jak połknąć zasób na konto usługi Media Services (poprzez zasiebienie obiektu blob w zasob AMS) i 
2. jak przesłać zadanie kodowania, które używa predefiniowanych ustawień "Adaptive Streaming" programu Media Encoder Standard.

W rzeczywistym scenariuszu najprawdopodobniej chcesz śledzić postęp zadania, a następnie opublikować zakodowany zasób. Aby uzyskać więcej informacji, zobacz [Monitorowanie powiadomień o zadaniach usługi Media Services za pomocą elementów elementów webhook platformy Azure](media-services-dotnet-check-job-progress-with-webhooks.md). Aby uzyskać więcej przykładów, zobacz [Usługi Media Services usługi Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Zastąp zawartość istniejącego pliku run.csx następującym kodem: Po zakończeniu definiowania funkcji kliknij przycisk **Zapisz i uruchom**.

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

Aby przetestować funkcję, należy przekazać plik MP4 do kontenera **wejściowego** konta magazynu, który został określony w ciągu połączenia.  

1. Wybierz konto magazynu określone w zmiennej środowiskowej **StorageConnection.**
2. Kliknij pozycję **Obiekty blob**.
3. Kliknij pozycję **+ Kontener**. Nazwij **dane wejściowe kontenera**.
4. Naciśnij **pozycję Przekaż** i przejdź do pliku .mp4, który chcesz przesłać.

>[!NOTE]
> Gdy używasz wyzwalacza obiektu blob w planie zużycia, może wystąpić maksymalnie 10-minutowe opóźnienie w przetwarzaniu nowych obiektów blob po przejściu aplikacji funkcji w stan bezczynny. Po uruchomieniu aplikacji funkcji obiekty blob są przetwarzane natychmiast. Aby uzyskać więcej informacji, zobacz [wyzwalacze i powiązania magazynu obiektów blob](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>Następne kroki

W tym momencie można przystąpić do rozpoczęcia tworzenia aplikacji usługi Media Services. 
 
Aby uzyskać więcej informacji i pełne przykłady/rozwiązania dotyczące korzystania z usług Azure Functions i logic apps z usługą Azure Media Services w celu tworzenia niestandardowych przepływów pracy tworzenia zawartości, zobacz [przykład integracji funkcji media services .NET w usłudze GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Zobacz też [Używanie elementów elementów webhook platformy Azure do monitorowania powiadomień o zadaniach usługi Media Services za pomocą platformy .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

