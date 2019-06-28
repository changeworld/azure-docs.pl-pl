---
title: Skonfiguruj swoje konto w trybie offline do przesyłania strumieniowego zawartości Widevine chronionych - Azure
description: W tym temacie pokazano, jak skonfigurować konto usługi Azure Media Services do przesyłania strumieniowego w trybie offline z Widevine chronione zawartości.
services: media-services
keywords: Android trybu Offline, ExoPlayer, Widevine DRM, myślnik
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 9e90951f810c5101a46c29570af8ad71b42be637
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341031"
---
# <a name="offline-widevine-streaming-for-android"></a>Widevine w trybie offline, przesyłania strumieniowego dla systemu Android

Oprócz ochrony zawartości online do przesyłania strumieniowego, multimediów zawartości subskrypcji i dzierżawa usługi oferty do pobrania zawartości, która działa, gdy nie są połączone z Internetem. Może być konieczne pobrać zawartość na telefonie lub tablecie do odtwarzania w tryb samolotowy, gdy pod odłączony od sieci. Dodatkowe scenariusze, w których możesz chcieć pobierania zawartości:

- Niektórzy dostawcy zawartości może nie zezwalaj na dostarczanie licencji DRM poza krawędź kraj/region. Jeśli użytkownik chce, aby obejrzeć zawartość podczas podróży granicą, pobierania w trybie offline jest konieczne.
- W niektórych krajach/regionach jest ograniczona dostępność internetowych i/lub przepustowości. Użytkownicy mogą zdecydować się na pobieranie zawartości, aby móc obejrzeć go na tyle wysokiej rozdzielczości dla środowiska oglądania zadowalające.

W tym artykule omówiono sposób implementacji w trybie offline odtwarzanie zawartości DASH chronione przez Widevine na urządzeniach z systemem Android. W trybie offline DRM pozwala na dostarczenie subskrypcję, wypożyczeń oraz zakupów modeli dla zawartości, dzięki czemu klienci usług mogą łatwo pobrać zawartość, która z nich podczas połączenia z Internetem.

Do tworzenia aplikacji odtwarzacza systemu Android, firma Microsoft przedstawiają trzy opcje:

> [!div class="checklist"]
> * Jak utworzyć odtwarzacz przy użyciu interfejsu API języka Java SDK ExoPlayer
> * Jak utworzyć odtwarzacz za pomocą powiązań Xamarin zestawu ExoPlayer SDK
> * Jak utworzyć odtwarzacz przy użyciu rozszerzenia zaszyfrowanych multimediów (EME) i rozszerzenie źródło nośnika (MSE) w v62 przenośnych przeglądarki Chrome lub nowszym

Artykuł zawiera również odpowiedzi na często zadawane pytania dotyczące transmisji strumieniowej w trybie offline zawartości Widevine chronione na.

> [!NOTE]
> DRM w trybie offline jest rozliczane tylko składania pojedyncze żądanie licencji, podczas pobierania zawartości. Wszelkie błędy nie są już naliczane.

## <a name="prerequisites"></a>Wymagania wstępne 

Przed wdrożeniem dla Widevine DRM w trybie offline, na urządzeniach z systemem Android, należy najpierw:

- Zapoznanie się z pojęciami opisanymi ochrony w trybie online zawartości przy użyciu technologii Widevine DRM. To zagadnienie opisano szczegółowo w następujących dokumentach/samples:
    - [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)
    - [Użyj DRM dynamiczne szyfrowanie i licencji usługi dostarczania](protect-with-drm.md)
