---
title: Transmisja strumieniowa na żywo z usługi Azure Media Services w wersji 3 | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki przesyłania strumieniowego na żywo z Media Services v3.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: b69bd62cb9bbe44fb37b3f3660c2f20f3965384e
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051575"
---
# <a name="tutorial-stream-live-with-media-services"></a>Samouczek: Przesyłanie strumieniowe na żywo za pomocą Media Services

> [!NOTE]
> Mimo że w samouczku są używane przykłady [zestawu SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , ogólne kroki są takie same dla [interfejsów API REST](https://docs.microsoft.com/rest/api/media/liveevents), interfejsu [wiersza polecenia](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)lub innych obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

W usłudze Azure Media Services [wydarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) są odpowiedzialne za przetwarzanie zawartości transmisji strumieniowej na żywo. Wydarzenie na żywo udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który należy przekazać do kodera na żywo. Wydarzenie na żywo odbiera strumienie wejściowe na żywo z kodera na żywo i udostępnia je do przesyłania strumieniowego za pośrednictwem co najmniej jednego [punktu końcowego przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints). Wydarzenia na żywo oferują również punkt końcowy podglądu (adres URL podglądu), dzięki któremu można wyświetlać podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem. W tym samouczku przedstawiono sposób użycia platformy .NET Core do tworzenia **kanału do przekazywania zawartości** wydarzeń na żywo. 

Ten samouczek przedstawia sposób wykonania następujących czynności:    

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji opisanej w temacie
> * Analizowanie kodu, który przeprowadza transmisję strumieniową na żywo
> * Obejrzyj to zdarzenie w odtwarzaczu [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) pod adresem https://ampdemo.azureedge.net
> * Oczyszczanie zasobów

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

- Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.
- Kamera lub urządzenie (np. laptop) do transmisji wydarzenia.
- Lokalny koder wideo na żywo, który konwertuje sygnały z kamery na strumienie wysyłane do usługi transmisji strumieniowej na żywo Media Services. Strumień musi być w formacie **RTMP** lub **Smooth Streaming**.

> [!TIP]
> Przed kontynuowaniem przejrzyj sekcję [Transmisja strumieniowa na żywo przy użyciu usługi Media Services v3](live-streaming-overview.md). 

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Przykład transmisji strumieniowej na żywo znajduje się w folderze [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Otwórz plik [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) z pobranego projektu. Zastąp wartości przy użyciu poświadczeń uzyskanych w sekcji z opisem [uzyskiwania dostępu do interfejsów API](access-api-cli-how-to.md).

> [!IMPORTANT]
> W przykładzie każdy zasób używa innego sufiksu. W przypadku anulowania debugowania lub zakończenia pracy aplikacji bez doprowadzenia jej do końca na Twoim koncie pozostanie wiele wydarzeń na żywo. <br/>Pamiętaj, aby zatrzymać uruchomione wydarzenia na żywo. W przeciwnym razie zostaną **naliczone opłaty**.

## <a name="examine-the-code-that-performs-live-streaming"></a>Analizowanie kodu, który przeprowadza transmisję strumieniową na żywo

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) projektu *MediaV3LiveApp*.

W przykładzie jest tworzony unikatowy sufiks dla każdego zasobu, tak aby uniknąć konfliktów nazw przy wielokrotnym uruchomieniu przykładu bez uprzedniego oczyszczenia.

> [!IMPORTANT]
> W przykładzie każdy zasób używa innego sufiksu. W przypadku anulowania debugowania lub zakończenia pracy aplikacji bez doprowadzenia jej do końca na Twoim koncie pozostanie wiele wydarzeń na żywo. <br/>
> Pamiętaj, aby zatrzymać uruchomione wydarzenia na żywo. W przeciwnym razie zostaną **naliczone opłaty**.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Utwórz wydarzenie na żywo

W tej sekcji przedstawiono sposób tworzenia **przekazywanego** typu wydarzenia na żywo (o wartości parametru LiveEventEncodingType ustawionej na None). Aby uzyskać więcej informacji na temat dostępnych typów zdarzeń na żywo, zobacz [typy zdarzeń na żywo](live-events-outputs-concept.md#live-event-types). 
 
Niektóre elementy, które można określić podczas tworzenia zdarzenia na żywo są następujące:

* Lokalizacja usług Media Services 
* Protokół transmisji strumieniowej wydarzenia na żywo (obecnie obsługiwane są protokoły RTMP i Smooth Streaming).<br/>Nie można zmienić opcji protokołu, gdy wydarzenie na żywo lub skojarzone z nim dane wyjściowe na żywo są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobne wydarzenie na żywo dla każdego protokołu przesyłania strumieniowego.  
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym wydarzeniu na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).<br/>Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą mieć jeden z następujących formatów: adres IPv4 z 4 cyframi, zakres adresów CIDR.
* Podczas tworzenia zdarzenia można określić, że będzie ono automatycznie uruchamiane. <br/>Jeśli automatyczne uruchamianie zostanie ustawione na wartość true, wydarzenie na żywo rozpocznie się po utworzeniu. Oznacza to, że rozliczanie zaczyna się zaraz po rozpoczęciu uruchamiania zdarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
* Aby adres URL pozyskiwania był predykcyjny, ustaw tryb "znaczącym". Aby uzyskać szczegółowe informacje, zobacz [adresy URL](live-events-outputs-concept.md#live-event-ingest-urls)pozyskiwania zdarzeń na żywo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Pobieranie adresów URL pozyskiwania

Po utworzeniu wydarzenia na żywo można pobrać adresy URL pozyskiwania, które należy udostępnić koderowi na żywo. Koder używa tych adresów URL do wprowadzenia strumienia na żywo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Pobieranie adresu URL podglądu

Użyj funkcji previewEndpoint do podglądu i sprawdź, czy dane wejściowe z kodera są faktycznie odbierane.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że wideo jest przesyłane do adresu URL podglądu!

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Tworzenie wydarzeń i danych wyjściowych na żywo oraz zarządzanie nimi

Po przesłaniu strumienia do wydarzenia na żywo można rozpocząć wydarzenie przesyłania strumieniowego poprzez utworzenie zasobu, danych wyjściowych na żywo i lokalizatora przesyłania strumieniowego. Spowoduje to archiwizację strumienia i udostępni go użytkownikom za pośrednictwem punktu końcowego przesyłania strumieniowego. 

#### <a name="create-an-asset"></a>Tworzenie zasobu

Utwórz zasób przeznaczony do użycia przez dane wyjściowe na żywo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Tworzenie danych wyjściowych na żywo

Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Usunięcie danych wyjściowych na żywo nie powoduje usunięcia bazowego zasobu ani znajdującej się w nim zawartości.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

> [!NOTE]
> Po utworzeniu konta usług Media Services do Twojego konta dodawany jest **domyślny** punkt końcowy przesyłania strumieniowego w stanie **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z [dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Podczas publikowania zasobu danych wyjściowych na żywo przy użyciu lokalizatora przesyłania strumieniowego wydarzenie na żywo (aż do długości okna DVR) będzie cały czas widoczne aż do momentu wygaśnięcia lokalizatora przesyłania strumieniowego lub jego usunięcia, zależnie od tego, co nastąpi wcześniej.

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
* Zatrzymaj wydarzenie na żywo. Po zatrzymaniu wydarzenia na żywo opłaty nie będą za nie naliczane. W razie potrzeby ponownego uruchomienia kanał będzie miał ten sam adres URL pozyskiwania, więc nie trzeba będzie ponownie konfigurować kodera.
* Można zatrzymać punkt końcowy przesyłania strumieniowego, chyba że chcesz nadal udostępniać archiwum zdarzenia na żywo w formie przesyłania strumieniowego na żądanie. Jeśli wydarzenie na żywo będzie w stanie zatrzymanym, żadne opłaty nie będą naliczane.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Oglądanie wydarzenia

Aby oglądać wydarzenie, skopiuj adres URL przesyłania strumieniowego, który uzyskano po uruchomieniu kodu opisanego w sekcji Tworzenie lokalizatora przesyłania strumieniowego, i użyj wybranego odtwarzacza. Możesz przetestować strumień za pomocą usługi [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) pod adresem https://ampdemo.azureedge.net. 

Po zatrzymaniu wydarzenia na żywo wydarzenie jest automatycznie konwertowane na zawartość na żądanie. Nawet po zatrzymaniu i usunięciu wydarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte. Nie można usunąć elementu zawartości, jeśli jest on używany przez wydarzenie. Najpierw należy usunąć wydarzenie. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Pozostawienie uruchomionego wydarzenia na żywo wiąże się z naliczaniem opłat. Należy pamiętać, że jeśli projekt/program ulegnie awarii lub zostanie zamknięty z jakiegokolwiek powodu, wydarzenie na żywo może pozostać uruchomione w stanie naliczania opłat.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

[Pliki strumieniowe](stream-files-tutorial-with-api.md)
 
