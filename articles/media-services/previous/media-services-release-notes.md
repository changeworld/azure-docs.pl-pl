---
title: Informacje o wersji usługi Media Services | Dokumentacja firmy Microsoft
description: Informacje o wersji usługi Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako
ms.openlocfilehash: 62f5ae4a9152d3f1b1641cc69136bf472527ba2a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942415"
---
# <a name="azure-media-services-release-notes"></a>Informacje o wersji usługi Azure Media Services
Te informacje o wersji dla usługi Azure Media Services podsumowano zmiany z poprzednich wersji i znane problemy.

> [!NOTE]
> Chcemy usłyszeć klientów, dzięki czemu możemy skupić się na rozwiązywanie problemów, które dotyczy. Aby zgłosić problem lub zadać pytania, przesłać post w [Forum MSDN usług Media Azure].
> 
> 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Obecnie znane problemy
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Ogólne problemy dotyczące usługi Media Services

| Problem | Opis |
| --- | --- |
| Kilka typowych nagłówków HTTP nie są udostępniane w interfejsie API REST. |W przypadku tworzenia aplikacji usługi Media Services przy użyciu interfejsu API REST, stwierdzisz, że niektóre typowe pola nagłówka HTTP (w tym CLIENT-REQUEST-ID REQUEST-ID, a ZWRACANY-CLIENT-REQUEST-ID) nie są obsługiwane. Nagłówki zostanie dodana w przyszłej aktualizacji. |
| Kodowanie procent nie jest dozwolona. |Usługa Media Services używa wartości właściwości IAssetFile.Name podczas kompilowania adresy URL przesyłania strumieniowego zawartości (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Z tego powodu kodowania procent nie jest dozwolona. Wartość właściwości Name nie może mieć następujące [procent kodowanie zarezerwowanych znaków](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * "();: @& = + $, /? [] % #". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku. |
| Metoda ListBlobs, która jest częścią zestawu SDK usługi Magazyn Azure w wersji 3.x kończy się niepowodzeniem. |Usługa Media Services generuje SAS adresy URL na podstawie [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) wersji. Jeśli chcesz użyć zestawu SDK usługi Magazyn do listy obiektów blob, w kontenerze obiektów blob, użyj [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) metodę, która jest częścią zestawu SDK usługi Magazyn wersji 2.x. |
| Media Services mechanizm ograniczania ogranicza użycie zasobów dla aplikacji, które tworzą nadmiernego żądania do usługi. Usługa może zwrócić "Usługa niedostępna" 503 kod stanu HTTP. |Aby uzyskać więcej informacji, zobacz opis kod stanu HTTP 503 w [kody błędów usługi Media Services](media-services-encoding-error-codes.md). |
| Kwerendę jednostek limit 1000 jednostek jest zwracany w tym samym czasie, ponieważ publiczny REST w wersji 2 ogranicza wyniki zapytania do 1000 wyników. |Użyj Skip i podjąć (.NET) / top (REST) zgodnie z opisem w [w tym przykładzie .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) i [w tym przykładzie interfejsu API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Niektórzy klienci mogą napotkać problemem powtarzania tag w manifeście Smooth Streaming. |Aby uzyskać więcej informacji, zobacz [w tej sekcji](media-services-deliver-content-overview.md#known-issues). |
| Obiekty .NET SDK usługi Media Services nie może być serializowany i w związku z tym nie działają z pamięci podręcznej Redis Azure. |Jeśli spróbujesz się do serializacji obiektu AssetCollection zestawu SDK, aby dodać go do pamięci podręcznej Redis Azure, jest zwracany wyjątek. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>Historia wersji interfejsu API REST
Informacje o historii wersji interfejsu API REST usług nośnika, zobacz [dokumentacja interfejsu API REST usługi Azure Media].

## <a name="may-2018"></a>2018 maja 

Uruchamianie kanałów na żywo 12 maja 2018 będzie już obsługi strumienia transportowego RTP/MPEG-2 protokołu pozyskiwania. Przeprowadź migrację z RTP/MPEG-2 RTMP lub pofragmentowany MP4 (Smooth Streaming) pozyskiwania protokołów.

## <a name="october-2017-release"></a>2017 października zlecenia
> [!IMPORTANT] 
> Wycofano obsługę kluczy uwierzytelniania w usłudze kontroli dostępu w usłudze Azure Media Services. Na 22 czerwca 2018 nie mogą uwierzytelniać z usługi Media Services zaplecza za pośrednictwem kodu za pomocą kluczy usługi kontroli dostępu. Zaktualizuj swój kod, aby używać usługi Azure Active Directory (Azure AD) na [Azure uwierzytelniania w usłudze AD](media-services-use-aad-auth-to-access-ams-api.md). Obejrzyj ostrzeżenia o tej zmianie w portalu Azure.

### <a name="updates-for-october-2017"></a>Aktualizacje dla 2017 października
#### <a name="sdks"></a>Zestawy SDK
* Zestaw .NET SDK został zaktualizowany do obsługi uwierzytelniania usługi Azure AD. Obsługa uwierzytelniania w usłudze kontroli dostępu został usunięty z najnowszego zestawu .NET SDK na Nuget.org wspieranie szybsze migracji do usługi Azure AD. 
* Zestaw SDK JAVA został zaktualizowany do obsługi uwierzytelniania usługi Azure AD. Obsługa uwierzytelniania usługi Azure AD został dodany do zestawu Java SDK. Aby uzyskać informacje na temat korzystania z usługi Media Services zestawu Java SDK, zobacz [rozpocząć pracę z klientem programu Java SDK dla usługi Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Kodowanie opartych na plikach
* Koder Premium można teraz używać do zakodowania zawartości wideo wysokiej wydajności H.265 kodowania kodera-dekodera wideo (HEVC). Nie ma to cenową wpływu po wybraniu H.265 za pośrednictwem innych koderów-dekoderów, takich jak H.264. Informacje o licencji patentowe HEVC znajdują się w temacie [warunki dotyczące usług Online](https://azure.microsoft.com/support/legal/).
* Dla źródła wideo, które są zakodowane za pomocą H.265 (HEVC) kodera-dekodera wideo, takich jak wideo przechwycone przy użyciu iOS11 lub GoPro bohater 6 teraz służy koder Premium i standardowa — koder do kodowania tych klipów wideo. Aby uzyskać informacje o licencjach patentowe, zobacz [warunki dotyczące usług Online](https://azure.microsoft.com/support/legal/).
* Dla zawartości, która zawiera wiele ścieżek audio języka wartościami języka muszą być prawidłowo oznaczone zgodnie z odpowiedniego specyfikacją formatu pliku (na przykład ISO MP4). Następnie można użyć standardowego kodera do kodowania zawartości do przesyłania strumieniowego. Wynikowe Lokalizator przesyłania strumieniowego wymieniono dostępne języki audio.
* Standardowa — koder obsługuje teraz dwa nowe ustawienia systemu tylko dźwięk, "AAC Audio" i "AAC dobra jakość dźwięku". Zarówno tworzy stereo Zaawansowane audio odpowiednio kodowania danych wyjściowych (AAC), która znajduje się w szybkości transmisji bitów 128 Kb/s i 192 kb/s.
* Koder Premium obsługuje teraz formatów plików QuickTime/MOV jako dane wejściowe. Kodera-dekodera wideo musi mieć jedną z [Apple ProRes typy wymienione w tym artykule GitHub](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Dźwięk musi być albo AAC lub impulsowe modulacji kodu (PCM). Koder Premium nie obsługują na przykład wideo DVC/DVCPro otoczona QuickTime/MOV pliki jako dane wejściowe. Standardowa — koder obsługuje następujące kodery-dekodery wideo.
* W kodery wprowadzono następujące poprawki:

    * Obecnie można przesłać zadania przy użyciu zasobów wejściowych. Po zakończeniu tych zadań, można zmodyfikować elementu zawartości (na przykład Dodaj, usuń lub zmień nazwy plików w ramach elementu zawartości) oraz wysyłanie dodatkowych zadań.
    * Zwiększona jakość miniatur JPEG utworzonego przez standardowego kodera.
    * Standardowa — koder obsługuje wejściowych metadanych i generowanie miniatur w bardzo krótki czas trwania wideo.
    * Ulepszeń dekoder H.264 używane w standardowego kodera wyeliminować niektórych rzadkich artefaktów. 

#### <a name="media-analytics"></a>Media Analytics
Ogólnej dostępności Redactor multimediów Azure: ten procesor multimediów wykonuje anonymization za rozmycia kroje wybrane osoby i nadaje się do użytku w publicznych scenariusze bezpieczeństwa i nośnika wiadomości. 

Omówienie tego nowego procesora, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/azure-media-redactor/). Aby uzyskać informacje na temat dokumentacji i ustawień, zobacz [redagowanie krojów z analizy multimediów Azure](media-services-face-redaction.md).



## <a name="june-2017-release"></a>2017 czerwca zlecenia

Usługa Media Services obsługuje teraz [Azure uwierzytelniania w usłudze AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje model uwierzytelniania w usłudze kontroli dostępu. Uwierzytelnianie usługi kontroli dostępu zostaną wycofane na 1 czerwca 2018. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="march-2017-release"></a>2017 marca zlecenia

Możesz teraz użyć standardowego kodera do [automatycznego generowania drabinę szybkości transmisji bitów](media-services-autogen-bitrate-ladder-with-mes.md) przez określenie "Adaptacyjne przesyłanie strumieniowe" ustawienia wstępnego ciągu, po utworzeniu zadania kodowania. Kodowanie wideo do przesyłania strumieniowego z usługi Media Services, należy użyć ustawienia wstępnego "Adaptacyjne przesyłanie strumieniowe". Aby dostosować ustawienia wstępnego dla konkretnego scenariusza kodowania, można przejść do [predefiniowane](media-services-mes-presets-overview.md).

Możesz teraz użyć Media Encoder Standard lub Media Encoder Premium przepływu pracy [Tworzenie zadania kodowania, które generuje fragmentów fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Lutego 2017 zlecenia

Uruchamianie 1 kwietnia 2017 r dowolnego rekordu zadania konta starsze niż 90 dni jest automatycznie usuwana, wraz z jego rekordów skojarzonego zadania. Usuwanie występuje, nawet jeśli jest to całkowita liczba rekordów poniżej maksymalny limit przydziału. Aby zarchiwizować informacje zadania lub zadanie, można użyć kodu opisane w [Zarządzanie zasobów i powiązanych jednostek z zestawu .NET SDK usługi Media](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Zwolnij stycznia 2017 r

W usłudze Media Services punktu końcowego przesyłania strumieniowego reprezentuje przesyłania strumieniowego usługa, która może dostarczać zawartość bezpośrednio do aplikacji klienckiej player lub sieci dostarczania zawartości (CDN) w celu dalszej dystrybucji. Usługa Media Services udostępnia również bezproblemową integrację Azure Content Delivery Network. Strumienia wychodzącego z usługi StreamingEndpoint może być strumień na żywo, wideo na żądanie lub pobierania progresywnego zawartości na koncie usługi Media Services. Każde konto usługi Media Services zawiera domyślnego punktu końcowego przesyłania strumieniowego. Można utworzyć dodatkowe punkty końcowe przesyłania strumieniowego na koncie. 

Istnieją dwie wersje punkty końcowe, przesyłania strumieniowego 1.0 i 2.0. Uruchamianie 10 stycznia 2017 wszelkie nowo utworzonego konta usługi Media Services zawierają domyślne w wersji 2.0 punktu końcowego przesyłania strumieniowego. Dodatkowe punktów końcowych przesyłania strumieniowego dodanych do tego konta są także w wersji 2.0. Ta zmiana nie ma wpływu na istniejących kont. Istniejące punkty końcowe przesyłania strumieniowego są w wersji 1.0 i może zostać uaktualnione do wersji 2.0. Brak zachowania, rozliczeń i funkcji zmian z tą zmianą. Aby uzyskać więcej informacji, zobacz [Streaming endpoints overview (Omówienie punktów końcowych przesyłania strumieniowego)](media-services-streaming-endpoints-overview.md).

Począwszy od wersji 2.15 Media Services dodano następujące właściwości jednostki przesyłania strumieniowego punktu końcowego:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Aby uzyskać więcej informacji na temat tych właściwości, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Zwolnij grudnia 2016

 Teraz można Media Services dostęp do danych telemetrycznych/metryki dla swoich usług. Można użyć bieżącej wersji usługi Media Services do zbierania danych telemetrycznych dla kanału na żywo punktu końcowego przesyłania strumieniowego i archiwizacji jednostek. Aby uzyskać więcej informacji, zobacz [telemetrii usługi Media Services](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Wersja z lipca 2016 r.
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aktualizacje do pliku manifestu (*. ISM) generowany przez kodowania zadań
Podczas przesyłania zadania kodowania Media Encoder Standard lub Premium koder nośników, kodowania zadań generuje [przesyłania strumieniowego pliku manifestu](media-services-deliver-content-overview.md) (* .ism) w zawartości wyjściowej. Najnowsza wersja usługi została zaktualizowana Składnia tego pliku manifestu przesyłania strumieniowego.

> [!NOTE]
> Składnia przesyłania strumieniowego pliku manifestu (.ism) jest zarezerwowany do użytku wewnętrznego. Jest może ulec zmianie w przyszłych wersjach. Nie wolno modyfikować ani modyfikowania zawartości tego pliku.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Nowy klient manifestu (*. Plik ISMC) jest generowana w elementu zawartości wyjściowej, gdy jeden lub więcej plików MP4 wyniki kodowania zadań
Po zakończeniu zadania kodowania, które generuje jeden lub więcej plików MP4, począwszy od najnowszej wersji usługi elementu zawartości wyjściowej zawiera także przesyłania strumieniowego pliku manifestu (*.ismc) klienta. Plik .ismc pomaga zwiększyć wydajność przesyłania strumieniowego dynamicznych. 

> [!NOTE]
> Składnia pliku manifestu (.ismc) klienta jest zarezerwowana do użytku wewnętrznego. Jest może ulec zmianie w przyszłych wersjach. Nie wolno modyfikować ani modyfikowania zawartości tego pliku.
> 
> 

Aby uzyskać więcej informacji, zobacz [ten blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Znane problemy
Niektórzy klienci mogą napotkać problemem powtarzania tag w manifeście Smooth Streaming. Aby uzyskać więcej informacji, zobacz [w tej sekcji](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>Zwolnij kwietnia 2016
### <a name="media-analytics"></a>Media Analytics
 Usługi Media Services wprowadzono analizy multimediów, zaawansowane analizy wideo. Aby uzyskać więcej informacji, zobacz [omówienie analizy usługi multimediów](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (wersja zapoznawcza)
Po wykonaniu użyć usługi Media Services do dynamicznego szyfrowania z HTTP Live Streaming (HLS) zawartości z FairPlay firmy Apple. Można również użyć usługi dostarczania licencji Media Services dostarczać licencje FairPlay do klientów. Aby uzyskać więcej informacji zobacz "Użytkowania usługi Azure Media Services przesyłać strumieniowo zawartość HLS chronione przy użyciu firmy Apple FairPlay".

## <a id="feb_changes16"></a>Zwolnij lutego 2016
Najnowszą wersję zestawu SDK usługi multimediów dla platformy .NET (3.5.3) zawiera poprawkę powiązane Google Widevine. Nie było możliwe ponowne AssetDeliveryPolicy dla wielu zasobów szyfrowane przy użyciu metody Widevine. W ramach tej poprawki błędów, następującą właściwość została dodana do zestawu SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Zwolnij styczeń 2016
Zastrzeżone jednostki kodowania zmieniono aby wyeliminować problemy z nazwami kodera.

Podstawowa, standardowa i Premium kodowania jednostki zarezerwowanego zostały zmieniona na S1, S2, i odpowiednio jednostki, zarezerwowane S3. Klienci, którzy korzystają obecnie podstawowe zastrzeżone jednostki kodowania Zobacz S1 jako etykieta w portalu Azure (i w BOM). Klienci, którzy korzystają standardowa i Premium Zobacz etykiety S2 i S3, odpowiednio. 

## <a id="dec_changes_15"></a>Zwolnij grudnia 2015 r.

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder amortyzacja anonsu

 Media Encoder będzie przestarzałe, począwszy od wersji Media Encoder Standard około 12 miesięcy.

### <a name="azure-sdk-for-php"></a>Zestaw Azure SDK dla języka PHP
Nowa wersja opublikowane zespołu zestawu Azure SDK [zestaw Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) pakiet, który zawiera aktualizacje i nowe funkcje dla usługi Media Services. W szczególności SDK usługi Media Services dla programów PHP i obsługuje teraz najnowsze [zawartości ochrony](media-services-content-protection-overview.md) funkcji. Te funkcje są dynamicznego szyfrowania AES i DRM (PlayReady i Widevine) z włączonymi i wyłączonymi ograniczenia tokenu. Obsługuje także skalowanie [jednostki kodowania](media-services-dotnet-encoding-units.md).

Aby uzyskać więcej informacji, zobacz:

* [SDK usługi Media Services dla programu PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blogu.
* Następujące [przykłady kodu](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) ułatwia szybkie rozpoczęcie pracy:
  * **vodworkflow_aes.php**: plik PHP ten przedstawia sposób użycia dynamicznego szyfrowania AES-128 i usługi dostarczania klucza. Jest on oparty na przykład .NET wyjaśniono w [dynamicznego szyfrowania AES-128 użycia i usługi dostarczania klucza](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: plik PHP ten przedstawia sposób użycia szyfrowania dynamicznego PlayReady i usługi dostarczania licencji. Jest on oparty na przykład .NET wyjaśniono w [Użyj PlayReady i Widevine dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: PHP ten plik zawiera sposób skalowania jednostek zarezerwowanego kodowania.

## <a id="nov_changes_15"></a>Listopad 2015 r.
 Usługa Media Services udostępnia teraz usługi dostarczania licencji Widevine w chmurze. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Zobacz też [w tym samouczku](media-services-protect-with-playready-widevine.md) i [repozytorium GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Usług dostarczania licencji Widevine udostępniane przez usługi Media Services są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Zwolnij października 2015
Usługa Media Services jest teraz na żywo w następujących centrach danych: Brazylia Południowa, Indie Zachodnie, Indie Południowe i Indie środkowe. Można teraz używać portalu Azure, aby [tworzenia kont usługi multimediów](media-services-portal-create-account.md) i wykonywania różnych zadań opisanych w [strony sieci Web w dokumentacji usługi Media Services](https://azure.microsoft.com/documentation/services/media-services/). Kodowanie na żywo nie jest włączone w tych centrach danych. Ponadto nie wszystkie typy zastrzeżone jednostki kodowania są dostępne w tych centrach danych.

* Brazylia Południowa: Tylko standardowa i podstawowa zastrzeżone jednostki kodowania są dostępne.
* Indie Zachodnie, Indie Południowe i Indie środkowe: tylko podstawowe zastrzeżone jednostki kodowania są dostępne.

## <a id="september_changes_15"></a>Września 2015 r.
Usługa Media Services oferuje możliwość ochrony zarówno wideo na żądanie i strumienie na żywo przy użyciu technologii DRM moduły Widevine. Ułatwiają licencje Widevine, można użyć następujących partnerów usługi dostarczania:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Aby skorzystać z usługi Widevine, można skonfigurować obiekt AssetDeliveryConfiguration przy użyciu zestawu [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (począwszy od wersji 3.5.1) lub interfejsu API REST. 
* Usługa Media Services dodano obsługę wideo ProRes firmy Apple. Możesz teraz przekazać pliki wideo źródła QuickTime korzystających z ProRes firmy Apple lub inne kodery-dekodery. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Teraz można Media Encoder Standard czy wyodrębniania archiwum subclipping i na żywo. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Wprowadzono następujące aktualizacje filtrowania: 
  
  * Teraz można w formacie Apple HLS tylko dźwięk filtru. Tę aktualizację można użyć do usunięcia Śledź tylko dźwięk, określając (tylko dźwięk = false) w adresie URL.
  * Po zdefiniowaniu filtrów trwałych teraz można połączyć wiele filtrów (maksymalnie trzy) w jeden adres URL.
    
    Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Usługa Media Services obsługuje teraz-ramki w HLS w wersji 4. Obsługa ramki optymalizuje operacje szybkie przewijanie do przodu i do tyłu. Domyślnie wszystkie dane wyjściowe w wersji 4 HLS zawierają listy odtwarzania ramki (EXT-X-I-FRAME-STREAM-INF).
Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Zwolnij sierpień 2015
* SDK usługi Media Services Java wersji 0.8.0 zlecenia i nowe próbki są teraz dostępne. Aby uzyskać więcej informacji, zobacz:
  
  * [Ten wpis w blogu](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Repozytorium przykłady Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Azure Media Player został zaktualizowany o obsługę wielu audio strumienia. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Wersja z lipca 2015 r.
* Ogłoszono ogólnej dostępności Media Encoder Standard. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard używa ustawienia, zgodnie z opisem w [w tej sekcji](http://go.microsoft.com/fwlink/?LinkId=618336). Jeśli używasz ustawienia domyślne dla 4K koduje, pobrać Premium zastrzeżone typu jednostki. Aby uzyskać więcej informacji, zobacz [kodowanie skali](media-services-scale-media-processing-overview.md).
* Podpisy w czasie rzeczywistym na żywo były używane z usługi Media Services i odtwarzacza multimedialnego. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje .NET SDK usługi Media Services
SDK .NET usługi Media Services jest teraz wersji 3.4.0.0. Wprowadzono następujące aktualizacje: 

* Obsługa został wdrożony w archiwum na żywo. Nie można pobrać zawartości, który zawiera archiwum na żywo.
* Obsługa została zaimplementowana dla filtrów dynamicznych.
* Funkcje został wdrożony, dzięki czemu użytkownicy mogą zachować kontenera magazynu, gdy one usunąć zasób.
* Powiązane, aby ponowić próbę zasady w kanałach wprowadzono poprawki błędów.
* Włączono Media Encoder Premium w przepływie pracy.

## <a id="june_changes_15"></a>Czerwca 2015 r.
### <a name="media-services-net-sdk-updates"></a>Aktualizacje .NET SDK usługi Media Services
SDK .NET usługi Media Services jest teraz wersji 3.3.0.0. Wprowadzono następujące aktualizacje: 

* Dodano obsługę specyfikacji odnajdywania OpenId Connect.
* Dodano obsługę obsługi przerzucania kluczy po stronie dostawcy tożsamości.

Jeśli używasz dostawcy tożsamości, który ujawnia dokument OpenID Connect (jako usługi Azure AD, Google i Salesforce zrobić), można zmodyfikować usługi Media Services, aby uzyskać klucze podpisywania do sprawdzania poprawności tokenów sieci Web JSON (Jwt) od specyfikacji odnajdywania OpenID Connect. 

Aby uzyskać więcej informacji, zobacz [kluczy sieci web JSON użycia z specyfikacji odnajdywania OpenID Connect do pracy z uwierzytelnianiem JWT w usłudze Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Maja 2015 r.
Anonsowane zostały następujące nowe funkcje:

* [Podgląd kodowanie na żywo w usłudze Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamiczne manifestu](media-services-dynamic-manifest-overview.md)
* [Podgląd procesor multimediów Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>Wersji z kwietnia 2015 r.
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
* [Odtwarzacz](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) ogłoszono.
* Począwszy od 2.10 REST usługi Media kanałów, które są skonfigurowane do pozyskiwania RTMP Real-Time Messaging Protocol () są tworzone z podstawowym i pomocniczym adresy URL pozyskiwania. Aby uzyskać więcej informacji, zobacz [konfiguracje pozyskiwania kanału](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media indeksatora została zaktualizowana.
* Dodano obsługę języka hiszpańskiego.
* Dodano nową konfigurację dla formatu XML.

Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje .NET SDK usługi Media Services
SDK .NET usługi Media Services jest teraz wersji 3.2.0.0. Wprowadzono następujące aktualizacje:

* Fundamentalne zmiany: TokenRestrictionTemplate.Issuer i TokenRestrictionTemplate.Audience zostały zmienione na być typu string.
* Aktualizacje zostały dokonane związanych z Tworzenie zasad niestandardowych ponów próbę.
* Wprowadzono poprawki dotyczące przekazywania i pobierania plików.
* Klasa MediaServicesCredentials akceptuje teraz punkty końcowe kontroli dostępu do podstawowego i pomocniczego do uwierzytelniania.

## <a id="march_changes_15"></a>Zwolnij marca 2015 roku
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
* Usługa Media Services udostępnia teraz integracji Content Delivery Network. Do obsługi integracji, właściwość CdnEnabled została dodana do StreamingEndpoint. CdnEnabled można używać z interfejsów API REST, począwszy od wersji 2.9. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled można przy użyciu zestawu .NET SDK, począwszy od wersji 3.1.0.2. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Ogłoszono przepływu pracy Media Encoder Premium. Aby uzyskać więcej informacji, zobacz [wprowadzenie Premium kodowania w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Lutego 2015 r.
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
Interfejs API REST usług Media jest teraz wersji 2.9. Począwszy od tej wersji, można włączyć integracji Content Delivery Network z punkty końcowe przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Stycznia 2015 r.
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
Ogłoszono ogólnej dostępności ochrony zawartości w przypadku szyfrowania dynamicznego. Aby uzyskać więcej informacji, zobacz [Media Services zwiększa bezpieczeństwo przesyłania strumieniowego z ogólnej dostępności technologii DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje .NET SDK usługi Media Services
SDK .NET usługi Media Services jest teraz wersji 3.1.0.1.

Ta wersja oznaczony jako domyślny konstruktor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate jako przestarzałe. Nowy konstruktor ma TokenType jako argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Wersja z grudnia 2014
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
* Niektóre aktualizacje i nowe funkcje zostały dodane do indeksatora nośnika. Aby uzyskać więcej informacji, zobacz [informacje o wersji usługi Azure Media indeksatora wersji 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Nowy interfejs API REST został dodany, której można zaktualizować zastrzeżone jednostki kodowania. Aby uzyskać więcej informacji, zobacz [EncodingReservedUnitType z POZOSTAŁĄ](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Dodano obsługę mechanizmu CORS usługi dostarczania klucza.
* Wprowadzono ulepszenia wydajności zapytań opcji zasad autoryzacji.
* W centrum danych Chin [klucza dostawy URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) jest teraz na klienta (podobnie jak w innych centrach danych).
* Czas trwania docelowych automatycznej HLS został dodany. W trakcie transmisji strumieniowych na żywo, HLS zawsze jest dostarczana dynamicznie. Domyślnie usługi Media Services automatycznie oblicza stosunek pakowania segment HLS (FragmentsPerSegment) na podstawie interwału klatki kluczowej (KeyFrameInterval). Ta metoda jest również nazywany grupy obrazów (GOP) odebrane z kodera na żywo. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe na żywo pracy z programem Media Services](http://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje .NET SDK usługi Media Services
[.NET SDK usługi Media Services](http://www.nuget.org/packages/windowsazure.mediaservices/) jest teraz wersji 3.1.0.0. Wprowadzono następujące aktualizacje:

* Zależności zestawu .NET SDK został uaktualniony do programu .NET Framework 4.5.
* Dodano nowy interfejs API, który służy do aktualizacji zastrzeżone jednostki kodowania. Aby uzyskać więcej informacji, zobacz [aktualizacji zastrzeżone typu jednostki i zwiększyć zastrzeżone jednostki kodowania przy użyciu programu .NET](media-services-dotnet-encoding-units.md).
* Dodano obsługę JWT tokenu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania tokenu JWT usługi Media Services i szyfrowania dynamicznego](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Względne przesunięć BeginDate i ExpirationDate w szablonie licencji PlayReady zostały dodane.

## <a id="november_changes_14"></a>Listopadzie 2014 roku
* Możesz teraz używać usługi Media Services pozyskiwanie zawartości na żywo Smooth Streaming (fMP4) za pośrednictwem połączenia SSL. Pozyskiwanie za pośrednictwem protokołu SSL, upewnij się, że aktualizacja adresu URL pozyskiwania HTTPS. Obecnie usługa Media Services nie obsługuje protokołu SSL z domen niestandardowych. Aby uzyskać więcej informacji na temat transmisja strumieniowa na żywo, zobacz [pracy Azure Media Services transmisji strumieniowej na żywo](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* Nie można obecnie pozyskiwania RTMP strumień na żywo, za pośrednictwem połączenia SSL.
* Tylko wtedy, gdy punkt końcowy przesyłania strumieniowego, z którego dostarczyć zawartość została utworzona po 10 września 2014 można przesłać strumieniowo za pośrednictwem protokołu SSL. Jeśli Twoje adresy URL przesyłania strumieniowego są oparte na punktów końcowych przesyłania strumieniowego utworzone po 10 września 2014 adres URL zawiera "streaming.mediaservices.windows.net" (nowy format). Adresy URL przesyłania strumieniowego zawierające "origin.mediaservices.windows.net" (stary format) nie obsługuje protokołu SSL. Jeśli adres URL jest w starym formacie i ma być przesyłana strumieniowo za pośrednictwem protokołu SSL, [utworzyć nowy punkt końcowy przesyłania strumieniowego](media-services-portal-manage-streaming-endpoints.md). Przesyłać strumieniowo zawartość przy użyciu protokołu SSL, użyj adresów URL oparte na nowy punkt końcowy przesyłania strumieniowego.

## <a id="october_changes_14"></a>Wersji z października 2014 r.
### <a id="new_encoder_release"></a>Wersja Media Encoder usług
 Nową wersję nośnika usługi Azure Media Encoder ogłoszono. Z najnowszych Media Encoder są pobierane tylko dla danych wyjściowych GB. W przeciwnym razie nowy koder to funkcja zgodny z poprzednim kodera. Aby uzyskać więcej informacji, zobacz [szczegóły cennika usługi Media Services].

### <a id="oct_sdk"></a>Zestaw .NET SDK usługi multimediów
SDK usługi Media Services dla platformy .NET, rozszerzenia jest teraz wersji 2.0.0.3.

Zestaw SDK usługi multimediów dla platformy .NET jest teraz wersji 3.0.0.8. Wprowadzono następujące aktualizacje:

* Refaktoryzacja został wdrożony w klasach zasad ponawiania.
* Ciąg agenta użytkownika został dodany do nagłówków żądań HTTP.
* Dodano kroku kompilacji przywracania NuGet.
* Scenariusz testy zostały usunięte do użycia x509 certyfikatu z repozytorium.
* Ustawienia weryfikacji zostały dodane do kanału i przesyłania strumieniowego zakończenia aktualizacji.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nowe repozytorium GitHub do hosta usługi Media Services próbek
Przykłady znajdują się w [repozytorium GitHub przykłady usługi Media Services](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Września 2014 o wersji
Metadane Media Services REST jest teraz wersji 2.7. Aby uzyskać więcej informacji dotyczących najnowszych aktualizacji REST, zobacz [dokumentacji interfejsu API REST usługi Media](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Zestaw SDK usługi multimediów dla platformy .NET jest teraz wersji 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Fundamentalne zmiany
* Źródła została zmieniona na [StreamingEndpoint].
* Zmiana została wprowadzona w zachowanie domyślne, jeśli używasz portalu Azure do kodowania, a następnie opublikować pliki MP4.

### <a id="sept_14_GA_changes"></a>Nowych funkcji/scenariuszy, które są częścią wersji ogólnodostępnej
* Procesor multimediów nośnika indeksatora została wprowadzona. Aby uzyskać więcej informacji, zobacz [indeksu plików multimedialnych na pliki z indeksatora nośnika](http://msdn.microsoft.com/library/azure/dn783455.aspx).
* Można użyć [StreamingEndpoint] jednostki w celu dodania nazw domen niestandardowych (hosta).
  
    Aby użyć niestandardowej nazwy domeny jako nazwa punktu końcowego przesyłania strumieniowego usługi Media Services, dodawać do punktu końcowego przesyłania strumieniowego nazwy hosta niestandardowego. Użyj interfejsów API REST usługi Media lub zestawu .NET SDK, aby dodać nazwy hosta niestandardowego.
  
    Następujące kwestie:
  
  * Musi mieć własność nazwy domeny niestandardowej.
  * Własność nazwy domeny musi zostać zweryfikowany przez usługę Media Services. Można zweryfikować domeny, należy utworzyć rekord CName mapujący domeny nadrzędnej MediaServicesAccountId, aby sprawdzić DNS w mediaservices strefy dns.
  * Należy utworzyć inny CName, mapujący nazwy hosta niestandardowego (na przykład sports.contoso.com) do nazwy hosta Media Services StreamingEndpoint (na przykład amstest.streaming.mediaservices.windows.net).

    Aby uzyskać więcej informacji, zobacz właściwość CustomHostNames w [StreamingEndpoint](http://msdn.microsoft.com/library/azure/dn783468.aspx) artykułu.

### <a id="sept_14_preview_changes"></a>Nowych funkcji/scenariuszy, które są częścią w publicznej wersji zapoznawczej
* Podgląd transmisji strumieniowej na żywo. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe na żywo pracy z programem Media Services](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* Usługa klucza dostawy. Aby uzyskać więcej informacji, zobacz [dynamicznego szyfrowania AES-128 użycia i usługi dostarczania klucza](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* Dynamicznego szyfrowania AES. Aby uzyskać więcej informacji, zobacz [dynamicznego szyfrowania AES-128 użycia i usługi dostarczania klucza](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* Usługi dostarczania licencji PlayReady. 
* PlayReady szyfrowania dynamicznego. 
* Szablon licencji PlayReady usługi multimediów. Aby uzyskać więcej informacji, zobacz [omówienie szablon licencji PlayReady usług Media].
* Strumień zasobów szyfrowane magazynu. Aby uzyskać więcej informacji, zobacz [strumieniowo zaszyfrowana magazynu zawartości](http://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>W wersji 2014 r. sierpnia
Podczas kodowania zasób zawartości wyjściowej jest generowany po zakończeniu zadania kodowania. Do tej wersji Media Encoder usług wyprodukowanych metadanych o zasobach danych wyjściowych. Począwszy od tej wersji, koder również tworzy metadane dotyczące zasobów wejściowych. Aby uzyskać więcej informacji, zobacz [wejściowych metadanych] i [metadanych dane wyjściowe].

## <a id="july_changes_14"></a>Lipiec 2014 roku
Azure Media Services Pakowarka i modułu szyfrującego wprowadzono następujące poprawki:

* Gdy zawartości na żywo archiwum są przesyłane do HLS, tylko audio odtwarza: ten problem został rozwiązany i teraz można odtwarzać audio i wideo.
* Gdy zasób jest dostarczana do HLS i AES 128-bitowego szyfrowania koperty, spakowanych strumieni nie odtwarzanie na urządzeniach z systemem Android: ten problem został rozwiązany i spakowane strumienia odtwarza na urządzeniach z systemem Android, które obsługują HLS.

## <a id="may_changes_14"></a>Zwolnij maja 2014
### <a id="may_14_changes"></a>Aktualizuje ogólne usługi Media Services
Można teraz używać [dynamicznego tworzenia pakietów] do strumienia HLS w wersji 3. Strumień HLS w wersji 3, Dodaj następujący format do ścieżki lokalizatora pochodzenia: * .ism/manifest(format=m3u8-aapl-v3). Aby uzyskać więcej informacji, zobacz [ten blog](http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/).

Dynamiczne tworzenie pakietów teraz obsługuje także dostarczanie HLS (w wersji 3 i w wersji 4) szyfrowany za pomocą PlayReady oparte na Smooth Streaming statycznie zaszyfrowane za pomocą PlayReady. Aby uzyskać informacje na temat szyfrowania, Smooth Streaming z PlayReady, zobacz [chronić Smooth Streaming z PlayReady](http://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Aktualizacje .NET SDK usługi Media Services
SDK .NET usługi Media Services jest teraz wersji 3.0.0.5. Wprowadzono następujące aktualizacje:

* Podczas przekazywania i pobierania zasoby nośnika szybkość i odporności są lepsze.
* Ulepszenia wprowadzono w ponownych prób logiki i przejściowy obsługi wyjątków: 
  
  * Błąd przejściowy logikę wykrywania i ponów próbę zostały ulepszona pod kątem wyjątków, które są spowodowane zapytania, Zapisz zmiany oraz przekazywanie lub pobieranie plików. 
  * Po przejściu wyjątki sieci web (na przykład podczas żądania tokenu usługi kontroli dostępu) błędy krytyczne niepowodzenie szybciej teraz.

Aby uzyskać więcej informacji, zobacz [ponów logikę w zestawie SDK usługi multimediów dla platformy .NET].

## <a id="april_changes_14"></a>Wersja kodera kwietnia 2014 r.
### <a name="april_14_enocer_changes"></a>Media Encoder usług aktualizacji
* Dodano obsługę pozyskiwanie plików AVI, które utworzono za pomocą edytora rożne trawy doliny EDIUS. W tym procesie wideo jest lekkim skompresowane za pomocą kodera-dekodera doliny trawy HQ/HQX. Aby uzyskać więcej informacji, zobacz [doliny trawy ogłasza 7 EDIUS strumieniowanie za pośrednictwem chmury].
*  Aby określić konwencję nazewnictwa pliki tworzone przez Media Encoder usług dodano obsługę. Aby uzyskać więcej informacji, zobacz [kontroli Media Encoder usług output nazwy plików](http://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Dodano obsługę nakładki wideo lub audio. Aby uzyskać więcej informacji, zobacz [Tworzenie nakładek](http://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Dodano obsługę do łączenie wielu segmentach wideo. Aby uzyskać więcej informacji, zobacz [połączyć segmenty wideo](http://msdn.microsoft.com/library/azure/dn640504.aspx).
* Błąd został rozwiązany, która została powiązana z transkodowanie MP4s, gdzie zostały zakodowane dźwięku z MPEG-1 Audio warstwy 3 (znanej także jako MP3).

## <a id="jan_feb_changes_14"></a>Zwalnia stycznia/lutego 2014 r.
### <a name="jan_fab_14_donnet_changes"></a>Zestaw .NET SDK 3.0.0.1, 3.0.0.2 i 3.0.0.3 usługi multimediów
Zmiany w 3.0.0.1 i 3.0.0.2 obejmują:

* Problemy związane z użycia zapytań LINQ z deklaracjami OrderBy zostały usunięte.
* Testowanie rozwiązań w [GitHub] zostały podzielone na podstawie jednostki testów i oparta na scenariuszu.

Aby uzyskać więcej informacji o zmianach, zobacz [zwalnia .NET SDK usługi Media Services 3.0.0.1 i 3.0.0.2](http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/).

W wersji 3.0.0.3 zostały wprowadzone następujące zmiany:

* Zależności usługi Magazyn Azure zostały uaktualnione do wersji 3.0.3.0.
* Problem zgodności z poprzednimi wersjami został rozwiązany dla 3.0. *.* wersje.

## <a id="december_changes_13"></a>Zwolnij grudniu 2013
### <a name="dec_13_donnet_changes"></a>Zestaw .NET SDK 3.0.0.0 usługi multimediów
> [!NOTE]
> Wersje 3.0.x.x nie są zgodne z wersjami 2.4.x.x.
> 
> 

Najnowszą wersję zestawu SDK usługi Media Services jest teraz 3.0.0.0. Możesz pobrać najnowszy pakiet NuGet lub pobrać z usługi bits [GitHub].

Począwszy od zestawu SDK usługi Media wersji 3.0.0.0, można użyć ponownie [usługi kontroli dostępu usługi Azure AD](http://msdn.microsoft.com/library/hh147631.aspx) tokenów. Aby uzyskać więcej informacji, zobacz sekcję "Usługa kontroli dostępu do ponownego użycia tokenami" w [nawiązywanie połączenia z usługi Media Services przy użyciu zestawu SDK usługi multimediów dla platformy .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Rozszerzenia .NET SDK usługi Media Services 2.0.0.0
 Rozszerzenia .NET SDK usługi Media Services to zestaw metod rozszerzeń i funkcji pomocniczych, które uprościć kod i ułatwiają tworzenie z usługi Media Services. Możesz uzyskać najnowsze usługi bits z [rozszerzenia .NET SDK usługi Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Zwolnij listopad 2013
### <a name="nov_13_donnet_changes"></a>Zmiany .NET SDK usługi Media Services
Począwszy od tej wersji zestawu SDK usługi Media .NET dojść do wystąpienia błędu przejściowego błędów, które mogą wystąpić, gdy wywołań do warstwy interfejsu API REST usług Media.

## <a id="august_changes_13"></a>Zwolnij sierpnia 2013
### <a name="aug_13_powershell_changes"></a>Polecenia cmdlet programu PowerShell usługi Media wchodzi w skład narzędzi zestawu Azure SDK
Następujące polecenia cmdlet programu PowerShell usługi nośnika znajdują się teraz w [Azure SDK tools](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Na przykład: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Na przykład: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Na przykład: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Na przykład: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Zwolnij czerwca 2013
### <a name="june_13_general_changes"></a>Zmiany usługi Media Services
Następujące zmiany, które są wymienione w tej sekcji są aktualizacje zawarte w wersjach czerwca 2013 Media Services:

* Możliwość łączenia wielu kont magazynu do konta usługi Media. 
    * StorageAccount
    * Asset.StorageAccountName i Asset.StorageAccount
* Możliwość Job.Priority aktualizacji. 
* Powiadomienia dotyczące jednostki i właściwości: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Zadanie
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Zmiany .NET SDK usługi Media Services
Następujące zmiany są uwzględniane w czerwca 2013 zwalnia SDK usługi Media Services. Najnowszy zestaw SDK usługi multimediów jest dostępna w witrynie GitHub.

* Począwszy od wersji 2.3.0.0, obsługuje SDK usługi Media Services łączenia wielu magazynu kont do konta usługi Media Services. Następujące interfejsy API obsługuje tę funkcję:
  
    * Typ IStorageAccount
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts property
    * Właściwość konto magazynu
    * Właściwość StorageAccountName
  
    Aby uzyskać więcej informacji, zobacz [zasoby Zarządzanie Media Services przez wiele kont magazynu](http://msdn.microsoft.com/library/azure/dn271889.aspx).
* Powiadomienie związane z interfejsów API. Począwszy od wersji 2.2.0.0, można słuchać powiadomienia magazynu kolejek Azure. Aby uzyskać więcej informacji, zobacz [powiadomienia zadania obsługi usługi Media Services](http://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions property
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint type
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription type
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection type
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType type
* Zależność od klienta magazynu SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Zależność od OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>Zwolnij grudnia 2012
### <a name="dec_12_dotnet_changes"></a>Zmiany .NET SDK usługi Media Services
* IntelliSense: Brak dokumentacji IntelliSense została dodana dla wielu typów.
* Microsoft.Practices.TransientFaultHandling.Core: Wystąpił problem został rozwiązany gdy zestaw SDK nadal miały zależności do starsza wersja tego zestawu. Zestaw SDK teraz odwołuje się do wersji 5.1.1209.1 tego zestawu.

Rozwiązuje problemy znalezione w listopad 2012 SDK:

* IAsset.Locators.Count: Ta liczba jest teraz prawidłowo raportowane na nowe interfejsy IAsset po usunięciu wszystkich lokalizatorów.
* IAssetFile.ContentFileSize: Ta wartość jest teraz prawidłowo ustawione po przesłaniu przez IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Ta właściwość teraz można ustawić podczas tworzenia pliku zasobów. On został wcześniej tylko do odczytu.
* IAssetFile.Upload(filepath): Wystąpił problem został rozwiązany gdzie ta metoda synchroniczna przekazywania została zgłaszanie następujący błąd kilka plików zostały przekazane do elementu zawartości. Wystąpił błąd "serwer nie można uwierzytelnić żądania. Upewnij się, że wartość nagłówka autoryzacji jest sformatowany poprawnie tym podpisu. "
* IAssetFile.UploadAsync: Wystąpił problem został rozwiązany ograniczoną jednoczesnych przekazywania plików do pięć plików.
* IAssetFile.UploadProgressChanged: To zdarzenie jest teraz udostępniany przez zestaw SDK.
* IAssetFile.DownloadAsync (ciąg, BlobTransferClient, ILocator, CancellationToken): przeciążenie tej metody jest teraz udostępniany.
* IAssetFile.DownloadAsync: Wystąpił problem został rozwiązany ograniczoną jednoczesnych pobierania plików do pięć plików.
* IAssetFile.Delete(): Wystąpił problem został rozwiązany gdzie delete wywołujący może zgłoszenia wyjątku, jeśli nie przekazano pliku dla IAssetFile.
* Zadania: Wystąpił problem został rozwiązany gdzie tworzenie łańcuchów "MP4 do sprawnego strumieni zadania" z "Zadania ochrony PlayReady" przy użyciu szablonu zadania nie utworzono żadnych zadań w ogóle.
* EncryptionUtils.GetCertificateFromStore(): Ta metoda nie jest już zgłasza wyjątek odwołania zerowego z powodu błędu w poszukiwaniu certyfikatu opartego na problemy z konfiguracją certyfikatu.

## <a id="november_changes_12"></a>Zwolnij listopad 2012
Zmiany wymienione w tej sekcji zostały aktualizacje zawarte w listopad 2012 (wersja 2.0.0.0) zestawu SDK. Te zmiany mogą wymagać żadnego kodu napisane dla podglądu czerwca 2012 SDK wersji modyfikację lub napisany od nowa.

* Elementy zawartości
  
    * Jest IAsset.Create(assetName) *tylko* funkcji tworzenia zasobów. Nie jest już IAsset.Create operacji przekazywania plików jako część wywołania metody. Użyj IAssetFile do przekazywania.
    * Metoda IAsset.Publish i wartość wyliczenia AssetState.Publish zostały usunięte z usługi SDK. Należy ponownie zapisać kod korzystający z tą wartością.
* FileInfo
  
    * Ta klasa została usunięta i zastępuje IAssetFile.
  
* IAssetFiles
  
    * IAssetFile zastępuje FileInfo i działa w różny sposób. Aby go użyć, należy utworzyć wystąpienia obiektu IAssetFiles, a następnie przekazywania pliku za pomocą zestawu SDK usługi Media lub zestawu SDK usługi Magazyn. Można użyć następujących przeciążeń IAssetFile.Upload:
  
        * IAssetFile.Upload(filePath): Ta metoda synchroniczna blokuje wątku i jest zalecane tylko w przypadku przekazywania jednego pliku.
        * IAssetFile.UploadAsync (filePath, blobTransferClient, lokalizatora, cancellationToken): Ta metoda asynchroniczna jest mechanizm przekazywania preferowanych. 
    
            Znaną usterką: Jeśli używasz token anulowania przekazywania została anulowana. Zadania mogą mieć wiele stanów anulowania. Należy poprawnie catch i obsługi wyjątków.
* Lokalizatory
  
    * Usunięto wersji określonego źródła. Kontekst specyficzne dla sygnatury dostępu Współdzielonego. Locators.CreateSasLocator (zasobów, accessPolicy), zostaną oznaczone przestarzałe lub usunięte przez ogólnej dostępności. Zobacz sekcję "Lokalizatory" w obszarze "Nowa funkcja" zachowanie zaktualizowane.

## <a id="june_changes_12"></a>Wersja zapoznawcza czerwiec 2012
Nowość w wersji listopada zestawu SDK to następujące funkcje:

* Usuwanie jednostek
  
    * Obiekty IAsset, IAssetFile ILocator, IAccessPolicy i IContentKey są teraz usuwane na poziomie obiektu, to znaczy IObject.Delete() zamiast usuwania w kolekcji, czyli cloudMediaContext.ObjCollection.Delete(objInstance).
* Lokalizatory
  
    * Lokalizatory musi zostać utworzone przy użyciu metody CreateLocator. Muszą oni korzystać wartości wyliczenia LocatorType.SAS lub LocatorType.OnDemandOrigin jako argument dla określonego typu lokalizatora się, że chcesz utworzyć.
    * Dodano nowe właściwości lokalizatorów, aby ułatwić uzyskiwanie można używać identyfikatorów URI dla zawartości. Ta zmianom lokalizatorów dla przyszłych rozszerzeń innych firm, zapewniają większą elastyczność i zwiększa łatwość użycia dla aplikacji klienckich.
* Obsługa metod asynchronicznych
  
    * Obsługa komunikacji asynchronicznej został dodany do wszystkich metod.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Forum MSDN usług Media Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[dokumentacja interfejsu API REST usługi Azure Media]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[szczegóły cennika usługi Media Services]: http://azure.microsoft.com/pricing/details/media-services/
[wejściowych metadanych]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[metadanych dane wyjściowe]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[omówienie szablon licencji PlayReady usług Media]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[dynamicznego tworzenia pakietów]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[ponów logikę w zestawie SDK usługi multimediów dla platformy .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[doliny trawy ogłasza 7 EDIUS strumieniowanie za pośrednictwem chmury]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: http://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

