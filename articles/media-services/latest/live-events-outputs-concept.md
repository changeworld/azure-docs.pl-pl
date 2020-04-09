---
title: Pojęcia dotyczące wydarzeń na żywo i wyjść na żywo w usłudze Azure Media Services w wersji 3
titleSuffix: Azure Media Services
description: Ten temat zawiera omówienie wydarzeń na żywo i wyjść na żywo w usłudze Azure Media Services w wersji 3.
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
ms.date: 04/08/2020
ms.author: juliako
ms.openlocfilehash: 8a00f7c0ec76510cc521966acf98b7250e723697
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985902"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Wydarzenia na żywo i wyjścia na żywo w usługach media

Usługa Azure Media Services umożliwia dostarczanie zdarzeń na żywo do klientów w chmurze platformy Azure. Aby skonfigurować zdarzenia przesyłania strumieniowego na żywo w umiaście Media Services w wersji 3, należy zrozumieć pojęcia omówione w tym artykule.

> [!TIP]
> W przypadku klientów migrujących z interfejsów API usługi Media Services w wersji 2 jednostka **Zdarzenia na żywo** zastępuje **kanał** w wersji 2, a wyjście **na żywo** zastępuje **program**.

## <a name="live-events"></a>Wydarzenia na żywo

[Wydarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Podczas tworzenia zdarzenia na żywo tworzony jest podstawowy i pomocniczy wejściowy punkt końcowy, którego można użyć do wysłania sygnału na żywo ze zdalnego kodera. Zdalny koder na żywo wysyła źródło danych wejściowych do tego wejściowego punktu końcowego przy użyciu protokołu wejściowego [RTMP](https://www.adobe.com/devnet/rtmp.html) lub [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmented-MP4). W przypadku protokołu pozyskiwania RTMP zawartość może być wysyłana w postaci przezroczystej`rtmp://`(`rtmps://`) lub bezpiecznie szyfrowana na poprzecznie( ). W przypadku protokołu płynnego pozyskiwania strumieniowego obsługiwane `http://` schematy adresów URL są lub `https://`.  

## <a name="live-event-types"></a>Typy zdarzeń na żywo

[Zdarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień wielu bitrate) lub *kodowanie* na żywo (lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów). Typy są ustawiane podczas tworzenia przy użyciu [LiveEventEncodingType:](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None:** Lokalny koder na żywo wysyła strumień o wielu szybkości transmisji bitów. Pozyskany strumień przechodzi przez wydarzenie na żywo bez dalszego przetwarzania. Nazywany również trybem pass-through.
* **LiveEventEncodingType.Standard:** Lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów do usługi Live Event, a usługa Media Services tworzy wiele strumieni szybkości transmisji bitów. Jeśli kanał informacyjny o szybkości 720p lub wyższej rozdzielczości, ustawienie predefiniowane **Default720p** zakoduje zestaw par 6 rozdzielczości/szybkości transmisji bitów.
* **LiveEventEncodingType.Premium1080p:** Lokalny koder na żywo wysyła pojedynczy strumień szybkości transmisji bitów do usługi Live Event, a usługa Media Services tworzy wiele strumieni szybkości transmisji bitów. Ustawienie predefiniowane Default1080p określa wyjściowy zestaw par rozdzielczości/szybkości transmisji bitów.

### <a name="pass-through"></a>Przekazywanie

![przykładowe zdarzenie na żywo przekazywane z usługą Media Services](./media/live-streaming/pass-through.svg)

Kiedy używasz przekazywanego **wydarzenia na żywo**, oczekujesz, że Twój lokalny koder na żywo wygeneruje strumień wideo z wieloma szybkościami transmisji bitów i wyśle go jako kanał informacyjny do wydarzenia na żywo (przy użyciu protokołu RTMP lub pliku MP4 podzielonego na fragmenty). Wydarzenie na żywo przekazuje następnie przychodzące strumienie wideo bez dalszego przetwarzania. Takie wydarzenie na żywo jest zoptymalizowane pod kątem długotrwałych wydarzeń na żywo lub liniowej transmisji na żywo 24x365. Tworząc wydarzenie na żywo tego typu, należy określić parametr None (LiveEventEncodingType.None).

Możesz wysyłać kanał informacyjny o rozdzielczości maksymalnie 4K oraz z szybkością 60 klatek na sekundę, za pomocą koderów-dekoderów wideo H.264/AVC lub H.265/HEVC i kodera-dekodera audio AAC (AAC-LC, HE-AACv1 lub HE-AACv2). Aby uzyskać więcej informacji, zobacz [porównanie typów zdarzeń na żywo](live-event-types-comparison.md).

> [!NOTE]
> Korzystanie z metody przekazywania jest najbardziej ekonomicznym sposobem przesyłania strumieniowego na żywo, gdy robisz wiele zdarzeń w długim okresie czasu, a już zainwestowałeś w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
>

Zobacz przykład kodu .NET w [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kodowanie na żywo  

![kodowanie na żywo za pomocą przykładowego diagramu usługi Media Services](./media/live-streaming/live-encoding.svg)

Korzystając z kodowania na żywo w układzie Media Services, można skonfigurować lokalny koder na żywo do wysyłania pojedynczego pliku wideo o szybkości transmisji bitów jako źródła danych w udziale zdarzenia na żywo (przy użyciu protokołu RTMP lub Fragmented-Mp4). Następnie skonfigurować zdarzenie na żywo tak, aby koduje, że przychodzące strumienia pojedynczej szybkości transmisji bitów do [strumienia wideo o wielu szybkościach transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)i udostępnia dane wyjściowe do dostarczania do odtwarzania urządzeń za pośrednictwem protokołów, takich jak MPEG-DASH, HLS i Smooth Streaming.

W przypadku używania kodowania na żywo można wysyłać kanał informacyjny tylko w rozdzielczości do 1080p z szybkością klatek 30 klatek na sekundę, z kodekiem wideo H.264/AVC i kodekiem audio AAC (AAC-LC, HE-AACv1 lub HE-AACv2). Należy pamiętać, że przekazywanie wydarzeń na żywo może obsługiwać rozdzielczości do 4K w 60 klatkach na sekundę. Aby uzyskać więcej informacji, zobacz [porównanie typów zdarzeń na żywo](live-event-types-comparison.md).

Rozdzielczości i szybkości transmisji bitów zawarte w danych wyjściowych z embarodzi na żywo jest określana przez predefiniowane. W przypadku korzystania ze **standardowego** kodera na żywo (LiveEventEncodingType.Standard), ustawienie predefiniowane *Default720p* określa zestaw sześciu par rozdzielczości/szybkości transmisji bitów, przechodząc od 720p przy 3,5 Mb/s do 192p przy 200 kbps. W przeciwnym razie, jeśli używasz kodera **premium1080p** na żywo (LiveEventEncodingType.Premium1080p), a następnie *default1080p* preset określa zestaw sześciu par rozdzielczości / szybkości transmisji bitów, począszy od 1080p przy 3,5 Mbps w dół do 180p przy 200 kbps. Aby uzyskać informacje, zobacz [Ustawienia wstępne systemu](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Jeśli chcesz dostosować ustawienia predefiniowane kodowania na żywo, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure portal. Określ żądaną tabelę rozdzielczości i szybkości transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa w rozdzielczości 720p (jeśli zażądasz predefiniowane dla standardowego kodera na żywo) lub w rozdzielczości 1080p (jeśli zażądasz predefiniowane dla kodera premium1080p na żywo) i co najwyżej 6 warstw.

## <a name="creating-live-events"></a>Tworzenie wydarzeń na żywo

### <a name="options"></a>Opcje

Podczas tworzenia zdarzenia na żywo można określić następujące opcje:

* Protokół transmisji strumieniowej wydarzenia na żywo (obecnie obsługiwane są protokoły RTMP i Smooth Streaming).<br/>Nie można zmienić opcji protokołu, gdy jest uruchomione zdarzenie na żywo lub skojarzone z nim dane wyjściowe na żywo. Jeśli potrzebujesz różnych protokołów, utwórz osobne wydarzenie na żywo dla każdego protokołu przesyłania strumieniowego.  
* Podczas tworzenia zdarzenia można określić jego automatyczne uruchamianie. <br/>Jeśli automatyczne uruchamianie zostanie ustawione na wartość true, wydarzenie na żywo rozpocznie się po utworzeniu. Rozliczenia rozpoczynają się natychmiast po uruchomieniu wydarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Alternatywnie można uruchomić zdarzenie, gdy będziesz gotowy do rozpoczęcia przesyłania strumieniowego.

    Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).

* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym wydarzeniu na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).<br/>Jeśli nie określono żadnych adresów IP i nie ma definicji reguły, nie będzie dozwolony żaden adres IP. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą być w jednym z następujących formatów: adres IpV4 z czterema numerami lub zakres adresów CIDR.

    Jeśli chcesz włączyć niektóre adresy IP na własnych zapór lub chcesz ograniczyć dane wejściowe do zdarzeń na żywo do adresów IP platformy Azure, pobierz plik JSON z [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Aby uzyskać szczegółowe informacje o tym pliku, wybierz sekcję **Szczegóły** na stronie.
    
* Podczas tworzenia wydarzenia można włączyć transkrypcje na żywo. <br/> Domyślnie transkrypcja na żywo jest wyłączona. Nie można zmienić tej właściwości, gdy jest uruchomione zdarzenie na żywo lub skojarzone z nim dane wyjściowe na żywo. 

### <a name="naming-rules"></a>Reguły nazewnictwa

* Maksymalna nazwa wydarzenia na żywo to 32 znaki.
* Nazwa powinna być zgodna z `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`tym wzorem [wyrażenia regularnego:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

Zobacz też [Konwencje nazewnictwa punktów końcowych przesyłania strumieniowego](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Aby zagwarantować unikatowość nazwy zdarzenia na żywo, można wygenerować identyfikator GUID, a następnie usunąć wszystkie łączniki i nawiasy klamrowe (jeśli istnieją). Ciąg będzie unikatowy we wszystkich wydarzeniach na żywo, a jego długość jest gwarantowana na 32.

## <a name="live-event-ingest-urls"></a>Adresy URL pozyskiwania wydarzeń na żywo

Po utworzeniu wydarzenia na żywo można uzyskać adresy URL pozyskiwania, które będą udostępniane do lokalnego kodera na żywo. Koder na żywo używa tych adresów URL do wprowadzenia strumienia na żywo. Aby uzyskać więcej informacji, zobacz [Zalecane lokalne kodery na żywo](recommended-on-premises-live-encoders.md).

Możesz użyć znaczących lub nieznaczących adresów URL.

> [!NOTE] 
> Aby adres URL pozyskiwania był predykcyjny, ustaw tryb "próżność".

* Adres URL niebędący próżnością

    Adres URL niebędący usuką jest trybem domyślnym w umiań Media Services w wersji 3. Potencjalnie szybko uzyskujesz wydarzenie na żywo, jednak adres URL pozyskiwania jest znany tylko po uruchomieniu wydarzenia na żywo. Adres URL ulegnie zmianie, jeśli zatrzymasz/uruchomisz wydarzenie na żywo. <br/>Non-Vanity jest przydatne w scenariuszach, gdy użytkownik końcowy chce przesyłać strumieniowo przy użyciu aplikacji, w której aplikacja chce uzyskać zdarzenie na żywo Jak najszybciej i o dynamiczny adres URL pozyskiwania nie jest problemem.

    Jeśli aplikacja kliencka nie musi wstępnie wygenerować adresu URL pozyskiwania przed utworzeniem zdarzenia na żywo, pozwól usługi Media Services automatyczniegenerować token dostępu dla zdarzenia na żywo.

* Adres URL próżności

    Tryb próżności jest preferowany przez dużych nadawców multimedialnych, którzy używają koderów emisji sprzętowych i nie chcą ponownie konfigurować swoich koderów po uruchomieniu wydarzenia na żywo. Chcą predykcyjnego adresu URL pozyskiwania, który nie zmienia się w czasie.
    
    > [!NOTE]
    > W witrynie Azure portal adres URL próżności nosi nazwę "*adres URL wprowadzania trwałego*".

    Aby określić ten tryb `vanityUrl` w `true` interfejsie API, `false`ustaw na czas tworzenia (domyślnie jest ). Należy również przekazać własny token`LiveEventInput.accessToken`dostępu ( ) w czasie tworzenia. Należy określić wartość tokenu, aby uniknąć losowego tokenu w adresie URL. Token dostępu musi być prawidłowym ciągiem GUID (z myślnikami lub bez nich). Po ustawieniu trybu nie można go zaktualizować.

    Token dostępu musi być unikatowy w centrum danych. Jeśli aplikacja musi używać adresu URL próżności, zaleca się zawsze utworzyć nowe wystąpienie identyfikatora GUID dla tokenu dostępu (zamiast ponownego użycia istniejącego identyfikatora GUID).

    Użyj następujących interfejsów API, aby włączyć adres URL próżności i ustawić `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`token dostępu na prawidłowy identyfikator GUID (na przykład ).  

    |Język|Włącz adres URL próżności|Określanie tokenu dostępu|
    |---|---|---|
    |REST|[właściwości.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |Interfejs wiersza polecenia|[--próżność-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.Vanityurl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Reguły nazewnictwa adresów URL pozyskiwania na żywo

* *Losowy* ciąg poniżej to 128-bitowa liczba szesnastkowa (która składa się z 32 znaków 0–9 a–f).
* *twój token dostępu:* prawidłowy ciąg GUID ustawiony podczas korzystania z trybu próżności. Na przykład `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nazwa strumienia*: Wskazuje nazwę strumienia dla określonego połączenia. Wartość nazwy strumienia jest zwykle dodawana przez koder na żywo, którego używasz. Koder na żywo można skonfigurować tak, aby używał dowolnej nazwy do opisania połączenia, na przykład: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-vanity-url"></a>Adres URL niebędący próżnością

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Adres URL próżności

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Adres URL podglądu wydarzenia na żywo

Po rozpoczęciu odbierania kanału informacyjnego na żywo można użyć jego punktu końcowego wersji zapoznawczej, aby wyświetlić podgląd i sprawdzić, czy otrzymujesz strumień na żywo przed dalszym publikowaniem. Po sprawdzeniu, czy strumień w wersji zapoznawczej jest dobry, możesz użyć zdarzenia na żywo, aby udostępnić strumień na żywo do dostarczenia za pośrednictwem jednego lub więcej (wstępnie utworzonych) punktów końcowych przesyłania strumieniowego. Aby to osiągnąć, utwórz nowe [dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs) na żywo w wydarzeniu na żywo.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że film jest przesyłany do adresu URL wersji zapoznawczej!

## <a name="live-event-long-running-operations"></a>Długotrwałe operacje na żywo

Aby uzyskać szczegółowe informacje, zobacz [długotrwałe operacje](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Dane wyjściowe na żywo

Po przejściu strumienia do zdarzenia na żywo można rozpocząć zdarzenie przesyłania strumieniowego, tworząc [zasób](https://docs.microsoft.com/rest/api/media/assets), [wyjście na żywo](https://docs.microsoft.com/rest/api/media/liveoutputs)i [lokalizator przesyłania strumieniowego.](https://docs.microsoft.com/rest/api/media/streaminglocators) Wyjście na żywo zarchiwizuje strumień i udostępni go widzom za pośrednictwem [punktu końcowego przesyłania strumieniowego.](https://docs.microsoft.com/rest/api/media/streamingendpoints)  

Aby uzyskać szczegółowe informacje na temat wyjść na żywo, zobacz [Korzystanie z rejestratora DVR w chmurze](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Zobacz artykuł [Często zadawane pytania.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Zadawaj pytania i otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

[Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
