---
title: Transmisja strumieniowa na żywo z usługi Azure Media Services w wersji 3 przy użyciu platformy .NET Core | Dokumentacja firmy Microsoft
description: Ten samouczek przedstawia czynności umożliwiające transmisję strumieniową na żywo za pomocą usługi Media Services w wersji 3 przy użyciu platformy .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: f02820f8382268dfedeee8d07de2438660e5e33e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236886"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>Transmisja strumieniowa na żywo z usługi Azure Media Services w wersji 3 przy użyciu platformy .NET Core

W usłudze Media Services kanały [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) są odpowiedzialne za przetwarzanie zawartości transmisji strumieniowej na żywo. Kanał LiveEvent udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który należy przekazać do kodera transmisji na żywo. Kanał LiveEvent odbiera strumienie wejściowe na żywo z kodera na żywo i udostępnia je do przesyłania strumieniowego za pośrednictwem co najmniej jednego [punktu końcowego strumieniowania](https://docs.microsoft.com/rest/api/media/streamingendpoints). Kanał oferuje również punkt końcowy podglądu (adres URL monitorowania), dzięki któremu można wyświetlać podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem. W tym samouczku przedstawiono sposób użycia platformy .NET Core do tworzenia **kanału do przekazywania zawartości** wydarzeń na żywo. 

> [!NOTE]
> Przed kontynuowaniem przejrzyj sekcję [Transmisja strumieniowa na żywo przy użyciu usługi Media Services v3](live-streaming-overview.md). 

Ten samouczek przedstawia sposób wykonania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie konta usługi Media Services
> * Uzyskiwanie dostępu do interfejsu API usługi Media Services
> * Konfigurowanie przykładowej aplikacji
> * Analizowanie kodu, który przeprowadza transmisję strumieniową na żywo
> * Obejrzyj to zdarzenie w odtwarzaczu [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) pod adresem http://ampdemo.azureedge.net
> * Oczyszczanie zasobów

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Instalowanie narzędzia Visual Studio Code lub Visual Studio
* Kamera lub urządzenie (np. laptop) do transmisji wydarzenia.
* Lokalny koder wideo na żywo, który konwertuje sygnały z kamery na strumienie wysyłane do usługi transmisji strumieniowej na żywo Media Services. Strumień musi być w formacie **RTMP** lub **Smooth Streaming**.

## <a name="download-the-sample"></a>Pobierz przykład

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Przykład transmisji strumieniowej na żywo znajduje się w folderze [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

> [!IMPORTANT]
> W przykładzie każdy zasób używa innego sufiksu. W przypadku anulowania debugowania lub zakończenie pracy aplikacji bez doprowadzenia jej do końca na Twoim koncie pozostanie wiele kanałów LiveEvent. <br/>
> Pamiętaj, aby zatrzymać uruchomione kanały LiveEvent. W przeciwnym razie zostaną **naliczone opłaty**.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Analizowanie kodu, który przeprowadza transmisję strumieniową na żywo

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) projektu *MediaV3LiveApp*.

W przykładzie jest tworzony unikatowy sufiks dla każdego zasobu, tak aby uniknąć konfliktów nazw przy wielokrotnym uruchomieniu przykładu bez uprzedniego oczyszczenia.

> [!IMPORTANT]
> W przykładzie każdy zasób używa innego sufiksu. W przypadku anulowania debugowania lub zakończenie pracy aplikacji bez doprowadzenia jej do końca na Twoim koncie pozostanie wiele kanałów LiveEvent. <br/>
> Pamiętaj, aby zatrzymać uruchomione kanały LiveEvent. W przeciwnym razie zostaną **naliczone opłaty**.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials w oparciu o poświadczenia podane w lokalnym pliku konfiguracji.  

```csharp
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>Utwórz wydarzenie na żywo

W tej sekcji przedstawiono sposób tworzenia **przekazywanego** typu kanału LiveEvent (o wartości parametru LiveEventEncodingType ustawionej na None). Jeśli chcesz utworzyć kanał LiveEvent obsługujący kodowanie na żywo, musisz nadać parametrowi LiveEventEncodingType wartość Basic. 

Jest kilka rzeczy, które warto określić podczas tworzenia zdarzenia na żywo:

* Lokalizacja usług Media Services 
* Protokół transmisji strumieniowej wydarzenia na żywo (obecnie obsługiwane są protokoły RTMP i Smooth Streaming)
       
    Nie można zmienić opcji protokołu, gdy kanał LiveEvent lub skojarzone z nim obiekty LiveOutput są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał LiveEvent dla każdego protokołu przesyłania strumieniowego.  
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym kanale LiveEvent. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).
    
    Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

Podczas tworzenia zdarzenia można określić, że będzie ono automatycznie uruchamiane. 

```csharp
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                new IPRange (
                    name: "AllowAll",
                    address: "0.0.0.0",
                    subnetPrefixLength: 0
                )
            }
        )
    )
};

