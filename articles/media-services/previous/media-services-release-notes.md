---
title: Informacje o wersji usługi Media Services | Dokumentacja firmy Microsoft
description: Informacje o wersji usługi Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 427ba2b386810749810397afed8ef3f62dcf9217
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506630"
---
# <a name="azure-media-services-release-notes"></a>Informacje o wersji usługi Azure Media Services

Te informacje o wersji dla usługi Azure Media Services podsumowują zmiany z poprzednich wersji i znane problemy.

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Chcemy poznać od naszych klientów, dzięki czemu możemy skupić się na rozwiązywanie problemów wpływających na użytkownik. Aby zgłosić problem lub pytania, Prześlij wpis w [forum MSDN dotyczącym usługi Azure Media Services]. 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Obecnie znane problemy
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Ogólne problemy dotyczące usługi Media Services

| Problem | Opis |
| --- | --- |
| Kilka typowych nagłówków HTTP nie są udostępniane w interfejsie API REST. |W przypadku tworzenia aplikacji usługi Media Services przy użyciu interfejsu API REST, zauważysz, że niektóre typowe pola nagłówka HTTP (w tym CLIENT-REQUEST-ID REQUEST-ID i ZWRÓCENIA-CLIENT-REQUEST-ID) nie są obsługiwane. Nagłówki zostanie dodana w przyszłej aktualizacji. |
| Kodowanie procent nie jest dozwolone. |Usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL przesyłania strumieniowego zawartości (na przykład `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Z tego powodu kodowania procent nie jest dozwolone. Wartość właściwości Name nie może zawierać żadnych z następujących [procent kodowanie — zastrzeżone znaki](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] % # ". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku. |
| Metoda ListBlobs, która jest częścią zestawu SDK usługi Azure Storage w wersji 3.x kończy się niepowodzeniem. |Usługa Media Services generuje adresów URL sygnatury dostępu Współdzielonego na podstawie [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) wersji. Jeśli chcesz używać zestawu SDK usługi Storage na wyświetlanie listy obiektów blob w kontenerze obiektów blob, użyj [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) metodę, która jest częścią zestawu SDK usługi Storage w wersji 2.x. |
| Usługi Media Services, mechanizm ograniczania ogranicza użycie zasobów dla aplikacji, które wysyłać nadmierne żądań do usługi. Usługa może zwrócić kod stanu HTTP "Usługa niedostępna" 503. |Aby uzyskać więcej informacji, zobacz opis kod stanu HTTP 503 w [kody błędów usługi Media Services](media-services-encoding-error-codes.md). |
| Gdy zapytanie jednostki limit 1000 jednostek jest zwracany w tym samym czasie, ponieważ publiczny REST w wersji 2 ogranicza wyniki zapytania do 1000 wyników. |Pomiń i podjąć (.NET) / top (REST), zgodnie z opisem w [w tym przykładzie .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) i [w tym przykładzie interfejs API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Niektórzy klienci mogą pochodzić między problemem powtórzeń tag w manifeście Smooth Streaming. |Aby uzyskać więcej informacji, zobacz [w tej sekcji](media-services-deliver-content-overview.md#known-issues). |
| Obiekty Media Services .NET SDK nie może być serializowany i dlatego nie działają z pamięć podręczna systemu Azure dla usługi Redis. |Jeśli zostanie podjęta próba serializacji obiektu AssetCollection zestawu SDK, aby dodać go do usługi Azure Cache dla pamięci podręcznej Redis, wyjątek jest generowany. |

## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>Historia wersji interfejsu API REST
Aby uzyskać informacji o historii wersji interfejsu API REST usługi Media Services, zobacz [Dokumentacja usługi Azure Media Services — interfejs API REST].

## <a name="december-2018"></a>Grudzień 2018 r.

Funkcja Media Hyperlapse w wersji zapoznawczej usługi Azure Media Services zostanie wkrótce wycofany. Od 19 grudnia 2018 r. usługi Media Services nie jest już wprowadzi zmiany i udoskonalenia Media Hyperlapse. Na 29 marca 2019 r będzie wycofane i nie będą już dostępne.

## <a name="october-2018"></a>Październik 2018 r.

### <a name="cmaf-support"></a>Obsługa CMAF

CMAF i "cbcs" Obsługa szyfrowania HLS firmy Apple (system iOS 11 +) i MPEG-DASH graczy, które obsługują CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Obrazki miniatury VTT sieci Web

Można teraz używać usługi Media Services do generowania miniatur obrazki VTT sieci Web, za pomocą naszych interfejsów API w wersji 2. Aby uzyskać więcej informacji, zobacz [generowanie miniatur sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Lipiec 2018 r.

Najnowsza wersja usługi istnieją drobne zmiany do komunikatów o błędach zwracanych przez usługę, gdy zadanie nie powiedzie się, w odniesieniu do sposobu jego został podzielony na dwie lub więcej wierszy formatowania.

## <a name="may-2018"></a>Maj 2018 r. 

Od 12 maja 2018 r. Usługa kanały na żywo będzie już obsługę strumienia transportu RTP/MPEG-2 protokołu pozyskiwania. Przeprowadź migrację z protokołu RTP/MPEG-2 do protokołu RTMP lub plików MP4 (Smooth Streaming) protokołów pozyskiwania.

## <a name="october-2017-release"></a>Wersja z października 2017 r.
> [!IMPORTANT] 
> Usługa Media Services jest wycofanie obsługi klucze uwierzytelniania usługi Azure Access Control Service. 22 czerwca 2018 r. nie będzie można uwierzytelniać z zapleczem usługi Media Services za pomocą kodu za pomocą kluczy usługi kontroli dostępu. Należy zaktualizować swój kod, aby usługa Azure Active Directory (Azure AD) na [uwierzytelnianie na podstawie usługi AD systemu Azure](media-services-use-aad-auth-to-access-ams-api.md). Obserwuj ostrzeżenia dotyczące tej zmiany w witrynie Azure portal.

### <a name="updates-for-october-2017"></a>Aktualizacje dla października 2017 r.
#### <a name="sdks"></a>SDK
* Zestaw .NET SDK został zaktualizowany do obsługi uwierzytelniania usługi Azure AD. Obsługa uwierzytelniania usługa Access Control Service została usunięta z najnowszy zestaw SDK platformy .NET w witrynie Nuget.org, aby zachęcić szybsza migracja do usługi Azure AD. 
* Zestaw JAVA SDK został zaktualizowany do obsługi uwierzytelniania usługi Azure AD. Obsługa uwierzytelniania usługi Azure AD została dodana do zestawu SDK języka Java. Aby uzyskać informacje na temat korzystania z zestawu SDK Java za pomocą usługi Media Services, zobacz [Rozpoczynanie pracy z zestawem SDK klienta Java dla usługi Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Kodowanie oparte na plikach
* Koder w warstwie Premium można teraz używać celu zakodowania zawartości wideo wysokiej wydajności H.265 kodowania kodera-dekodera wideo (— HEVC). Jeśli wybierzesz H.265 za pośrednictwem innych koderów-dekoderów, takich jak H.264 jest nie wpływ na cenę. Aby uzyskać informacji na temat — HEVC licencje patentowe, zobacz [Online Services — warunki](https://azure.microsoft.com/support/legal/).
* Dla źródłowy plik wideo, który jest kodowany za pomocą H.265 (— HEVC) kodera-dekodera wideo, takich jak wideo, przechwycone przy użyciu system IOS 11 lub GoPro Hero 6 teraz służy koder w warstwie Premium lub koder w warstwie standardowa do kodowania tych filmów wideo. Aby uzyskać informacji na temat licencje patentowe, zobacz [Online Services — warunki](https://azure.microsoft.com/support/legal/).
* Dla zawartości, która zawiera wiele języka ścieżki audio wartościami języka muszą być prawidłowo oznaczone według specyfikacji formatu pliku (na przykład ISO MP4). Następnie przy użyciu kodera w warstwie standardowa, aby zakodować zawartość do przesyłania strumieniowego. Wynikowe Lokalizator przesyłania strumieniowego Wyświetla listę dostępnych języków audio.
* Koder w warstwie standardowa obsługuje teraz dwa nowe ustawienia systemu tylko dane audio, "AAC Audio" i "AAC dobrą jakość dźwięku". Zarówno generuje stereo Zaawansowane audio odpowiednio kodowania danych wyjściowych (AAC), która znajduje się na szybkości transmisji bitów 128 Kb/s i 192 kb/s.
* Koder w warstwie Premium obsługuje teraz formaty plików QuickTime/MOV jako dane wejściowe. Kodera-dekodera wideo musi być jednym z [ProRes firmy Apple, typy wymienione w tym artykule GitHub](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Audio, musi być albo adaptacyjnych kontrolek aplikacji lub impulsowe modulacji kodu (PCM). Koder w warstwie Premium nie obsługują na przykład wideo DVC/DVCPro opakowane w plikach QuickTime/MOV jako dane wejściowe. Koder w warstwie standardowa obsługują te koderów-dekoderów wideo.
* Następujące poprawki zostały wprowadzone w kodery:

    * Można teraz przesyłać zadania za pomocą wejściowego elementu. Po zakończeniu tych zadań, można zmodyfikować elementu zawartości (na przykład, Dodaj, usuń lub zmień nazwy plików w ramach zasobu) i przesłać dodatkowe zadania.
    * Zwiększona jakości miniatury JPEG produkowane przez koder w warstwie standardowa.
    * Koder w warstwie standardowa obsługuje metadanych wejściowych oraz generowanie miniatur w bardzo krótki czas trwania wideo.
    * Ulepszenia do dekodera H.264 używane w koder w warstwie standardowa wyeliminowanie niektórych rzadkich artefaktów. 

#### <a name="media-analytics"></a>Media Analytics
Ogólna dostępność usługi Azure Media Redactor: To procesor multimediów wykonuje anonimowości za rozmywając twarze wybranych osób co stanowi idealne rozwiązanie w scenariuszach publicznych, bezpieczeństwa i mediów informacyjnych. 

Aby uzyskać omówienie tego nowego procesora, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/azure-media-redactor/). Aby uzyskać informacje na temat dokumentacji i ustawień, zobacz [redagowanie twarze za pomocą usługi Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Wersja z czerwca 2017 r.

Usługa Media Services obsługuje teraz [uwierzytelnianie na podstawie usługi AD systemu Azure](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje modelu uwierzytelnienia korzysta usługa kontroli dostępu. Uwierzytelnianie usługi kontroli dostępu zostaną wycofane 1 czerwca 2018 r. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="march-2017-release"></a>Wydanie z marca 2017 r.

Teraz możesz używać kodera w warstwie standardowa do [automatyczne generowanie drabiny szybkości transmisji bitów](media-services-autogen-bitrate-ladder-with-mes.md) przez określenie "adaptacyjnego przesyłania strumieniowego" ustawienie wstępne ciągu, po utworzeniu zadania kodowania. Do kodowania wideo do przesyłania strumieniowego za pomocą usługi Media Services, należy użyć ustawienia wstępnego "Adaptacyjnego przesyłania strumieniowego". Aby dostosować ustawienia wstępnego dla określonego scenariusza kodowania, możesz zacząć od [predefiniowane](media-services-mes-presets-overview.md).

Możesz teraz użyć usługi Media Encoder Standard lub Media Encoder Premium Workflow do [Utwórz zadanie kodowania, które fragmenty fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Wersji z lutego 2017 r.

Od 1 kwietnia 2017 r. wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni jest automatycznie usuwana, wraz z jego rekordów skojarzonych zadań. Usuwanie występuje, nawet jeśli łączna liczba rekordów jest poniżej maksymalny limit przydziału. Aby archiwizację informacji zadania lub zadania podrzędnego, można użyć kodu opisanego w [zarządzanie zasobami i powiązanymi jednostkami za pomocą zestawu SDK .NET usługi Media Services](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Wersja stycznia 2017 r.

W usłudze Media Services punktu końcowego przesyłania strumieniowego reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub sieci dostarczania zawartości (CDN) w celu dalszego rozpowszechniania. Media Services udostępnia również bezproblemową integrację usługi Azure Content Delivery Network. Strumień wychodzący, z usługi StreamingEndpoint może być strumień na żywo, wideo na żądanie lub pobierania progresywnego elementów zawartości w ramach konta usługi Media Services. Każde konto usługi Media Services obejmuje domyślny punkt końcowy przesyłania strumieniowego. Dodatkowe punkty końcowe przesyłania strumieniowego mogą być tworzone w ramach konta. 

Istnieją dwie wersje punkty końcowe, przesyłania strumieniowego 1.0 i 2.0. Od 10 stycznia 2017 r. wszystkie nowo utworzone konta usługi Media Services zawierają domyślnie w wersji 2.0, punkt końcowy przesyłania strumieniowego. Dodatkowe punkty końcowe przesyłania strumieniowego, które dodajesz do tego konta są również w wersji 2.0. Ta zmiana nie ma wpływu na istniejące konta. Istniejące punkty końcowe przesyłania strumieniowego są w wersji 1.0 i mogą być uaktualniane do wersji 2.0. Istnieją zmiany funkcji za pomocą tej zmiany zachowania i rozliczeniami. Aby uzyskać więcej informacji, zobacz [Streaming endpoints overview (Omówienie punktów końcowych przesyłania strumieniowego)](media-services-streaming-endpoints-overview.md).

Począwszy od wersji 2.15 usługi Media Services dodane następujące właściwości do jednostki przesyłania strumieniowego punktu końcowego:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Aby uzyskać więcej informacji na temat tych właściwości, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Wersja grudnia 2016 r.

 Teraz służy usługa Media Services na dostęp do danych telemetrycznych/metryki danych dla swoich usług. Możesz używać bieżącej wersji usługi Media Services do zbierania danych telemetrycznych dla kanału na żywo, punkt końcowy przesyłania strumieniowego i archiwizacji jednostek. Aby uzyskać więcej informacji, zobacz [telemetrii usługi Media Services](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Wersja z lipca 2016 r.
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aktualizacje do pliku manifestu (*. ISM) generowany przez kodowania zadań
Po przesłaniu zadania kodowania do usługi Media Encoder Standard lub Media Encoder Premium zadania kodowania generuje [pliku manifestu przesyłania strumieniowego](media-services-deliver-content-overview.md) (* ISM) w zasobie danych wyjściowych. Za pomocą najnowszej wersji usługi została zaktualizowana Składnia tego pliku manifestu przesyłania strumieniowego.

> [!NOTE]
> Składnia przesyłania strumieniowego pliku manifestu (ISM) jest zarezerwowany do użytku wewnętrznego. To może ulec zmianie w przyszłych wersjach. Nie wolno modyfikować ani modyfikować zawartości tego pliku.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Nowy manifest klienta (*. Wygenerowany plik ISMC) w zasobie danych wyjściowych, gdy zadanie kodowania generuje co najmniej jeden plik w formacie MP4
Po zakończeniu zadania kodowania, które generuje pliki MP4 z jedną lub więcej, począwszy od najnowszej wersji usługi elementu zawartości wyjściowej zawiera także przesyłania strumieniowego plik manifestu (*.ismc) klienta. Plik .ismc pomaga w zwiększeniu wydajności przesyłania strumieniowego dynamicznych. 

> [!NOTE]
> Składnia pliku manifestu (.ismc) klienta jest zarezerwowana do użytku wewnętrznego. To może ulec zmianie w przyszłych wersjach. Nie wolno modyfikować ani modyfikować zawartości tego pliku.
> 
> 

Aby uzyskać więcej informacji, zobacz [ten blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Znane problemy
Niektórzy klienci mogą pochodzić między problemem powtórzeń tag w manifeście Smooth Streaming. Aby uzyskać więcej informacji, zobacz [w tej sekcji](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>Wersja kwietnia 2016
### <a name="media-analytics"></a>Media Analytics
 Usługa Media Services wprowadzono Media Analytics dla zaawansowanych funkcji analizy wideo. Aby uzyskać więcej informacji, zobacz [Przegląd usługi Media Services Analytics](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Technologia FairPlay firmy Apple (wersja zapoznawcza)
Możesz teraz używać usługi Media Services do dynamicznego szyfrowania usługi HTTP Live Streaming (HLS) zawartości z użyciem technologii FairPlay firmy Apple. Możesz również użyć usługi dostarczania licencji Media Services dostarczać licencje usługi fairplay: dla klientów. Aby uzyskać więcej informacji zobacz sekcję "Używanie usługi Azure Media Services przesyłać strumieniowo zawartość chronioną przy użyciu technologii FairPlay firmy Apple HLS."

## <a id="feb_changes16"></a>Wersja lutego 2016 r.
Najnowszą wersję zestawu SDK usługi multimediów dla platformy .NET (3.5.3) zawiera poprawkę usterki związane z Google Widevine. Nie było możliwe ponowne użycie AssetDeliveryPolicy dla wielu zasobów szyfrowane przy użyciu metody Widevine. W ramach tej poprawki następujące właściwość została dodana do zestawu SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Wersja stycznia 2016 r.
Jednostki zarezerwowane kodowania zostały zmienione na pomyłek z nazwami kodera.

Podstawowa, standardowa i Premium zastrzeżone jednostki kodowania zostały zmienione do S1, S2, i odpowiednio jednostki zarezerwowane S3. Klienci, którzy obecnie używają podstawowe jednostki zarezerwowane kodowania widzieć S1 jako etykieta w witrynie Azure portal (i na rachunku). Klienci, którzy używają Standard i Premium Zobacz etykiety, S2 i S3, odpowiednio. 

## <a id="dec_changes_15"></a>Wersji z grudnia 2015 r.

### <a name="media-encoder-deprecation-announcement"></a>Ogłoszenie wycofywania Media Encoder

 Usługa Media Encoder staną się przestarzałe począwszy od około 12 miesięcy od wydania usługi Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Zestaw Azure SDK dla języka PHP
Zespół zestawu Azure SDK opublikowane nowej wersji zestawu [zestawu Azure SDK dla języka PHP](https://github.com/Azure/azure-sdk-for-php) pakiet, który zawiera aktualizacje i nowe funkcje dla usługi Media Services. W szczególności, Media Services SDK dla języka PHP teraz obsługuje najnowsze [content protection](media-services-content-protection-overview.md) funkcji. Te funkcje są dynamiczne szyfrowanie za pomocą szyfrowania AES i technologii DRM (PlayReady i Widevine), z lub bez ograniczenia tokenu. Obsługuje również skalowanie [jednostek kodowania](media-services-dotnet-encoding-units.md).

Aby uzyskać więcej informacji, zobacz:

* Następujące [przykłady kodu](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) pomóc Ci szybko rozpocząć pracę:
  * **vodworkflow_aes.php**: Ten plik PHP pokazuje, jak korzystać z dynamicznego szyfrowania AES-128 i usługę dostarczania kluczy. Jest on oparty na przykład .NET, szczegółowo [szyfrowania dynamicznego użycie algorytmu AES-128 i usługę dostarczania kluczy](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Ten plik PHP pokazuje, jak używać dynamiczne szyfrowanie PlayReady i usługi dostarczania licencji. Jest on oparty na przykład .NET, szczegółowo [Użyj PlayReady i Widevine dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Ten plik PHP pokazuje, jak skalować jednostki zarezerwowane kodowania.

## <a id="nov_changes_15"></a>Listopad 2015 r.
 Usługa Media Services oferuje teraz usługę dostarczania licencji Widevine w chmurze. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Zobacz też [w tym samouczku](media-services-protect-with-playready-widevine.md) i [repozytorium GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Usługach dostarczania licencji Widevine udostępnianego przez usługi multimediów są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Wersja z października 2015 r.
Usługa Media Services jest teraz na żywo w następujących centrach danych: Brazylia Południowa, Indie Zachodnie, Indie Południowe i Indie środkowe. Można teraz używać portalu Azure w celu [tworzenia kont usługi multimediów](media-services-portal-create-account.md) i wykonywania różnych zadań opisanych w [stronę dokumentacji usługi Media Services](https://azure.microsoft.com/documentation/services/media-services/). Usługa Live Encoding nie jest włączone w tych centrach danych. Ponadto nie wszystkie typy zastrzeżonych jednostek kodowania są dostępne w tych centrach danych.

* Brazylia Południowa:                                          Dostępne są tylko standardowe i Basic zastrzeżonych jednostek kodowania.
* Indie Zachodnie, Indie Południowe i Indie środkowe:             Dostępne są wyłącznie podstawowe jednostki zarezerwowane kodowania.

## <a id="september_changes_15"></a>Wydanie z września 2015 r.
Usługa Media Services oferuje teraz możliwość ochrony zarówno wideo na żądanie, jak i strumieni na żywo przy użyciu modułowej technologii DRM Widevine. Aby łatwiej dostarczać licencje Widevine, można użyć następujących partnerów usługi dostarczania:
* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Aby skorzystać z usługi Widevine, można skonfigurować obiekt AssetDeliveryConfiguration przy użyciu zestawu [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (począwszy od wersji 3.5.1) lub interfejsu API REST. 
* Usługa Media Services dodano obsługę dla filmów wideo ProRes firmy Apple. Możesz teraz przekazać pliki wideo QuickTime źródłowego, które używają ProRes firmy Apple lub inne kodery-dekodery. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Teraz umożliwia Media Encoder Standard celu wyodrębniania archiwum subclipping, jak i na żywo. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Wprowadzono następujące aktualizacje filtrowania: 
  
  * Można teraz użyć formatu Apple HLS z filtrem tylko dane audio. Tę aktualizację można użyć, aby usunąć tylko dane audio śledzenie, określając (tylko dźwięk = false) w adresie URL.
  * Podczas definiowania filtrów zasobów, teraz można połączyć wiele filtrów (maksymalnie trzy) w pojedynczego adresu URL.
    
    Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Usługa Media Services obsługuje teraz-ramki w HLS w wersji 4. Obsługa ramki optymalizuje operacje szybkie przewijanie do przodu i do tyłu. Domyślnie wszystkie dane wyjściowe w wersji 4 HLS zawierają listy odtwarzania ramki (EXT-X-I-FRAME-STREAM-INF).
Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Sierpień 2015 r.
* Media Services SDK dla języka Java wersji 0.8.0 i nowe przykłady są teraz dostępne. Aby uzyskać więcej informacji, zobacz:
    
* Usługa Azure Media Player została zaktualizowana o obsługę strumienia audio w wielu. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Wersja z lipca 2015 r.
* Ogłoszono ogólną dostępność usługi Media Encoder Standard. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Usługi Media Encoder Standard używa ustawienia wstępne, zgodnie z opisem w [w tej sekcji](https://go.microsoft.com/fwlink/?LinkId=618336). Kiedy używasz ustawienia domyślne dla 4K koduje i Uzyskaj tego typu jednostki zastrzeżone w warstwie Premium. Aby uzyskać więcej informacji, zobacz [kodowanie skalowania](media-services-scale-media-processing-overview.md).
* Podpisy w czasie rzeczywistym na żywo były używane przy użyciu usługi Media Services i Media Player. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje Media Services .NET SDK
Media Services .NET SDK jest teraz w wersji 3.4.0.0. Wprowadzono następujące aktualizacje: 

* Obsługa został wdrożony w archiwum na żywo. Nie można pobrać element zawartości zawierający dynamiczne archiwum.
* Obsługa został wdrożony w filtrów dynamicznych.
* Funkcje został wdrożony, aby użytkownicy można zachować kontenera magazynu, podczas ich usuwania zasobu.
* Pokrewne, aby ponowić próbę zasady w kanałach wprowadzono poprawki błędów.
* Media Encoder Premium Workflow została włączona.

## <a id="june_changes_15"></a>Czerwca 2015 r.
### <a name="media-services-net-sdk-updates"></a>Aktualizacje Media Services .NET SDK
Media Services .NET SDK jest teraz w wersji 3.3.0.0. Wprowadzono następujące aktualizacje: 

* Dodano obsługę specyfikacji odnajdywania protokołu OpenId Connect.
* Dodano obsługę obsługi przerzucania kluczy po stronie dostawcy tożsamości.

Jeśli używasz dostawcy tożsamości, która udostępnia dokument odnajdywania protokołu OpenID Connect (jako usługi Azure AD, Google i Salesforce do), można nakazać usługi Media Services, aby uzyskać klucze podpisywania do weryfikacji tokenów sieci Web JSON (tokenów Jwt) od Specyfikacja odnajdywania protokołu OpenID Connect. 

Aby uzyskać więcej informacji, zobacz [kluczy sieci web użycia formatu JSON z Specyfikacja odnajdywania protokołu OpenID Connect do pracy z uwierzytelnianiem tokenu JWT w usłudze Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Wersja maja 2015 r.
Następujące nowe funkcje zostały ogłoszone:

* [Podgląd kodowania na żywo za pomocą usługi Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamiczne manifestu](media-services-dynamic-manifest-overview.md)

## <a id="april_changes_15"></a>Kwietnia 2015 r.
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
* [Usługa Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) zostało zapowiedziane.
* Począwszy od wersji 2.10 REST dla usługi Media kanały, które są skonfigurowane do odbierania komunikatów protokołu RTMP (Real-Time) są tworzone przy użyciu podstawowego i pomocniczego adresy URL pozyskiwania. Aby uzyskać więcej informacji, zobacz [konfiguracje pozyskiwania kanału](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Usługa Azure Media Indexer zostało zaktualizowane.
* Dodano obsługę języka hiszpańskiego.
* Dodano nową konfigurację dla formatu XML.

Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje Media Services .NET SDK
Media Services .NET SDK jest teraz w wersji 3.2.0.0. Wprowadzono następujące aktualizacje:

* Zmiana powodująca niezgodność: TokenRestrictionTemplate.Issuer i TokenRestrictionTemplate.Audience zostały zmienione na być typu string.
* Aktualizacje zostały wprowadzone związane z tworzenia zasady niestandardowe ponawiania prób.
* Poprawki zostały wprowadzone związanych z przekazywanie i pobieranie plików.
* Klasa MediaServicesCredentials akceptuje teraz można uwierzytelnić, punkty końcowe kontroli dostępu podstawowego i pomocniczego.

## <a id="march_changes_15"></a>Marca 2015 r.
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
* Usługa Media Services udostępnia teraz integracji Content Delivery Network. Aby obsługiwać integrację, właściwość CdnEnabled został dodany do StreamingEndpoint. CdnEnabled może służyć za pomocą interfejsów API REST, począwszy od wersji 2.9. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled może służyć za pomocą zestawu SDK platformy .NET, począwszy od wersji 3.1.0.2. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Ogłoszono Media Encoder Premium Workflow. Aby uzyskać więcej informacji, zobacz [Premium wprowadzenie do kodowania w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Lutego 2015 r.
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
Interfejsu API REST usługi Media Services jest teraz w wersji 2.9. Począwszy od tej wersji, aby umożliwić integrację Content Delivery Network, za pomocą punktów końcowych przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Stycznia 2015 r.
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
Ogłoszono ogólne udostępnienie usług ochrony zawartości w przypadku szyfrowania dynamicznego. Aby uzyskać więcej informacji, zobacz [usługi Media Services zwiększa bezpieczeństwo przesyłania strumieniowego ogólne udostępnienie technologii DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje Media Services .NET SDK
Media Services .NET SDK jest teraz w wersji 3.1.0.1.

W tej wersji są oznaczone domyślnego konstruktora Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate jako przestarzałe. Nowy Konstruktor przyjmuje TokenType jako argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Wersji z grudnia 2014
### <a name="general-media-services-updates"></a>Aktualizuje ogólne usługi Media Services
* Niektóre aktualizacje i nowe funkcje zostały dodane do Media Indexer. Aby uzyskać więcej informacji, zobacz [usługi Azure Media Indexer 1.1.6.7 informacje o wersji](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Nowy interfejs API REST został dodany, że można użyć do zaktualizowania jednostki zarezerwowane kodowania. Aby uzyskać więcej informacji, zobacz [EncodingReservedUnitType z użyciem usług REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Dodano obsługę mechanizmu CORS usługi dostarczania kluczy.
* Wprowadzono ulepszenia wydajności zapytania opcji zasad autoryzacji.
* W centrum danych w Chinach [dostarczania adres URL klucza](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) jest teraz na klienta (podobnie jak w innych centrach danych).
* Czas trwania docelowych automatycznej HLS został dodany. W trakcie transmisji strumieniowej na żywo, HLS, jest zawsze dostarczana dynamicznie. Domyślnie usługa Media Services automatycznie oblicza współczynnik pakowania segmentu HLS (FragmentsPerSegment) na podstawie interwału ramki kluczowej (KeyFrameInterval). Ta metoda jest również określany jako grupę obrazów (GOP) odebrane z kodera na żywo. Aby uzyskać więcej informacji, zobacz [pracy z usługą Media Services transmisja strumieniowa na żywo](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje Media Services .NET SDK
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) jest teraz w wersji 3.1.0.0. Wprowadzono następujące aktualizacje:

* Zależności zestawu .NET SDK został uaktualniony do programu .NET Framework 4.5.
* Dodano nowy interfejs API, który umożliwia zaktualizowanie jednostki zarezerwowane kodowania. Aby uzyskać więcej informacji, zobacz [aktualizacji zarezerwowane typu jednostki i zastrzeżonych jednostek kodowania przy użyciu platformy .NET Zwiększ](media-services-dotnet-encoding-units.md).
* Obsługa tokenów JWT token uwierzytelniania został dodany. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie przy użyciu tokenów JWT usługi Media Services i szyfrowania dynamicznego](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Dodano względne przesunięć BeginDate i ExpirationDate w szablonu licencji technologii PlayReady.

## <a id="november_changes_14"></a>Listopadzie 2014 roku
* Za pomocą usługi Media Services możesz teraz pozyskiwanie zawartości na żywo Smooth Streaming (fMP4) za pośrednictwem połączenia SSL. Pozyskanie za pośrednictwem protokołu SSL, upewnij się zaktualizować adres URL pozyskiwania protokołu HTTPS. Obecnie usługa Media Services nie obsługuje protokołu SSL z zastosowaniem domen niestandardowych. Aby uzyskać więcej informacji na temat transmisji strumieniowej na żywo, zobacz [pracy za pomocą usługi Azure Media Services transmisji strumieniowej na żywo](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Nie można obecnie pozyskiwania RTMP transmisji strumieniowej na żywo, za pośrednictwem połączenia SSL.
* Można przesyłać strumieniowo za pośrednictwem protokołu SSL, tylko wtedy, gdy punkt końcowy przesyłania strumieniowego, z którego można dostarczać zawartość została utworzona po 10 września 2014 r. Jeśli Twoje adresy URL przesyłania strumieniowego są oparte na punkty końcowe przesyłania strumieniowego utworzonych po 10 września 2014 r. adres URL zawiera "streaming.mediaservices.windows.net" (nowy format). Adresy URL przesyłania strumieniowego, które zawierają "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu SSL. Jeśli adres URL jest w starym formacie i chcesz strumieniowo przesyłać za pośrednictwem protokołu SSL, [utworzyć nowy punkt końcowy przesyłania strumieniowego](media-services-portal-manage-streaming-endpoints.md). Aby przesyłać strumieniowo zawartość, za pośrednictwem protokołu SSL, należy użyć adresów URL, w oparciu o nowy punkt końcowy przesyłania strumieniowego.

### <a id="oct_sdk"></a>Zestaw SDK platformy .NET usługi Media Services
Media Services SDK dla platformy .NET, rozszerzenia jest teraz w wersji 2.0.0.3.

Media Services SDK dla platformy .NET jest teraz w wersji 3.0.0.8. Wprowadzono następujące aktualizacje:

* Refaktoryzacja został wdrożony w klasach zasad ponawiania prób.
* Ciąg agenta użytkownika została dodana do nagłówków żądań HTTP.
* Dodano kroku kompilacji Przywracanie NuGet.
* Testy scenariusza zostały poprawione x509 używać certyfikatu z repozytorium.
* Ustawienia weryfikacji zostały dodane do zaktualizowania kanału i na końcu przesyłania strumieniowego.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nowe repozytorium usługi GitHub do hosta usługi Media Services przykładów
Przykłady znajdują się w [repozytorium GitHub samples usługi Media Services](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Wydanie z września 2014
Metadane Media Services REST jest teraz w wersji 2.7. Aby uzyskać więcej informacji o najnowszych aktualizacjach REST, zobacz [dokumentacja interfejsu API REST usługi Media](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services SDK dla platformy .NET jest teraz w wersji 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Fundamentalne zmiany
* Źródła została zmieniona na [StreamingEndpoint].
* Zmiana została wprowadzona w domyślne zachowanie, gdy używasz witryny Azure portal do kodowania, a następnie opublikować pliki MP4.

### <a id="sept_14_GA_changes"></a>Nowe funkcje/scenariusze, które są dostępne w ramach wersji ogólnodostępnej
* Wprowadzono procesora media Media Indexer. Aby uzyskać więcej informacji, zobacz [indeksowanie plików multimedialnych za pomocą Media Indexer](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Możesz użyć [StreamingEndpoint] jednostka do dodania nazwy domeny niestandardowej (host).
  
    Aby użyć niestandardowej nazwy domeny jako nazwa punktu końcowego przesyłania strumieniowego usługi Media Services, należy dodać nazwy niestandardowego hosta do punktu końcowego przesyłania strumieniowego. Aby dodać nazwy niestandardowego hosta, należy użyć interfejsów API REST usługi Media lub zestawu .NET SDK.
  
    Mają zastosowanie następujące kwestie:
  
  * Musi mieć prawa własności nazwy domeny niestandardowej.
  * Przez usługę Media Services, można sprawdzić poprawności własności nazwy domeny. Aby zweryfikować domenę, Utwórz rekord CName mapujący domeny nadrzędnej MediaServicesAccountId, aby sprawdzić DNS w mediaservices strefy dns.
  * Należy utworzyć innego rekordu CName, który mapuje nazwę niestandardowego hosta (na przykład sports.contoso.com) usługi Media Services StreamingEndpoint nazwy hosta (na przykład amstest.streaming.mediaservices.windows.net).

    Aby uzyskać więcej informacji, zobacz właściwość CustomHostNames [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) artykułu.

### <a id="sept_14_preview_changes"></a>Nowych funkcji/scenariuszy, które są dostępne w ramach publicznej wersji zapoznawczej
* Podgląd transmisji strumieniowej na żywo. Aby uzyskać więcej informacji, zobacz [pracy z usługą Media Services transmisja strumieniowa na żywo](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Usługa dostarczania kluczy. Aby uzyskać więcej informacji, zobacz [szyfrowania dynamicznego użycie algorytmu AES-128 i usługę dostarczania kluczy](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Dynamiczne szyfrowanie AES. Aby uzyskać więcej informacji, zobacz [szyfrowania dynamicznego użycie algorytmu AES-128 i usługę dostarczania kluczy](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Usługi dostarczania licencji PlayReady. 
* Dynamiczne szyfrowanie PlayReady. 
* Szablon licencji PlayReady usługi Media Services. Aby uzyskać więcej informacji, zobacz [omówienie szablonu licencji PlayReady usługi Media Services].
* Stream magazyn szyfrowany zasoby. Aby uzyskać więcej informacji, zobacz [Stream szyfrowany magazyn zawartości](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Wersji z sierpnia 2014
Podczas kodowania zasobów trwałego danych wyjściowych jest generowany po zakończeniu zadania kodowania. Do tej wersji Media Services Encoder utworzone metadane o zasobach danych wyjściowych. Począwszy od tej wersji kodera również tworzy metadane o zasobach danych wejściowych. Aby uzyskać więcej informacji, zobacz [metadane wejściowe] i [Metadane wyjściowe].

## <a id="july_changes_14"></a>Wersja z lipca 2014
Następujące poprawki zostały wprowadzone do usługi Azure Media Services Pakowarki a modułu szyfrującego:

* Gdy trwałego archiwum na żywo są przesyłane do HLS, tylko dźwięk odtwarza: Ten problem został rozwiązany, a teraz można odtworzyć audio i wideo.
* Gdy element zawartości jest dostarczana do szyfrowania koperty HLS i AES 128-bitowy, spakowanych strumieni nie odtwarzanie na urządzeniach z systemem Android: Ten błąd został naprawiony, a strumień spakowanych odtwarza na urządzeniach z systemem Android, które obsługują HLS.

## <a id="may_changes_14"></a>Wersja maj 2014
### <a id="may_14_changes"></a>Aktualizuje ogólne usługi Media Services
Teraz możesz używać [funkcję dynamicznego tworzenia pakietów] do strumienia HLS w wersji 3. Do strumienia HLS w wersji 3, należy dodać następujący format do lokalizatora ścieżki źródła: * .ism/manifest(format=m3u8-aapl-v3). Aby uzyskać więcej informacji, zobacz [to forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamiczne pakowanie teraz obsługuje również dostarczanie HLS (w wersji 3 i w wersji 4) zaszyfrowany za pomocą technologii PlayReady oparte na funkcji Smooth Streaming statycznie zaszyfrowany za pomocą technologii PlayReady. Aby uzyskać informacje na temat szyfrowania, Smooth Streaming przy użyciu technologii PlayReady, zobacz [chronić Smooth Streaming przy użyciu technologii PlayReady](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Aktualizacje Media Services .NET SDK
Media Services .NET SDK jest teraz w wersji 3.0.0.5. Wprowadzono następujące aktualizacje:

* Podczas przekazywania i pobierania zasobów multimedialnych szybkości i odporności na błędy są lepsze.
* Wprowadzono ulepszenia w obsłudze wyjątków logiki i przejściowe ponownych prób: 
  
  * Do obsługi wyjątków, które powstają, gdy zapytanie, Zapisz zmiany i przekazywać i pobierać pliki udoskonalone Błąd przejściowy logikę wykrywania, a następnie spróbuj ponownie. 
  * Po otrzymaniu wyjątki w sieci web (na przykład podczas żądania tokenu Access Control Service), błędy krytyczne niepowodzenie szybciej teraz.

Aby uzyskać więcej informacji, zobacz [Logika ponawiania w zestawie SDK usługi multimediów dla platformy .NET].

## <a id="jan_feb_changes_14"></a>Stycznia/lutego 2014 roku
### <a name="jan_fab_14_donnet_changes"></a>Zestaw SDK platformy .NET 3.0.0.1, 3.0.0.2 i 3.0.0.3 usługi Media Services
Zmiany w 3.0.0.1 i 3.0.0.2 obejmują:

* Rozwiązano problemy związane z użycia zapytania LINQ w instrukcjach OrderBy.
* Testowanie rozwiązań w [GitHub] zostały podzielone na podstawie jednostki badań i testów opartych na scenariuszach.

Aby uzyskać więcej informacji na temat zmian, zobacz [zwalnia Media Services .NET SDK 3.0.0.1 i 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

W wersji 3.0.0.3 zostały wprowadzone następujące zmiany:

* Zależności usługi Azure storage zostały uaktualnione do używania wersji 3.0.3.0.
* Naprawiono problem zgodności z poprzednimi wersjami 3.0. *.* zwalnia.

## <a id="december_changes_13"></a>Wersja grudnia 2013
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> Wersje 3.0.x.x nie są zgodne z poprzednimi wersjami z wersjami 2.4.x.x.
> 
> 

Najnowszą wersję zestawu SDK usługi Media Services jest teraz 3.0.0.0. Można pobrać najnowszy pakiet z pakietów NuGet lub pobieranie usługi bits z [GitHub].

Począwszy od Media Services SDK w wersji 3.0.0.0, można użyć ponownie [usługi Azure AD Access Control Service](https://msdn.microsoft.com/library/hh147631.aspx) tokenów. Aby uzyskać więcej informacji, zobacz sekcję "Tokenami ponownemu użyciu starych Access Control Service" w [nawiązywanie połączenia z usługi Media Services za pomocą zestawu Media Services SDK dla platformy .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK rozszerzenia 2.0.0.0 lub nowszej
 Rozszerzenia zestawu SDK .NET usługi Media Services to zestaw metod rozszerzeń i funkcji pomocnika, które upraszczają kod i ułatwiają tworzenie przy użyciu usługi Media Services. Można uzyskać najnowsze elementy z [Media Services .NET SDK rozszerzenia](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Wersja listopada 2013 r.
### <a name="nov_13_donnet_changes"></a>Zmiany zestawu SDK .NET usługi Media Services
Począwszy od tej wersji, Media Services SDK dla platformy .NET obsługuje błędów przejściowych błędów, które mogą wystąpić podczas wywołań do warstwy interfejsu API REST usługi Media Services.

## <a id="august_changes_13"></a>Wersji z sierpnia 2013
### <a name="aug_13_powershell_changes"></a>Media Services w programie PowerShell poleceń cmdlet zawartych w zestawie Azure SDK tools
Następujące polecenia cmdlet programu PowerShell usługi multimediów są objęte [zestawu Azure SDK tools](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Na przykład: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Na przykład: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Na przykład: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Na przykład: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Wersja z czerwca 2013 r.
### <a name="june_13_general_changes"></a>Zmiany usługi Media Services
Następujące zmiany, które są wymienione w tej sekcji są aktualizacje zawarte w wersjach usługi Media Services czerwca 2013 r.:

* Możliwość łączenia wielu kont magazynu do konta usługi multimediów. 
    * StorageAccount
    * Asset.StorageAccountName i Asset.StorageAccount
* Możliwość aktualizacji Job.Priority. 
* Powiadomienie dotyczące jednostek i właściwości: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Zadanie
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Zmiany zestawu SDK .NET usługi Media Services
Następujące zmiany zostały uwzględnione w czerwcu 2013 wersje zestawu SDK usługi Media Services. Najnowszy zestaw SDK usługi multimediów jest dostępna w witrynie GitHub.

* Począwszy od wersji 2.3.0.0 obsługuje zestaw SDK usług Media Services, łączenie wielu magazynu kont do konta usługi Media Services. Następujące interfejsy API obsługują tę funkcję:
  
    * Typ IStorageAccount
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts property
    * Właściwość StorageAccount
    * Właściwość StorageAccountName
  
      Aby uzyskać więcej informacji, zobacz [zasoby zarządzania usługi Media Services na wielu kontach magazynu](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Interfejsy API związane z powiadomień. Począwszy od wersji 2.2.0.0 można słuchać Azure Queue storage powiadomienia. Aby uzyskać więcej informacji, zobacz [obsługi usługi Media Services zadania powiadomienia](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions property
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint type
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription type
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection type
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType type
* Zależność od klienta usługi Storage SDK w wersji 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Zależność od protokołu OData w wersji 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>Wersja grudnia 2012
### <a name="dec_12_dotnet_changes"></a>Zmiany zestawu SDK .NET usługi Media Services
* IntelliSense: Brak dokumentacji IntelliSense została dodana dla wielu typów.
* Microsoft.Practices.TransientFaultHandling.Core: Problem został rozwiązany, gdy zestaw SDK nadal miały zależności do starszej wersji tego zestawu. Zestaw SDK obecnie odwołuje się do wersji 5.1.1209.1 tego zestawu.

Poprawki dotyczące problemów znalezionych w listopad 2012 SDK:

* IAsset.Locators.Count: Ten licznik teraz jest prawidłowo raportowane w nowych interfejsach IAsset po usunięciu wszystkich lokalizatorów.
* IAssetFile.ContentFileSize: Ta wartość jest teraz prawidłowo ustawiona po przesłaniu przez IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Teraz można ustawić tę właściwość, podczas tworzenia pliku elementu zawartości. Wcześniej był tylko odczytać.
* IAssetFile.Upload(filepath): Problem został rozwiązany, gdy ta metoda synchroniczna przekazywania została zgłaszanie następujący błąd wiele plików zostały przekazane do elementu zawartości. Wystąpił błąd "serwer nie powiodło się uwierzytelnienie żądania. Upewnij się, że wartość nagłówka autoryzacji, został utworzony prawidłowo tym podpis. "
* IAssetFile.UploadAsync: Problem został rozwiązany, co ogranicza jednoczesnych przekazywanie plików do pięciu plików.
* IAssetFile.UploadProgressChanged: To zdarzenie jest teraz udostępniany przez zestaw SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Tego przeciążenia metody jest teraz udostępniany.
* IAssetFile.DownloadAsync: Problem został rozwiązany, co ogranicza jednoczesnych pobierania plików do pięciu plików.
* IAssetFile.Delete(): Problem został rozwiązany, gdzie wywoływania usuwania może zgłosić wyjątek, jeśli plik nie został przekazany do IAssetFile.
* Zadania: Problem został rozwiązany, gdzie łańcucha "MP4 do sprawnego strumienie zadania" z "PlayReady ochrony Task" przy użyciu szablonu zadania nie utworzono żadnych zadań w ogóle.
* EncryptionUtils.GetCertificateFromStore(): Ta metoda nie jest już zgłasza wyjątek pustej referencji, z powodu błędu podczas wyszukiwania certyfikatu oparte na problemy z konfiguracją certyfikatu.

## <a id="november_changes_12"></a>Listopad 2012 release
Zmiany opisane w tej sekcji zostały aktualizacjach listopad 2012 (w wersji 2.0.0.0 lub nowszej) zestawu SDK. Te zmiany mogą wymagać każdy kod napisany dla czerwca 2012 r. (wersja zapoznawcza) wersji zestawu SDK modyfikację lub przepisany.

* Zasoby
  
    * Jest IAsset.Create(assetName) *tylko* funkcję tworzenia zasobów. IAsset.Create już przekazuje pliki jako część wywołania metody. Na użytek IAssetFile przekazywania.
    * Metoda IAsset.Publish i wartość wyliczenia AssetState.Publish zostały usunięte z zestawu SDK usługi. Muszą zostać przepisane wszelki kod, który opiera się na tę wartość.
* FileInfo
  
    * Ta klasa zostało usunięte i zastąpione przez IAssetFile.
  
* IAssetFiles
  
    * IAssetFile zastępuje FileInfo i działa w różny sposób. Aby go użyć, Utwórz wystąpienie obiektu IAssetFiles, następuje przekazywania pliku za pomocą zestawu SDK usługi Media Services lub zestawu SDK usługi Storage. Można użyć następujących przeciążenia IAssetFile.Upload:
  
        * IAssetFile.Upload(filePath): Ta metoda synchroniczna blokuje wątek i jest zalecane tylko wtedy, gdy przekazać jeden plik.
        * IAssetFile.UploadAsync (filePath, blobTransferClient, lokalizatora, cancellationToken): Ta metoda asynchronicznego jest mechanizm przekazywania preferowany. 
    
            Znany błąd: Jeśli używasz token anulowania, przekazywanie zostało anulowane. Zadania mogą mieć wiele stanów anulowania. Należy poprawnie przechwycić i obsłużyć wyjątki.
* Lokalizatory
  
    * Wersje specyficzne dla danego źródła zostały usunięte. Kontekst określonej sygnatury dostępu Współdzielonego. Locators.CreateSasLocator (trwałego, accessPolicy) są oznaczane przestarzałe lub usunięta wg ogólnej dostępności. Zobacz sekcję "Lokalizatory" w obszarze "Nowa funkcja" zachowanie zaktualizowane.

## <a id="june_changes_12"></a>Czerwiec 2012 w wersji zapoznawczej
Nowość w wersji listopada zestawu SDK został następujące funkcje:

* Usuwanie jednostek
  
    * Obiekty IAsset IAssetFile, ILocator, IAccessPolicy i IContentKey teraz są usuwane na poziomie obiektu, czyli IObject.Delete() zamiast usuwania w kolekcji, czyli cloudMediaContext.ObjCollection.Delete(objInstance).
* Lokalizatory
  
    * Lokalizatory muszą zostać utworzone za pomocą metody CreateLocator. Muszą oni korzystać LocatorType.SAS lub LocatorType.OnDemandOrigin wartości wyliczenia jako argument dla określonego typu lokalizatora, którą chcesz utworzyć.
    * Dodano nowe właściwości lokalizatory, aby ułatwić uzyskiwanie można używać identyfikatorów URI zawartości. Tym zmianom lokalizatorów zapewnia większą elastyczność dla przyszłych rozszerzalności innych firm i zwiększa łatwość użycia dla aplikacji klienckich nośnika.
* Metoda asynchroniczna pomocy technicznej
  
    * Dodano obsługę asynchroniczną wszystkich metod.

## <a name="provide-feedback"></a>Przesyłanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Forum MSDN dotyczącym usługi Azure Media Services]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Dokumentacja usługi Azure Media Services — interfejs API REST]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Metadane wejściowe]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadane wyjściowe]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Omówienie szablonu licencji PlayReady usługi Media Services]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[funkcję dynamicznego tworzenia pakietów]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[Logika ponawiania w zestawie SDK usługi multimediów dla platformy .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

