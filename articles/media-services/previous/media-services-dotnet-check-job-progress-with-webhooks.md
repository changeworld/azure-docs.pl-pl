---
title: Używanie łączy webhook platformy Azure do monitorowania powiadomień o zadaniach usługi Media Services za pomocą platformy .NET | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać elementów Webhook platformy Azure do monitorowania powiadomień o zadaniach usługi Media Services. Przykładowy kod jest napisany w języku C# i używa zestawu SDK usług multimedialnych dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a61fe157-81b1-45c1-89f2-224b7ef55869
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a29381bded4bb2562227bd5f23ccb59bb5add028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67059207"
---
# <a name="use-azure-webhooks-to-monitor-media-services-job-notifications-with-net"></a>Monitorowanie powiadomień o zadaniach usługi Media Services za pomocą funkcji .NET za pomocą funkcji Azure Webhooks 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Podczas uruchamiania zadań często wymaga się sposobu śledzenia postępu zadania. Powiadomienia o zadaniach usługi Media Services można monitorować za pomocą programów Azure Webhooks lub [usługi Azure Queue storage](media-services-dotnet-check-job-progress-with-queues.md). W tym artykule pokazano, jak pracować z elementami webhook.

W tym artykule pokazano, jak

*  Zdefiniuj funkcję platformy Azure, która jest dostosowana do odpowiadania na pliki webhook. 
    
    W takim przypadku element webhook jest wyzwalany przez program Media Services, gdy zadanie kodowania zmienia stan. Funkcja nasłuchuje wywołania elementu webhook z powiadomień usługi Media Services i publikuje zasób wyjściowy po zakończeniu zadania. 
    
    >[!TIP]
    >Przed kontynuowaniem upewnij się, że rozumiesz, jak działają [powiązania HTTP i webhook usług Azure Functions.](../../azure-functions/functions-bindings-http-webhook.md)
    >
    
* Dodaj element webhook do zadania kodowania i określ adres URL elementu webhook i klucz tajny, na który odpowiada ten element webhook. Znajdziesz przykład, który dodaje element webhook do zadania kodowania na końcu artykułu.  