// This can sometimes take awhile. Be patient.
LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location, 
    description:"Sample LiveEvent for testing",
    vanityUrl:false,
    encoding: new LiveEventEncoding(
                // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                encodingType:LiveEventEncodingType.None, 
                presetName:null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP), 
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency.
        // Low latency reduces the amount of buffering Media Services does.
        // Low latency can also reduce the stability of the live stream. 
        StreamOptionsFlag.Default
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");
liveEvent = client.LiveEvents.Create(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart:true);
```

### <a name="get-ingest-urls"></a>Pobieranie adresów URL pozyskiwania

Po utworzeniu kanału można pobrać adresy URL pozyskiwania, które należy udostępnić koderowi na żywo. Koder używa tych adresów URL do wprowadzenia strumienia na żywo.


```csharp
// Get the input endpoint to configure the on premise encoder with
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on premise encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>Pobieranie adresu URL podglądu

Użyj funkcji previewEndpoint do podglądu i sprawdź, czy dane wejściowe z kodera są faktycznie odbierane.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że wideo jest przesyłane do adresu URL podglądu!

```sharp
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}");
Console.WriteLine();
```

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>Tworzenie obiektów LiveEvent i LiveOutput i zarządzanie nimi

Po przesłaniu strumienia do kanału LiveEvent można rozpocząć zdarzenie przesyłania strumieniowego poprzez utworzenie elementu zawartości oraz obiektu LiveOutput i lokalizatora przesyłania strumieniowego. Spowoduje to archiwizację strumienia i udostępnienie go użytkownikom za pośrednictwem punktu końcowego przesyłania strumieniowego. 

#### <a name="create-an-asset"></a>Tworzenie zasobu

Utwórz zasób przeznaczony do użycia przez obiekt LiveOutput.

