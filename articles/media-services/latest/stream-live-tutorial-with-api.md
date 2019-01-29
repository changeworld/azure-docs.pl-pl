---
title: Transmisja strumieniowa na żywo z usługi Azure Media Services w wersji 3 | Microsoft Docs
description: Ten samouczek przedstawia czynności umożliwiające transmisję strumieniową na żywo za pomocą usługi Media Services w wersji 3 przy użyciu platformy .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/22/2019
ms.author: juliako
ms.openlocfilehash: c51a36f4380199de1ac62ef3f0c32bd0a8f06c01
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811217"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-apis"></a>Samouczek: transmisja strumieniowa na żywo z usługi Media Services w wersji 3 przy użyciu interfejsów API

W usłudze Azure Media Services kanały [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) są odpowiedzialne za przetwarzanie zawartości transmisji strumieniowej na żywo. Kanał LiveEvent udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który należy przekazać do kodera transmisji na żywo. Kanał LiveEvent odbiera strumienie wejściowe na żywo z kodera na żywo i udostępnia je do przesyłania strumieniowego za pośrednictwem co najmniej jednego [punktu końcowego strumieniowania](https://docs.microsoft.com/rest/api/media/streamingendpoints). Kanał oferuje również punkt końcowy podglądu (adres URL monitorowania), dzięki któremu można wyświetlać podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem. W tym samouczku przedstawiono sposób użycia platformy .NET Core do tworzenia **kanału do przekazywania zawartości** wydarzeń na żywo. 

> [!NOTE]
> Przed kontynuowaniem przejrzyj sekcję [Transmisja strumieniowa na żywo przy użyciu usługi Media Services v3](live-streaming-overview.md). 

Ten samouczek przedstawia sposób wykonania następujących czynności:    

> [!div class="checklist"]
> * Uzyskiwanie dostępu do interfejsu API usługi Media Services
> * Konfigurowanie przykładowej aplikacji
> * Analizowanie kodu, który przeprowadza transmisję strumieniową na żywo
> * Obejrzyj to zdarzenie w odtwarzaczu [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) pod adresem http://ampdemo.azureedge.net
> * Oczyszczanie zasobów

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

- Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
- Zainstaluj interfejs wiersza polecenia i korzystaj z niego lokalnie. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie polecenia [interfejsu wiersza polecenia usługi Media Services w wersji 3](https://aka.ms/ams-v3-cli-ref) działają w usłudze Azure Cloud Shell. Zaleca się używanie interfejsu wiersza polecenia lokalnie.

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).

    Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services

- Kamera lub urządzenie (np. laptop) do transmisji wydarzenia.
- Lokalny koder wideo na żywo, który konwertuje sygnały z kamery na strumienie wysyłane do usługi transmisji strumieniowej na żywo Media Services. Strumień musi być w formacie **RTMP** lub **Smooth Streaming**.

## <a name="download-the-sample"></a>Pobierz przykład

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Przykład transmisji strumieniowej na żywo znajduje się w folderze [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

> [!IMPORTANT]
> W przykładzie każdy zasób używa innego sufiksu. W przypadku anulowania debugowania lub zakończenie pracy aplikacji bez doprowadzenia jej do końca na Twoim koncie pozostanie wiele kanałów LiveEvent. <br/>
> Pamiętaj, aby zatrzymać uruchomione kanały LiveEvent. W przeciwnym razie zostaną **naliczone opłaty**.

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Analizowanie kodu, który przeprowadza transmisję strumieniową na żywo

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) projektu *MediaV3LiveApp*.

W przykładzie jest tworzony unikatowy sufiks dla każdego zasobu, tak aby uniknąć konfliktów nazw przy wielokrotnym uruchomieniu przykładu bez uprzedniego oczyszczenia.

> [!IMPORTANT]
> W przykładzie każdy zasób używa innego sufiksu. W przypadku anulowania debugowania lub zakończenie pracy aplikacji bez doprowadzenia jej do końca na Twoim koncie pozostanie wiele kanałów LiveEvent. <br/>
> Pamiętaj, aby zatrzymać uruchomione kanały LiveEvent. W przeciwnym razie zostaną **naliczone opłaty**.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Utwórz wydarzenie na żywo

W tej sekcji przedstawiono sposób tworzenia **przekazywanego** typu kanału LiveEvent (o wartości parametru LiveEventEncodingType ustawionej na None). Jeśli chcesz utworzyć kanał LiveEvent obsługujący kodowanie na żywo, musisz nadać parametrowi LiveEventEncodingType wartość **Standard**. 

Jest kilka rzeczy, które warto określić podczas tworzenia zdarzenia na żywo:

* Lokalizacja usług Media Services 
* Protokół transmisji strumieniowej wydarzenia na żywo (obecnie obsługiwane są protokoły RTMP i Smooth Streaming)
       
    Nie można zmienić opcji protokołu, gdy kanał LiveEvent lub skojarzone z nim obiekty LiveOutput są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał LiveEvent dla każdego protokołu przesyłania strumieniowego.  
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym kanale LiveEvent. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).
    
    Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.
    
    Adresy IP muszą mieć jeden z następujących formatów: adres IPv4 z 4 cyframi, zakres adresów CIDR.