Definicje różnych usług Media Services .NET Azure Functions (w tym definicje pokazane w tym artykule) można [znaleźć tutaj](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są niezbędne następujące elementy:

* Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Konto usługi Media Services. Aby utworzyć konto usługi Media Services, zobacz temat [Jak utworzyć konto usługi Media Services](media-services-portal-create-account.md).
* Zrozumienie [sposobu korzystania z usługi Azure Functions](../../azure-functions/functions-overview.md). Ponadto przejrzyj [powiązania HTTP i webhook usługi Azure Functions HTTP i webhook](../../azure-functions/functions-bindings-http-webhook.md).

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. Przejdź do [witryny Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta Azure.
2. Utwórz aplikację funkcyjną w sposób opisany [w tym miejscu](../../azure-functions/functions-create-function-app-portal.md).

## <a name="configure-function-app-settings"></a>Konfigurowanie ustawień aplikacji funkcji

Podczas tworzenia funkcji usługi Media Services, jest przydatne do dodawania zmiennych środowiskowych, które będą używane w całej funkcji. Aby skonfigurować ustawienia aplikacji, kliknij łącze Konfiguruj ustawienia aplikacji. 

Sekcja [ustawień aplikacji](media-services-dotnet-how-to-use-azure-functions.md#configure-function-app-settings) definiuje parametry, które są używane w elementach webhook zdefiniowanych w tym artykule. Dodaj również następujące parametry do ustawień aplikacji. 

|Nazwa|Definicja|Przykład| 
|---|---|---|
|Signingkey |Klucz podpisywania.| j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt|
|Punkt webhookEndpoint | Adres punktu końcowego elementu webhook. Po utworzeniu funkcji elementu webhook można skopiować adres URL z linku **Pobierz adres URL funkcji.** | https:\//juliakofuncapp.azurewebsites.net/api/Notification_Webhook_Function?code=iN2phdrTnCxmvaKExFWOTulfnm4C71mMLIy8tzLr7Zvf6Z22HHIK5g==.|

## <a name="create-a-function"></a>Tworzenie funkcji

Po wdrożeniu aplikacji funkcji można ją znaleźć wśród **usług App Services** usługi Azure Functions.

1. Wybierz aplikację funkcji i kliknij pozycję **Nowa funkcja**.
2. Wybierz kod **C#** i api & scenariusz **webhooks.** 
3. Wybierz **ogólny element webhook - C#**.
4. Nazwij element webhook i naciśnij klawisz **Utwórz**.

### <a name="files"></a>Pliki

Funkcja platformy Azure jest skojarzona z plikami kodu i innymi plikami, które są opisane w tej sekcji. Domyślnie funkcja jest skojarzona z plikami **function.json** i **run.csx** (C#). Należy dodać plik **project.json.** W pozostałej części tej sekcji przedstawiono definicje tych plików.

![files](./media/media-services-azure-functions/media-services-azure-functions003.png)

#### <a name="functionjson"></a>function.json

Plik function.json definiuje powiązania funkcji i inne ustawienia konfiguracji. Środowisko wykonawcze używa tego pliku do określenia zdarzeń do monitorowania i jak przekazywać dane i zwracać dane z wykonywania funkcji. 

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

#### <a name="projectjson"></a>project.json

Plik project.json zawiera zależności. 

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
    
#### <a name="runcsx"></a>plik run.csx

Kod w tej sekcji przedstawia implementację funkcji platformy Azure, która jest elementem webhook. W tym przykładzie funkcja nasłuchuje wywołania elementu webhook z powiadomień usługi Media Services i publikuje zasób wyjściowy po zakończeniu zadania.

Element webhook oczekuje, że klucz podpisywania (poświadczenia) będzie zgodny z tym, który zostanie zdaszy podczas konfigurowania punktu końcowego powiadomienia. Klucz podpisywania to 64-bajtowa wartość zakodowana base64, która jest używana do ochrony i zabezpieczania wywołań zwrotnych elementów WebHook z usługi Azure Media Services. 

W kodzie definicji elementu webhook, który następuje **VerifyWebHookRequestSignature** metoda nie weryfikacji komunikatu powiadomienia. Celem tej weryfikacji jest upewnienie się, że wiadomość została wysłana przez usługę Azure Media Services i nie została zmodyfikowana. Podpis jest opcjonalny dla usługi Azure Functions, ponieważ ma wartość **Kod** jako parametr kwerendy za pomocą zabezpieczeń warstwy transportu (TLS). 

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

```csharp
///////////////////////////////////////////////////
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using Newtonsoft.Json;
using Microsoft.Azure;
using System.Net;
using System.Security.Cryptography;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

internal const string SignatureHeaderKey = "sha256";
internal const string SignatureHeaderValueTemplate = SignatureHeaderKey + "={0}";
static string _webHookEndpoint = Environment.GetEnvironmentVariable("WebHookEndpoint");
static string _signingKey = Environment.GetEnvironmentVariable("SigningKey");

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");

static readonly string _AMSClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _AMSClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static CloudMediaContext _context = null;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
    byte[] requestBody = await taskForRequestBody;

    string jsonContent = await req.Content.ReadAsStringAsync();
    log.Info($"Request Body = {jsonContent}");

    IEnumerable<string> values = null;
    if (req.Headers.TryGetValues("ms-signature", out values))
    {
        byte[] signingKey = Convert.FromBase64String(_signingKey);
        string signatureFromHeader = values.FirstOrDefault();

        if (VerifyWebHookRequestSignature(requestBody, signatureFromHeader, signingKey))
        {
            string requestMessageContents = Encoding.UTF8.GetString(requestBody);

            NotificationMessage msg = JsonConvert.DeserializeObject<NotificationMessage>(requestMessageContents);

            if (VerifyHeaders(req, msg, log))
            { 
                string newJobStateStr = (string)msg.Properties.Where(j => j.Key == "NewState").FirstOrDefault().Value;
                if (newJobStateStr == "Finished")
                {
                    AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                                AzureEnvironments.AzureCloudEnvironment);

                    AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                    if(_context!=null)   
                    {                        
                        string urlForClientStreaming = PublishAndBuildStreamingURLs(msg.Properties["JobId"]);
                        log.Info($"URL to the manifest for client streaming using HLS protocol: {urlForClientStreaming}");
                    }
                }

                return req.CreateResponse(HttpStatusCode.OK, string.Empty);
            }
            else
            {
                log.Info($"VerifyHeaders failed.");
                return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyHeaders failed.");
            }
        }
        else
        {
            log.Info($"VerifyWebHookRequestSignature failed.");
            return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyWebHookRequestSignature failed.");
        }
    }

    return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
}

private static string PublishAndBuildStreamingURLs(String jobID)
{
    IJob job = _context.Jobs.Where(j => j.Id == jobID).FirstOrDefault();
    IAsset asset = job.OutputMediaAssets.FirstOrDefault();

    // Create a 30-day readonly access policy. 
    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
    TimeSpan.FromDays(30),
    AccessPermissions.Read);

    // Create a locator to the streaming content on an origin. 
    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
    policy,
    DateTime.UtcNow.AddMinutes(-5));

    // Get a reference to the streaming manifest file from the  
    // collection of files in the asset. 
    var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                EndsWith(".ism")).
                FirstOrDefault();

    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest" +  "(format=m3u8-aapl)";
    return urlForClientStreaming;

}

private static bool VerifyWebHookRequestSignature(byte[] data, string actualValue, byte[] verificationKey)
{
    using (var hasher = new HMACSHA256(verificationKey))
    {
        byte[] sha256 = hasher.ComputeHash(data);
        string expectedValue = string.Format(CultureInfo.InvariantCulture, SignatureHeaderValueTemplate, ToHex(sha256));

        return (0 == String.Compare(actualValue, expectedValue, System.StringComparison.Ordinal));
    }
}

private static bool VerifyHeaders(HttpRequestMessage req, NotificationMessage msg, TraceWriter log)
{
    bool headersVerified = false;

    try
    {
        IEnumerable<string> values = null;
        if (req.Headers.TryGetValues("ms-mediaservices-accountid", out values))
        {
            string accountIdHeader = values.FirstOrDefault();
            string accountIdFromMessage = msg.Properties["AccountId"];

            if (0 == string.Compare(accountIdHeader, accountIdFromMessage, StringComparison.OrdinalIgnoreCase))
            {
                headersVerified = true;
            }
            else
            {
                log.Info($"accountIdHeader={accountIdHeader} does not match accountIdFromMessage={accountIdFromMessage}");
            }
        }
        else
        {
            log.Info($"Header ms-mediaservices-accountid not found.");
        }
    }
    catch (Exception e)
    {
        log.Info($"VerifyHeaders hit exception {e}");
        headersVerified = false;
    }

    return headersVerified;
}

private static readonly char[] HexLookup = new char[] { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };

/// <summary>
/// Converts a <see cref="T:byte[]"/> to a hex-encoded string.
/// </summary>
private static string ToHex(byte[] data)
{
    if (data == null)
    {
        return string.Empty;
    }

    char[] content = new char[data.Length * 2];
    int output = 0;
    byte d;

    for (int input = 0; input < data.Length; input++)
    {
        d = data[input];
        content[output++] = HexLookup[d / 0x10];
        content[output++] = HexLookup[d % 0x10];
    }

    return new string(content);
}

internal enum NotificationEventType
{
    None = 0,
    JobStateChange = 1,
    NotificationEndPointRegistration = 2,
    NotificationEndPointUnregistration = 3,
    TaskStateChange = 4,
    TaskProgress = 5
}

internal sealed class NotificationMessage
{
    public string MessageVersion { get; set; }
    public string ETag { get; set; }
    public NotificationEventType EventType { get; set; }
    public DateTime TimeStamp { get; set; }
    public IDictionary<string, string> Properties { get; set; }
}
```

Zapisz i uruchom swoją funkcję.

### <a name="function-output"></a>Wyjście funkcji

Po wyzwoleniu elementu webhook, powyższy przykład daje następujące dane wyjściowe, wartości będą się różnić.

    C# HTTP trigger function processed a request. RequestUri=https://juliako001-functions.azurewebsites.net/api/Notification_Webhook_Function?code=9376d69kygoy49oft81nel8frty5cme8hb9xsjslxjhalwhfrqd79awz8ic4ieku74dvkdfgvi
    Request Body = 
    {
      "MessageVersion": "1.1",
      "ETag": "b8977308f48858a8f224708bc963e1a09ff917ce730316b4e7ae9137f78f3b20",
      "EventType": 4,
      "TimeStamp": "2017-02-16T03:59:53.3041122Z",
      "Properties": {
        "JobId": "nb:jid:UUID:badd996c-8d7c-4ae0-9bc1-bd7f1902dbdd",
        "TaskId": "nb:tid:UUID:80e26fb9-ee04-4739-abd8-2555dc24639f",
        "NewState": "Finished",
        "OldState": "Processing",
        "AccountName": "mediapkeewmg5c3peq",
        "AccountId": "301912b0-659e-47e0-9bc4-6973f2be3424",
        "NotificationEndPointId": "nb:nepid:UUID:cb5d707b-4db8-45fe-a558-19f8d3306093"
      }
    }
    
    URL to the manifest for client streaming using HLS protocol: http://mediapkeewmg5c3peq.streaming.mediaservices.windows.net/0ac98077-2b58-4db7-a8da-789a13ac6167/BigBuckBunny.ism/manifest(format=m3u8-aapl)

## <a name="add-a-webhook-to-your-encoding-task"></a>Dodawanie elementu webhook do zadania kodowania

W tej sekcji kod, który dodaje powiadomienie elementu webhook do zadania jest wyświetlany. Można również dodać powiadomienie o poziomie zadania, które byłoby bardziej przydatne dla zadania z zadaniami łańcuchowymi.  

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Uzupełnij informacje w polach Nazwa, Lokalizacja i Nazwa rozwiązania, a następnie kliknij przycisk OK.
2. Użyj [NuGet,](https://www.nuget.org/packages/windowsazure.mediaservices) aby zainstalować usługę Azure Media Services.
3. Zaktualizuj plik App.config o odpowiednie wartości: 
    
   * Informacje o połączeniu usługi Azure Media Services, 
   * adres URL elementu webhook, który oczekuje otrzymywanych powiadomień, 
   * klucz podpisywania, który odpowiada kluczowi oczekiwanemu przez element webhook. Klucz podpisywania to 64-bajtowa wartość zakodowana base64, która jest używana do ochrony i zabezpieczania wywołań zwrotnych łączy webhook z usługi Azure Media Services. 

     ```xml
           <appSettings>
               <add key="AMSAADTenantDomain" value="domain" />
               <add key="AMSRESTAPIEndpoint" value="endpoint" />

               <add key="AMSClientId" value="clinet id" />
               <add key="AMSClientSecret" value="client secret" />

               <add key="WebhookURL" value="https://yourapp.azurewebsites.net/api/functionname?code=ApiKey" />
               <add key="WebhookSigningKey" value="j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt" />
           </appSettings>
     ```

4. Zaktualizuj plik Program.cs o następujący kod:

    ```csharp
            using System;
            using System.Configuration;
            using System.Linq;
            using Microsoft.WindowsAzure.MediaServices.Client;

            namespace NotificationWebHook
            {
                class Program
                {
                // Read values from the App.config file.
                private static readonly string _AMSAADTenantDomain =
                    ConfigurationManager.AppSettings["AMSAADTenantDomain"];
                private static readonly string _AMSRESTAPIEndpoint =
                    ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];

                private static readonly string _AMSClientId =
                    ConfigurationManager.AppSettings["AMSClientId"];
                private static readonly string _AMSClientSecret =
                    ConfigurationManager.AppSettings["AMSClientSecret"];

                private static readonly string _webHookEndpoint =
                    ConfigurationManager.AppSettings["WebhookURL"];
                private static readonly string _signingKey =
                    ConfigurationManager.AppSettings["WebhookSigningKey"];

                // Field for service context.
                private static CloudMediaContext _context = null;

                static void Main(string[] args)
                {
                    AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AMSAADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                    AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_AMSRESTAPIEndpoint), tokenProvider);

                    byte[] keyBytes = Convert.FromBase64String(_signingKey);

                    IAsset newAsset = _context.Assets.FirstOrDefault();

                    // Check for existing Notification Endpoint with the name "FunctionWebHook"

                    var existingEndpoint = _context.NotificationEndPoints.Where(e => e.Name == "FunctionWebHook").FirstOrDefault();
                    INotificationEndPoint endpoint = null;

                    if (existingEndpoint != null)
                    {
                    Console.WriteLine("webhook endpoint already exists");
                    endpoint = (INotificationEndPoint)existingEndpoint;
                    }
                    else
                    {
                    endpoint = _context.NotificationEndPoints.Create("FunctionWebHook",
                        NotificationEndPointType.WebHook, _webHookEndpoint, keyBytes);
                    Console.WriteLine("Notification Endpoint Created with Key : {0}", keyBytes.ToString());
                    }

                    // Declare a new encoding job with the Standard encoder
                    IJob job = _context.Jobs.Create("MES Job");

                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    ITask task = job.Tasks.AddNew("My encoding task",
                    processor,
                    "Adaptive Streaming",
                    TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(newAsset);

                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew(newAsset.Name, AssetCreationOptions.None);

                    // Add the WebHook notification to this Task and request all notification state changes.
                    // Note that you can also add a job level notification
                    // which would be more useful for a job with chained tasks.  
                    if (endpoint != null)
                    {
                    task.TaskNotificationSubscriptions.AddNew(NotificationJobState.All, endpoint, true);
                    Console.WriteLine("Created Notification Subscription for endpoint: {0}", _webHookEndpoint);
                    }
                    else
                    {
                    Console.WriteLine("No Notification Endpoint is being used");
                    }

                    job.Submit();

                    Console.WriteLine("Expect WebHook to be triggered for the Job ID: {0}", job.Id);
                    Console.WriteLine("Expect WebHook to be triggered for the Task ID: {0}", task.Id);

                    Console.WriteLine("Job Submitted");

                }
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                    if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                    return processor;
                }
                }
            }
    ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
