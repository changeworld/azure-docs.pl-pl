---
title: Omówienie transmisji strumieniowej na żywo za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Dzięki temu artykuł z omówieniem transmisja strumieniowa na żywo za pomocą usługi Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/26/2018
ms.author: juliako
ms.openlocfilehash: 3a2b3752926a3a4391ae9479ba636694533c97a8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788212"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Przesyłanie strumieniowe przy użyciu usługi Azure Media Services v3 na żywo

Usługa Azure Media Services umożliwia dostarczanie wydarzeń na żywo dla klientów w chmurze Azure. Aby przesyłać strumieniowo zdarzenia na żywo za pomocą usługi Media Services, potrzebne są następujące elementy:  

- Kamera, która jest używana do przechwytywania zdarzeń na żywo.
- Koder wideo na żywo, który konwertuje sygnały z kamery (lub innego urządzenia, takie jak laptop) na udział źródła danych są wysyłane do usługi Media Services. Kanał informacyjny udział może obejmować sygnały związane z reklamy, takie jak znaczniki SCTE 35.
- Składniki w usłudze Media Services pozwalają pozyskiwać, w wersji zapoznawczej, pakiet, rejestrowanie, szyfrowania i emisję wydarzenia na żywo dla klientów lub do sieci CDN w celu dalszej dystrybucji.

W tym artykule szczegółowo omówiono, uzyskać wskazówki i zawiera diagramy główne składniki zaangażowane w transmisji strumieniowej na żywo za pomocą usługi Media Services.

## <a name="overview-of-main-components"></a>Omówienie głównych składników

Aby dostarczać strumienie na żądanie lub na żywo za pomocą usługi Media Services, musisz mieć co najmniej jedną [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints). Po utworzeniu konta usługi Media Services **domyślne** StreamingEndpoint zostanie dodany do Twojego konta w **zatrzymane** stanu. Należy uruchomić StreamingEndpoint, z którego chcesz strumieniowo przesyłać zawartość do swojej przeglądarki. Można użyć domyślnie **StreamingEndpoint**, lub utworzyć inny dostosowane **StreamingEndpoint** z wymaganą konfiguracją i ustawień sieci CDN. Użytkownik może zdecydować umożliwić wielu punkty, z których każdy z nich przeznaczone dla różnych sieci CDN i podając unikatową nazwę hosta w dostarczaniu zawartości. 

