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
ms.date: 04/16/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: 55d59871ee72f755fcd881e4f89e56e6d6d1d84e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995323"
---
# <a name="offline-widevine-streaming-for-android"></a>Widevine w trybie offline, przesyłania strumieniowego dla systemu Android  

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Wersja 3](../latest/offline-widevine-for-android.md)
> * [Wersja 2](offline-widevine-for-android.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Oprócz ochrony zawartości online do przesyłania strumieniowego, multimediów zawartości subskrypcji i dzierżawa usługi oferty do pobrania zawartości, która działa, gdy nie są połączone z Internetem. Może być konieczne pobrać zawartość na telefonie lub tablecie do odtwarzania w tryb samolotowy, gdy pod odłączony od sieci. Dodatkowe scenariusze, w których możesz chcieć pobierania zawartości:

- Niektórzy dostawcy zawartości może nie zezwalaj na dostarczanie licencji DRM poza obramowania kraju. Jeśli użytkownik chce, aby obejrzeć zawartość podczas podróży granicą, pobierania w trybie offline jest konieczne.
- W niektórych krajach dostępność internetowych i/lub przepustowości jest ograniczona. Użytkownicy mogą zdecydować się na pobieranie zawartości, aby móc obejrzeć go na tyle wysokiej rozdzielczości dla środowiska oglądania zadowalające.

W tym artykule omówiono sposób implementacji w trybie offline odtwarzanie zawartości DASH chronione przez Widevine na urządzeniach z systemem Android. W trybie offline DRM pozwala na dostarczenie subskrypcję, wypożyczeń oraz zakupów modeli dla zawartości, dzięki czemu klienci usług mogą łatwo pobrać zawartość, która z nich podczas połączenia z Internetem.

Do tworzenia aplikacji odtwarzacza systemu Android, firma Microsoft przedstawiają trzy opcje:

> [!div class="checklist"]
> * Jak utworzyć odtwarzacz przy użyciu interfejsu API języka Java SDK ExoPlayer
> * Jak utworzyć odtwarzacz za pomocą powiązań Xamarin zestawu ExoPlayer SDK
> * Jak utworzyć odtwarzacz przy użyciu rozszerzenia zaszyfrowanych multimediów (EME) i rozszerzenie źródło nośnika (MSE) w v62 przenośnych przeglądarki Chrome lub nowszym

Artykuł zawiera również odpowiedzi na często zadawane pytania dotyczące transmisji strumieniowej w trybie offline zawartości Widevine chronione na.

## <a name="requirements"></a>Wymagania 

Przed wdrożeniem dla Widevine DRM w trybie offline, na urządzeniach z systemem Android, należy najpierw:

- Zapoznanie się z pojęciami opisanymi ochrony w trybie online zawartości przy użyciu technologii Widevine DRM. To zagadnienie opisano szczegółowo w następujących dokumentach/samples:
    - [Usługa Azure Media Services umożliwia dostarczanie licencji DRM lub kluczy AES](media-services-deliver-keys-and-licenses.md)
    - [CENC przy użyciu technologii Multi-DRM i kontroli dostępu: Odwołanie do projektowania i implementacji na platformie Azure i usługi Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Przy użyciu szyfrowania Common Encryption PlayReady i Widevine dynamicznie przy użyciu platformy .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Użyj usługi Azure Media Services w celu dostarczania licencji PlayReady i Widevine przy użyciu platformy .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Zapoznanie się z zestawu SDK ExoPlayer Google dla systemów Android, typu open-source odtwarzacza wideo zestaw SDK może obsłużyć w trybie offline odtwarzania Widevine DRM. 
    - [Zestaw SDK ExoPlayer](https://github.com/google/ExoPlayer)
    - [Przewodnik dla deweloperów ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog deweloperów EoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfiguracja ochrony zawartości w usłudze Azure Media Services

Po skonfigurowaniu ochrony Widevine elementu zawartości w usłudze Media Services, musisz utworzyć ContentKeyAuthorizationPolicyOption, który określono następujące trzy rzeczy:

1. Systemem zarządzania prawami Cyfrowymi (Widevine)
2. ContentKeyAuthorizationPolicyRestriction Określanie jak zawartości dostarczania kluczy jest autoryzowany w usługi dostarczania licencji (otwarte lub tokenu autoryzacji)
3. Szablon licencji DRM (Widevine)

Aby włączyć **offline** tryb licencji Widevine, musisz skonfigurować [szablonu licencji Widevine](media-services-widevine-license-template-overview.md). W **policy_overrides** obiektu, należy ustawić **can_persist** właściwości **true** (wartość domyślna to false). 

Poniższy przykład kodu używa platformy .NET, aby umożliwić **offline** tryb licencji Widevine. Kod opiera się na [ za pomocą PlayReady i Widevine dynamicznego szyfrowania Common Encryption przy użyciu platformy .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) próbki. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

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

Deweloperzy powinny odwoływać się [przewodnik dla deweloperów ExoPlayer](https://google.github.io/ExoPlayer/guide.html) i odpowiedni [Blog deweloperów](https://medium.com/google-exoplayer) podczas tworzenia aplikacji. Google nie opublikowała kod odwołania w pełni udokumentowane implementacji lub przykładowej aplikacji ExoPlayer wspieranie Widevine w trybie offline w tej chwili, więc informacje są ograniczone do blogu dotyczącym i przewodnik dla deweloperów. 

### <a name="working-with-older-android-devices"></a>Praca z starsze urządzenia z systemem Android

Niektóre starsze urządzenia z systemem Android, należy ustawić wartości dla następujących **policy_overrides** właściwości (zdefiniowane w [szablonu licencji Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, i **license_duration_seconds**. Alternatywnie można je ustawić na zero, co oznacza, że nieograniczoną/nieograniczony czas trwania.  

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
Nie trzeba zdublowana zawartość. Można po prostu użyj jednej kopii zawartości i jednym ContentKeyAuthorizationPolicy, ale dwie oddzielne ContentKeyAuthorizationPolicyOption firmy:

1. IContentKeyAuthorizationPolicyOption 1: korzysta z licencji trwałe i 1 ContentKeyAuthorizationPolicyRestriction, który zawiera oświadczenia, takich jak license_type = "Trwały"
2. IContentKeyAuthorizationPolicyOption 2: korzysta z licencji nietrwałe i ContentKeyAuthorizationPolicyRestriction 2, który zawiera oświadczenia, takich jak license_type = "Nonpersistent"

Dzięki temu, gdy żądanie licencji pochodzą z aplikacji klienckiej z żądania licencji nie ma żadnej różnicy. Jednak innego użytkownika/urządzenia, Usługa STS powinny mieć logikę biznesową do wystawiania inny tokenów JWT zawierającym oświadczenia różnych (jedna powyżej license_type dwóch). Wartość oświadczenia w JWT token będzie służyć do określania przez usługę licencji do wystawiania jakiego typu licencji: stałe i nietrwałe.

Oznacza to, że Secure Token Service (STS) musi mieć business Logic Apps i klienckich informacje Dodaj odpowiednie wartości oświadczenia w tokenie.

### <a name="question"></a>Pytanie

Dla poziomów zabezpieczeń Widevine, w Google [doc omówienie architektury usługi Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentacji, definiuje trzy poziomy zabezpieczeń. Jednak w [dokumentacji usługi Azure Media Services na szablonu licencji Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), przedstawiono pięć różnych poziomów ochrony. Co to jest relacja lub mapowanie między dwoma zestawami różnych poziomów zabezpieczeń?

### <a name="answer"></a>Odpowiedź

W Google [omówienie architektury usługi Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), definiuje następujących trzech poziomów ochrony:

1.  Poziom zabezpieczeń 1: Przetwarzanie zawartości, kryptografii i kontrolki są wykonywane w ramach środowiska zaufane wykonywanie (TEE). W niektórych modelach wdrażania przetwarzanie zabezpieczeń może być wykonywane w różnych mikroukładami.
2.  Poziom zabezpieczeń 2: Wykonuje kryptografii (ale przetwarzanie wideo nie) w ramach TEE: odszyfrowany buforów są zwracane do domeny aplikacji i przetwarzane przez oddzielny wideo sprzętu lub oprogramowania. Na poziomie 2, jednak kryptograficznych informacje są nadal przetwarzane tylko w obrębie TEE.
3.  Poziom ochrony 3 nie ma TEE na urządzeniu. Odpowiednie środki mogą chronić informacje kryptograficzne i odszyfrowania zawartości w systemie operacyjnym hosta. Implementacja Level 3 mogą również obejmować aparatem kryptograficznych sprzętu, ale tylko zwiększa to wydajność, nie zabezpieczeń.

Jednocześnie, w [dokumentacji usługi Azure Media Services na szablonu licencji Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), właściwość security_level content_key_specs może mieć następujące pięć różnych wartości (niezawodności wymagania dotyczące klienta dla odtwarzania):

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
| **Poziom ochrony 3**: Nie ma TEE na urządzeniu. Odpowiednie środki mogą chronić informacje kryptograficzne i odszyfrowania zawartości w systemie operacyjnym hosta. Implementacja Level 3 mogą również obejmować aparatem kryptograficznych sprzętu, ale tylko zwiększa to wydajność, nie zabezpieczeń. | **security_level=2**: Wymagane jest oprogramowanie kryptografii i zaciemnionego dekodera.<br/><br/>**security_level=1**: Wymagany jest oparty na oprogramowaniu whitebox kryptograficznego.|

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