```csharp
string assetName = "archiveAsset" + uniqueness;
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = client.Assets.CreateOrUpdate(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-liveoutput"></a>Tworzenie obiektu LiveOutput

```csharp
string manifestName = "output";
string liveOutputName = "liveOutput" + uniqueness;
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = client.LiveOutputs.Create(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaminglocator"></a>Tworzenie lokalizatora przesyłania strumieniowego

> [!NOTE]
> Po utworzeniu konta usług Media Services do Twojego konta dodawany jest **domyślny** punkt końcowy przesyłania strumieniowego w stanie **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 


```csharp
StreamingLocator locator = new StreamingLocator(assetName: assetName, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = client.StreamingLocators.Create(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(config.ResourceGroup, config.AccountName, "default");

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    client.StreamingEndpoints.Start(config.ResourceGroup, config.AccountName, "default");
}

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Aby po zakończeniu strumieniowego przesyłania zdarzeń, wyczyścić udostępnione wcześniej zasoby, postępuj zgodnie z poniższą procedurą.

* Zatrzymaj wypychanie strumienia z kodera.
* Zatrzymaj obiekt LiveEvent. Po zatrzymaniu kanału LiveEvent opłaty nie będą naliczane. W razie potrzeby ponownego uruchomienia kanał będzie miał ten sam adres URL pozyskiwania, więc nie trzeba będzie ponownie konfigurować kodera.
* Można zatrzymać punkt końcowy przesyłania strumieniowego, chyba że chcesz nadal udostępniać archiwum zdarzenia na żywo w formie przesyłania strumieniowego na żądanie. Jeśli kanał LiveEvent jest zatrzymany, żadne opłaty nie będą naliczane.

```csharp
private static void CleanupLiveEventAndOutput(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName, string liveOutputName)
{
    // Delete the LiveOutput
    client.LiveOutputs.Delete(resourceGroup, accountName, liveEventName, liveOutputName);

    // Stop and delete the LiveEvent
    client.LiveEvents.Stop(resourceGroup, accountName, liveEventName);
    client.LiveEvents.Delete(resourceGroup, accountName, liveEventName);
}

private static void CleanupLocatorAssetAndStreamingEndpoint(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    // Delete the Streaming Locator
    client.StreamingLocators.Delete(resourceGroup, accountName, streamingLocatorName);

    // Delete the Archive Asset
    client.Assets.Delete(resourceGroup, accountName, assetName);
}

private static void CleanupAccount(IAzureMediaServicesClient client, string resourceGroup, string accountName)
{
    try{
        Console.WriteLine("Cleaning up the resources used, stopping the LiveEvent. This can take a few minutes to complete.");
        Console.WriteLine();

        var events = client.LiveEvents.List(resourceGroup, accountName);
        
        foreach (LiveEvent l in events)
        {
            if (l.Name == liveEventName){
                var outputs = client.LiveOutputs.List(resourceGroup, accountName, l.Name);

                foreach (LiveOutput o in outputs)
                {
                    client.LiveOutputs.Delete(resourceGroup, accountName, l.Name, o.Name);
                    Console.WriteLine($"LiveOutput: {o.Name} deleted from LiveEvent {l.Name}. The archived Asset and Streaming URLs are still retained for on-demand viewing.");
                }

                if (l.ResourceState == LiveEventResourceState.Running){
                    client.LiveEvents.Stop(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Stopped.");
                    client.LiveEvents.Delete(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Deleted.");
                    Console.WriteLine();
                }
            }
        }
    } 
    catch(ApiErrorException e)
    {
        Console.WriteLine("Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();

    }
}
```        

## <a name="watch-the-event"></a>Oglądanie wydarzenia

Aby oglądać wydarzenie, skopiuj adres URL przesyłania strumieniowego, który uzyskano po uruchomieniu kodu opisanego w sekcji [Tworzenie lokatora przesyłania strumieniowego](#create-a-streaminglocator) i użyj wybranego odtwarzacza. Możesz przetestować strumień za pomocą usługi [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) pod adresem http://ampdemo.azureedge.net. 

Po zatrzymaniu wydarzenia na żywo wydarzenie jest automatycznie konwertowane na zawartość na żądanie. Nawet po zatrzymaniu i usunięciu wydarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte. Nie można usunąć elementu zawartości, jeśli jest on używany przez wydarzenie. Najpierw należy usunąć wydarzenie. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej. Do tego celu możesz użyć narzędzia **CloudShell**.

W usłudze **CloudShell** uruchom następujące polecenie:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Pozostawienie uruchomionego obiektu LiveEvent wiąże się z naliczaniem opłat. Należy pamiętać, jeśli projekt/program ulegnie awarii lub zostanie zamknięty z jakiegokolwiek powodu, kanał LiveEvent pozostanie uruchomiony w stanie naliczania opłat.

## <a name="next-steps"></a>Następne kroki

[Pliki strumieniowe](stream-files-tutorial-with-api.md)

