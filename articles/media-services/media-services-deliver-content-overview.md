---
title: Dostarczanie zawartości dla klientów | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie co uczestniczy w dostarczaniu zawartości przy użyciu usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: juliako
ms.openlocfilehash: 815aae57af93b0e4870bd9f61da248e4be328db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23860808"
---
# <a name="deliver-content-to-customers"></a>Dostarczania zawartości dla klientów
Podczas przesyłania strumieniowego lub wideo na żądanie zawartości jest dostarczania klientom, celem użytkownika jest do dostarczania wideo o wysokiej jakości do różnych urządzeń bez względu na warunki panujące w sieci.

Na osiągnięcie tego celu, możesz:

* Kodowanie strumienia do strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). To zajmie się jakość i warunki sieciowe.
* Użyj usługi Microsoft Azure Media Services [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md) do dynamicznie ponownego skompilowania pakietów strumienia do różnych protokołów. To zajmie się przesyłania strumieniowego na różnych urządzeniach. Usługa Media Services obsługuje dostarczanie następujących technologii przesyłania strumieniowego adaptacyjnej szybkości bitowej: <br/>
    * **HTTP transmisji strumieniowej na żywo** (HLS) - Dodaj "(format = m3u8-aapl)" Ścieżka do "/ Manifest" część adresu URL mówić przesyłania strumieniowego serwera pochodzenia zwraca wstecz zawartości HLS wykorzystania na **systemu Apple iOS** urządzenia natywnego (Aby uzyskać szczegółowe informacje, zobacz [lokalizatory](#locators) i [adresy URL](#URLs)),
    * **MPEG-DASH** — Dodaj "(format = mpd-time-csf)" Ścieżka "/ Manifest" części adresu URL, aby sprawdzić serwer przesyłania strumieniowego ze źródła — wersja do zwrócenia kopii MPEG-DASH (Aby uzyskać więcej informacji, zobacz [lokalizatory](#locators) i [adresy URL](#URLs) ),
    * **Smooth Streaming**.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Ten artykuł zawiera omówienie założeń ważne dostarczania zawartości.

Aby sprawdzić znanych problemów, zobacz [znane problemy](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów
Dzięki funkcji dynamicznego tworzenia pakietów tej usługi Media Services udostępnia, można dostarczyć zawartość o adaptacyjnej szybkości bitowej MP4 lub Smooth Streaming zakodowane w formatach transmisji strumieniowej obsługiwanych przez usługę Media Services (MPEG-DASH, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatach transmisji strumieniowej. Firma Microsoft zaleca dostarczania zawartości z dynamicznego tworzenia pakietów.

Aby skorzystać z dynamicznego tworzenia pakietów, należy zakodować (źródłowy) mezzanine do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów.

Dzięki funkcji dynamicznego tworzenia pakietów, przechowywać i opłacać pliki w jednym formacie magazynu. Usługa Media Services skompiluje oraz udostępni właściwą odpowiedź na podstawie żądań użytkownika.

Dynamiczne tworzenie pakietów jest dostępna dla standardowa i premium punkty końcowe przesyłania strumieniowego. 

Aby uzyskać więcej informacji, zobacz [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtry i manifestów dynamiczne
Można zdefiniować filtry dla zasobów z usługi Media Services. Te filtry są reguły po stronie serwera, które ułatwiają wykonywanie czynności, takie jak odtwarzanie określonej sekcji wideo lub określić podzestaw wersji audio i wideo, które urządzenia klienta może obsłużyć (a nie wszystkie wersje, które są skojarzone z elementu zawartości) klientów. Filtrowanie to odbywa się za pośrednictwem *dynamiczne manifestów* które są tworzone, gdy klient żąda do strumienia wideo na podstawie jednej lub większej liczby określonych filtrów.

Aby uzyskać więcej informacji, zobacz [filtry i manifestów dynamiczne](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Lokalizatory
Aby podać użytkownikowi adres URL, który może służyć do przesyłania strumieniowego lub pobierania zawartości, należy najpierw opublikować zawartości, tworząc Lokalizator. Lokalizator zapewnia punkt wejścia, dostęp do plików objętych zasób. Usługa Media Services obsługuje dwa typy lokalizatorów:

* Lokalizatory OnDemandOrigin. Te są używane do przesyłania strumieniowego multimediów (na przykład MPEG-DASH, HLS lub Smooth Streaming) lub pobrać progresywnie pliki.
* Lokalizatory adres URL sygnatury dostępu Współdzielonego dostępu współdzielonego. Są one używane do pobierania plików multimedialnych na komputerze lokalnym.

*Zasady dostępu* służy do definiowania uprawnienia (na przykład odczytu, zapisu i listy) i czas trwania, dla których klient ma dostęp do określonego zasobu. Należy pamiętać, że uprawnienia do listy (AccessPermissions.List) nie należy używać w tworzeniu Lokalizator OrDemandOrigin.

Lokalizatory mają datę wygaśnięcia. Azure portal ustawia datę wygaśnięcia w przyszłości 100 lat. lokalizatorów.

> [!NOTE]
> Jeśli używasz portalu Azure do utworzenia lokalizatorów przed marcem 2015 r. te lokalizatorów zostały ustawione wygaśnie po upływie dwóch lat.
> 
> 

Do aktualizacji daty wygaśnięcia na lokalizatorze użyj interfejsu API [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) lub [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Należy pamiętać, że po zaktualizowaniu daty wygaśnięcia lokalizatora SAS następuje zmiana adresu URL.

Lokalizatory nie są zaprojektowane w celu zarządzania kontrolą dostępu dla poszczególnych użytkowników. Przy użyciu rozwiązań zarządzania prawami cyfrowymi (DRM), można nadać prawa dostępu różnych dla poszczególnych użytkowników. Aby uzyskać więcej informacji, zobacz [zabezpieczania nośnika](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Tworząc Lokalizator, może być 30-sekundowe opóźnienie z powodu wymagane magazynu i procesy propagacji w usłudze Azure Storage.

## <a name="adaptive-streaming"></a>Adaptacyjną przesyłania strumieniowego
Technologie adaptacyjną szybkością transmisji bitów Zezwalaj aplikacji odtwarzacza wideo do określenia warunków sieciowych i wybrać jeden z kilku szybkości transmisji bitów. Podczas komunikacji sieciowej obniża, klient może wybrać niższe szybkości transmisji bitów, odtwarzania można kontynuować niższej jakości wideo. Jak zwiększyć się warunków sieciowych, klient może przełączyć do wyższej szybkości transmisji bitów z poprawy jakości wideo. Usługa Azure Media Services obsługuje następujące technologie adaptacyjną szybkością transmisji bitów: HTTP Live Streaming (HLS), Smooth Streaming i MPEG-DASH.

Aby zapewnić użytkownikom adresów URL przesyłania strumieniowego, trzeba najpierw utworzyć Lokalizator OnDemandOrigin. Tworzenie Lokalizator umożliwia podstawowa ścieżka do zawartości, który zawiera zawartość, którą chcesz przesyłać strumieniowo. Jednak aby można było strumienia tej zawartości, należy zmodyfikować tę ścieżkę do dalszego. Aby utworzyć pełny adres URL do przesyłania strumieniowego pliku manifestu, musi łączyć lokalizatora ścieżki wartość i plik manifestu (filename.ism) Nazwa pliku. Następnie dołącz **/Manifest** i odpowiedni format (w razie potrzeby), do lokalizatora ścieżki.

> [!NOTE]
> Można również strumienia zawartości za pośrednictwem połączenia SSL. Aby to zrobić, upewnij się, że Twoje adresy URL przesyłania strumieniowego rozpoczynać HTTPS. Należy zauważyć, że obecnie AMS nie obsługuje protokołu SSL z domen niestandardowych.  
> 

Można tylko strumienia za pośrednictwem protokołu SSL Jeśli punktu końcowego przesyłania strumieniowego, z którego dostarczyć zawartość została utworzona po 10 września 2014 r. Jeśli Twoje adresy URL przesyłania strumieniowego są oparte na punktów końcowych przesyłania strumieniowego utworzone po 10 września 2014 adres URL zawiera "streaming.mediaservices.windows.net." Adresy URL przesyłania strumieniowego zawierające "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu SSL. Jeśli adres URL jest w starym formacie i chcesz mieć możliwość przesyłania strumieniowego za pośrednictwem protokołu SSL, należy utworzyć nowy punkt końcowy przesyłania strumieniowego. Użyj adresy URL na podstawie nowego punktu końcowego przesyłania strumieniowego przesyłać strumieniowo zawartość przy użyciu protokołu SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Formaty adresu URL przesyłania strumieniowego

### <a name="mpeg-dash-format"></a>Format MPEG-DASH
{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=mpd-time-csf) przesyłania strumieniowego

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=mpd-Time-CSF)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Format V4 Apple HTTP Live Streaming (HLS)
{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=m3u8-aapl) przesyłania strumieniowego

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Format V3 Apple HTTP Live Streaming (HLS)
{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3) przesyłania strumieniowego

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Format Apple HTTP Live Streaming (HLS) z filtrem tylko audio
Domyślnie tylko dźwięk ścieżki są objęte HLS manifestu. Jest to wymagane do sklepu Apple certyfikacji dla sieci komórkowej. W takim przypadku jeśli klient nie ma wystarczającą przepustowość lub jest połączony za pośrednictwem połączenia 2G, odtwarzania zmienia się na tylko audio. Dzięki temu przesyłania strumieniowego zawartości bez buforowania, ale nie ma żadnego obrazu. W niektórych scenariuszach player buforowanie może mieć pierwszeństwo tylko audio. Jeśli chcesz usunąć Śledź tylko dźwięk, Dodaj **tylko dźwięk = false** do adresu URL.

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-aapl-v3,audio-Only=false)

Aby uzyskać więcej informacji, zobacz [wyjściowe dodatkowe funkcje obsługi tworzenia manifestu dynamicznych i HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming formatu
{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

Przykład:

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0 manifestu (starszej wersji manifestu)
Domyślnie Smooth Streaming format manifestu zawiera tag powtórzeń (r-tag). Jednak niektóre odtwarzacze nie obsługują r-tag. Klienci z te odtwarzacze mogą używać formatu, który powoduje wyłączenie r-tag:

{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=fmp4-v20) przesyłania strumieniowego

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Pobierania progresywnego
Pobierania progresywnego można rozpocząć odtwarzanie multimediów przed całego pliku została pobrana. Nie można pobrać progresywnie .ism * (ismv isma, ismt pliki lub ismc).

Aby pobrać progresywnie zawartość, należy użyć typu OnDemandOrigin lokalizatora. W poniższym przykładzie przedstawiono adres URL, który jest oparty na typie OnDemandOrigin lokalizatora:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Może odszyfrować wszystkie zaszyfrowane magazynu zasoby, które mają być przesyłane strumieniowo z usługi punkt początkowy dla pobierania progresywnego.

## <a name="download"></a>Do pobrania
Aby pobrać zawartość na urządzeniu klienckim, należy utworzyć lokalizatora SAS. Lokalizatora SAS umożliwia dostęp do kontenera magazynu Azure, gdzie znajduje się plik. Aby utworzyć adres URL pobierania, należy osadzić nazwa pliku między hostem a sygnatury SAS.

W poniższym przykładzie przedstawiono adres URL, który jest oparty na lokalizatora SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Następujące kwestie:

* Może odszyfrować wszystkie zaszyfrowane magazynu zasoby, które mają być przesyłane strumieniowo z usługi punkt początkowy dla pobierania progresywnego.
* Pobieranie, które nie zostało ukończone w ciągu 12 godzin zakończy się niepowodzeniem.

## <a name="streaming-endpoints"></a>Punkty końcowe przesyłania strumieniowego

Punkt końcowy przesyłania strumieniowego reprezentuje przesyłania strumieniowego usługa, która może dostarczać zawartość bezpośrednio do aplikacji klienckiej player lub sieci dostarczania zawartości (CDN) w celu dalszej dystrybucji. Strumienia wychodzącego z usługą punktu końcowego przesyłania strumieniowego może być strumień na żywo lub zasobów wideo na żądanie, w ramach konta usługi Media Services. Istnieją dwa typy punkty końcowe, przesyłania strumieniowego **standardowe** i **premium**. Aby uzyskać więcej informacji, zobacz [Omówienie punktów końcowych przesyłania strumieniowego](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

## <a name="known-issues"></a>Znane problemy
### <a name="changes-to-smooth-streaming-manifest-version"></a>Zmiany w funkcji Smooth Streaming manifestu wersji
Przed wprowadzeniem usługi lipca 2016 — gdy zasoby utworzone przez Media Encoder Standard Media Encoder Premium w przepływie pracy lub starszej usługi Azure Media Encoder zostały strumieniowego za pomocą dynamicznego tworzenia pakietów--Smooth Streaming manifest zwrócił czy odpowiadają wersji 2.0. W wersji 2.0 czas trwania fragmentu nie należy używać tagów tak zwane Powtórz (r). Na przykład:

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

Wersja usługi lipca 2016 wygenerowanego manifestu Smooth Streaming odpowiada wersji 2.2, o czasie trwania fragmentu przy użyciu powtarzania tagów. Na przykład:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Niektóre starszych klientów Smooth Streaming może nie obsługiwać tagi powtórzeń i nie będzie można załadować manifestu. Aby zminimalizować ten problem, można użyć parametru starszej wersji manifestu format **(format = fmp4 v20)** lub zaktualizować klienta do najnowszej wersji, obsługuje powtarzania znaczników. Aby uzyskać więcej informacji, zobacz [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Aktualizacja usługi Media Services lokalizatorów po wycofanie magazynu kluczy](media-services-roll-storage-access-keys.md)

