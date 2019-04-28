---
title: Dostarczanie zawartości dla klientów | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie co to jest zaangażowane w dostarczanie zawartości przy użyciu usługi Azure Media Services.
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
ms.openlocfilehash: 5db2cb983c0c3cd0e2194f7686964d9ec3828d6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232279"
---
# <a name="deliver-content-to-customers"></a>Dostarczanie zawartości do klientów
Podczas przesyłania strumieniowego lub wideo na żądanie zawartość dostarczać klientom, celem jest dostarczanie wideo wysokiej jakości do różnych urządzeń warunki panujące w sieci.

Aby osiągnąć ten cel, możesz wykonywać następujące czynności:

* Kodowanie strumienia do strumienia wideo wielokrotnej szybkości transmisji bitów (adaptacyjnej szybkości transmisji bitów). To zajmie się jakość i warunki sieciowe.
* Użyj usługi Microsoft Azure Media Services [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md) do dynamicznego ponownego skompilowania pakietów strumienia do różnych protokołów. Ten proces obsłuży przesyłania strumieniowego na różnych urządzeniach. Usługa Media Services obsługuje dostarczanie następujących technologii przesyłania strumieniowego adaptacyjną szybkością transmisji bitów: <br/>
    * **HTTP Live Streaming** (HLS) — Dodaj "(format = m3u8-aapl)" Ścieżka "/ Manifest" część adresu URL, które nakazuje przesyłania strumieniowego server pochodzenia zwraca wstecz zawartości HLS do użycia na **systemu Apple iOS** urządzenia z natywnym (Aby uzyskać szczegółowe informacje, zobacz [lokalizatory](#locators) i [adresy URL](#URLs)),
    * **MPEG-DASH** — Dodaj "(format = mpd-time-csf)" Ścieżka "/ Manifest" części adresu URL, aby poinformować serwer pochodzenia przesyłania strumieniowego do zwrócenia kopię standardu MPEG-DASH (Aby uzyskać więcej informacji, zobacz [lokalizatory](#locators) i [adresy URL](#URLs)),
    * **Smooth Streaming**.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Ten artykuł zawiera omówienie koncepcji ważne dostarczania zawartości.

Aby sprawdzić znanych problemów, zobacz [znane problemy dotyczące](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów
Za pomocą funkcji dynamicznego tworzenia pakietów tej usługi Media Services udostępnia, można dostarczać zawartość z adaptacyjną szybkością transmisji bitów w formacie MP4 lub Smooth Streaming, kodowane, w formatach transmisji strumieniowej obsługiwanych przez usługę Media Services (MPEG-DASH, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatów przesyłania strumieniowego. Firma Microsoft zaleca dostarczanie zawartości za pomocą funkcji dynamicznego tworzenia pakietów.

Aby móc korzystać z dynamicznego tworzenia pakietów, musisz zakodować plik (źródłowy) mezzanine do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów.

Za pomocą funkcji dynamicznego tworzenia pakietów, możesz przechowywać i opłacać pliki w jednym formacie magazynu. Media Services skompiluje oraz udostępni właściwą odpowiedź na podstawie Twojej żądań.

Funkcję dynamicznego tworzenia pakietów jest dostępna dla standardowej i premium, punkty końcowe przesyłania strumieniowego. 

Aby uzyskać więcej informacji, zobacz [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtrów i manifestów dynamicznych
Można zdefiniować filtry dla zasoby za pomocą usługi Media Services. Te filtry są reguły po stronie serwera, które pomogą klientom wykonywania czynności takich jak odtworzyć określonej sekcji filmu wideo lub wskazać ich podzbiór odwzorowaniami audio i wideo, które może obsłużyć Twojego klienta urządzenia, (a nie wszystkie wersje, które są skojarzone z elementem zawartości). Filtrowanie odbywa się za pośrednictwem *o nazwie manifesty dynamiczne* które są tworzone, gdy klient żąda do przesyłania strumieniowego wideo na podstawie jednej lub większej liczby określonych filtrów.

Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Lokalizatory
Aby podać użytkownikowi adres URL, który może służyć do przesyłania strumieniowego lub pobierania zawartości, najpierw musisz opublikować swój element zawartości przez utworzenie lokalizatora. Lokalizator zapewnia punkt wejścia, dostępu do plików znajdujących się w zasobie. Usługa Media Services obsługuje dwa typy lokalizatorów:

* Lokalizatory OnDemandOrigin. Te są używane do przesyłania strumieniowego multimediów (na przykład MPEG-DASH, HLS lub Smooth Streaming) lub pobrać progresywnie pliki.
* Lokalizatory adresu URL (SAS) sygnatury dostępu współdzielonego. Są one używane do pobierania plików multimedialnych na komputerze lokalnym.

*Zasady dostępu* służy do definiowania uprawnienia (na przykład odczytu, zapisu i listy) i czasu trwania, dla którego klient ma dostęp do określonego zasobu. Należy pamiętać, że uprawnienia listy (AccessPermissions.List) nie powinien być używany w tworzeniu Lokalizator OnDemandOrigin.

Lokalizatory mają datę wygaśnięcia. Witryna Azure portal ustawia datę wygaśnięcia w przyszłości 100 lat dla lokalizatorów.

> [!NOTE]
> Jeśli używasz witryny Azure portal do utworzenia lokalizatorów przed marcem 2015 tych lokalizatorów ustawiono wygaśnie po upływie dwóch lat.
> 
> 

Do aktualizacji daty wygaśnięcia na lokalizatorze użyj interfejsu API [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) lub [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Należy pamiętać, że po zaktualizowaniu daty wygaśnięcia lokalizatora SAS następuje zmiana adresu URL.

Lokalizatory nie są przeznaczone do zarządzania kontroli dostępu poszczególnych użytkowników. Za pomocą rozwiązania Digital Rights Management (DRM), można nadać różne uprawnienia poszczególnym użytkownikom. Aby uzyskać więcej informacji, zobacz [zabezpieczania nośnika](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Podczas tworzenia lokalizatora może być 30-sekundowe opóźnienie z powodu wymaganego do magazynowania i procesy propagacji w usłudze Azure Storage.

## <a name="adaptive-streaming"></a>Adaptacyjną przesyłania strumieniowego
Technologie adaptacyjną szybkością transmisji bitów umożliwia aplikacji odtwarzacza wideo określić warunki w sieci, a następnie wybierz z kilku różnych. Podczas komunikacji sieciowej spadku, klient może wybrać niższe szybkości transmisji bitów, więc odtwarzania można kontynuować niższa jakość wideo. Jak poprawić wydajność się warunków sieciowych, klient może przełączyć do wyższej szybkości transmisji bitów przy użyciu poprawy jakości wideo. Usługa Azure Media Services obsługuje następujące technologie adaptacyjną szybkością transmisji bitów: HTTP na żywo, przesyłania strumieniowego (HLS), Smooth Streaming i MPEG-DASH.

Aby zapewnić użytkownikom adresów URL przesyłania strumieniowego, trzeba najpierw utworzyć Lokalizator OnDemandOrigin. Tworzenie lokalizatora daje Ci ścieżki podstawowej z zawartością, który ma być przesyłana strumieniowo element zawartości. Jednak aby można było przesyłać strumieniowo zawartość, musisz zmodyfikować tę ścieżkę do dalszych. Do konstruowania pełny adres URL do pliku manifestu przesyłania strumieniowego, należy połączyć wartość ścieżki lokalizatora i manifest (filename.ism) Nazwa pliku. Następnie dołącz **/Manifest** i odpowiedni format (jeśli jest to konieczne), do ścieżki lokalizatora.

> [!NOTE]
> Można również przesyłać strumieniowo zawartość, za pośrednictwem połączenia SSL. Aby to zrobić, upewnij się, że Twoje adresy URL przesyłania strumieniowego uruchomić przy użyciu protokołu HTTPS. Należy pamiętać, że obecnie usługi AMS nie obsługuje protokołu SSL z zastosowaniem domen niestandardowych.  
> 

Można tylko przesyłanie strumieniowe za pośrednictwem protokołu SSL Jeśli utworzono punkt końcowy przesyłania strumieniowego, z którego umożliwia dostarczanie zawartości po 10 września 2014 r. Jeśli Twoje adresy URL przesyłania strumieniowego są oparte na punkty końcowe przesyłania strumieniowego utworzonych po 10 września 2014 r. adres URL zawiera "streaming.mediaservices.windows.net." Adresy URL przesyłania strumieniowego, które zawierają "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu SSL. Jeśli adres URL jest w starym formacie i chcesz mieć możliwość przesyłania strumieniowego za pośrednictwem protokołu SSL, należy utworzyć nowy punkt końcowy przesyłania strumieniowego. Adresy URL, w oparciu o nowy punkt końcowy przesyłania strumieniowego umożliwiają przesyłanie strumieniowe zawartości za pośrednictwem protokołu SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Przesyłanie strumieniowe formatów adresów URL

### <a name="mpeg-dash-format"></a>Format MPEG-DASH
{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Format V4 Apple HTTP Live Streaming (HLS)
{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Format V3 Apple HTTP Live Streaming (HLS)
{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Format Apple HTTP Live Streaming (HLS), za pomocą filtru tylko dane audio
Domyślnie ścieżek zawierających tylko dane audio są objęte HLS manifestu. Jest to wymagane do certyfikacji firmy Apple Store sieci komórkowej. W takim przypadku klient nie ma wystarczającą przepustowością lub jest połączony za pośrednictwem połączenia 2G, odtwarzania przechodzi do tylko dane audio. Pomaga to zachować strumieniowania zawartości, bez buforowania, ale nie ma żadnego obrazu. W niektórych scenariuszach odtwarzacza, buforowanie może mieć pierwszeństwo tylko dane audio. Jeśli chcesz usunąć tylko dane audio śledzenie, Dodaj **tylko dane audio = false** do adresu URL.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Aby uzyskać więcej informacji, zobacz [dodatkowe funkcje wyjściowe obsługę tworzenia manifestów dynamicznych i HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Zestaw Smooth Streaming formatu
{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

Przykład:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0 manifestu (starszej wersji manifestu)
Domyślnie Smooth Streaming format manifestu zawiera tag powtórzeń (r-tag). Jednak niektóre odtwarzacze nie obsługują tagu języka r. Klientów za pomocą tych odtwarzaczy można użyć formatu, który wyłącza r-tag:

{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Pobierania progresywnego
Przy użyciu pobierania progresywnego możesz rozpocząć odtwarzanie multimediów, zanim cały plik został pobrany. Nie można pobrać progresywnie ISM * (ismv isma, ismt pliki lub ismc).

Aby pobrać progresywnie zawartość, należy użyć typu OnDemandOrigin lokalizatora. Adres URL, który jest oparty na typie OnDemandOrigin lokalizatora można znaleźć w poniższym przykładzie:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Należy odszyfrować wszystkie zasoby szyfrowany magazyn, które mają być przesyłane strumieniowo z usługi punkt początkowy dla pobierania progresywnego.

## <a name="download"></a>Do pobrania
Aby pobrać zawartość do urządzenia klienckiego, należy utworzyć lokalizatora sygnatury dostępu Współdzielonego. Lokalizatora sygnatury dostępu Współdzielonego zapewnia dostęp do kontenera usługi Azure Storage, gdzie znajduje się plik. Aby utworzyć adres URL pobierania, należy osadzić nazwę pliku między hostem a sygnatura dostępu Współdzielonego.

Adres URL, który jest oparty na lokalizatora sygnatury dostępu Współdzielonego można znaleźć w poniższym przykładzie:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Mają zastosowanie następujące kwestie:

* Należy odszyfrować wszystkie zasoby szyfrowany magazyn, które mają być przesyłane strumieniowo z usługi punkt początkowy dla pobierania progresywnego.
* Pobieranie, które nie zostało zakończone w ciągu 12 godzin zakończy się niepowodzeniem.

## <a name="streaming-endpoints"></a>Punkty końcowe przesyłania strumieniowego

Punkt końcowy przesyłania strumieniowego reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub sieci dostarczania zawartości (CDN) w celu dalszego rozpowszechniania. Strumień wychodzący usługi punktu końcowego przesyłania strumieniowego może być strumień na żywo lub zawartości wideo na żądanie, w ramach konta usługi Media Services. Istnieją dwa typy punktów końcowych, przesyłania strumieniowego **standardowa** i **premium**. Aby uzyskać więcej informacji, zobacz [Streaming endpoints overview (Omówienie punktów końcowych przesyłania strumieniowego)](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

## <a name="known-issues"></a>Znane problemy
### <a name="changes-to-smooth-streaming-manifest-version"></a>Wersja manifestu zmiany Smooth Streaming
Przed wydaniem usługa lipca 2016 r. — gdy zasoby są tworzone przez usługi Media Encoder Standard, Media Encoder Premium Workflow lub starszej usługi Azure Media Encoder były przesyłane strumieniowo przy użyciu funkcji dynamicznego tworzenia pakietów — Smooth Streaming manifest zwracana będzie zgodne z wersji w wersji 2.0. W wersji 2.0 czas trwania fragmentu nie należy używać tagów tak zwane Powtórz ("r"). Na przykład:


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

W wersji usługi lipca 2016 r. wygenerowanego manifestu Smooth Streaming odpowiada wersji 2.2, o czasie trwania fragmentu, za pomocą tagów Powtórz tę procedurę. Na przykład:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Niektórych starszych klientów Smooth Streaming mogą nie obsługiwać powtórzeń tagów i nie będzie można załadować manifestu. Aby rozwiązać ten problem, można użyć parametru starszy format manifestu **(format = fmp4 v20)** lub zaktualizuj klienta do najnowszej wersji, obsługuje tagi Powtórz tę procedurę. Aby uzyskać więcej informacji, zobacz [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Aktualizowanie lokalizatorów usługi Media Services po stopniowym uaktualnieniu kluczy magazynu](media-services-roll-storage-access-keys.md)

