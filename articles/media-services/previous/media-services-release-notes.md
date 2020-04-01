---
title: Informacje o wersji usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule o tym o informacjach o wersji usługi Microsoft Azure Media Services w wersji 2.
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
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: a3893c8d19c89b639e0584f203cbcd1adf7e2dee
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474831"
---
# <a name="azure-media-services-release-notes"></a>Informacje o wersji usługi Azure Media Services

Te informacje o wersji dla usługi Azure Media Services podsumowują zmiany z poprzednich wersji i znanych problemów.

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Chcemy usłyszeć od naszych klientów, abyśmy mogli skupić się na rozwiązywaniu problemów, które cię dotyczą. Aby zgłosić problem lub zadać pytania, prześlij post na [forum usługi Azure Media Services MSDN]. 

## <a name="known-issues"></a><a id="issues"/>Znane problemy
### <a name="media-services-general-issues"></a><a id="general_issues"/>Problemy ogólne usługi Media Services

| Problem | Opis |
| --- | --- |
| Kilka typowych nagłówków HTTP nie są dostępne w interfejsie API REST. |Jeśli programujesz aplikacje usługi Media Services przy użyciu interfejsu API REST, okaże się, że niektóre typowe pola nagłówka HTTP (w tym CLIENT-REQUEST-ID, REQUEST-ID i RETURN-CLIENT-REQUEST-ID) nie są obsługiwane. Nagłówki zostaną dodane w przyszłej aktualizacji. |
| Kodowanie procentowe jest niedozwolone. |Usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL zawartości `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`przesyłanej strumieniowo (na przykład). Z tego powodu kodowanie procentowe jest niedozwolone. Wartość Name właściwości nie może mieć żadnych z następujących [znaków zarezerwowanych do kodowania procentowego:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'(;@&=+$,/?%#[]". Ponadto może istnieć tylko jedno "." dla rozszerzenia nazwy pliku. |
| Metoda ListBlobs, która jest częścią narzędzia Azure Storage SDK w wersji 3.x, kończy się niepowodzeniem. |Usługa Media Services generuje adresy URL sygnatury dostępu Współdzielonego na podstawie wersji [2012-02-12.](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) Jeśli chcesz użyć SDK magazynu do listy obiektów blob w kontenerze obiektów blob, należy użyć [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) metody, która jest częścią SDK magazynu w wersji 2.x. |
| Mechanizm ograniczania przepustowości usługi Media Services ogranicza użycie zasobów dla aplikacji, które żądają nadmiernych żądań do usługi. Usługa może zwrócić kod stanu HTTP "Usługa niedostępna" 503. |Aby uzyskać więcej informacji, zobacz opis kodu stanu HTTP 503 w [kodach błędów usługi Media Services](media-services-encoding-error-codes.md). |
| Podczas wykonywania zapytań encje, limit 1000 jednostek jest zwracany w tym samym czasie, ponieważ publiczny REST w wersji 2 ogranicza wyniki kwerendy do 1000 wyników. |Użyj pomiń i weź (.NET)/top (REST), jak opisano w [tym przykładzie .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) i [tym przykładzie interfejsu API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Niektórzy klienci mogą natknąć się na problem z powtórzeniem znacznika w manifeście Płynne przesyłanie strumieniowe. |Aby uzyskać więcej informacji, zobacz [tę sekcję](media-services-deliver-content-overview.md#known-issues). |
| Nie można serializował obiektów zestawów SDK usługi Media Services i w rezultacie nie działają z usługą Azure Cache for Redis. |Jeśli spróbujesz serializować obiekt SDK AssetCollection, aby dodać go do usługi Azure Cache for Redis, zostanie zgłoszony wyjątek. |
|Interfejs API REST odpowiada komunikatem o błędzie "Filtr nie może uzyskać dostępu do tej wersji interfejsu API REST" podczas próby uzyskania filtru na poziomie zasobu lub konta.|Filtr został utworzony lub zmodyfikowany przy użyciu nowszej wersji interfejsu API niż jest używany do próby uzyskania filtru. Może się tak zdarzyć, jeśli dwie wersje interfejsu API są używane przez kod lub narzędzia używane przez klienta.  Najlepszym rozwiązaniem w tym miejscu jest uaktualnienie kodu lub narzędzi do korzystania z nowszych lub dwóch wersji interfejsu API.|

## <a name="rest-api-version-history"></a><a id="rest_version_history"/>Historia wersji interfejsu API REST
Aby uzyskać informacje na temat historii wersji interfejsu API REST usługi Media Services, zobacz [odwołanie interfejsu API REST usługi Azure Media Services].

## <a name="february-2020"></a>Luty 2020 r.

Niektóre procesory multimediów analitycznych zostaną wycofane. W przypadku dat wycofania zobacz temat [starszych składników.](legacy-components.md)

## <a name="september-2019"></a>Wrzesień 2019 r.

### <a name="deprecation-of-media-processors"></a>Wycofanie procesorów multimediów

Ogłaszamy wycofanie *indeksatora multimediów Azure* i usługi Azure Media *Indexer 2 Preview.* [Indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje te starsze procesory multimediów.

Aby uzyskać daty wycofania, zobacz ten temat [starszych składników.](legacy-components.md)

Zobacz też [Migrowanie z indeksatora multimediów azure i indeksatora multimediów azure 2 do indeksatora wideo usługi Azure Media Services.](migrate-indexer-v1-v2.md)

## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="deprecation-of-media-processors"></a>Wycofanie procesorów multimediów

Ogłaszamy wycofanie procesorów *multimediów Windows Azure Media Encoder* (WAME) i Azure Media *Encoder* (AME). Aby uzyskać daty wycofania, zobacz ten temat [starszych składników.](legacy-components.md)

Aby uzyskać szczegółowe informacje, zobacz [Migrowanie wame do standardu kodera multimediów](https://go.microsoft.com/fwlink/?LinkId=2101334) i [migrowanie AME do media encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).

## <a name="march-2019"></a>Marzec 2019 r.

Funkcja Media Hyperlapse Preview usługi Azure Media Services została przestarzała.

## <a name="december-2018"></a>Grudzień 2018 r.

Funkcja Media Hyperlapse Preview usługi Azure Media Services zostanie wkrótce wycofana. Od 19 grudnia 2018 r. program Media Services nie będzie już wprowadzać zmian ani ulepszeń w funkcji Media Hyperlapse. 29 marca 2019 r. zostanie wycofany i nie będzie już dostępny.

## <a name="october-2018"></a>Październik 2018 r.

### <a name="cmaf-support"></a>Wsparcie CMAF

Obsługa szyfrowania CMAF i "cbcs" dla odtwarzaczy Apple HLS (iOS 11+) i MPEG-DASH obsługujących cmaf.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT miniatury sprites

Teraz można używać usługi Media Services do generowania ikon miniatur web VTT za pomocą naszych interfejsów API w wersji 2. Aby uzyskać więcej informacji, zobacz [Generowanie ikonki miniatur](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Lipiec 2018 r.

W najnowszej wersji usługi istnieją niewielkie zmiany formatowania komunikatów o błędach zwracanych przez usługę, gdy zadanie kończy się niepowodzeniem, w odniesieniu do sposobu, w jaki jest podzielony na dwa lub więcej wierszy.

## <a name="may-2018"></a>Maj 2018 r. 

Od 12 maja 2018 r. kanały na żywo nie będą już obsługiwać protokołu pozyskiwania strumienia transportu RTP/MPEG-2. Przemiń z RTP/MPEG-2 do RTMP lub pofragmentowanych protokołów pozyskiwania MP4 (Smooth Streaming).

## <a name="october-2017-release"></a>Wersja z października 2017 r.
> [!IMPORTANT] 
> Usługa Media Services przestarzała obsługę kluczy uwierzytelniania usługi Azure Access Control Service. W dniu 22 czerwca 2018 r. nie można już uwierzytelniać się za pomocą zaplecza usługi Media Services za pomocą kodu przy użyciu kluczy usługi kontroli dostępu. Należy zaktualizować kod, aby używać usługi Azure Active Directory (Azure AD) na [podstawie uwierzytelniania opartego na usłudze Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) Uważaj na ostrzeżenia dotyczące tej zmiany w witrynie Azure portal.

### <a name="updates-for-october-2017"></a>Aktualizacje na październik 2017 r.
#### <a name="sdks"></a>Zestawy SDK
* Zestaw SDK platformy .NET został zaktualizowany w celu obsługi uwierzytelniania usługi Azure AD. Obsługa uwierzytelniania usługi kontroli dostępu została usunięta z najnowszego kodu SDK platformy .NET w Nuget.org aby zachęcić do szybszej migracji do usługi Azure AD. 
* Zestaw JAVA SDK został zaktualizowany w celu obsługi uwierzytelniania usługi Azure AD. Obsługa uwierzytelniania usługi Azure AD została dodana do zestawu Java SDK. Aby uzyskać informacje na temat korzystania z zestawu Java SDK z usługą Media Services, zobacz [Wprowadzenie do sdk klienta Java dla usługi Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Kodowanie oparte na plikach
* Teraz możesz użyć kodera Premium do kodowania zawartości do kodera kodu wideo H.265 o wysokiej wydajności (HEVC). Nie ma wpływu na ceny, jeśli wybierzesz H.265 w stosunku do innych kodeków, takich jak H.264. Aby uzyskać informacje na temat licencji patentowych HEVC, zobacz [Warunki usług online](https://azure.microsoft.com/support/legal/).
* W przypadku źródłowego wideo zakodowanego kodekiem wideo H.265 (HEVC), takiego jak wideo przechwycone za pomocą systemu iOS11 lub GoPro Hero 6, można teraz użyć kodera Premium lub kodera standardowego do kodowania tych filmów. Aby uzyskać informacje na temat licencji patentowych, zobacz [Warunki usług online](https://azure.microsoft.com/support/legal/).
* W przypadku zawartości zawierającej wiele ścieżek audio w języku, wartości języka muszą być poprawnie oznaczone zgodnie z odpowiednią specyfikacją formatu pliku (na przykład ISO MP4). Następnie można użyć kodera standardowego do kodowania zawartości do przesyłania strumieniowego. Wynikowy lokalizator przesyłania strumieniowego wyświetla listę dostępnych języków audio.
* Standard Encoder obsługuje teraz dwa nowe ustawienia systemu audio, "AAC Audio" i "AAC Good Quality Audio". Oba wytwarzają zaawansowane wyjście do kodowania dźwięku stereo (AAC), z szybkością transmisji bitów odpowiednio 128 Kb/s i 192 Kb/s.
* Koder Premium obsługuje teraz formaty plików QuickTime/MOV jako dane wejściowe. Kodek wideo musi być jednym z [typów Apple ProRes wymienionych w tym artykule GitHub](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). Dźwięk musi być albo AAC lub modulacji kodu impulsowego (PCM). Koder Premium nie obsługuje na przykład wideo DVC/DVCPro zawinięte w plikach QuickTime/MOV jako dane wejściowe. Koder standardowy obsługuje te kodeki wideo.
* W koderach wprowadzono następujące poprawki błędów:

    * Teraz można przesyłać zadania przy użyciu zasobu wejściowego. Po zakończeniu tych zadań można zmodyfikować zasób (na przykład dodać, usunąć lub zmienić nazwę plików w zasobie) i przesłać dodatkowe zadania.
    * Jakość miniatur JPEG produkowanych przez standard koder jest lepsza.
    * Standardowy koder lepiej obsługuje metadane wejściowe i generowanie miniatur w bardzo krótkich filmach.
    * Ulepszenia dekodera H.264 używanego w koderze standardowym eliminują niektóre rzadkie artefakty. 

#### <a name="media-analytics"></a>Media Analytics
Ogólna dostępność usługi Azure Media Redactor: Ten procesor multimediów wykonuje anonimizację przez rozmycie twarzy wybranych osób i jest idealny do użycia w scenariuszach bezpieczeństwa publicznego i mediów informacyjnych. 

Aby zapoznać się z omówieniem tego nowego procesora, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/azure-media-redactor/). Aby uzyskać informacje na temat dokumentacji i ustawień, zobacz [Redagowanie twarzy za pomocą usługi Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Wydanie z czerwca 2017 r.

Usługa Media Services obsługuje teraz [uwierzytelnianie oparte na usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje model uwierzytelniania usługi kontroli dostępu. Autoryzacja usługi kontroli dostępu zostanie przestarzała 1 czerwca 2018 r. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="march-2017-release"></a>Wydanie z marca 2017 r.

Teraz można użyć standardowego kodera do [automatycznego generowania drabiny szybkości transmisji bitów,](media-services-autogen-bitrate-ladder-with-mes.md) określając ustawiony ciąg "Adaptive Streaming" podczas tworzenia zadania kodowania. Aby zakodować wideo do przesyłania strumieniowego za pomocą usługi Media Services, użyj predefiniowanego ustawienia "Adaptive Streaming". Aby dostosować ustawienie kodowania dla określonego scenariusza, można rozpocząć od [tych ustawień predefiniowanych](media-services-mes-presets-overview.md).

Teraz można użyć media encoder standard lub Media Encoder Premium Workflow do [utworzenia zadania kodowania, które generuje fragmenty fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Wydanie z lutego 2017 r.

Od 1 kwietnia 2017 r. każdy rekord zadania na koncie starszym niż 90 dni jest automatycznie usuwany wraz z powiązanymi rekordami zadań. Usunięcie następuje, nawet jeśli całkowita liczba rekordów jest niższa od maksymalnego przydziału. Aby zarchiwizować informacje o zadaniu/zadaniu, można użyć kodu opisanego w [programie Zarządzanie zasobami i encjami pokrewnych za pomocą programu Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Wydanie ze stycznia 2017 r.

W usłudze Media Services punkt końcowy przesyłania strumieniowego reprezentuje usługę przesyłania strumieniowego, która może dostarczać zawartość bezpośrednio do aplikacji odtwarzacza klienckiego lub do sieci dostarczania zawartości (CDN) w celu dalszej dystrybucji. Usługa Media Services zapewnia również bezproblemową integrację z siecią dostarczania zawartości platformy Azure. Strumień wychodzący z usługi StreamingEndpoint może być transmisją na żywo, wideo na żądanie lub progresywnym pobieraniem zasobu na koncie usługi Media Services. Każde konto usługi Media Services zawiera domyślny punkt końcowy przesyłania strumieniowego. Dodatkowe punkty końcowe przesyłania strumieniowego można utworzyć w ramach konta. 

Istnieją dwie wersje punktów końcowych przesyłania strumieniowego, 1.0 i 2.0. Od 10 stycznia 2017 r. wszystkie nowo utworzone konta usługi Media Services zawierają domyślny punkt końcowy przesyłania strumieniowego w wersji 2.0. Dodatkowe punkty końcowe przesyłania strumieniowego, które można dodać do tego konta są również w wersji 2.0. Ta zmiana nie ma wpływu na istniejące konta. Istniejące punkty końcowe przesyłania strumieniowego są w wersji 1.0 i można uaktualnić do wersji 2.0. Z tą zmianą występują zachowania, rozliczenia i funkcje. Aby uzyskać więcej informacji, zobacz [Streaming endpoints overview (Omówienie punktów końcowych przesyłania strumieniowego)](media-services-streaming-endpoints-overview.md).

Począwszy od wersji 2.15, usługa Media Services dodała następujące właściwości do jednostki punktu końcowego przesyłania strumieniowego:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime (Czas bezpłatnego użycia) 
* StreamingEndpointVersion 

Aby uzyskać więcej informacji na temat tych właściwości, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Wydanie z grudnia 2016 r.

 Teraz można użyć usługi Media Services, aby uzyskać dostęp do danych telemetrycznych/metryk dla swoich usług. Bieżącej wersji programu Media Services można używać do zbierania danych telemetrycznych dla kanałów na żywo, punktu końcowego przesyłania strumieniowego i encji archiwum. Aby uzyskać więcej informacji, zobacz [dane telemetryczne usługi Media Services](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a id="july_changes16"/>Wydanie z lipca 2016 r.
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aktualizacje pliku manifestu (*. ISM) generowane przez zadania kodowania
Gdy zadanie kodowania jest przesyłane do media encoder standard lub Media Encoder Premium, zadanie kodowania generuje [plik manifestu przesyłania strumieniowego](media-services-deliver-content-overview.md) (*.ism) w zasobie wyjściowym. W najnowszej wersji usługi składnia tego pliku manifestu przesyłania strumieniowego została zaktualizowana.

> [!NOTE]
> Składnia pliku manifestu przesyłania strumieniowego (.ism) jest zarezerwowana do użytku wewnętrznego. To może ulec zmianie w przyszłych wydaniach. Nie należy modyfikować ani manipulować zawartością tego pliku.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Nowy manifest klienta (*. ISMC) jest generowany w zasób wyjściowy, gdy zadanie kodowania wyprowadza jeden lub więcej plików MP4
Począwszy od najnowszej wersji usługi, po zakończeniu zadania kodowania, które generuje jeden lub więcej plików MP4, zasób wyjściowy zawiera również plik manifestu klienta przesyłania strumieniowego (*.ismc). Plik .ismc pomaga poprawić wydajność dynamicznego przesyłania strumieniowego. 

> [!NOTE]
> Składnia pliku manifestu klienta (.ismc) jest zarezerwowana do użytku wewnętrznego. To może ulec zmianie w przyszłych wydaniach. Nie należy modyfikować ani manipulować zawartością tego pliku.
> 
> 

Aby uzyskać więcej informacji, zobacz [ten blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Znane problemy
Niektórzy klienci mogą natknąć się na problem z powtórzeniem znacznika w manifeście Płynne przesyłanie strumieniowe. Aby uzyskać więcej informacji, zobacz [tę sekcję](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Wydanie z kwietnia 2016 r.
### <a name="media-analytics"></a>Media Analytics
 Usługi Media Services wprowadziły analizę multimedialną dla zaawansowanej analizy wideo. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Media Services Analytics](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (wersja zapoznawcza)
Teraz możesz korzystać z usługi Media Services do dynamicznego szyfrowania zawartości http live streaming (HLS) za pomocą apple FairPlay. Można również użyć usługi dostarczania licencji usługi Media Services, aby dostarczać licencje FairPlay klientom. Aby uzyskać więcej informacji, zobacz "Używanie usługi Azure Media Services do przesyłania strumieniowego zawartości HLS chronionej za pomocą usługi Apple FairPlay".

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Wydanie z lutego 2016 r.
Najnowsza wersja narzędzia Media Services SDK for .NET (3.5.3) zawiera poprawkę dotyczącą błędów związanych z google widevine. Nie można było ponownie użyć AssetDeliveryPolicy dla wielu zasobów zaszyfrowanych za pomocą Widevine. W ramach tej poprawki błędu do SDK dodano następującą właściwość: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Wydanie ze stycznia 2016 r.
Nazwa jednostek zarezerwowanych kodowania została zmieniona w celu zmniejszenia pomyłek z nazwami koderów.

Jednostki zarezerwowane kodowania Basic, Standard i Premium zostały zmienione odpowiednio na jednostki zarezerwowane S1, S2 i S3. Klienci, którzy używają podstawowych jednostek zarezerwowanych kodowania dzisiaj zobaczyć S1 jako etykietę w witrynie Azure portal (i na rachunku). Klienci korzystający ze standardów i premium zobaczą odpowiednio etykiety S2 i S3. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Wydanie z grudnia 2015 r.

### <a name="media-encoder-deprecation-announcement"></a>Ogłoszenie o usunięciu kodera mediów

 Koder multimediów zostanie przestarzały, począwszy od około 12 miesięcy od wydania programu Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Zestaw Azure SDK dla środowiska PHP
Zespół zestawu SDK platformy Azure opublikował nową wersję pakietu [Zestawu SDK platformy Azure dla PHP,](https://github.com/Azure/azure-sdk-for-php) który zawiera aktualizacje i nowe funkcje dla usługi Media Services. W szczególności zestaw SDK usługi media services dla PHP obsługuje teraz najnowsze funkcje [ochrony zawartości.](media-services-content-protection-overview.md) Funkcje te są szyfrowanie dynamiczne z AES i DRM (PlayReady i Widevine) z i bez ograniczeń tokenu. Obsługuje również [jednostki kodowania skalowania](media-services-dotnet-encoding-units.md).

Aby uzyskać więcej informacji, zobacz:

* Poniższe [przykłady kodu](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) ułatwią szybkie rozpoczęcie pracy:
  * **vodworkflow_aes.php**: Ten plik PHP pokazuje, jak korzystać z szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy. Jest on oparty na przykładzie .NET wyjaśnionym w [użyj szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy.](media-services-protect-with-aes128.md)
  * **vodworkflow_aes.php**: Ten plik PHP pokazuje, jak korzystać z dynamicznego szyfrowania PlayReady i usługi dostarczania licencji. Jest on oparty na próbce .NET wyjaśnione w [Użyj PlayReady i / lub Widevine dynamiczne wspólne szyfrowanie](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Ten plik PHP pokazuje, jak skalować kodowanie zastrzeżonych jednostek.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Wydanie z listopada 2015 r.
 Usługa Media Services oferuje teraz usługę dostarczania licencji Widevine w chmurze. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Zobacz także [ten samouczek](media-services-protect-with-playready-widevine.md) i [repozytorium GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Usługi dostarczania licencji Widevine świadczone przez program Media Services są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Wydanie z października 2015 r.
Usługa Media Services działa obecnie w następujących centrach danych: Brazylia Południowa, Indie Zachodnie, Indie Południowe i Indie Środkowe. Teraz można użyć portalu Azure do [tworzenia kont usługi Media Service](media-services-portal-create-account.md) i wykonywania różnych zadań opisanych na stronie internetowej dokumentacji usługi Media [Services](https://azure.microsoft.com/documentation/services/media-services/). Kodowanie na żywo nie jest włączone w tych centrach danych. Ponadto nie wszystkie typy jednostek zarezerwowanych kodowania są dostępne w tych centrach danych.

* Brazylia Południowa: Dostępne są tylko standardowe i podstawowe jednostki zarezerwowane kodowania.
* Indie Zachodnie, Indie Południowe i Indie Środkowe: Dostępne są tylko podstawowe jednostki zarezerwowane do kodowania.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Wydanie z września 2015 r.
Usługa Media Services oferuje teraz możliwość ochrony zarówno wideo na żądanie, jak i transmisji na żywo dzięki modułowej technologii DRM Widevine. Aby pomóc w dostarczaniu licencji Widevine, można skorzystać z następujących partnerów świadczących usługi dostawy:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Aby skorzystać z usługi Widevine, można skonfigurować obiekt AssetDeliveryConfiguration przy użyciu zestawu [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (począwszy od wersji 3.5.1) lub interfejsu API REST. 
* Usługa Media Services dodała obsługę filmów Apple ProRes. Teraz możesz przesyłać pliki źródłowe filmów quicktime, które używają Apple ProRes lub innych kodeków. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Teraz można używać media encoder standard do wykonywania podklunia i wyodrębniania archiwum na żywo. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Wprowadzono następujące aktualizacje filtrowania: 
  
  * Teraz możesz używać formatu Apple HLS z filtrem tylko audio. Za pomocą tej aktualizacji można usunąć ścieżkę tylko do dźwięku, określając (tylko dźwięk = fałsz) w adresie URL.
  * Podczas definiowania filtrów zasobów można teraz połączyć wiele (maksymalnie trzech) filtrów w jednym adresie URL.
    
    Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Usługi Media Services obsługują teraz ramki I w wersji HLS w wersji 4. Obsługa i-frame optymalizuje operacje przewijania do przodu i do tyłu. Domyślnie wszystkie wyjścia HLS w wersji 4 zawierają listę odtwarzania I-frame (EXT-X-I-FRAME-STREAM-INF).
Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Wydanie z sierpnia 2015 r.
* Zestaw SDK usług multimedialnych dla wersji Java w wersji 0.8.0 i nowe przykłady są teraz dostępne. Aby uzyskać więcej informacji, zobacz:
    
* Program Azure Media Player został zaktualizowany o obsługę strumienia wielu audio. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Wydanie z lipca 2015 r.
* Ogłoszono ogólną dostępność media encoder standard. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard używa ustawień wstępnych, zgodnie z opisem w [tej sekcji](https://go.microsoft.com/fwlink/?LinkId=618336). Jeśli używasz ustawienia wstępnego dla kodów 4K, pobierz typ jednostki zarezerwowanej premium. Aby uzyskać więcej informacji, zobacz [Skalowanie kodowania](media-services-scale-media-processing-overview.md).
* Napisy w czasie rzeczywistym na żywo były używane z usługami Media Services i media playerem. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje sdk usługi Media Services .NET
SDK usługi Media Services .NET jest teraz w wersji 3.4.0.0. Wprowadzono następujące aktualizacje: 

* Wsparcie zostało zaimplementowane dla archiwum na żywo. Nie można pobrać zasobu zawierającego archiwum na żywo.
* Obsługa została zaimplementowana dla filtrów dynamicznych.
* Zaimplementowano funkcjonalność, dzięki czemu użytkownicy mogą przechowywać kontener magazynu podczas usuwania zasobu.
* Poprawki błędów zostały wprowadzone związane z zasadami ponawiania prób w kanałach.
* Włączono przepływ pracy usługi Media Encoder Premium.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Wydanie z czerwca 2015 r.
### <a name="media-services-net-sdk-updates"></a>Aktualizacje sdk usługi Media Services .NET
SDK usługi Media Services .NET jest teraz w wersji 3.3.0.0. Wprowadzono następujące aktualizacje: 

* Dodano obsługę specyfikacji odnajdywania OpenId Connect.
* Dodano obsługę obsługi przerzucania kluczy po stronie dostawcy tożsamości.

Jeśli używasz dostawcy tożsamości, który udostępnia dokument odnajdywania OpenID Connect (jak Azure AD, Google i Salesforce zrobić), można poinstruować Media Services, aby uzyskać klucze podpisywania do sprawdzania poprawności tokenów JSON Web Tokens (JWTs) ze specyfikacji odnajdywania OpenID Connect. 

Aby uzyskać więcej informacji, zobacz [Używanie kluczy sieci Web JSON ze specyfikacji odnajdywania OpenID Connect do pracy z uwierzytelnianiem JWT w umiań mediach .](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Wydanie z maja 2015 r.
Ogłoszono następujące nowe funkcje:

* [Podgląd kodowania na żywo za pomocą usługi Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Manifest dynamiczny](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Wydanie z kwietnia 2015 r.
### <a name="general-media-services-updates"></a>Aktualizacje usługi General Media Services
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) został ogłoszony.
* Począwszy od usługi Media Services REST 2.10, kanały skonfigurowane do pozyskiwania protokołu RTMP (Real-Time Messaging Protocol) są tworzone z podstawowymi i pomocniczymi adresami URL pozyskiwania. Aby uzyskać więcej informacji, zobacz [Konfiguracje pozyskiwania kanałów](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Usługa Azure Media Indexer została zaktualizowana.
* Dodano obsługę języka hiszpańskiego.
* Dodano nową konfigurację formatu XML.

Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje sdk usługi Media Services .NET
SDK usługi Media Services .NET jest teraz w wersji 3.2.0.0. Wprowadzono następujące aktualizacje:

* Zmiana podziału: TokenRestrictionTemplate.Issuer i TokenRestrictionTemplate.Audience zostały zmienione na typu ciągu.
* Wprowadzono aktualizacje związane z tworzeniem niestandardowych zasad ponawiania prób.
* Poprawki zostały wprowadzone związane z przesyłaniem i pobieraniem plików.
* Klasa MediaServicesCredentials akceptuje teraz podstawowe i pomocnicze punkty końcowe kontroli dostępu do uwierzytelnienia.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Wydanie z marca 2015 r.
### <a name="general-media-services-updates"></a>Aktualizacje usługi General Media Services
* Usługi media zapewniają teraz integrację z siecią dostarczania zawartości. Aby obsługiwać integracji, CdnEnabled właściwość została dodana do StreamingEndpoint. CdnEnabled może być używany z interfejsami API REST, począwszy od wersji 2.9. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled może być używany z .NET SDK począwszy od wersji 3.1.0.2. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Ogłoszono przepływ pracy Media Encoder Premium. Aby uzyskać więcej informacji, zobacz [Wprowadzenie kodowania premium w usłudze Azure Media Services.](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Wydanie z lutego 2015 r.
### <a name="general-media-services-updates"></a>Aktualizacje usługi General Media Services
Interfejs API REST usługi Media Services jest teraz w wersji 2.9. Począwszy od tej wersji, można włączyć integrację sieci dostarczania zawartości z punktami końcowymi przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Wydanie ze stycznia 2015 r.
### <a name="general-media-services-updates"></a>Aktualizacje usługi General Media Services
Ogłoszono ogólną dostępność ochrony zawartości z szyfrowaniem dynamicznym. Aby uzyskać więcej informacji, zobacz [Usługa Media Services zwiększa bezpieczeństwo przesyłania strumieniowego dzięki ogólnej dostępności technologii DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje sdk usługi Media Services .NET
SDK usługi Media Services .NET jest teraz w wersji 3.1.0.1.

W tej wersji oznaczył domyślny konstruktor microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate jako przestarzały. Nowy konstruktor przyjmuje TokenType jako argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Wydanie z grudnia 2014 r.
### <a name="general-media-services-updates"></a>Aktualizacje usługi General Media Services
* Niektóre aktualizacje i nowe funkcje zostały dodane do indeksatora multimediów. Aby uzyskać więcej informacji, zobacz [informacje o wersji programu Azure Media Indexer w wersji 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Dodano nowy interfejs API REST, którego można użyć do aktualizacji jednostek zarezerwowanych kodowania. Aby uzyskać więcej informacji, zobacz [EncodingReservedUnitType with REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Dodano obsługę CORS dla usługi dostarczania kluczy.
* Wprowadzono ulepszenia wydajności do wykonywania zapytań o opcje zasad autoryzacji.
* W centrum danych w Chinach [adres URL dostarczania klucza](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) jest teraz na klienta (podobnie jak w innych centrach danych).
* Dodano czas trwania automatycznego celu HLS. Podczas przesyłania strumieniowego na żywo HLS jest zawsze pakowany dynamicznie. Domyślnie usługa Media Services automatycznie oblicza współczynnik pakowania segmentu HLS (FragmentsPerSegment) na podstawie interwału klatki kluczowej (KeyFrameInterval). Ta metoda jest również określana jako grupa obrazów (GOP), która jest odbierana z kodera na żywo. Aby uzyskać więcej informacji, zobacz [Praca z usługą Media Services przesyłanie strumieniowe na żywo](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Aktualizacje sdk usługi Media Services .NET
[SDK usługi Media Services .NET](https://www.nuget.org/packages/windowsazure.mediaservices/) jest teraz w wersji 3.1.0.0. Wprowadzono następujące aktualizacje:

* Zależność sdk .NET została uaktualniona do platformy .NET 4.5 Framework.
* Dodano nowy interfejs API, którego można użyć do aktualizacji jednostek zarezerwowanych kodowania. Aby uzyskać więcej informacji, zobacz [Aktualizowanie typu jednostki zarezerwowanej i zwiększanie kodowania jednostek zarezerwowanych przy użyciu programu .NET](media-services-dotnet-encoding-units.md).
* Dodano obsługę JWT dla uwierzytelniania tokenu. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie tokenów JWT w umiaźniku i szyfrowanie dynamiczne](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Dodano względne przesunięcia dla BeginDate i ExpirationDate w szablonie licencji PlayReady.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Wydanie z listopada 2014 r.
* Teraz można użyć usługi Media Services do pozyskiwania zawartości płynnego przesyłania strumieniowego na żywo (fMP4) za pomocą połączenia TLS. Aby nalecieć za pośrednictwem protokołu TLS, należy zaktualizować adres URL pozyskiwania do protokołu HTTPS. Obecnie usługa Media Services nie obsługuje protokołu TLS z domenami niestandardowymi. Aby uzyskać więcej informacji na temat przesyłania strumieniowego na żywo, zobacz [Praca z usługą Azure Media Services przesyłanie strumieniowe na żywo](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Obecnie nie można pochłonieć strumienia na żywo RTMP za połączenie TLS.
* Można przesyłać strumieniowo za pomocą protokołu TLS tylko wtedy, gdy punkt końcowy przesyłania strumieniowego, z którego dostarczasz zawartość została utworzona po 10 września 2014 r. Jeśli adresy URL przesyłania strumieniowego są oparte na punktach końcowych przesyłania strumieniowego utworzonych po 10 września 2014 r., adres URL zawiera "streaming.mediaservices.windows.net" (nowy format). Adresy URL przesyłania strumieniowego zawierające "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu TLS. Jeśli adres URL jest w starym formacie i chcesz przesyłać strumieniowo za pomocą protokołu TLS, [utwórz nowy punkt końcowy przesyłania strumieniowego](media-services-portal-manage-streaming-endpoints.md). Aby przesyłać strumieniowo zawartość za pomocą protokołu TLS, użyj adresów URL opartych na nowym punkcie końcowym przesyłania strumieniowego.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Zestaw .NET SDK usługi Media Services
SDK usługi Media Services dla rozszerzeń .NET jest teraz w wersji 2.0.0.3.

SDK usługi media services dla platformy .NET jest teraz w wersji 3.0.0.8. Wprowadzono następujące aktualizacje:

* Refaktoryzowanie zostało zaimplementowane w klasach zasad ponawiania próby.
* Ciąg agenta użytkownika został dodany do nagłówków żądań HTTP.
* Dodano krok kompilacji przywracania NuGet.
* Testy scenariusza zostały naprawione w celu użycia certyfikatu x509 z repozytorium.
* Ustawienia sprawdzania poprawności zostały dodane podczas aktualizacji zakończenia kanału i przesyłania strumieniowego.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nowe repozytorium GitHub do obsługi przykładów usługi Media Services
Przykłady znajdują się w [przykładowym repozytorium Usługi Media Services GitHub](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Wydanie z września 2014 r.
Metadane USŁUGI MEDIA SERVICES REST są teraz w wersji 2.7. Aby uzyskać więcej informacji na temat najnowszych aktualizacji REST, zobacz [odwołanie do interfejsu API REST usługi Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

SDK usługi media services dla platformy .NET jest teraz w wersji 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Fundamentalne zmiany
* Nazwa origin została zmieniona na [StreamingEndpoint].
* Zmiana została włączona do zachowania domyślnego podczas używania witryny Azure Portal do kodowania, a następnie publikowania plików MP4.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nowe funkcje/scenariusze, które są częścią ogólnej wersji dostępności
* Wprowadzono procesor multimediów Media Indexer. Aby uzyskać więcej informacji, zobacz [Indeks plików multimedialnych za pomocą indeksatora multimediów](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Za pomocą jednostki [StreamingEndpoint] można dodawać nazwy domen niestandardowych (hosta).
  
    Aby użyć niestandardowej nazwy domeny jako nazwy punktu końcowego przesyłania strumieniowego usługi Media Services, dodaj niestandardowe nazwy hostów do punktu końcowego przesyłania strumieniowego. Dodawanie niestandardowych nazw hostów za pomocą interfejsów API REST usługi Media Services lub sdk .NET.
  
    Obowiązują następujące zastrzeżenia:
  
  * Musisz mieć własność niestandardowej nazwy domeny.
  * Własność nazwy domeny musi zostać zweryfikowana przez program Media Services. Aby sprawdzić poprawność domeny, należy utworzyć nazwę CName mapu, która mapuje domenę nadrzędną MediaServicesAccountId w celu zweryfikowania strefy media-dns usługi DNS DNS.
  * Należy utworzyć inną nazwę CName, która mapuje niestandardową nazwę hosta (na przykład sports.contoso.com) na nazwę hosta usługi Media Services StreamingEndpoint (na przykład amstest.streaming.mediaservices.windows.net).

    Aby uzyskać więcej informacji, zobacz CustomHostNames właściwości w [streamingendpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) artykułu.

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nowe funkcje/scenariusze, które są częścią publicznej wersji zapoznawczej
* Podgląd transmisji na żywo. Aby uzyskać więcej informacji, zobacz [Praca z usługą Media Services przesyłanie strumieniowe na żywo](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Usługa dostarczania kluczy. Aby uzyskać więcej informacji, zobacz [Korzystanie z szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Szyfrowanie dynamiczne AES. Aby uzyskać więcej informacji, zobacz [Korzystanie z szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* Usługa dostarczania licencji PlayReady. 
* PlayReady szyfrowanie dynamiczne. 
* Szablon licencji Programu Media Services PlayReady. Aby uzyskać więcej informacji, zobacz [omówienie szablonu licencji Programu Media Services PlayReady].
* Przesyłaj strumieniowo zasoby zaszyfrowane magazynem. Aby uzyskać więcej informacji, zobacz [Przesyłanie strumieniowe zawartości zaszyfrowanej magazynem](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Wydanie z sierpnia 2014 r.
Podczas kodowania zasobu zasób wyjściowy jest wytwarzany po zakończeniu zadania kodowania. Do tej wersji koder usługi Media Services produkowane metadane dotyczące zasobów wyjściowych. Począwszy od tej wersji koder tworzy również metadane dotyczące zasobów wejściowych. Aby uzyskać więcej informacji, zobacz [Metadane wejściowe] i [Metadane wyjściowe].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Wydanie z lipca 2014 r.
Następujące poprawki błędów zostały wprowadzone dla pakietu Azure Media Services Packager i Encryptor:

* Gdy zasób archiwum na żywo jest przesyłany do HLS, odtwarzany jest tylko dźwięk: ten problem został rozwiązany, a teraz można odtwarzać zarówno dźwięk, jak i wideo.
* Gdy zasób jest pakowany do szyfrowania kopert HLS i AES 128-bitowych, spakowane strumienie nie są odtwarzane na urządzeniach z systemem Android: ten błąd został naprawiony, a spakowany strumień jest odtwarzany na urządzeniach z systemem Android obsługujących HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Wydanie z maja 2014 r.
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Aktualizacje usługi General Media Services
Teraz można używać [dynamicznego opakowania] do strumieniowego przesyłania HLS w wersji 3. Aby przesyłać strumieniowo HLS w wersji 3, dodaj następujący format do ścieżki lokalizatora początku: * .ism/manifest(format=m3u8-aapl-v3). Aby uzyskać więcej informacji, zobacz [to forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamiczne opakowanie obsługuje teraz również dostarczanie HLS (wersja 3 i wersja 4) szyfrowane za pomocą PlayReady na podstawie Smooth Streaming statycznie zaszyfrowane z PlayReady. Aby uzyskać informacje na temat szyfrowania płynnego przesyłania strumieniowego za pomocą playready, zobacz [Ochrona płynnego przesyłania strumieniowego za pomocą playready](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Aktualizacje sdk usługi Media Services .NET
SDK usługi Media Services .NET jest teraz w wersji 3.0.0.5. Wprowadzono następujące aktualizacje:

* Szybkość i odporność są lepsze podczas przesyłania i pobierania zasobów multimedialnych.
* Wprowadzono ulepszenia w logice ponawiania prób i obsługi wyjątków przejściowych: 
  
  * Udoskonalono logikę wykrywania błędów przejściowych i ponawiania prób dla wyjątków, które są spowodowane podczas wykonywania zapytań, zapisywania zmian oraz przekazywania lub pobierania plików. 
  * Po wprowadzeniu wyjątków sieci web (na przykład podczas żądania tokenu usługi kontroli dostępu), błędy krytyczne nie powiedzie się teraz szybciej.

Aby uzyskać więcej informacji, zobacz [Ponowić ponawianie logiki w sdk usług multimedialnych dla platformy .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Wersje styczeń/luty 2014 r.
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Usługi multimedialne .NET SDK 3.0.0.1, 3.0.0.2 i 3.0.0.3
Zmiany w 3.0.0.1 i 3.0.0.2 obejmują:

* Naprawiono problemy związane z użyciem zapytań LINQ z instrukcjami OrderBy.
* Rozwiązania testowe w [usłudze GitHub] zostały podzielone na testy jednostkowe i testy oparte na scenariuszach.

Aby uzyskać więcej informacji na temat zmian, zobacz [media services .NET SDK 3.0.0.1 i 3.0.0.2 wersje](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

W wersji 3.0.0.3 wprowadzono następujące zmiany:

* Zależności magazynu platformy Azure zostały uaktualnione do wersji 3.0.3.0.
* Naprawiono błąd ze zgodnością z przeszytem dla 3.0. *.* Zwalnia.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Wydanie z grudnia 2013 r.
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> Wersje 3.0.x.x nie są wstecznie kompatybilne z wersjami 2.4.x.
> 
> 

Najnowsza wersja sdk usługi Media Services jest teraz 3.0.0.0. Możesz pobrać najnowszy pakiet z NuGet lub pobrać bity z [GitHub].

Począwszy od zestawu SDK usługi Media Services w wersji 3.0.0.0, można ponownie użyć tokenów [usługi Azure AD Access Control Service.](https://msdn.microsoft.com/library/hh147631.aspx) Aby uzyskać więcej informacji, zobacz sekcję "Ponowne używanie tokenów usługi kontroli dostępu" w [programie Łączenie się z usługami multimedialnymi przy pomocy zestawu SDK usług multimedialnych dla platformy .NET](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Rozszerzenia SDK usługi media.NET 2.0.0.0
 Rozszerzenia zestawu SDK usługi Media Services .NET to zestaw metod rozszerzenia i funkcji pomocniczych, które upraszczają kod i ułatwiają tworzenie za pomocą usługi Media Services. Najnowsze bity można uzyskać z [rozszerzeń SDK usługi Media Services .NET](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Wydanie z listopada 2013 r.
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Zmiany w sdku w usługach multimedialnych .NET
Począwszy od tej wersji, SDK usługi Media Services dla platformy .NET obsługuje błędy błędów przejściowych, które mogą wystąpić podczas wywołania do warstwy interfejsu API REST usługi Media Services.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Wydanie z sierpnia 2013 r.
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Polecenia cmdlet programu Media Services PowerShell zawarte w narzędziach zestawu SDK platformy Azure
Następujące polecenia cmdlet programu Media Services PowerShell są teraz zawarte w [narzędziach zestawu SDK platformy Azure:](https://github.com/Azure/azure-sdk-tools)

* Usługi Get-AzureMediaServices 

    Na przykład: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Na przykład: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Na przykład: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Na przykład: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Wydanie z czerwca 2013 r.
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Zmiany w usługach multimedialnych
Następujące zmiany wymienione w tej sekcji są aktualizacje zawarte w czerwcu 2013 Media Services wydań:

* Możliwość łączenia wielu kont magazynu z kontem usługi Media Service. 
    * StorageAccount
    * Asset.StorageName i Asset.StorageKontaks
* Możliwość aktualizacji Job.Priority. 
* Jednostki i właściwości związane z powiadomieniami: 
    * JobNotificationSubscription (Opis z ofertą pracy)
    * NotificationEndPoint (Punkt powiadomienia)
    * Zadanie
* Asset.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Zmiany w sdku w usługach multimedialnych .NET
Następujące zmiany są uwzględniane w wersjach SDK usługi Media Services z czerwca 2013 r. Najnowszy sdk usługi media services jest dostępny w usłudze GitHub.

* Począwszy od wersji 2.3.0.0 zestaw SDK usługi Media Services obsługuje łączenie wielu kont magazynu z kontem usługi Media Services. Następująca funkcja obsługuje interfejsy API:
  
    * Typ konta IStorageAccount
    * Właściwość Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Właściwość StorageAccount
    * Właściwość StorageAccountName
  
      Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami usługi Media Services na wielu kontach magazynu](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Interfejsy API związane z powiadomieniami. Począwszy od wersji 2.2.0.0, można nasłuchiwać powiadomień usługi Azure Queue storage. Aby uzyskać więcej informacji, zobacz [Obsługa powiadomień o zadaniach usługi Media Services](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Właściwość Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Typ microsoft.windowsazure.mediaservices.client.INotificationEndPoint
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription typ
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection typ
    * Typ typu Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Zależność od sdk 2.0 klienta magazynu (Microsoft.WindowsAzure.StorageClient.dll)
* Zależność od pliku OData 5.5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Wydanie z grudnia 2012 r.
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Zmiany w sdku w usługach multimedialnych .NET
* IntelliSense: Brak dokumentacji IntelliSense został dodany dla wielu typów.
* Microsoft.Practices.TransientFaultHandling.Core: Rozwiązano problem polegający na tym, że zestaw SDK nadal miał zależność od starej wersji tego zestawu. Zestaw SDK odwołuje się teraz do wersji 5.1.1209.1 tego zestawu.

Poprawki dotyczące problemów znalezionych w sdk z listopada 2012:

* IAsset.Locators.Count: Ta liczba jest teraz poprawnie raportowana w nowych interfejsach IAsset po usunięciu wszystkich lokalizatorów.
* IAssetFile.ContentFileSize: Ta wartość jest teraz poprawnie ustawiona po przesłaniu przez iAssetFile.Upload(ścieżka pliku).
* IAssetFile.ContentFileSize: Tę właściwość można teraz ustawić podczas tworzenia pliku zasobu. Wcześniej był tylko do odczytu.
* IAssetFile.Upload(ścieżka pliku): Rozwiązano problem polegający na tym, że ta synchroniczne metody przekazywania zgłaszała następujący błąd podczas przekazywania wielu plików do zasobu. Wystąpił błąd "Uwierzytelnienie żądania przez serwer nie powiodło się. Upewnij się, że wartość nagłówka Autoryzacja jest poprawnie utworzona wraz z podpisem."
* IAssetFile.UploadAsync: Rozwiązano problem, który ograniczał jednoczesne przekazywanie plików do pięciu plików.
* IAssetFile.UploadProgressZanged: To zdarzenie jest teraz dostarczane przez zestaw SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Ta metoda przeciążenia jest teraz podana.
* IAssetFile.DownloadAsync: Naprawiono problem, który ograniczał jednoczesne pobieranie plików do pięciu plików.
* IAssetFile.Delete(): Rozwiązano problem polegający na tym, że wywołanie delete może zgłosić wyjątek, jeśli nie został przekazany żaden plik dla pliku IAsset.
* Zadania: Rozwiązano problem polegający na tym, że tworzenie łańcucha "zadania MP4 do płynnych strumieni" za pomocą zadania "Ochrona PlayReady" przy użyciu szablonu zadania nie tworzyło żadnych zadań.
* EncryptionUtils.GetCertificateFromStore(): Ta metoda nie zgłasza już wyjątku odwołania null z powodu niepowodzenia w znalezieniu certyfikatu na podstawie problemów z konfiguracją certyfikatu.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Wydanie z listopada 2012 r.
Zmiany wymienione w tej sekcji były aktualizacje zawarte w listopadzie 2012 (wersja 2.0.0.0) SDK. Te zmiany mogą wymagać zmodyfikowanego lub przepisanego kodu napisanego dla wersji SDK w wersji zapoznawczej z czerwca 2012 r.

* Elementy zawartości
  
    * IAsset.Create(assetName) jest *jedyną* funkcją tworzenia zasobów. IAsset.Create nie przekazuje już plików w ramach wywołania metody. Użyj IAssetFile do przekazywania.
    * Metoda IAsset.Publish i AssetState.Publish wartość wyliczenia zostały usunięte z zestawu SDK usług. Każdy kod, który opiera się na tej wartości musi być przepisany.
* Fileinfo
  
    * Ta klasa została usunięta i zastąpiona przez IAssetFile.
  
* Pliki IAsset
  
    * IAssetFile zastępuje FileInfo i ma inne zachowanie. Aby go użyć, wystąpienia obiektu IAssetFiles, a następnie przekazywania pliku przy użyciu zestawu SDK usług multimedialnych lub zestawu SDK magazynu. Można użyć następujących przeciążeń IAssetFile.Upload:
  
        * IAssetFile.Upload(filePath): Ta metoda synchroniczne blokuje wątek i zaleca się tylko podczas przekazywania pojedynczego pliku.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, lokalizator, cancellationToken): Ta metoda asynchroniczna jest preferowanym mechanizmem przekazywania. 
    
            Znany błąd: Jeśli używasz tokenu anulowania, przekazywanie zostanie anulowane. Zadania mogą mieć wiele stanów anulowania. Należy prawidłowo złapać i obsługiwać wyjątki.
* Locators
  
    * Usunięto wersje specyficzne dla pochodzenia. Kontekst specyficzny dla sygnatury dostępu Współdzielonego. Lokalizatory.CreateSasLocator (zasób, accessPolicy) zostaną oznaczone przestarzałe lub usunięte przez ogólną dostępność. Aby uzyskać zaktualizowane zachowanie, zobacz sekcję "Lokalizatory" w sekcji "Nowe funkcje".

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Wersja zapoznawcza z czerwca 2012 r.
Następujące funkcje zostały nowe w listopadowej wersji SDK:

* Usuwanie encji
  
    * Obiekty IAsset, IAssetFile, ILocator, IAccessPolicy i IContentKey są teraz usuwane na poziomie obiektu, czyli IObject.Delete(), zamiast wymagać usunięcia w kolekcji, czyli cloudMediaContext.ObjCollection.Delete(objInstance).
* Locators
  
    * Lokalizatory teraz muszą być tworzone przy użyciu CreateLocator metody. Muszą używać wartości wyliczenia LocatorType.SAS lub LocatorType.OnDemandOrigin jako argumentu dla określonego typu lokalizatora, który chcesz utworzyć.
    * Nowe właściwości zostały dodane do lokalizatorów, aby ułatwić uzyskanie użytecznych identyfikatorów URI dla zawartości. To przeprojektowanie lokalizatorów zapewnia większą elastyczność dla przyszłej rozszerzalności innych firm i zwiększa łatwość użycia w aplikacjach klienckich multimediów.
* Obsługa metod asynchronicznych
  
    * Asynchronii wsparcie został dodany do wszystkich metod.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN Forum]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Odwołanie interfejsu API REST usługi Azure Media Services]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Metadane wejściowe]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadane wyjściowe]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[Punkt przesyłania strumieniowego]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Omówienie szablonu licencji PlayReady usługi Media Services]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dynamiczne pakowanie]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[Logika ponawiania próby w sdku usług multimedialnych dla platformy .NET]: https://msdn.microsoft.com/library/azure/dn745650.aspx
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