* Podczas tworzenia zdarzenia można określić, że będzie ono automatycznie uruchamiane. 

    Jeśli automatyczne uruchamianie zostanie ustawione na wartość true, wydarzenie na żywo rozpocznie się po utworzeniu. Oznacza to, że rozliczenia rozpoczną się po uruchomieniu wydarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie LiveEvent, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [LiveEvent states and billing (Stany i rozliczenia dotyczące elementu LiveEvent)](live-event-states-billing.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Pobieranie adresów URL pozyskiwania

Po utworzeniu kanału LiveEvent można pobrać adresy URL pozyskiwania, które należy udostępnić koderowi na żywo. Koder używa tych adresów URL do wprowadzenia strumienia na żywo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Pobieranie adresu URL podglądu

Użyj funkcji previewEndpoint do podglądu i sprawdź, czy dane wejściowe z kodera są faktycznie odbierane.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że wideo jest przesyłane do adresu URL podglądu!

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>Tworzenie obiektów LiveEvent i LiveOutput i zarządzanie nimi

Po przesłaniu strumienia do kanału LiveEvent można rozpocząć zdarzenie przesyłania strumieniowego poprzez utworzenie elementu zawartości oraz obiektu LiveOutput i lokalizatora przesyłania strumieniowego. Spowoduje to archiwizację strumienia i udostępnienie go użytkownikom za pośrednictwem punktu końcowego przesyłania strumieniowego. 

#### <a name="create-an-asset"></a>Tworzenie zasobu

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

Utwórz zasób przeznaczony do użycia przez obiekt LiveOutput.

#### <a name="create-a-liveoutput"></a>Tworzenie obiektu LiveOutput

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaminglocator"></a>Tworzenie lokalizatora przesyłania strumieniowego

> [!NOTE]
> Po utworzeniu konta usług Media Services do Twojego konta dodawany jest **domyślny** punkt końcowy przesyłania strumieniowego w stanie **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

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

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Oglądanie wydarzenia

Aby oglądać wydarzenie, skopiuj adres URL przesyłania strumieniowego, który uzyskano po uruchomieniu kodu opisanego w sekcji [Tworzenie lokatora przesyłania strumieniowego](#create-a-streaminglocator) i użyj wybranego odtwarzacza. Możesz przetestować strumień za pomocą usługi [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) pod adresem http://ampdemo.azureedge.net. 

Po zatrzymaniu wydarzenia na żywo wydarzenie jest automatycznie konwertowane na zawartość na żądanie. Nawet po zatrzymaniu i usunięciu wydarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte. Nie można usunąć elementu zawartości, jeśli jest on używany przez wydarzenie. Najpierw należy usunąć wydarzenie. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Pozostawienie uruchomionego obiektu LiveEvent wiąże się z naliczaniem opłat. Należy pamiętać, jeśli projekt/program ulegnie awarii lub zostanie zamknięty z jakiegokolwiek powodu, kanał LiveEvent pozostanie uruchomiony w stanie naliczania opłat.

## <a name="next-steps"></a>Następne kroki

[Pliki strumieniowe](stream-files-tutorial-with-api.md)

