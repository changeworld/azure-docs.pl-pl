---
title: Dostarczanie treści klientom | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie tego, co jest związane z dostarczaniem zawartości za pomocą usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c8d32a6434db0fad18b9fe7c2d6e2117795eb651
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476738"
---
# <a name="deliver-content-to-customers"></a>Dostarczanie treści klientom
Gdy dostarczasz klientom treści przesyłane strumieniowo lub wideo na żądanie, twoim celem jest dostarczanie wysokiej jakości wideo na różnych urządzeniach w różnych warunkach sieciowych.

Aby osiągnąć ten cel, możesz:

* Koduj strumień do strumienia wideo o wielu szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Zajmie to jakość i warunki sieciowe.
* [Dynamiczne pakowanie](media-services-dynamic-packaging-overview.md) usługi Microsoft Azure Media Services umożliwia dynamiczne przepakowanie strumienia do różnych protokołów. To zajmie się streaming na różnych urządzeniach. Usługa Media Services obsługuje dostarczanie następujących technologii adaptacyjnego przesyłania strumieniowego z szybkością transmisji bitów: <br/>
    * **HTTP Live Streaming** (HLS) - dodaj ścieżkę "(format=m3u8-aapl)" do części "/Manifest" adresu URL, aby poinformować serwer pochodzenia przesyłania strumieniowego o zwrot zawartości HLS do użytku na urządzeniach natywnych **Apple iOS** (szczegółowe informacje można znaleźć [w lokalizatorach](#locators) i [adresach URL](#URLs)),
    * **MPEG-DASH** - dodaj ścieżkę "(format=mpd-time-csf)" do części "/Manifest" adresu URL, aby poinformować serwer pochodzenia strumieniowego o powrocie MPEG-DASH (szczegółowe informacje można znaleźć [w lokalizatorach](#locators) i [adresach URL](#URLs)),
    * **Płynne przesyłanie strumieniowe**.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

W tym artykule przedstawiono omówienie ważnych pojęć dostarczania zawartości.

Aby sprawdzić znane problemy, zobacz [Znane problemy](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów
Dzięki dynamicznemu pakietowi, które zapewnia usługa Media Services, można dostarczać adaptacyjną zawartość kodowaną mp4 z szybkością transmisji bitów lub płynną transmisję strumieniową w formatach przesyłania strumieniowego obsługiwanych przez usługę Media Services (MPEG-DASH, HLS, Smooth Streaming) bez konieczności ponownego pakowania do tych formatów przesyłania strumieniowego. Zalecamy dostarczanie treści za pomocą dynamicznego opakowania.

Aby skorzystać z dynamicznego pakowania, musisz zakodować plik antresoli (źródłowej) w zestawie plików MP4 o adaptacyjnej szybkości transmisji bitów lub adaptacyjnych plikach płynnego przesyłania strumieniowego.

Dzięki dynamicznemu pakowaniu przechowujesz i płacisz za pliki w formacie pojedynczego przechowywania. Usługa Media Services będzie tworzyć i obsługiwać odpowiednią odpowiedź na podstawie twoich żądań.

Dynamiczne opakowanie jest dostępne dla standardowych i premium punktów końcowych przesyłania strumieniowego. 

Aby uzyskać więcej informacji, zobacz [Dynamiczne pakowanie](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtry i manifesty dynamiczne
Za pomocą usługi Media Services można zdefiniować filtry dla zasobów. Filtry te są regułami po stronie serwera, które pomagają klientom wykonywać takie czynności, jak odtwarzanie określonej sekcji wideo lub określanie podzbioru wersji audio i wideo, które może obsługiwać urządzenie klienta (zamiast wszystkich wersji skojarzonych z zasobem). To filtrowanie jest osiągane za pomocą *dynamicznych manifestów,* które są tworzone, gdy klient żąda strumieniowania wideo na podstawie jednego lub więcej określonych filtrów.

Aby uzyskać więcej informacji, zobacz [Filtry i manifesty dynamiczne](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a><a id="locators"/>Locators
Aby udostępnić użytkownikowi adres URL, który może służyć do przesyłania strumieniowego lub pobierania zawartości, musisz najpierw opublikować zasób, tworząc lokalizator. Lokalizator udostępnia punkt wejścia, aby uzyskać dostęp do plików zawartych w zasobie. Usługa Media Services obsługuje dwa typy lokalizatorów:

* Lokalizatory OnDemandOrigin. Są one używane do strumieniowego przesyłania multimediów (na przykład MPEG-DASH, HLS lub Smooth Streaming) lub stopniowego pobierania plików.
* Lokalizatory adresów URL sygnatury dostępu współdzielonego (SAS). Są one używane do pobierania plików multimedialnych na komputer lokalny.

*Zasady dostępu* są używane do definiowania uprawnień (takich jak odczyt, zapis i lista) oraz czasu trwania, dla którego klient ma dostęp do określonego zasobu. Należy zauważyć, że uprawnienie listy (AccessPermissions.List) nie powinny być używane do tworzenia lokalizatora OnDemandOrigin.

Lokalizatory mają daty wygaśnięcia. Portal Azure ustawia datę wygaśnięcia 100 lat w przyszłości dla lokalizatorów.

> [!NOTE]
> Jeśli do tworzenia lokalizatorów przed marcem 2015 r. użyto witryny Azure portal, lokalizatory te miały wygasnąć po dwóch latach.
> 
> 

Do aktualizacji daty wygaśnięcia na lokalizatorze użyj interfejsu API [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) lub [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Należy pamiętać, że po zaktualizowaniu daty wygaśnięcia lokalizatora SAS następuje zmiana adresu URL.

Lokalizatory nie są przeznaczone do zarządzania kontrolą dostępu dla użytkownika. Korzystając z rozwiązań DRM(Digital Rights Management), można przyznać innym prawom dostępu poszczególnym użytkownikom. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie multimediów](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Podczas tworzenia lokalizatora może wystąpić 30-sekundowe opóźnienie z powodu wymaganych procesów magazynowania i propagacji w usłudze Azure Storage.

## <a name="adaptive-streaming"></a>Adaptacyjne przesyłanie strumieniowe
Adaptacyjne technologie szybkości transmisji bitów umożliwiają aplikacjom odtwarzacza wideo określenie warunków sieciowych i wybór spośród kilku szybkości transmisji bitów. Gdy komunikacja sieciowa ulega pogorszeniu, klient może wybrać niższą szybkość transmisji bitów, dzięki czemu odtwarzanie może być kontynuowane przy niższej jakości wideo. Wraz z poprawą warunków sieciowych klient może przełączyć się na wyższą szybkość transmisji bitów z lepszą jakością wideo. Usługa Azure Media Services obsługuje następujące technologie adaptacyjnej szybkości transmisji bitów: http live streaming (HLS), Smooth Streaming i MPEG-DASH.

Aby zapewnić użytkownikom adresy URL przesyłania strumieniowego, należy najpierw utworzyć lokalizator OnDemandOrigin. Utworzenie lokalizatora daje podstawową ścieżkę do zasobu, który zawiera zawartość, którą chcesz przesyłać strumieniowo. Jednak aby móc przesyłać strumieniowo tę zawartość, należy zmodyfikować tę ścieżkę dalej. Aby utworzyć pełny adres URL do pliku manifestu przesyłania strumieniowego, należy połączyć wartość ścieżki lokalizatora i nazwę pliku manifestu (nazwa pliku filename.ism). Następnie dołącz **/Manifest** i odpowiedni format (w razie potrzeby) do ścieżki lokalizatora.

> [!NOTE]
> Można również przesyłać strumieniowo zawartość za pomocą połączenia TLS. Aby to zrobić, upewnij się, że adresy URL przesyłania strumieniowego zaczynają się od protokołu HTTPS. Należy zauważyć, że obecnie usługa AMS nie obsługuje protokołu TLS z domenami niestandardowymi.  
> 

Można przesyłać strumieniowo za pomocą protokołu TLS tylko wtedy, gdy punkt końcowy przesyłania strumieniowego, z którego dostarczasz zawartość, został utworzony po 10 września 2014 r. Jeśli adresy URL przesyłania strumieniowego są oparte na punktach końcowych przesyłania strumieniowego utworzonych po 10 września 2014 r., adres URL zawiera "streaming.mediaservices.windows.net". Adresy URL przesyłania strumieniowego, które zawierają "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu TLS. Jeśli adres URL jest w starym formacie i chcesz mieć możliwość przesyłania strumieniowego za pomocą protokołu TLS, utwórz nowy punkt końcowy przesyłania strumieniowego. Użyj adresów URL opartych na nowym punkcie końcowym przesyłania strumieniowego, aby przesyłać strumieniowo zawartość za pomocą protokołu TLS.

## <a name="streaming-url-formats"></a><a id="URLs"/>Formaty adresów URL przesyłania strumieniowego

### <a name="mpeg-dash-format"></a>Format MPEG-DASH
{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) Format V4
{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) Format V3
{nazwa konta usług końcowych przesyłania strumieniowego}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Format przesyłania strumieniowego na żywo (HLS) firmy Apple z filtrem tylko do dźwięku
Domyślnie ścieżki tylko audio są uwzględniane w manifeście HLS. Jest to wymagane w przypadku certyfikacji Apple Store dla sieci komórkowych. W takim przypadku, jeśli klient nie ma wystarczającej przepustowości lub jest podłączony za pomocą połączenia 2G, odtwarzanie przełącza się tylko na dźwięk. Pomaga to zachować przesyłanie strumieniowe zawartości bez buforowania, ale nie ma wideo. W niektórych scenariuszach buforowanie odtwarzacza może być preferowane tylko w stosunku do dźwięku. Jeśli chcesz usunąć ścieżkę tylko do dźwięku, dodaj **tylko dźwięk = false** do adresu URL.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Aby uzyskać więcej informacji, zobacz [Obsługa kompozycji dynamicznego manifestu i dodatkowe funkcje wyjściowe HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Płynny format przesyłania strumieniowego
{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

Przykład:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Manifest Smooth Streaming 2.0 (manifest starszej wersji)
Domyślnie format manifestu Płynne przesyłanie strumieniowe zawiera znacznik powtarzalny (r-tag). Jednak niektórzy gracze nie obsługują r-tag. Klienci z tymi odtwarzaczami mogą używać formatu, który wyłącza r-tag:

{nazwa konta usług mediów punktu końcowego przesyłania strumieniowego}.streaming.mediaservices.windows.net/{Identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Pobieranie progresywne
Po pobieraniu progresywnym możesz rozpocząć odtwarzanie multimediów przed pobraniem całego pliku. Nie można stopniowo pobierać plików .ism* (ismv, isma, ismt lub ismc).

Aby stopniowo pobierać zawartość, użyj lokalizatora typu OnDemandOrigin. W poniższym przykładzie pokazano adres URL, który jest oparty na onDemandOrigin typu lokalizatora:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Należy odszyfrować wszystkie zasoby zaszyfrowane magazynem, które mają być przesyłane strumieniowo z usługi pochodzenia w celu pobrania progresywnego.

## <a name="download"></a>Pobierz
Aby pobrać zawartość na urządzenie klienckie, należy utworzyć lokalizator sygnatury dostępu Współdzielonego. Lokalizator sygnatury dostępu współdzielonego zapewnia dostęp do kontenera usługi Azure Storage, w którym znajduje się plik. Aby utworzyć adres URL pobierania, należy osadzić nazwę pliku między hostem a podpisem sygnatury dostępu Współdzielonego.

W poniższym przykładzie pokazano adres URL oparty na lokalizatorze sygnatury dostępu Współdzielonego:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Obowiązują następujące zastrzeżenia:

* Należy odszyfrować wszystkie zasoby zaszyfrowane magazynem, które mają być przesyłane strumieniowo z usługi pochodzenia w celu pobrania progresywnego.
* Pobieranie, które nie zostało ukończone w ciągu 12 godzin, zakończy się niepowodzeniem.

## <a name="streaming-endpoints"></a>Punkty końcowe przesyłania strumieniowego

Punkt końcowy przesyłania strumieniowego reprezentuje usługę przesyłania strumieniowego, która może dostarczać zawartość bezpośrednio do aplikacji odtwarzacza klienckiego lub do sieci dostarczania zawartości (CDN) w celu dalszej dystrybucji. Strumień wychodzący z usługi punktu końcowego przesyłania strumieniowego może być strumieniem na żywo lub zasobem wideo na żądanie na koncie usługi Media Services. Istnieją dwa typy punktów końcowych przesyłania strumieniowego, **standardowe** i **premium.** Aby uzyskać więcej informacji, zobacz [Streaming endpoints overview (Omówienie punktów końcowych przesyłania strumieniowego)](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

## <a name="known-issues"></a>Znane problemy
### <a name="changes-to-smooth-streaming-manifest-version"></a>Zmiany w wersji manifestu Smooth Streaming
Przed wydaniem usługi z lipca 2016 r. — gdy zasoby wyprodukowane przez Media Encoder Standard, Media Encoder Premium Workflow lub wcześniejszy koder Azure Media Encoder były przesyłane strumieniowo przy użyciu dynamicznego pakowania — manifest płynnego przesyłania strumieniowego zostanie zgodny z wersją 2.0. W wersji 2.0 czas trwania fragmentu nie używa tak zwanych tagów powtarzania ('r).In version 2.0, the fragment durations do not use the so called repeat ('r') tags. Przykład:


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

W wersji usługi z lipca 2016 r. wygenerowany manifest płynnego przesyłania strumieniowego jest zgodny z wersją 2.2, z czasem trwania fragmentu przy użyciu tagów powtarzanych. Przykład:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Niektóre starsze klientów Smooth Streaming może nie obsługiwać tagów powtórzeń i nie można załadować manifestu. Aby rozwiązać ten problem, można użyć starszego parametru formatu manifestu **(format=fmp4-v20)** lub zaktualizować klienta do najnowszej wersji, która obsługuje tagi powtarzające. Aby uzyskać więcej informacji, zobacz [Płynne przesyłanie strumieniowe 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Aktualizowanie lokalizatorów usług Media Services po przetaczaniu kluczy pamięci masowej](media-services-roll-storage-access-keys.md)

