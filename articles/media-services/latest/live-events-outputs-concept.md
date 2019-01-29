---
title: Transmisja strumieniowa pojęcia w usłudze Azure Media Services — zdarzenia na żywo i na żywo dane wyjściowe na żywo | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie koncepcji transmisji strumieniowej na żywo w usłudze Azure Media Services v3.
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
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: b5bd08219c6e48f07e93d7704216e813d75dcd72
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099921"
---
# <a name="live-events-and-live-outputs"></a>Wydarzenia i dane wyjściowe na żywo

Usługa Azure Media Services umożliwia dostarczanie wydarzeń na żywo dla klientów w chmurze Azure. Aby skonfigurować usługi przesyłania strumieniowego wydarzeń na żywo usługi Media Services v3, należy zrozumieć kwestie omówione w tym artykule:

* [Wydarzenia na żywo](#live-events)
* [Typy zdarzeń na żywo](#live-vent-types)
* [Porównanie typów zdarzeń na żywo](#live-event-types-comparison)
* [Opcje tworzenia zdarzenia na żywo](#live-event-creation-options)
* [Adresy URL pozyskiwania na żywo z wydarzeń](#live-event-ingest-urls)
* [Adres URL Podglądu zdarzeń na żywo](#live-event-preview-url)
* [Dane wyjściowe na żywo](#live-outputs).

## <a name="live-events"></a>Wydarzenia na żywo

[Wydarzeń na żywo](https://docs.microsoft.com/rest/api/media/liveevents) jest odpowiedzialny za przyjęciem i przetwarzania na żywo strumieniowych źródeł wideo. Po utworzeniu zdarzenia na żywo, wejściowy punkt końcowy jest tworzony, której można wysyłać sygnał na żywo z kodera zdalnego. Zdalny koder na żywo wysyła wkładu, kanał informacyjny do wejściowy punkt końcowy przy użyciu [RTMP](https://www.adobe.com/devnet/rtmp.html) lub [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) protokołu (pofragmentowany plik MP4). Funkcji Smooth Streaming protokołu pozyskiwania, są obsługiwane schematy adresów URL `http://` lub `https://`. Przypadku protokołu pozyskiwania RTMP, są obsługiwane schematy adresów URL `rtmp://` lub `rtmps://`. 

## <a name="live-event-types"></a>Typy zdarzeń na żywo

A [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie przekazywania i na żywo. 

### <a name="pass-through"></a>Przekazywanie

![przekazywane](./media/live-streaming/pass-through.png)

Przy użyciu przekazywanego **wydarzenie na żywo**, opierają się na swoje lokalny koder na żywo do generowania wielu strumienia wideo o szybkości transmisji bitów i wysyłania, że jako udział Kanał informacyjny do wydarzenie na żywo (przy użyciu protokołu RTMP lub pofragmentowany plik MP4). Wydarzenie na żywo następnie niesie ze sobą za pośrednictwem przychodzących strumieni wideo bez dalszego przetwarzania. Takie przekazywanego element LiveEvent jest zoptymalizowany do wydarzeń na żywo długotrwałych lub 24 x 365 liniowej transmisja strumieniowa na żywo. Podczas tworzenia tego typu zdarzenia na żywo, należy określić Brak (LiveEventEncodingType.None).

Możesz wysłać wkład kanał przy rozdzielczości, maksymalnie 4 K oraz w ramce stopień 60 klatek na sekundę koder H.264/AVC lub H.265/HEVC koderów-dekoderów wideo i AAC (AAC-LC, HE-AACv1 lub HE-AACv2) kodera-dekodera audio.  Zobacz [wydarzenie na żywo typy porównania](live-event-types-comparison.md) artykuł, aby uzyskać więcej informacji.

> [!NOTE]
> Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
> 

Zobacz przykładowy kod platformy .NET w [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kodowanie na żywo  

![funkcji Live encoding](./media/live-streaming/live-encoding.png)

Korzystając z kodowania na żywo za pomocą usługi Media Services, należy skonfigurować usługi na lokalny koder na żywo, aby wysłać pojedyncza szybkość transmisji bitów wideo jako udział Kanał informacyjny do wydarzenie na żywo (przy użyciu protokołu RTMP lub podzielonej zawartości w formacie Mp4). Wydarzenie na żywo koduje tej przychodzącej pojedyncza szybkość transmisji bitów do usługi stream [wielu strumienia wideo o szybkości transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), udostępnia dostarczania odtworzyć urządzeń za pośrednictwem protokołów, takich jak MPEG-DASH, HLS i Smooth Streaming. Podczas tworzenia tego typu zdarzenia na żywo, określ typ kodowania jako **standardowa** (LiveEventEncodingType.Standard).

Możesz wysłać wkład kanału informacyjnego w maksymalnie 1080 p rozwiązania z prędkością 30 klatek, / sekundę, przy użyciu kodera-dekodera wideo koder H.264/AVC i AAC (AAC-LC, HE-AACv1 lub HE-AACv2) kodera-dekodera audio. Zobacz [wydarzenie na żywo typy porównania](live-event-types-comparison.md) artykuł, aby uzyskać więcej informacji.

## <a name="live-event-creation-options"></a>Opcje tworzenia zdarzenia na żywo

Podczas tworzenia zdarzenia na żywo, można określić następujące opcje:

* Protokół przesyłania strumieniowego dla wydarzenie na żywo (obecnie obsługiwane są protokoły RTMP i Smooth Streaming).<br/>Nie można zmienić opcji protokołu, gdy są uruchomione wydarzenie na żywo lub jej skojarzone dane wyjściowe na żywo. Jeśli potrzebujesz różnych protokołów, należy utworzyć oddzielne zdarzenie na żywo dla każdego protokołu przesyłania strumieniowego.  
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo do tego zdarzenia na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).<br/>Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą znajdować się w jednym z następujących formatów: Adres IpV4 z 4 cyfry, zakres adresów CIDR.
* Podczas tworzenia zdarzenia można określić, że będzie ono automatycznie uruchamiane. <br/>Gdy autostart jest ustawiona na wartość true, wydarzenie na żywo zostanie uruchomiona po utworzeniu. Oznacza to, rozliczeń rozpoczyna się zaraz po uruchomieniu wydarzenie na żywo. Należy jawnie wywołać Stop zasobu wydarzenie na żywo, aby zatrzymać dalsze rozliczeń. Alternatywnie możesz rozpocząć zdarzenie, gdy jesteś gotowy rozpocząć przesyłanie strumieniowe. 

    Aby uzyskać więcej informacji, zobacz [stany wydarzenie na żywo i rozliczenia](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>Adresy URL pozyskiwania na żywo z wydarzeń

Po utworzeniu zdarzenia na żywo możesz uzyskać pozyskiwania adresów URL, zapewniających do kodera na żywo w środowisku lokalnym. Koder na żywo używa tych adresów URL do wprowadzenia strumienia na żywo. Aby uzyskać więcej informacji, zobacz [zalecane kodery na żywo lokalne](recommended-on-premises-live-encoders.md). 

Możesz użyć innych niestandardowych adresów URL lub niestandardowych adresów URL. 

* Adres URL bez znaczących

    Adres URL znaczących inne niż jest to domyślny tryb w wersji 3 usługi AMS. Możesz potencjalnie szybko wydarzenie na żywo, ale adres URL pozyskiwania jest znane tylko po uruchomieniu wydarzenia na żywo. Adres URL ulegnie zmianie, jeśli możesz zatrzymać/uruchomić wydarzenie na żywo. <br/>Bez znaczących jest przydatne w scenariuszach, gdy użytkownik końcowy chce przesyłać strumieniowo przy użyciu aplikacji, w którym aplikacja chce uzyskać wydarzenia na żywo ASAP i konieczności dynamiczny adres URL pozyskiwania nie jest problemem.
* Znaczącym adresie URL

    Tryb znaczących jest wybierany przez nośnik dużych, zastosowań studyjnych, którzy korzystają z sprzętu emisji koderów i nie chcesz ponownie skonfigurować ich koderów, po uruchomieniu wydarzenie na żywo. Chcą predykcyjne pozyskanie adresu URL, które nie zmieniają się wraz z upływem czasu.

> [!NOTE] 
> Adres URL pozyskiwania się należy użyć trybu "znaczących" i przekazać własny token dostępu (w celu uniknięcia losowe token w adresie URL).

### <a name="live-ingest-url-naming-rules"></a>Pozyskiwanie na żywo reguł nazewnictwa adresów URL

*Losowych* poniższe parametry to liczba szesnastkowa 128-bitowego (który składa się z 32 znaki 0 – 9-f).<br/>
*Token dostępu* poniżej co jest potrzebne do określenia dla stałej adresu URL. Jest również liczba szesnastkowa 128-bitowego.

#### <a name="non-vanity-url"></a>Adres URL bez znaczących

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>Znaczącym adresie URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>Adres URL Podglądu zdarzeń na żywo

Gdy **wydarzenie na żywo** rozpoczyna odbieranie wkład źródła danych, można użyć punktu końcowego (wersja zapoznawcza) do wyświetlania podglądu i weryfikowania, przed opublikowaniem Dodatkowo otrzymujesz transmisji strumieniowej na żywo. Po sprawdzeniu, że Podgląd strumienia jest dobra, aby udostępnić transmisji strumieniowej na żywo do dostarczania za pomocą co najmniej jeden (wstępnie utworzone), można użyć element LiveEvent **punkty końcowe przesyłania strumieniowego**. Aby to osiągnąć, należy utworzyć nową [na żywo dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs) na **wydarzenie na żywo**. 

> [!IMPORTANT]
> Upewnij się, że wideo będą przepływać do adresu URL w wersji zapoznawczej przed kontynuowaniem.

## <a name="live-outputs"></a>Dane wyjściowe na żywo

Po przesłaniu strumienia do wydarzenie na żywo, można rozpocząć zdarzenie przesyłania strumieniowego, tworząc [zasobów](https://docs.microsoft.com/rest/api/media/assets), [na żywo dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs), i [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators). Dane wyjściowe na żywo będzie archiwizację strumienia i udostępnić go użytkownikom za pośrednictwem [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Na żywo dane wyjściowe start przy tworzeniu i Zatrzymaj po usunięciu. Po usunięciu danych wyjściowych na żywo nie powoduje usunięcia podstawowych zasobów i zawartości w elemencie zawartości. 

Relacja między **wydarzenie na żywo** i jego **na żywo dane wyjściowe** jest podobny do tradycyjnego emisji telewizyjnych, według której kanał (**wydarzenie na żywo**) reprezentuje stałą strumień i nagranie wideo (**na żywo dane wyjściowe**) obejmuje zasięgiem segmencie określony czas (na przykład wieczorami wiadomości od 18:30:00 do 19:00:00). Można rejestrować za pomocą cyfrowego rejestratora wideo (DVR) telewizyjnych — funkcji równoważnej zdarzeń na żywo jest zarządzana za pośrednictwem **ArchiveWindowLength** właściwości. Nadszedł czas przedział czasu ISO 8601 (na przykład PTHH:MM:SS) Określa pojemność DVR, która może być równa z co najmniej 3 minuty maksymalnie 25 godzin.

**Na żywo dane wyjściowe** obiektu przypomina rejestratora taśm, który będzie przechwytywać i zarejestruj transmisji strumieniowej na żywo do elementu zawartości w ramach konta usługi Media Services. Zarejestrowana zawartość zostaną utrwalone do konta magazynu platformy Azure dołączonych do konta, do kontenera, zdefiniowane przez zasób trwały. **Na żywo dane wyjściowe** również pozwala na kontrolowanie niektórych właściwości wychodzącej transmisji strumieniowej na żywo, takie jak ilość strumienia jest przechowywana w nagraniu archiwum (na przykład pojemność funkcji DVR w chmurze) i czy można uruchomić przeglądarki Obserwowanie transmisji strumieniowej na żywo. Archiwum na dysku jest cykliczna archiwum "okno" zawiera tylko ilość zawartości, który jest określony w **archiveWindowLength** właściwość **na żywo dane wyjściowe**. Zawartość, która wykracza poza tym oknem jest automatycznie odrzucane z kontenera magazynu, a nie jest możliwe do odzyskania. Możesz tworzyć wiele **na żywo dane wyjściowe** (maksymalnie trzy maksymalna) na **wydarzenie na żywo** archiwum różne długości i ustawień.  

Po opublikowaniu **na żywo dane wyjściowe**firmy **zasobów** przy użyciu **lokalizatora przesyłania strumieniowego**, **wydarzenie na żywo** (maksymalna długość okna DVR) będzie nadal będą widoczne do czasu wygaśnięcia lub usunięcia lokalizatora przesyłania strumieniowego, osiągnięta jako pierwsza.

Aby uzyskać więcej informacji, zobacz [przy użyciu funkcji DVR w chmurze](live-event-cloud-dvr.md).

## <a name="next-steps"></a>Kolejne kroki

- [Przesyłanie strumieniowe wydarzeń na żywo](live-streaming-overview.md)
- [Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)
