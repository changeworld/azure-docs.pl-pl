---
title: Koncepcje przesyłania strumieniowego na żywo w ramach zdarzeń Azure Media Services-Live i danych wyjściowych na żywo | Microsoft Docs
description: Ten artykuł zawiera omówienie pojęć dotyczących przesyłania strumieniowego na żywo w Azure Media Services v3.
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
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: efe0aaf7c7d5516401f8c72721121a5dff247b95
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350374"
---
# <a name="live-events-and-live-outputs"></a>Wydarzenia i dane wyjściowe na żywo

Azure Media Services pozwala na dostarczanie na żywo wydarzeń klientom w chmurze platformy Azure. Aby skonfigurować Twoje zdarzenia przesyłania strumieniowego na żywo w Media Services v3, należy zrozumieć koncepcje omówione w tym artykule.

> [!TIP]
> W przypadku klientów przeprowadzających migrację z interfejsów API Media Services V2, jednostka **zdarzenia na żywo** zastępuje **kanał** w wersji 2, a **dane wyjściowe na żywo** zastępują **program**.

## <a name="live-events"></a>Wydarzenia na żywo

[Wydarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Podczas tworzenia zdarzenia na żywo tworzony jest podstawowy i pomocniczy punkt końcowy wejścia, którego można użyć do wysłania sygnału na żywo ze zdalnego kodera. Zdalny koder na żywo wysyła strumieniowe źródło danych do tego wejściowego punktu końcowego przy użyciu protokołu [RTMP](https://www.adobe.com/devnet/rtmp.html) lub [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmentacja-MP4). W przypadku protokołu pozyskiwania RTMP zawartość może być wysyłana w postaci zwykłej`rtmp://`() lub bezpiecznie zaszyfrowanej w`rtmps://`sieci (). W przypadku protokołu pozyskiwania Smooth Streaming obsługiwane schematy adresów URL to `http://` lub `https://`.  

## <a name="live-event-types"></a>Typy zdarzeń na żywo

[Wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: przekazywaniem i kodowaniem na żywo. Typy są ustawiane podczas tworzenia przy użyciu [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** — lokalny koder na żywo wysyła strumień o wielokrotnej szybkości transmisji bitów. Pozyskiwany strumień przeszedł przez wydarzenie na żywo bez dalszej obróbki. 
* **LiveEventEncodingType. Standard** — lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, a Media Services tworzy wiele strumieni szybkości transmisji bitów. Jeśli kanał informacyjny udziału ma rozdzielczość 720 lub wyższą, ustawienie wstępne **Default720p** będzie kodować zestaw par rozdzielczości/szybkości transmisji bitów.
* **LiveEventEncodingType. Premium1080p** — lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, a Media Services tworzy wiele strumieni szybkości transmisji bitów. Ustawienie wstępne Default1080p określa wyjściowy zestaw par rozdzielczości/szybkości transmisji bitów. 

### <a name="pass-through"></a>Przekazywanie

![Przekazywanie](./media/live-streaming/pass-through.svg)

Kiedy używasz przekazywanego **wydarzenia na żywo**, oczekujesz, że Twój lokalny koder na żywo wygeneruje strumień wideo z wieloma szybkościami transmisji bitów i wyśle go jako kanał informacyjny do wydarzenia na żywo (przy użyciu protokołu RTMP lub pliku MP4 podzielonego na fragmenty). Wydarzenie na żywo przekazuje następnie przychodzące strumienie wideo bez dalszego przetwarzania. Takie zdarzenie przekazywania na żywo jest zoptymalizowane pod kątem długotrwałych wydarzeń na żywo lub 24x365 liniowe przesyłanie strumieniowe na żywo. Tworząc wydarzenie na żywo tego typu, należy określić parametr None (LiveEventEncodingType.None).

Możesz wysyłać kanał informacyjny o rozdzielczości maksymalnie 4K oraz z szybkością 60 klatek na sekundę, za pomocą koderów-dekoderów wideo H.264/AVC lub H.265/HEVC i kodera-dekodera audio AAC (AAC-LC, HE-AACv1 lub HE-AACv2).  Aby uzyskać więcej informacji, zobacz artykuł [Porównanie typów wydarzeń na żywo](live-event-types-comparison.md).

> [!NOTE]
> Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
> 

Zobacz przykład kodu platformy .NET w [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kodowanie na żywo  

![Kodowanie na żywo](./media/live-streaming/live-encoding.svg)

W przypadku korzystania z kodowania na żywo za pomocą usługi Media Services należy skonfigurować lokalny koder na żywo, aby wysłać klip wideo z jedną szybkością transmisji bitów jako kanał informacyjny do wydarzenia na żywo (przy użyciu protokołu RTMP lub pliku MP4 podzielonego na fragmenty). Następnie należy skonfigurować wydarzenie na żywo, aby zakodować ten strumień przychodzącej pojedynczej szybkości transmisji bitów do [strumienia wideo o wielu szybkościach transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)i udostępnić dane wyjściowe umożliwiające dostarczenie do odtwarzania urządzeń za pomocą protokołów takich jak MPEG-kreska, HLS i Smooth Streaming.

W przypadku korzystania z kodowania na żywo można wysłać kanał informacyjny dotyczący tylko rozdzielczości o rozdzielczości do 1080p (30 klatek na sekundę) przy użyciu kodera-dekoder wideo H. 264/AVC i AAC (AAC-LC, AACv1 lub AACv2). Należy pamiętać, że zdarzenia przekazywane na żywo mogą obsługiwać rozdzielczość do 4K w 60 klatkach na sekundę. Aby uzyskać więcej informacji, zobacz artykuł [Porównanie typów wydarzeń na żywo](live-event-types-comparison.md).

Rozdzielczości i szybkości transmisji bitów zawarte w danych wyjściowych z kodera na żywo są określane przez ustawienie wstępne. Jeśli używany jest **Standardowy** koder na żywo (LiveEventEncodingType. Standard), a następnie ustawienie wstępne *Default720p* określa zestaw par szybkości rozdzielczości 6/bitowego, przechodząc od 720 przy 3,5 MB/s do 192p o 200 KB/s. W przeciwnym razie, jeśli używasz kodera **Premium1080p** na żywo (LiveEventEncodingType. Premium1080p), a następnie ustawienia wstępnego *Default1080p* określa zestaw 6 par rozdzielczości/bitów, przechodząc z 1080P przy 3,5 MB/s do 180p o 200 KB/s. Aby uzyskać informacje, zobacz [Ustawienia wstępne systemu](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Jeśli musisz dostosować ustawienie wstępne kodowania na żywo, Otwórz bilet pomocy technicznej za pośrednictwem Azure Portal. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Upewnij się, że istnieje tylko jedna warstwa o rozdzielczości 720 (jeśli żądasz ustawienia wstępnego dla standardowego kodera na żywo) lub w lokalizacji 1080p (Jeśli zażądasz ustawienia wstępnego dla kodera Premium1080p na żywo) i maksymalnie 6 warstw.

## <a name="creating-live-events"></a>Tworzenie zdarzeń na żywo 

### <a name="options"></a>Opcje

Podczas tworzenia zdarzenia na żywo można określić następujące opcje:

* Protokół transmisji strumieniowej wydarzenia na żywo (obecnie obsługiwane są protokoły RTMP i Smooth Streaming).<br/>Nie można zmienić opcji protokołu, gdy wydarzenie na żywo lub skojarzone z nim dane wyjściowe na żywo są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobne wydarzenie na żywo dla każdego protokołu przesyłania strumieniowego.  
* Podczas tworzenia zdarzenia można określić, że będzie ono automatycznie uruchamiane. <br/>Jeśli automatyczne uruchamianie zostanie ustawione na wartość true, wydarzenie na żywo rozpocznie się po utworzeniu. Rozliczanie zaczyna się zaraz po rozpoczęciu uruchamiania zdarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Możesz też uruchomić zdarzenie, gdy wszystko jest gotowe do rozpoczęcia przesyłania strumieniowego. 

    Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym wydarzeniu na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).<br/>Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą mieć jeden z następujących formatów: adres IPv4 z 4 cyframi, zakres adresów CIDR.

    Jeśli chcesz włączyć niektóre adresy IP na własnych zaporach lub chcesz ograniczyć dane wejściowe do zdarzeń na żywo do adresów IP platformy Azure, Pobierz plik JSON z [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Aby uzyskać szczegółowe informacje na temat tego pliku, kliknij sekcję **szczegóły** na stronie.
        
### <a name="naming-rules"></a>Reguły nazewnictwa

* Maksymalna Nazwa zdarzenia na żywo to 32 znaków.
* Nazwa powinna być zgodna z tym wzorcem [wyrażenia regularnego](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

Zobacz również [konwencje nazewnictwa punktów końcowych przesyłania strumieniowego](streaming-endpoint-concept.md#naming-convention).

## <a name="live-event-ingest-urls"></a>Adresy URL pozyskiwania zdarzeń na żywo

Po utworzeniu wydarzenia na żywo można pobrać adresy URL pozyskiwania, które należy udostępnić lokalnemu koderowi na żywo. Koder na żywo używa tych adresów URL do wprowadzenia strumienia na żywo. Aby uzyskać więcej informacji, zobacz [zalecane lokalne kodery na żywo](recommended-on-premises-live-encoders.md). 

Możesz użyć znaczących lub nieznaczących adresów URL. 

> [!NOTE] 
> Aby adres URL pozyskiwania był predykcyjny, ustaw tryb "znaczącym".

* Adres URL inny niż znaczącym

    Znaczącym adres URL jest trybem domyślnym w Media Services v3. Potencjalnie szybko uzyskujesz wydarzenie na żywo, jednak adres URL pozyskiwania jest znany tylko po uruchomieniu wydarzenia na żywo. Adres URL ulegnie zmianie, jeśli zatrzymasz/uruchomisz wydarzenie na żywo. <br/>Tryb nieznaczący jest przydatny w scenariuszach, kiedy użytkownik końcowy chce przesyłać strumieniowo przy użyciu aplikacji, która chce uzyskać wydarzenie na żywo jak najszybciej, i dynamiczny adres URL pozyskiwania nie jest problemem.
    
    Jeśli aplikacja kliencka nie musi wstępnie wygenerować adresu URL pozyskiwania przed utworzeniem zdarzenia na żywo, po prostu pozwól Media Services na automatyczne generowanie tokenu dostępu dla zdarzenia na żywo.
* Adres URL znaczącym

    Tryb znaczący jest preferowany przez dużych nadawców multimediów, którzy używają sprzętowych koderów emisyjnych i nie chcą ponownie konfigurować swoich koderów, kiedy uruchamiają wydarzenie na żywo. Wolą dysponować predykcyjnym adresem URL pozyskiwania, który nie zmienia się w czasie.
    
    Aby określić ten tryb, należy ustawić `vanityUrl` na `true` wartość Godzina `false`utworzenia (domyślnie). Należy również przekazać własny token dostępu (`LiveEventInput.accessToken`) podczas tworzenia. Należy określić wartość tokenu, aby uniknąć losowego tokenu w adresie URL. Token dostępu musi być prawidłowym ciągiem identyfikatora GUID (z łącznikami lub bez nich). Po ustawieniu trybu nie można go zaktualizować.

    Token dostępu musi być unikatowy w centrum danych. Jeśli aplikacja musi używać adresu URL znaczącym, zaleca się, aby zawsze utworzyć nowe wystąpienie GUID dla tokenu dostępu (zamiast ponownie używać dowolnego istniejącego identyfikatora GUID). 

    Użyj następujących interfejsów API, aby włączyć adres URL znaczącym i ustawić token dostępu na prawidłowy identyfikator GUID (na przykład `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`).  
    
    |Język|Włącz adres URL znaczącym|Określanie tokenu dostępu|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |Interfejs wiersza polecenia|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--token dostępu](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Reguły nazewnictwa adresów URL pozyskiwania na żywo

* *Losowy* ciąg poniżej to 128-bitowa liczba szesnastkowa (która składa się z 32 znaków 0–9 a–f).
* *token dostępu* — prawidłowy ciąg identyfikatora GUID ustawiany podczas korzystania z trybu znaczącym. Na przykład `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *Nazwa strumienia* — wskazuje nazwę strumienia dla określonego połączenia. Wartość nazwy strumienia jest zwykle dodawana przez używany koder na żywo. Można skonfigurować koder na żywo, aby używał dowolnych nazw do opisywania połączenia, na przykład: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>Adres URL inny niż znaczącym

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Adres URL znaczącym

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Adres URL podglądu zdarzenia na żywo

Gdy wydarzenie na żywo zacznie otrzymywać strumieniowe źródło danych, można użyć jego punktu końcowego w wersji zapoznawczej, aby wyświetlić podgląd i sprawdzić, czy otrzymujesz strumień na żywo przed dalszej publikacji. Po sprawdzeniu, czy strumień wersji zapoznawczej jest dobry, możesz użyć zdarzenia na żywo, aby udostępnić strumień na żywo na potrzeby dostarczania przez jeden lub więcej (wstępnie utworzony) punktów końcowych przesyłania strumieniowego. Aby to osiągnąć, należy utworzyć nowe [dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs) na żywo dla zdarzenia na żywo. 

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że wideo przepływa do adresu URL wersji zapoznawczej.

## <a name="live-event-long-running-operations"></a>Operacje długotrwałe dla zdarzenia na żywo

Aby uzyskać szczegółowe informacje, zobacz [długotrwałe operacje](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Dane wyjściowe na żywo

Gdy strumień przepływa do zdarzenia na żywo, możesz rozpocząć zdarzenie przesyłania strumieniowego, tworząc element [zawartości](https://docs.microsoft.com/rest/api/media/assets), [dane wyjściowe](https://docs.microsoft.com/rest/api/media/liveoutputs)i [lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators). Na żywo wyjście będzie archiwizować strumień i udostępnić je osobom oglądającym za pomocą [punktu końcowego przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Aby uzyskać szczegółowe informacje na temat danych wyjściowych na żywo, zobacz [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

[Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