- Klonuj https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Konieczne będzie modyfikować kodu w [szyfrowania przy użyciu DRM, przy użyciu platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) można dodać konfiguracji Widevine.  
- Zapoznanie się z zestawu SDK ExoPlayer Google dla systemów Android, typu open-source odtwarzacza wideo zestaw SDK może obsłużyć w trybie offline odtwarzania Widevine DRM. 
    - [Zestaw SDK ExoPlayer](https://github.com/google/ExoPlayer)
    - [Przewodnik dla deweloperów ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog deweloperów EoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurowanie ochrony zawartości w usłudze Azure Media Services

W [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metody znajdują się następujące etapy:

1. Określ sposób dostarczania klucza jest autoryzowany w usługi dostarczania licencji zawartości: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Konfigurowanie szablonu licencji Widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Create ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Włącz tryb offline

Aby włączyć **offline** tryb licencji Widevine, musisz skonfigurować [szablonu licencji Widevine](widevine-license-template-overview.md). W **policy_overrides** obiektu, należy ustawić **can_persist** właściwości **true** (domyślna to false), jak pokazano na [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurowanie odtwarzacza systemu Android do odtwarzania w trybie offline

Najprostszym sposobem na opracowywanie aplikacji odtwarzacza dla urządzeń z systemem Android jest użycie [zestaw SDK Google ExoPlayer](https://github.com/google/ExoPlayer), typu open-source odtwarzacza wideo zestawu SDK. ExoPlayer obsługuje funkcje, które nie są obecnie obsługiwane przez systemu Android native MediaPlayer interfejs API, w tym między innymi MPEG-DASH i Smooth Streaming protokołów dostarczania.

ExoPlayer wersji 2.6 lub nowszej zawiera wiele klas, które obsługują offline odtwarzania Widevine DRM. W szczególności klasy OfflineLicenseHelper udostępnia funkcje narzędzia ułatwiające użytkowania DefaultDrmSessionManager pobierania, odnowienia i zwalniania licencji w trybie offline. Klas dostarczonych z folderu zestawu SDK "biblioteki/core/src/main/java/com/google/android/exoplayer2/offline /" pomocy technicznej w trybie offline wideo pobierania zawartości.

Poniższa lista klas ułatwia trybu offline w zestawie SDK ExoPlayer dla systemu Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Deweloperzy powinny odwoływać się [przewodnik dla deweloperów ExoPlayer](https://google.github.io/ExoPlayer/guide.html) i odpowiedni [Blog deweloperów](https://medium.com/google-exoplayer) podczas tworzenia aplikacji. Google nie opublikowała kod odwołania w pełni udokumentowane implementacji lub przykładowej aplikacji ExoPlayer wspieranie Widevine w trybie offline w tej chwili, więc informacje są ograniczone do przewodnika i blog przez deweloperów. 

### <a name="working-with-older-android-devices"></a>Praca z starsze urządzenia z systemem Android

Niektóre starsze urządzenia z systemem Android, należy ustawić wartości dla następujących **policy_overrides** właściwości (zdefiniowane w [szablonu licencji Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, i **license_duration_seconds**. Alternatywnie można je ustawić na zero, co oznacza, że nieograniczoną/nieograniczony czas trwania.  

Wartości muszą ustawione w taki sposób, aby uniknąć błędów przepełnienia liczby całkowitej. Aby uzyskać więcej informacji na temat problemu, zobacz https://github.com/google/ExoPlayer/issues/3150 i https://github.com/google/ExoPlayer/issues/3112. <br/>Jeśli nie ustawisz wartości jawnie, bardzo dużych wartości **PlaybackDurationRemaining** i **LicenseDurationRemaining** zostanie przypisany (na przykład 9223372036854775807, co jest maksymalną dodatnia wartość 64-bitowa liczba całkowita). W rezultacie licencji Widevine pojawia się wygasły i dlatego nie nastąpi odszyfrowywania. 

Ten problem nie występuje w systemie Android 5.0 Lollipop lub nowszym ponieważ Android 5.0 to pierwsza wersja systemu Android został zaprojektowany w celu zapewnienia pełnej obsługi ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) i 64-bitowych platform podczas systemu Android 4.4 KitKat pierwotnie zaprojektowany do obsługi architektur ARMv7 i platformy 32-bitowych przy użyciu innych starszych wersji systemu Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Za pomocą platformy Xamarin do tworzenia aplikacji dla systemu Android odtwarzania

Można znaleźć powiązań platformy Xamarin dla ExoPlayer, korzystając z następujących linków:

- [Biblioteka powiązań platformy Xamarin dla biblioteki Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Powiązania Xamarin ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Zobacz też następujące wątku: [Powiązania Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome apps odtwarzacza dla systemu Android

Począwszy od wersji [Chrome dla systemu Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), trwałe licencji w EME jest obsługiwana. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) jest teraz również obsługiwane w przeglądarce Chrome dla systemu Android. Dzięki temu można tworzyć aplikacje w trybie offline odtwarzanie w przeglądarce Chrome, jeśli użytkownicy końcowi mają to (lub nowszej) wersji przeglądarki Chrome. 

Ponadto Google ma utworzone przykładowe progresywnego Web App (PWA) i open source go: 

- [Kod źródłowy](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Wersja Google hostowanych](https://biograf-155113.appspot.com/ttt/episode-2/) (działa tylko w przeglądarce Chrome v 62 lub nowszej na urządzeniach z systemem Android)

W przypadku uaktualnienia dla programu Chrome przeglądarce dla urządzeń przenośnych v62 (lub nowszy) telefon z systemem Android i testowania powyższych zorganizował przykładowej aplikacji, zobaczysz tej pracy odtwarzania online przesyłania strumieniowego i w trybie offline.

Powyższe aplikacji typu open source w PWA jest tworzone w środowisku Node.js. Jeśli chcesz hostować własnej wersji w systemie Ubuntu server, należy pamiętać następujące typowe problemy napotkano, które mogą uniemożliwić odtwarzania:

1. Problem CORS: Wideo w przykładowej aplikacji przykładowych znajduje się w https://storage.googleapis.com/biograf-video-files/videos/. Google ma ustawienia mechanizmu CORS dla ich przykłady testu hostowanej w usłudze Google Cloud Storage zasobnika. Są one obsługiwane z nagłówków CORS, jawne określenie wpis mechanizmu CORS: https://biograf-155113.appspot.com (domeny, w których google hostuje ich próbki) blokowanie dostępu przez inne witryny. Jeśli spróbujesz, zostanie wyświetlony następujący błąd HTTP: Nie można załadować https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: Brak nagłówka "Access-Control-Allow-Origin" jest obecny dla żądanego zasobu. Źródło "https:\//13.85.80.81:8080" nie ma związku z tym mieć dostęp. Jeśli odpowiedź nieprzezroczyste spełnia Twoje potrzeby, ustaw tryb żądania "nie-cors" można pobrać zasobu z mechanizmem CORS wyłączone.
2. Wystawienie certyfikatu: Począwszy od Chrome v 58, EME dla Widevine wymaga protokołu HTTPS. Dlatego należy do hostowania przykładowej aplikacji przy użyciu protokołu HTTPS z X509 certyfikatu. Certyfikat testowy zwykle nie działa ze względu na następujące wymagania: Należy uzyskać certyfikat spełniających poniższe wymagania minimalne:
    - Chrome i Firefox wymagane ustawienie alternatywna nazwa podmiotu dla sieci SAN ma ich w certyfikacie
    - Certyfikat musi być zaufany urząd certyfikacji i certyfikat programistyczny z podpisem własnym nie działa
    - Certyfikat musi mieć CN, pasującą do nazwy DNS serwera sieci web lub bram

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="question"></a>Pytanie

Jak można dostarczać trwałe (w trybie offline — włączone) dla niektórych klientów/użytkowników i nietrwałe licencje (w trybie offline wyłączona) dla innych użytkowników? Czy muszę zdublowana zawartość i użyć oddzielnych klucza zawartości

### <a name="answer"></a>Odpowiedź
Ponieważ usługa Media Services v3 umożliwia zasobu, aby mieć wiele StreamingLocators. Może mieć

1.  Jeden ContentKeyPolicy z license_type = "stałe", ContentKeyPolicyRestriction z oświadczenia na "stałe" i jego StreamingLocator;
2.  Inny ContentKeyPolicy z license_type = "nietrwałych" ContentKeyPolicyRestriction z oświadczenia na "nietrwałych" i jego StreamingLocator.
3.  Dwa StreamingLocators mają różne ContentKey.

W zależności od logiki biznesowej niestandardowej usługi STS różne oświadczenia są wystawiane w JWT token. Przy użyciu tokenu można uzyskać odpowiednią licencję, i tylko odpowiedni adres URL może być odtwarzany.

### <a name="question"></a>Pytanie

Dla poziomów zabezpieczeń Widevine, w Google [doc omówienie architektury usługi Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentacji, definiuje trzy poziomy zabezpieczeń. Jednak w [dokumentacji usługi Azure Media Services na szablonu licencji Widevine](widevine-license-template-overview.md), przedstawiono pięć różnych poziomów ochrony. Co to jest relacja lub mapowanie między dwoma zestawami różnych poziomów zabezpieczeń?

### <a name="answer"></a>Odpowiedź

W Google [omówienie architektury usługi Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), definiuje następujących trzech poziomów ochrony:

1.  Poziom zabezpieczeń 1: Przetwarzanie zawartości, kryptografii i kontrolki są wykonywane w ramach środowiska zaufane wykonywanie (TEE). W niektórych modelach wdrażania przetwarzanie zabezpieczeń może być wykonywane w różnych mikroukładami.
2.  Poziom zabezpieczeń 2: Wykonuje kryptografii (ale przetwarzanie wideo nie) w ramach TEE: odszyfrowany buforów są zwracane do domeny aplikacji i przetwarzane przez oddzielny wideo sprzętu lub oprogramowania. Na poziomie 2, jednak kryptograficznych informacje są nadal przetwarzane tylko w obrębie TEE.
3.  Poziom ochrony 3 nie ma TEE na urządzeniu. Odpowiednie środki mogą chronić informacje kryptograficzne i odszyfrowania zawartości w systemie operacyjnym hosta. Implementacja Level 3 mogą również obejmować aparatem kryptograficznych sprzętu, ale tylko zwiększa to wydajność, nie zabezpieczeń.

Jednocześnie, w [dokumentacji usługi Azure Media Services na szablonu licencji Widevine](widevine-license-template-overview.md), właściwość security_level content_key_specs może mieć następujące pięć różnych wartości (niezawodności wymagania dotyczące klienta dla odtwarzania):

1.  Wymagany jest oparty na oprogramowaniu whitebox kryptograficznego.
2.  Wymagane jest oprogramowanie kryptografii i zaciemnionego dekodera.
3.  Materiał klucza i operacje kryptograficzne, należy wykonać w ramach sprzętowego kopii TEE.
4.  Usług kryptograficznych i dekodowania zawartości jest wykonywana w ramach sprzętowego kopii TEE.
5.  Kryptograficznego dekodowania i wszystkie Obsługa nośnika (skompresowanym i nieskompresowanym formatem) muszą być obsługiwane w ramach sprzętowego kopii TEE.

Zarówno poziomy zabezpieczeń są definiowane przez Google Widevine. Różnica polega na tym poziomie jego użycia: poziomie interfejsu API lub architektury. Poziomy zabezpieczeń pięć są używane w interfejsie API Widevine. Obiekt content_key_specs, który zawiera security_level jest przeprowadzona i przekazywane do usługi globalne dostarczanie Widevine przez usługę licencji Widevine usługi multimediów Azure. W poniższej tabeli przedstawiono mapowania między dwoma zestawami poziomów zabezpieczeń.

| **Poziomy zabezpieczeń zdefiniowane w architekturze Widevine** |**Poziomy zabezpieczeń używane w Widevine interfejsu API**|
|---|---| 
| **Poziom zabezpieczeń 1**: Przetwarzanie zawartości, kryptografii i kontrolki są wykonywane w ramach środowiska zaufane wykonywanie (TEE). W niektórych modelach wdrażania przetwarzanie zabezpieczeń może być wykonywane w różnych mikroukładami.|**security_level = 5**: Kryptograficznego dekodowania i wszystkie Obsługa nośnika (skompresowanym i nieskompresowanym formatem) muszą być obsługiwane w ramach sprzętowego kopii TEE.<br/><br/>**security_level=4**: Usług kryptograficznych i dekodowania zawartości jest wykonywana w ramach sprzętowego kopii TEE.|
**Poziom zabezpieczeń 2**: Wykonuje kryptografii (ale przetwarzanie wideo nie) w ramach TEE: odszyfrowany buforów są zwracane do domeny aplikacji i przetwarzane przez oddzielny wideo sprzętu lub oprogramowania. Na poziomie 2, jednak kryptograficznych informacje są nadal przetwarzane tylko w obrębie TEE.| **security_level = 3**: Materiał klucza i operacje kryptograficzne, należy wykonać w ramach sprzętowego kopii TEE. |
| **Poziom ochrony 3**: Nie ma TEE na urządzeniu. Odpowiednie środki mogą chronić informacje kryptograficzne i odszyfrowania zawartości w systemie operacyjnym hosta. Implementacja Level 3 mogą również obejmować aparatem kryptograficznych sprzętu, ale tylko zwiększa to wydajność, nie zabezpieczeń. | **security_level=2**: Crypto oprogramowania i zaciemnionego dekodera są wymagane.<br/><br/>**security_level=1**: Wymagany jest oparty na oprogramowaniu whitebox kryptograficznego.|

### <a name="question"></a>Pytanie

Dlaczego pobierania zawartości podąża tak długo?

### <a name="answer"></a>Odpowiedź

Istnieją dwa sposoby, aby zwiększyć szybkość pobierania:

1.  Tak, aby użytkownicy końcowi są bardziej prawdopodobne trafić CDN zamiast punkt końcowy przesyłania strumieniowego/punkt początkowy dla pobierania zawartości, należy włączyć sieć CDN. Jeśli użytkownik osiągnie punkt końcowy przesyłania strumieniowego, każdego segmentu HLS lub DASH fragment dynamicznie jest spakowane i szyfrowane. Mimo że ten czas oczekiwania znajduje się w skali milisekund dla każdego segmentu/fragmentu, w przypadku długo wideo godzinę, skumulowany czas oczekiwania mogą być duże, co powoduje dłuższe pobierania.
2.  Zapewnić użytkownikom końcowym możliwość selektywnie pobierać warstwy jakość wideo i ścieżki audio, a nie całej zawartości. W trybie offline nie istnieje żaden punkt, aby pobrać wszystkie warstwy jakości. Istnieją dwa sposoby osiągnięcia tego:
    1.  Klient kontrolowane: player aplikacji automatycznie wybiera albo użytkownik wybierze warstwy jakość wideo i ścieżki audio, aby pobrać;
    2.  Usługa kontrolowane: jeden funkcja manifestów dynamicznych w usłudze Azure Media Services do tworzenia filtru (globalna), który ogranicza listy odtwarzania HLS lub DASH MPD do warstwy pojedynczego jakość wideo i wybrane ścieżki audio. Następnie adres URL pobierania wyświetlone użytkownikom końcowym będzie zawierać tego filtru.

## <a name="summary"></a>Podsumowanie

W tym artykule omówiono sposób implementacji w trybie offline odtwarzanie zawartości DASH chronione przez Widevine na urządzeniach z systemem Android.  Również odpowiedzi na często zadawane pytania dotyczące transmisji strumieniowej w trybie offline zawartości Widevine chronione na.
