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
ms.date: 03/30/2019
ms.author: juliako
ms.openlocfilehash: 00dab8381c26a6331dd325eacd4a550892bd3411
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495177"
---
# <a name="live-events-and-live-outputs"></a>Wydarzenia i dane wyjściowe na żywo

Usługa Azure Media Services umożliwia dostarczanie wydarzeń na żywo dla klientów w chmurze Azure. Aby skonfigurować usługi przesyłania strumieniowego wydarzeń na żywo usługi Media Services v3, należy zrozumieć kwestie omówione w tym artykule. <br/>Lista sekcjach znajduje się w prawej części strony.

## <a name="live-events"></a>Wydarzenia na żywo

[Wydarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Kiedy utworzysz wydarzenie na żywo, tworzony jest wejściowy punkt końcowy, którego możesz użyć do wysyłania sygnału na żywo z kodera zdalnego. Zdalny koder na żywo wysyła kanał informacyjny do tego wejściowego punktu końcowego przy użyciu protokołu [RTMP](https://www.adobe.com/devnet/rtmp.html) lub [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (plik MP4 podzielony na fragmenty). Funkcji Smooth Streaming protokołu pozyskiwania, są obsługiwane schematy adresów URL `http://` lub `https://`. Przypadku protokołu pozyskiwania RTMP, są obsługiwane schematy adresów URL `rtmp://` lub `rtmps://`. 

## <a name="live-event-types"></a>Typy zdarzeń na żywo

A [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie przekazywania i na żywo. 

### <a name="pass-through"></a>Przekazywanie

![przekazywane](./media/live-streaming/pass-through.svg)

Kiedy używasz przekazywanego **wydarzenia na żywo**, oczekujesz, że Twój lokalny koder na żywo wygeneruje strumień wideo z wieloma szybkościami transmisji bitów i wyśle go jako kanał informacyjny do wydarzenia na żywo (przy użyciu protokołu RTMP lub pliku MP4 podzielonego na fragmenty). Wydarzenie na żywo przekazuje następnie przychodzące strumienie wideo bez dalszego przetwarzania. Takie przekazywane wydarzenie na żywo jest zoptymalizowane pod kątem długoterminowych wydarzeń na żywo lub liniowej transmisji strumieniowej na żywo w formacie 24x365. Tworząc wydarzenie na żywo tego typu, należy określić parametr None (LiveEventEncodingType.None).

Możesz wysyłać kanał informacyjny o rozdzielczości maksymalnie 4K oraz z szybkością 60 klatek na sekundę, za pomocą koderów-dekoderów wideo H.264/AVC lub H.265/HEVC i kodera-dekodera audio AAC (AAC-LC, HE-AACv1 lub HE-AACv2).  Aby uzyskać więcej informacji, zobacz artykuł [Porównanie typów wydarzeń na żywo](live-event-types-comparison.md).

> [!NOTE]
> Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
> 

Zobacz przykładowy kod platformy .NET w [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kodowanie na żywo  

![funkcji Live encoding](./media/live-streaming/live-encoding.svg)

W przypadku korzystania z kodowania na żywo za pomocą usługi Media Services należy skonfigurować lokalny koder na żywo, aby wysłać klip wideo z jedną szybkością transmisji bitów jako kanał informacyjny do wydarzenia na żywo (przy użyciu protokołu RTMP lub pliku MP4 podzielonego na fragmenty). Wydarzenie na żywo koduje ten strumień z jedną szybkością transmisji bitów do [strumienia z wieloma szybkościami transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), udostępnia go do dostarczenia do urządzeń odtwarzania za pomocą protokołów, takich jak MPEG-DASH, HLS i Smooth Streaming. Podczas tworzenia tego typu wydarzenia na żywo określ typ kodowania jako **Standardowy** (LiveEventEncodingType.Standard).

Możesz wysyłać kanał informacyjny o rozdzielczości maksymalnie 1080p oraz z szybkością 30 klatek na sekundę za pomocą kodera-dekodera wideo H.264/AVC i kodera-dekodera audio AAC (AAC-LC, HE-AACv1 lub HE-AACv2). Aby uzyskać więcej informacji, zobacz artykuł [Porównanie typów wydarzeń na żywo](live-event-types-comparison.md).

W przypadku korzystania z kodowania na żywo (wydarzenie na żywo z ustawieniem **Standardowy**) ustawienie wstępne kodowania definiuje sposób kodowania przychodzącego strumienia w wielu różnych szybkościach transmisji bitów lub warstwach. Aby uzyskać informacje, zobacz [Ustawienia wstępne systemu](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Obecnie jedyną dozwoloną wartością ustawienia wstępnego dla standardowego typu wydarzenia na żywo jest *Default720p*. Jeśli chcesz użyć niestandardowego ustawienia wstępnego kodowania na żywo, wyślij wiadomość na adres amshelp@microsoft.com. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa dla rozdzielczości 720p i maksymalnie 6 warstw.

## <a name="live-event-creation-options"></a>Opcje tworzenia zdarzenia na żywo

Podczas tworzenia zdarzenia na żywo, można określić następujące opcje:

* Protokół transmisji strumieniowej wydarzenia na żywo (obecnie obsługiwane są protokoły RTMP i Smooth Streaming).<br/>Nie można zmienić opcji protokołu, gdy wydarzenie na żywo lub skojarzone z nim dane wyjściowe na żywo są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobne wydarzenie na żywo dla każdego protokołu przesyłania strumieniowego.  
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym wydarzeniu na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).<br/>Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą mieć jeden z następujących formatów: adres IPv4 z 4 cyframi, zakres adresów CIDR.
* Podczas tworzenia zdarzenia można określić, że będzie ono automatycznie uruchamiane. <br/>Jeśli automatyczne uruchamianie zostanie ustawione na wartość true, wydarzenie na żywo rozpocznie się po utworzeniu. Naliczanie opłat rozpoczyna się zaraz po uruchomieniu wydarzenie na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Alternatywnie możesz rozpocząć zdarzenie, gdy jesteś gotowy rozpocząć przesyłanie strumieniowe. 

    Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).

## <a name="live-event-ingest-urls"></a>Adresy URL pozyskiwania na żywo z wydarzeń

Po utworzeniu wydarzenia na żywo można pobrać adresy URL pozyskiwania, które należy udostępnić lokalnemu koderowi na żywo. Koder na żywo używa tych adresów URL do wprowadzenia strumienia na żywo. Aby uzyskać więcej informacji, zobacz [zalecane kodery na żywo lokalne](recommended-on-premises-live-encoders.md). 

Możesz użyć znaczących lub nieznaczących adresów URL. 

* Adres URL bez znaczących

    Nieznaczący adres URL to tryb domyślny w usłudze AMS w wersji 3. Potencjalnie szybko uzyskujesz wydarzenie na żywo, jednak adres URL pozyskiwania jest znany tylko po uruchomieniu wydarzenia na żywo. Adres URL ulegnie zmianie, jeśli zatrzymasz/uruchomisz wydarzenie na żywo. <br/>Tryb nieznaczący jest przydatny w scenariuszach, kiedy użytkownik końcowy chce przesyłać strumieniowo przy użyciu aplikacji, która chce uzyskać wydarzenie na żywo jak najszybciej, i dynamiczny adres URL pozyskiwania nie jest problemem.
* Znaczącym adresie URL

    Tryb znaczący jest preferowany przez dużych nadawców multimediów, którzy używają sprzętowych koderów emisyjnych i nie chcą ponownie konfigurować swoich koderów, kiedy uruchamiają wydarzenie na żywo. Wolą dysponować predykcyjnym adresem URL pozyskiwania, który nie zmienia się w czasie.

> [!NOTE] 
> Adres URL pozyskiwania się należy użyć trybu "znaczących" i przekazać własny token dostępu (w celu uniknięcia losowe token w adresie URL).

### <a name="live-ingest-url-naming-rules"></a>Pozyskiwanie na żywo reguł nazewnictwa adresów URL

*Losowy* ciąg poniżej to 128-bitowa liczba szesnastkowa (która składa się z 32 znaków 0–9 a–f).<br/>
Poniższy *token dostępu* to element, który należy określić na potrzeby stałego adresu URL. Jest to również 128-bitowa liczba szesnastkowa.

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

## <a name="live-event-long-running-operations"></a>Operacji długotrwałych zdarzenia na żywo

Aby uzyskać więcej informacji, zobacz [długotrwałych operacji](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Dane wyjściowe na żywo

Po przesłaniu strumienia do wydarzenie na żywo, można rozpocząć zdarzenie przesyłania strumieniowego, tworząc [zasobów](https://docs.microsoft.com/rest/api/media/assets), [na żywo dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs), i [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators). Dane wyjściowe na żywo będzie archiwizację strumienia i udostępnić go użytkownikom za pośrednictwem [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Usunięcie danych wyjściowych na żywo nie powoduje usunięcia bazowego zasobu ani znajdującej się w nim zawartości. 

Relacja między **wydarzenie na żywo** i jego **na żywo dane wyjściowe** jest podobny do tradycyjnego emisji telewizyjnych, według której kanał (**wydarzenie na żywo**) reprezentuje stałą strumień i nagranie wideo (**na żywo dane wyjściowe**) obejmuje zasięgiem segmencie określony czas (na przykład wieczorami wiadomości od 18:30:00 do 19:00:00). Program telewizyjny można rejestrować za pomocą cyfrowego rejestratora wideo (DVR) — równoważna funkcja dla zdarzeń na żywo jest zarządzana za pośrednictwem właściwości **ArchiveWindowLength**. Nadszedł czas przedział czasu ISO 8601 (na przykład PTHH:MM:SS) Określa pojemność DVR, która może być równa z co najmniej 3 minuty maksymalnie 25 godzin.

**Na żywo dane wyjściowe** obiektu przypomina rejestratora taśm, który będzie przechwytywać i zarejestruj transmisji strumieniowej na żywo do elementu zawartości w ramach konta usługi Media Services. Zarejestrowana zawartość zostaną utrwalone do konta magazynu platformy Azure dołączonych do konta, do kontenera, zdefiniowane przez zasób trwały. **Na żywo dane wyjściowe** również pozwala na kontrolowanie niektórych właściwości wychodzącej transmisji strumieniowej na żywo, takie jak ilość strumienia jest przechowywana w nagraniu archiwum (na przykład pojemność funkcji DVR w chmurze) i czy można uruchomić przeglądarki Obserwowanie transmisji strumieniowej na żywo. Archiwum na dysku jest cykliczna archiwum "okno" zawiera tylko ilość zawartości, który jest określony w **archiveWindowLength** właściwość **na żywo dane wyjściowe**. Zawartość, która wykracza poza tym oknem jest automatycznie odrzucane z kontenera magazynu, a nie jest możliwe do odzyskania. Możesz tworzyć wiele **na żywo dane wyjściowe** (maksymalnie trzy maksymalna) na **wydarzenie na żywo** archiwum różne długości i ustawień.  

Po opublikowaniu **na żywo dane wyjściowe**firmy **zasobów** przy użyciu **lokalizatora przesyłania strumieniowego**, **wydarzenie na żywo** (maksymalna długość okna DVR) będzie nadal będą widoczne do czasu wygaśnięcia lub usunięcia lokalizatora przesyłania strumieniowego, osiągnięta jako pierwsza.

Aby uzyskać więcej informacji, zobacz [przy użyciu funkcji DVR w chmurze](live-event-cloud-dvr.md).

## <a name="next-steps"></a>Kolejne kroki

[Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)
