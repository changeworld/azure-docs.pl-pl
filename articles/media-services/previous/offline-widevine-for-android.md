---
title: Konfigurowanie konta do przesyłania strumieniowego w trybie offline zawartości chronionej widevine — Azure
description: W tym temacie pokazano, jak skonfigurować konto usługi Azure Media Services do przesyłania strumieniowego w trybie offline zawartości chronionej widevine.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
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
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: f3bd7bc78eeb62cc33a01ed31bb04d94078cae4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294332"
---
# <a name="offline-widevine-streaming-for-android"></a>Przesyłanie strumieniowe przy użyciu technologii Widevine w trybie offline dla systemu Android  

> [!div class="op_single_selector" title1="Wybierz używana wersja usługi Media Services:"]
> * [Wersja 3](../latest/offline-widevine-for-android.md)
> * [Wersja 2](offline-widevine-for-android.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Oprócz ochrony zawartości do przesyłania strumieniowego online, subskrypcja treści multimedialnych i usługi wynajmu oferują zawartość do pobrania, która działa, gdy nie masz połączenia z Internetem. Może być konieczne pobranie zawartości na telefon lub tablet do odtwarzania w trybie samolotowym po odłączeniu lotu od sieci. Dodatkowe scenariusze, w których można pobrać zawartość:

- Niektórzy dostawcy treści mogą nie zezwalać na dostarczanie licencji DRM poza granicami kraju/regionu. Jeśli użytkownik chce oglądać zawartość podczas podróży za granicę, wymagane jest pobieranie w trybie offline.
- W niektórych krajach/regionach dostępność Internetu i/lub przepustowość jest ograniczona. Użytkownicy mogą pobrać zawartość, aby móc oglądać ją w wystarczająco wysokiej rozdzielczości, aby zapewnić zadowalające wrażenia wizualne.

W tym artykule omówiono sposób implementowania odtwarzania w trybie offline dla zawartości DASH chronionej przez Widevine na urządzeniach z systemem Android. Usługa DRM w trybie offline umożliwia dostarczanie modeli subskrypcji, wypożyczania i zakupu zawartości, umożliwiając klientom usług łatwe zabieranie ich z zawartością po odłączeniu od Internetu.

Do tworzenia aplikacji dla graczy na Androida przedstawiamy trzy opcje:

> [!div class="checklist"]
> * Tworzenie odtwarzacza za pomocą interfejsu JAVA exoPlayer SDK
> * Zbuduj gracza za pomocą wiązania Xamarin z ExoPlayer SDK
> * Tworzenie odtwarzacza przy użyciu rozszerzenia EME (Encrypted Media Extension) i rozszerzenia źródła multimediów (MSE) w przeglądarce mobilnej Chrome w wersji 62 lub nowszej

W tym artykule odpowiedzi również na niektóre typowe pytania związane z przesyłania strumieniowego w trybie offline zawartości chronionej Widevine.

## <a name="requirements"></a>Wymagania 

Przed wdrożeniem technologii DRM w trybie offline dla Widevine na urządzeniach z systemem Android należy najpierw:

- Zapoznaj się z pojęciami wprowadzonymi do ochrony treści online za pomocą Widevine DRM. Jest to szczegółowo opisane w następujących dokumentach/przykładach:
    - [Dostarczanie licencji drm lub kluczy AES za pomocą usługi Azure Media Services](media-services-deliver-keys-and-licenses.md)
    - [Szyfrowanie CENC przy użyciu technologii Multi-DRM i kontroli dostępu: odwołanie do projektowania i implementacji na platformie Azure i w usłudze Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Korzystanie z funkcji PlayReady i/lub Widevine Dynamic Common Encryption z programem .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Korzystanie z usługi Azure Media Services w celu dostarczania licencji PlayReady i/lub Widevine za pomocą platformy .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Zapoznaj się z Google ExoPlayer SDK dla Androida, open-source video player SDK zdolny do obsługi odtwarzania online Widevine DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Przewodnik po deweloperach ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer Blog](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfiguracja ochrony zawartości w usłudze Azure Media Services

Podczas konfigurowania ochrony Widevine zasobu w umiaźnej usługi Media Services, należy utworzyć ContentKeyAuthorizationPolicyOption, który określił następujące trzy rzeczy:

1. System DRM (Widevine)
2. ContentKeyAuthorizationPolicyRestriction określające sposób dostarczania klucza zawartości jest autoryzowany w usłudze dostarczania licencji (otwórz lub autoryzacji tokenu)
3. Szablon licencji DRM (Widevine)

Aby włączyć tryb **offline** dla licencji Widevine, należy skonfigurować [szablon licencji Widevine](media-services-widevine-license-template-overview.md). W **policy_overrides** obiektu ustaw właściwość **can_persist** na **true** (domyślnie jest false). 

Poniższy przykładowy kod używa platformy .NET do włączania trybu **offline** dla licencji Widevine. Kod jest oparty na [użyciu playready i/lub widevine dynamiczne wspólne szyfrowanie z .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) próbki. 

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
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
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

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurowanie odtwarzacza Android do odtwarzania w trybie offline

Najprostszym sposobem na opracowanie aplikacji dla rodzimych graczy dla urządzeń z systemem Android jest użycie [SDK Google ExoPlayer](https://github.com/google/ExoPlayer), open-source video player SDK. ExoPlayer obsługuje funkcje, które nie są obecnie obsługiwane przez natywne api MediaPlayer systemu Android, w tym protokoły dostarczania MPEG-DASH i Microsoft Smooth Streaming.

ExoPlayer w wersji 2.6 lub wyższej zawiera wiele klas, które obsługują odtwarzanie widevine DRM w trybie offline. W szczególności offlineLicenseHelper klasy zapewnia funkcje narzędzia w celu ułatwienia korzystania z DefaultDrmSessionManager do pobierania, odnawiania i zwalniania licencji w trybie offline. Klasy dostępne w folderze SDK "library/core/src/main/java/com/google/android/exoplayer2/offline/" obsługują pobieranie zawartości wideo w trybie offline.

Poniższa lista klas ułatwia tryb offline w zestawie ExoPlayer SDK dla systemu Android:

- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- biblioteka/rdzeń/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- biblioteka/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Deweloperzy powinni odwoływać się do [ExoPlayer Developer Guide](https://google.github.io/ExoPlayer/guide.html) i odpowiedniego [bloga dewelopera](https://medium.com/google-exoplayer) podczas opracowywania aplikacji. Google nie wydała w pełni udokumentowaną implementację odniesienia lub przykładowy kod dla aplikacji ExoPlayer obsługującej Widevine w trybie offline w tej chwili, więc informacje są ograniczone do przewodnika programistów i bloga. 

### <a name="working-with-older-android-devices"></a>Praca ze starszymi urządzeniami z Androidem

W przypadku niektórych starszych urządzeń z systemem Android należy ustawić wartości dla następujących właściwości **policy_overrides** (zdefiniowanych w [szablonie licencji Widevine:](media-services-widevine-license-template-overview.md) **rental_duration_seconds**, **playback_duration_seconds**i **license_duration_seconds**. Alternatywnie, można ustawić je na zero, co oznacza nieskończony / nieograniczony czas trwania.  

Wartości muszą być ustawione, aby uniknąć błędu przepełnienia liczby całkowitej. Aby uzyskać więcej informacji https://github.com/google/ExoPlayer/issues/3150 na https://github.com/google/ExoPlayer/issues/3112ten temat, zobacz i . <br/>Jeśli nie ustawisz wartości jawnie, zostaną przypisane bardzo duże wartości dla **odtwarzaniaDurationRemaining** i **LicenseDurationRemaining** (na przykład 9223372036854775807, co jest maksymalną wartością dodatnią dla 64-bitowej liczby całkowitej). W rezultacie licencja Widevine wydaje się wygasła i dlatego odszyfrowywanie nie nastąpi. 

Ten problem nie występuje na Android 5.0 Lollipop lub później, ponieważ Android 5.0 jest pierwszą wersją Androida, która została zaprojektowana do pełnej obsługi ARMv8[(Advanced RISC Machine)](https://en.wikipedia.org/wiki/ARM_architecture)i platform 64-bitowych, podczas gdy Android 4.4 KitKat został pierwotnie zaprojektowany do obsługi platform ARMv7 i 32-bitowych, jak w przypadku innych starszych wersji Androida.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Tworzenie aplikacji do odtwarzania androida za pomocą platformy Xamarin

Powiązania platformy Xamarin dla Programu ExoPlayer można znaleźć pod następującymi łączami:

- [Biblioteka powiązań platformy Xamarin dla biblioteki Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Wiązania Xamarin dla ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Zobacz też następujący wątek: [Powiązanie platformy Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplikacje dla odtwarzacza Chrome dla Androida

Począwszy od wydania [Chrome dla Androida v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), trwała licencja w EME jest obsługiwana. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) jest teraz również obsługiwany w Chrome dla Androida. Dzięki temu możesz tworzyć aplikacje do odtwarzania offline w Chrome, jeśli użytkownicy końcowi mają tę (lub wyższą) wersję Chrome. 

Ponadto, Google wyprodukował progressive web app (PWA) próbki i open-sourceed go: 

- [Kod źródłowy](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Wersja hostowana przez Google](https://biograf-155113.appspot.com/ttt/episode-2/) (działa tylko w Chrome v 62 i nowszym na urządzeniach z Androidem)

Jeśli uaktualnisz przeglądarkę Chrome do wersji 62 (lub nowszej) na telefonie z Androidem i przetestujesz powyższych hostowanych przykładowych aplikacji, zobaczysz, że zarówno przesyłanie strumieniowe online, jak i odtwarzanie w trybie offline działa.

Powyższa aplikacja PWA typu open source jest autorem w pliku Node.js. Jeśli chcesz hostować własną wersję na serwerze Ubuntu, pamiętaj o następujących typowych napotkanych problemach, które mogą uniemożliwić odtwarzanie:

1. Problem CORS: przykładowy film wideo w https://storage.googleapis.com/biograf-video-files/videos/przykładowej aplikacji jest hostowany w pliku . Google utworzyłrównos dla wszystkich próbek testowych hostowanych w wiadrze Google Cloud Storage. Są one obsługiwane z nagłówkami CORS, określając wyraźnie `https://biograf-155113.appspot.com` wpis CORS: (domena, w której google hostuje ich próbki) uniemożliwia dostęp przez inne witryny. Jeśli spróbujesz, zobaczysz następujący błąd HTTP:`Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Wydanie certyfikatu: Począwszy od Chrome v 58, EME for Widevine wymaga protokołu HTTPS. W związku z tym należy hostować przykładową aplikację za pośrednictwem protokołu HTTPS z certyfikatem X509. Zwykły certyfikat testowy nie działa z powodu następujących wymagań: Musisz uzyskać certyfikat spełniające następujące minimalne wymagania:
    - Chrome i Firefox wymagają ustawienia alternatywnej nazwy SAN-Subject, aby istniało w certyfikacie
    - Certyfikat musi mieć zaufany urząd certyfikacji, a certyfikat dewelopera z podpisem własnym nie działa
    - Certyfikat musi mieć cn pasujące do nazwy DNS serwera sieci web lub bramy

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="question"></a>Pytanie

Jak dostarczać licencje trwałe (z obsługą w trybie offline) dla niektórych klientów/użytkowników i licencje nietrwałe (wyłączone w trybie offline) dla innych? Czy muszę powielać zawartość i używać oddzielnego klucza zawartości?

### <a name="answer"></a>Odpowiedź
Nie trzeba powielać zawartości. Można po prostu użyć jednej kopii zawartości i pojedynczej ContentKeyAuthorizationPolicy, ale dwa oddzielne ContentKeyAuthorizationPolicyOption's:

1. IContentKeyAuthorizationPolicyOption 1: używa licencji trwałej i ContentKeyAuthorizationPolicyRestriction 1, która zawiera oświadczenie, takie jak license_type = "Trwałe"
2. IContentKeyAuthorizationPolicyOption 2: używa licencji nietrwałej i ContentKeyAuthorizationPolicyRestriction 2, która zawiera oświadczenie, takie jak license_type = "Nonpersistent"

W ten sposób, gdy żądanie licencji pochodzi z aplikacji klienckiej, z żądania licencji nie ma różnicy. Jednak dla różnych użytkowników końcowych/urządzenia STS powinien mieć logikę biznesową do wydawania różnych tokenów JWT zawierających różne oświadczenia (jeden z powyższych dwóch license_type). Wartość oświadczeń w tokenie JWT będzie używana do decydowania przez usługę licencyjną o wydanie typu licencji: trwałego lub nietrwałego.

Oznacza to, że usługa bezpiecznego tokenu (STS) musi mieć logikę biznesową i informacje o kliencie/urządzeniu, aby dodać odpowiednią wartość oświadczenia do tokenu.

### <a name="question"></a>Pytanie

W przypadku poziomów zabezpieczeń Widevine w dokumentacji [doc Przegląd architektury DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) firmy Google definiuje trzy różne poziomy zabezpieczeń. Jednak w [dokumentacji usługi Azure Media Services na szablonie licencji Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)przedstawiono pięć różnych poziomów zabezpieczeń. Co to jest relacja lub mapowanie między dwoma różnymi zestawami poziomów zabezpieczeń?

### <a name="answer"></a>Odpowiedź

W [przeglądzie architektury Drm Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)firmy Google określono trzy następujące poziomy zabezpieczeń:

1.  Poziom zabezpieczeń 1: Wszystkie przetwarzanie zawartości, kryptografia i kontrola są wykonywane w środowisku zaufanego wykonywania (TEE). W niektórych modelach implementacji przetwarzanie zabezpieczeń może być wykonywane w różnych układach.
2.  Poziom zabezpieczeń 2: Wykonuje kryptografię (ale nie przetwarzanie wideo) w tee: odszyfrowane bufory są zwracane do domeny aplikacji i przetwarzane za pośrednictwem oddzielnego sprzętu wideo lub oprogramowania. Jednak na poziomie 2 informacje kryptograficzne są nadal przetwarzane tylko w ramach TEE.
3.  Poziom zabezpieczeń 3 Nie ma tee na urządzeniu. Można podjąć odpowiednie środki w celu ochrony informacji kryptograficznych i odszyfrowanej zawartości w systemie operacyjnym hosta. Implementacja poziomu 3 może również zawierać sprzętowy aparat kryptograficzny, ale to tylko zwiększa wydajność, a nie bezpieczeństwo.

W tym samym czasie w [dokumentacji usługi Azure Media Services w szablonie licencji Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)security_level właściwości content_key_specs może mieć następujące pięć różnych wartości (wymagania dotyczące niezawodności klienta dla odtwarzania):

1.  Wymagane jest oprogramowanie oparte na krypto whitebox.
2.  Wymagane jest oprogramowanie krypto i zaciemniony dekoder.
3.  Kluczowe materiały i operacje kryptograficzne muszą być wykonywane w ramach tee wspieranego sprzętowo.
4.  Krypto i dekodowanie zawartości muszą być wykonywane w ramach tee wspieranego sprzętowo.
5.  Krypto, dekodowanie i cała obsługa nośnika (skompresowanego i nieskompresowanego) muszą być obsługiwane w ramach tee wspieranego sprzętowo.

Oba poziomy zabezpieczeń są definiowane przez Google Widevine. Różnica polega na jego poziomie użycia: poziom architektury lub poziom interfejsu API. Pięć poziomów zabezpieczeń są używane w interfejsie API Widevine. Obiekt content_key_specs, który zawiera security_level jest deserializowany i przekazywany do globalnej usługi dostarczania Widevine przez usługę licencjonowania Azure Media Services Widevine. W poniższej tabeli przedstawiono mapowanie między dwoma zestawami poziomów zabezpieczeń.

| **Poziomy zabezpieczeń zdefiniowane w architekturze Widevine** |**Poziomy zabezpieczeń używane w interfejsie API Widevine**|
|---|---| 
| **Poziom zabezpieczeń 1:** Wszystkie przetwarzanie zawartości, kryptografia i kontrola są wykonywane w środowisku zaufanego wykonywania (TEE). W niektórych modelach implementacji przetwarzanie zabezpieczeń może być wykonywane w różnych układach.|**security_level=5**: Krypto, dekodowanie i cała obsługa nośnika (skompresowanego i nieskompresowanego) muszą być obsługiwane w tee ze sprzętem.<br/><br/>**security_level=4**: Krypto i dekodowanie zawartości muszą być wykonywane w ramach tee wspieranego sprzętowo.|
**Poziom zabezpieczeń 2:** Wykonuje kryptografię (ale nie przetwarzanie wideo) w tee: odszyfrowane bufory są zwracane do domeny aplikacji i przetwarzane za pośrednictwem oddzielnego sprzętu wideo lub oprogramowania. Jednak na poziomie 2 informacje kryptograficzne są nadal przetwarzane tylko w ramach TEE.| **security_level=3**: Materiał klucza i operacje kryptograficzne muszą być wykonywane w tee kopii sprzętowej. |
| **Poziom zabezpieczeń 3:** Nie ma tee na urządzeniu. Można podjąć odpowiednie środki w celu ochrony informacji kryptograficznych i odszyfrowanej zawartości w systemie operacyjnym hosta. Implementacja poziomu 3 może również zawierać sprzętowy aparat kryptograficzny, ale to tylko zwiększa wydajność, a nie bezpieczeństwo. | **security_level=2**: Wymagane jest krypto oprogramowania i zaciemniony dekoder.<br/><br/>**security_level=1**: Wymagane jest oprogramowanie oparte na krypto whitebox.|

### <a name="question"></a>Pytanie

Dlaczego pobieranie zawartości trwa tak długo?

### <a name="answer"></a>Odpowiedź

Istnieją dwa sposoby poprawy szybkości pobierania:

1.  Włącz sieć CDN, aby użytkownicy końcowi częściej trafiali do sieci CDN zamiast punktu końcowego pochodzenia/przesyłania strumieniowego w celu pobrania zawartości. Jeśli użytkownik osiągnie punkt końcowy przesyłania strumieniowego, każdy segment HLS lub dash fragment jest dynamicznie pakowane i szyfrowane. Mimo że to opóźnienie jest w skali milisekund dla każdego segmentu/fragmentu, gdy masz godzinny film wideo, skumulowane opóźnienie może być duże, co powoduje dłuższe pobieranie.
2.  Zapewnij użytkownikom końcowym możliwość selektywnego pobierania warstw jakości wideo i ścieżek audio zamiast całej zawartości. W trybie offline nie ma sensu pobierać wszystkich warstw jakości. Istnieją dwa sposoby osiągnięcia tego celu:
    1.  Kontrolowane przez klienta: aplikacja odtwarzacza automatycznie wybiera lub użytkownik wybiera warstwę jakości wideo i ścieżki audio do pobrania;
    2.  Sterowana usługą: można użyć funkcji manifestu dynamicznego w usłudze Azure Media Services, aby utworzyć (globalny) filtr, który ogranicza listę odtwarzania HLS lub DASH MPD do jednej warstwy jakości wideo i wybranych ścieżek audio. Następnie adres URL pobierania prezentowany użytkownikom końcowym będzie zawierał ten filtr.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="summary"></a>Podsumowanie

W tym artykule omówiono sposób implementowania odtwarzania w trybie offline dla zawartości DASH chronionej przez Widevine na urządzeniach z systemem Android.  Odpowiedziała również na niektóre typowe pytania związane z przesyłaniem strumieniowe widevine w trybie offline.
