---
title: Transmisja na żywo za pomocą usługi Media Services w wersji 3
titleSuffix: Azure Media Services
description: Dowiedz się, jak przesyłać strumieniowo na żywo za pomocą usługi Azure Media Services w wersji 3.
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
ms.openlocfilehash: 0b6667965ddd1fce30bb2da2593e2a9274b595ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472020"
---
# <a name="tutorial-stream-live-with-media-services"></a>Samouczek: Przesyłanie strumieniowe na żywo za pomocą usług multimedialnych

> [!NOTE]
> Mimo że w samouczku użyto przykładów [zestawu .NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) ogólne kroki są takie same dla [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/liveevents) [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)lub innych [obsługiwanych zestawu SDK.](media-services-apis-overview.md#sdks)

W usłudze Azure Media Services [wydarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) są odpowiedzialne za przetwarzanie zawartości transmisji strumieniowej na żywo. Wydarzenie na żywo udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który należy przekazać do kodera na żywo. Wydarzenie na żywo odbiera strumienie wejściowe na żywo z kodera na żywo i udostępnia je do przesyłania strumieniowego za pośrednictwem co najmniej jednego [punktu końcowego przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints). Wydarzenia na żywo oferują również punkt końcowy podglądu (adres URL podglądu), dzięki któremu można wyświetlać podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem. W tym samouczku przedstawiono sposób użycia platformy .NET Core do tworzenia **kanału do przekazywania zawartości** wydarzeń na żywo.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykładową aplikację opisaną w temacie.
> * Sprawdź kod, który wykonuje transmisje strumieniowe na żywo.
> * Obejrzyj wydarzenie za pomocą [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net)programu Azure Media [Player](https://amp.azure.net/libs/amp/latest/docs/index.html) pod adresem .
> * Oczyszczenie zasobów.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są niezbędne następujące elementy:

- Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Pamiętaj, aby zapamiętać wartości używane dla nazwy grupy zasobów i nazwy konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Musisz ich używać, aby uzyskać dostęp do interfejsu API.
- Aparat lub urządzenie (takie jak laptop), które jest używane do emisji wydarzenia.
- Lokalny koder na żywo, który konwertuje sygnały z kamery na strumienie wysyłane do usługi Przesyłania strumieniowego na żywo w usłudze Media Services, zobacz [zalecane lokalne kodery na żywo.](recommended-on-premises-live-encoders.md) Strumień musi być w formacie **RTMP** lub **Smooth Streaming**.

> [!TIP]
> Przed kontynuowaniem przejrzyj sekcję [Transmisja strumieniowa na żywo przy użyciu usługi Media Services v3](live-streaming-overview.md). 

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Przykład transmisji strumieniowej na żywo znajduje się w folderze [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Otwórz [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) w pobranym projekcie. Zastąp wartości poświadczeniami, które otrzymałeś [w celu uzyskania dostępu do interfejsów API](access-api-cli-how-to.md).

> [!IMPORTANT]
> W tym przykładzie użyto unikatowego sufiksu dla każdego zasobu. Jeśli anulujesz debugowanie lub zakończysz aplikację bez uruchamiania jej, skończysz z wieloma wydarzeniami na żywo na swoim koncie. <br/>Pamiętaj, aby zatrzymać uruchomione wydarzenia na żywo. W przeciwnym razie zostanie **naliczona faktura!**

## <a name="examine-the-code-that-performs-live-streaming"></a>Analizowanie kodu, który przeprowadza transmisję strumieniową na żywo

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) projektu *MediaV3LiveApp*.

Przykład tworzy unikatowy sufiks dla każdego zasobu, dzięki czemu nie masz kolizji nazw, jeśli próbka zostanie uruchomiony wiele razy bez czyszczenia.

> [!IMPORTANT]
> W tym przykładzie użyto unikatowego sufiksu dla każdego zasobu. Jeśli anulujesz debugowanie lub zakończysz aplikację bez uruchamiania jej, skończysz z wieloma wydarzeniami na żywo na swoim koncie. <br/>
> Pamiętaj, aby zatrzymać uruchomione wydarzenia na żywo. W przeciwnym razie zostanie **naliczona faktura!**

### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Utwórz wydarzenie na żywo

W tej sekcji przedstawiono sposób tworzenia **przekazywanego** typu wydarzenia na żywo (o wartości parametru LiveEventEncodingType ustawionej na None). Aby uzyskać więcej informacji na temat dostępnych typów wydarzeń na żywo, zobacz [Typy zdarzeń na żywo](live-events-outputs-concept.md#live-event-types). 
 
Niektóre rzeczy, które można określić podczas tworzenia wydarzenia na żywo są:

* Lokalizacja usługi Media Services.
* Protokół transmisji strumieniowej wydarzenia na żywo (obecnie obsługiwane są protokoły RTMP i Smooth Streaming).<br/>Nie można zmienić opcji protokołu, gdy jest uruchomione zdarzenie na żywo lub skojarzone z nim dane wyjściowe na żywo. Jeśli potrzebujesz różnych protokołów, utwórz oddzielne wydarzenie na żywo dla każdego protokołu przesyłania strumieniowego.  
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym wydarzeniu na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).<br/>Jeśli nie określono żadnych adresów IP i nie ma definicji reguły, nie będzie dozwolony żaden adres IP. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą być w jednym z następujących formatów: adres IpV4 z czterema numerami lub zakres adresów CIDR.
* Podczas tworzenia zdarzenia można określić jego automatyczne uruchamianie. <br/>Jeśli automatyczne uruchamianie zostanie ustawione na wartość true, wydarzenie na żywo rozpocznie się po utworzeniu. Oznacza to, że rozliczenia rozpoczyna się natychmiast po uruchomieniu zdarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
* Aby adres URL pozyskiwania był predykcyjny, ustaw tryb "próżność". Aby uzyskać szczegółowe informacje, zobacz [Adresy URL pozyskiwania zdarzeń na żywo](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Pobieranie adresów URL pozyskiwania

Po utworzeniu wydarzenia na żywo możesz uzyskać adresy URL pozyskiwania, które podasz koderowi na żywo. Koder używa tych adresów URL do wprowadzenia strumienia na żywo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Pobieranie adresu URL podglądu

Użyj funkcji previewEndpoint do podglądu i sprawdź, czy dane wejściowe z kodera są faktycznie odbierane.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że film jest przesyłany do adresu URL podglądu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Tworzenie wydarzeń i danych wyjściowych na żywo oraz zarządzanie nimi

Po przesłaniu strumienia do wydarzenia na żywo można rozpocząć wydarzenie przesyłania strumieniowego poprzez utworzenie zasobu, danych wyjściowych na żywo i lokalizatora przesyłania strumieniowego. Spowoduje to archiwizację strumienia i udostępni go użytkownikom za pośrednictwem punktu końcowego przesyłania strumieniowego.

#### <a name="create-an-asset"></a>Tworzenie zasobu

Utwórz zasób przeznaczony do użycia przez dane wyjściowe na żywo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Tworzenie danych wyjściowych na żywo

Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Po usunięciu danych wyjściowych na żywo nie usuwasz bazowego zasobu i zawartości zasobu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

> [!NOTE]
> Po utworzeniu konta usługi Media Services **domyślny** punkt końcowy przesyłania strumieniowego jest dodawany do konta w stanie **Zatrzymane.** Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z [dynamicznego pakowania](dynamic-packaging-overview.md) i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz przesyłać strumieniowo zawartość, musi być w stanie **Uruchomione.**

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

Jeśli skończysz przesyłanie strumieniowe i chcesz wyczyścić zasoby aprowidzone wcześniej, wykonaj następującą procedurę:

* Zatrzymaj wypychanie strumienia z kodera.
* Zatrzymaj wydarzenie na żywo. Po zatrzymaniu wydarzenia na żywo nie będzie ono objęć żadnymi opłatami. W razie potrzeby ponownego uruchomienia kanał będzie miał ten sam adres URL pozyskiwania, więc nie trzeba będzie ponownie konfigurować kodera.
* Można zatrzymać punkt końcowy przesyłania strumieniowego, chyba że chcesz nadal udostępniać archiwum zdarzenia na żywo w formie przesyłania strumieniowego na żądanie. Jeśli zdarzenie na żywo jest w stanie zatrzymania, nie będzie ponosić żadnych opłat.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Oglądanie wydarzenia

Aby obejrzeć zdarzenie, skopiuj adres URL przesyłania strumieniowego, który został uruchomiony po uruchomieniu kodu opisanego w pliku Utwórz lokalizator przesyłania strumieniowego. Możesz użyć wybranego odtwarzacza multimedialnego. [Program Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) jest https://ampdemo.azureedge.netdostępny do testowania strumienia pod adresem .

Po zatrzymaniu wydarzenia na żywo wydarzenie jest automatycznie konwertowane na zawartość na żądanie. Nawet po zatrzymaniu i usunięciu zdarzenia użytkownicy mogą przesyłać strumieniowo zarchiwizowanej zawartości jako wideo na żądanie, o ile nie usuniesz zasobu. Zasobu nie można usunąć, jeśli jest on używany przez zdarzenie; zdarzenie musi zostać usunięte w pierwszej kolejności.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Pozostawienie uruchomionego wydarzenia na żywo wiąże się z naliczaniem opłat. Należy pamiętać, że jeśli projekt/program ulegnie awarii lub zostanie zamknięty z jakiegokolwiek powodu, wydarzenie na żywo może pozostać uruchomione w stanie naliczania opłat.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)
 