W usłudze Media Services [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) jest odpowiedzialny za przyjęciem i przetwarzania na żywo strumieniowych źródeł wideo. Podczas tworzenia element LiveEvent wejściowy punkt końcowy jest tworzony, której można wysyłać sygnał na żywo z kodera zdalnego. Zdalny koder na żywo wysyła wkładu, kanał informacyjny do wejściowy punkt końcowy przy użyciu [RTMP](https://www.adobe.com/devnet/rtmp.html) lub [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) protokołu (pofragmentowany plik MP4). Funkcji Smooth Streaming protokołu pozyskiwania, są obsługiwane schematy adresów URL `http://` lub `https://`. Przypadku protokołu pozyskiwania RTMP, są obsługiwane schematy adresów URL `rtmp://` lub `rtmps://`. Aby uzyskać więcej informacji, zobacz [zalecane strumieniowo koderów na żywo](recommended-on-premises-live-encoders.md).

Gdy **element LiveEvent** rozpoczyna odbieranie wkład źródła danych, można użyć punktu końcowego (wersja zapoznawcza) (w wersji zapoznawczej adres URL do wyświetlania podglądu i weryfikowania otrzymują przed opublikowaniem dalsze transmisji strumieniowej na żywo. Po sprawdzeniu, że Podgląd strumienia jest dobra, aby udostępnić transmisji strumieniowej na żywo do dostarczania za pomocą co najmniej jeden (wstępnie utworzone), można użyć element LiveEvent **punkty**. Aby to osiągnąć, należy utworzyć nową [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) na **element LiveEvent**. 

**LiveOutput** obiektu przypomina rejestratora taśm, który będzie przechwytywać i zarejestruj transmisji strumieniowej na żywo do elementu zawartości w ramach konta usługi Media Services. Zarejestrowana zawartość zostaną utrwalone do konta magazynu platformy Azure dołączonych do konta, do kontenera, zdefiniowane przez zasób trwały.  **LiveOuput** również pozwala na kontrolowanie niektórych właściwości wychodzącej transmisji strumieniowej na żywo, takie jak ile strumienia jest przechowywana w nagraniu archiwum (na przykład pojemność funkcji DVR w chmurze). Archiwum na dysku jest cykliczna archiwum "okno" zawiera tylko ilość zawartości, który jest określony w **archiveWindowLength** właściwość **LiveOutput**. Zawartość, która wykracza poza tym oknem jest automatycznie odrzucane z kontenera magazynu, a nie jest możliwe do odzyskania. Na element LiveEvent archiwum różne długości i ustawienia, można utworzyć wiele LiveOutputs (maksymalnie trzy).  

Usługa Media Services możesz korzystać z zalet **funkcję dynamicznego tworzenia pakietów**, co pozwala na przeglądanie i emisji strumieni na żywo w [formatów MPEG DASH, HLS i Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z udziału kanału informacyjnego Aby wysłać do usługi. Przeglądającym można odtwarzać transmisji strumieniowej na żywo za pomocą dowolnego odtwarzaczy zgodne HLS, DASH lub Smooth Streaming. Możesz użyć [usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) w sieci web lub aplikacji mobilnych, aby dostarczać strumień we wszystkich tych protokołów.

Usługa Media Services umożliwia dostarczanie zawartości dynamicznie zaszyfrowany (**szyfrowania dynamicznego**) za pomocą Advanced Encryption Standard (AES-128) lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM licencje do autoryzowanych klientów. Aby uzyskać więcej informacji na temat sposobu szyfrowanie zawartości przy użyciu usługi Media Services, zobacz [ochrony zawartości — omówienie](content-protection-overview.md)

Jeśli to konieczne, można także zastosować filtrowanie dynamiczne, dzięki której może służyć do kontrolowania liczby ścieżek, formatów, szybkości transmisji i prezentacji okna czasowe, które są wysłane do odtwarzaczy. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

### <a name="new-capabilities-for-live-streaming-in-v3"></a>Nowe możliwości transmisji strumieniowej na żywo w wersji 3

Za pomocą v3 interfejsów API usługi Media Services, możesz skorzystać z następujące nowe funkcje:

- Nowy tryb małymi opóźnieniami. Aby uzyskać więcej informacji, zobacz [opóźnienie](live-event-latency.md).
- Ulepszona obsługa protokołu RTMP (Zwiększona stabilność i więcej obsługę kodera źródłowego).
- Pozyskuj RTMPS bezpieczne.<br/>Gdy utworzysz element LiveEvent, otrzymasz 4 adresy URL pozyskiwania. Pozyskiwanie 4 adresy URL są niemal identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numer portu jest inny. Są dwa adresy URL podstawowego i zapasowego dla RTMPS.   
- Można przesyłać strumieniowo wydarzenia na żywo, które są do 24 godzin długo po za pomocą usługi Media Services w celu przetranskodowania jej wkład pojedyncza szybkość transmisji bitów źródła danych do strumienia wyjściowego, który ma wielokrotnych. 

## <a name="liveevent-types"></a>Element LiveEvent typów

A [element LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie przekazywania i na żywo. 

### <a name="pass-through"></a>Przekazywanie

![przekazywane](./media/live-streaming/pass-through.png)

Korzystając z przekazywania element LiveEvent, możesz polegać na Twoje lokalny koder na żywo do generowania wielu strumienia wideo o szybkości transmisji bitów i wysyłania, że jako udział Kanał informacyjny do element LiveEvent (przy użyciu protokołu RTMP lub pofragmentowany plik MP4). Element LiveEvent następnie niesie ze sobą za pośrednictwem przychodzących strumieni wideo bez dalszego przetwarzania. Takie przekazywanego element LiveEvent jest zoptymalizowany do wydarzeń na żywo długotrwałych lub 24 x 365 liniowej transmisja strumieniowa na żywo. Podczas tworzenia tego typu element LiveEvent należy określić Brak (LiveEventEncodingType.None).

Możesz wysłać wkład kanał przy rozdzielczości, maksymalnie 4 K oraz w ramce stopień 60 klatek na sekundę koder H.264/AVC lub H.265/HEVC koderów-dekoderów wideo i AAC (AAC-LC, HE-AACv1 lub HE-AACv2) kodera-dekodera audio.  Zobacz [element LiveEvent typów porównania i ograniczenia](live-event-types-comparison.md) artykuł, aby uzyskać więcej informacji.

> [!NOTE]
> Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
> 

Zobacz przykład połączenia na żywo w [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kodowanie na żywo  

![funkcji Live encoding](./media/live-streaming/live-encoding.png)

Korzystając z kodowania na żywo za pomocą usługi Media Services, należy skonfigurować usługi na lokalny koder na żywo, aby wysłać pojedyncza szybkość transmisji bitów wideo jako udział Kanał informacyjny do element LiveEvent (przy użyciu protokołu RTMP lub podzielonej zawartości w formacie Mp4). Element LiveEvent koduje tej przychodzącej pojedyncza szybkość transmisji bitów do usługi stream [wielu strumienia wideo o szybkości transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), udostępnia dostarczania odtworzyć urządzeń za pośrednictwem protokołów, takich jak MPEG-DASH, HLS i Smooth Streaming. Podczas tworzenia tego typu element LiveEvent, określ typ kodowania jako **standardowa** (LiveEventEncodingType.Standard).

Możesz wysłać wkład kanału informacyjnego w maksymalnie 1080 p rozwiązania z prędkością 30 klatek, / sekundę, przy użyciu kodera-dekodera wideo koder H.264/AVC i AAC (AAC-LC, HE-AACv1 lub HE-AACv2) kodera-dekodera audio. Zobacz [element LiveEvent typów porównania i ograniczenia](live-event-types-comparison.md) artykuł, aby uzyskać więcej informacji.

## <a name="liveevent-types-comparison"></a>Porównanie typów element LiveEvent

Następujący artykuł zawiera tabelę, która zawiera porównanie funkcji dwa typy element LiveEvent: [Porównanie](live-event-types-comparison.md).

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) umożliwia sterowanie właściwości wychodzącej transmisji strumieniowej na żywo, takie jak ilość strumienia jest rejestrowane (na przykład pojemność funkcji DVR w chmurze) i czy osoby oglądające można uruchomić, oglądając transmisji strumieniowej na żywo. Relacja między **element LiveEvent** i jego **LiveOutput**s relacji jest podobny do tradycyjnych telewizyjnych emisji, zgodnie z którą kanału (**element LiveEvent**) reprezentuje stały strumień i nagranie wideo (**LiveOutput**) obejmuje zasięgiem segmencie określony czas (na przykład wieczorami wiadomości od 18:30:00 do 19:00:00). Można rejestrować za pomocą cyfrowego rejestratora wideo (DVR) telewizyjnych — funkcji równoważnej w LiveEvents odbywa się za pomocą właściwości ArchiveWindowLength. Nadszedł czas przedział czasu ISO 8601 (na przykład PTHH:MM:SS) Określa pojemność DVR, która może być równa z co najmniej 3 minuty maksymalnie 25 godzin.


> [!NOTE]
> **LiveOutput**s start przy tworzeniu i Zatrzymaj po usunięciu. Po usunięciu **LiveOutput**, nie powoduje usunięcia podstawowych **zasobów** i zawartości w elemencie zawartości.  

Aby uzyskać więcej informacji, zobacz [używanie funkcji DVR w chmurze](live-event-cloud-dvr.md).

## <a name="streamingendpoint"></a>StreamingEndpoint

Po przesłaniu strumienia do **element LiveEvent**, można rozpocząć zdarzenie przesyłania strumieniowego, tworząc **zasobów**, **LiveOutput**, i **StreamingLocator** . Spowoduje to archiwizację strumienia i udostępnić go użytkownikom za pośrednictwem [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Po utworzeniu konta usługi Media Services domyślny punkt końcowy przesyłania strumieniowego jest dodawany do swojego konta w stanie zatrzymania. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość musi być w stanie uruchomiona.

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />Element LiveEvent stanów i rozliczenia

Element LiveEvent rozpoczyna się rozliczeń zaraz po jego stan przechodzi do **systemem**. Aby zatrzymać element LiveEvent z rozliczeniami, musisz zatrzymać element LiveEvent.

Aby uzyskać szczegółowe informacje, zobacz [stanów i rozliczenia](live-event-states-billing.md).

## <a name="latency"></a>Opóźnienie

Aby uzyskać szczegółowe informacje na temat LiveEvents opóźnienia, zobacz [opóźnienie](live-event-latency.md).

## <a name="live-streaming-workflow"></a>Przepływ pracy transmisji strumieniowej na żywo

Poniżej przedstawiono kroki, aby uzyskać przepływ pracy transmisji strumieniowej na żywo:

1. Utwórz element LiveEvent.
2. Utwórz nowy obiekt zasobów.
3. Utwórz LiveOutput i użyj nazwy zasobu, który został utworzony.
4. Utwórz zasady przesyłania strumieniowego i klucza zawartości, aby przeprowadzić szyfrowanie zawartości przy użyciu DRM.
5. W przeciwnym razie przy użyciu technologii DRM, utworzyć Lokalizator przesyłania strumieniowego za pomocą wbudowanych typów zasad przesyłania strumieniowego.
6. Lista ścieżek zasady przesyłania strumieniowego, aby wrócić adresy URL, aby użyć (są to deterministyczne).
7. Pobieranie nazwy hosta punktu końcowego przesyłania strumieniowego chcesz przesyłać strumieniowo z. 
8. Adres URL w kroku 6 należy połączyć z nazwą hosta w kroku 7, aby uzyskać pełny adres URL.

Aby uzyskać więcej informacji, zobacz [transmisji strumieniowej na żywo w ramach samouczka](stream-live-tutorial-with-api.md) opartego na [na żywo platformy .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live) próbki.

## <a name="next-steps"></a>Kolejne kroki

- [Porównanie typów element LiveEvent](live-event-types-comparison.md)
- [Stany i rozliczenia](live-event-states-billing.md)
- [Opóźnienie](live-event-latency.md)
