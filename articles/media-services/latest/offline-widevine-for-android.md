---
title: Stream Widevine Android offline z usługą Azure Media Services w wersji 3
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
ms.date: 04/07/2020
ms.author: willzhan
ms.openlocfilehash: 94edec8261d9916b7575fb247e1698273f244130
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887201"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Przesyłanie strumieniowe widevine w trybie offline dla systemu Android z usługą Media Services w wersji 3

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

> [!NOTE]
> Usługa DRM w trybie offline jest naliczona tylko za złożenie jednego żądania licencji podczas pobierania zawartości. Wszelkie błędy nie są rozliczane.

## <a name="prerequisites"></a>Wymagania wstępne 

Przed wdrożeniem technologii DRM w trybie offline dla Widevine na urządzeniach z systemem Android należy najpierw:

- Zapoznaj się z pojęciami wprowadzonymi do ochrony treści online za pomocą Widevine DRM. Jest to szczegółowo opisane w następujących dokumentach/przykładach:
    - [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)
    - [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
- Klonuj https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Należy zmodyfikować kod w [encrypt with DRM przy użyciu platformy .NET,](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) aby dodać konfiguracje Widevine.  
- Zapoznaj się z Google ExoPlayer SDK dla Androida, open-source video player SDK zdolny do obsługi odtwarzania online Widevine DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Przewodnik po deweloperach ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer Blog](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurowanie ochrony zawartości w usłudze Azure Media Services

W [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metody następujące kroki są następujące niezbędne kroki są obecne:

1. Określ, w jaki sposób dostarczanie klucza zawartości jest autoryzowane w usłudze dostarczania licencji: 

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

3. Tworzenie contentkeypolicyoptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Włączanie trybu offline

Aby włączyć tryb **offline** dla licencji Widevine, należy skonfigurować [szablon licencji Widevine](widevine-license-template-overview.md). W **policy_overrides** obiekcie ustaw właściwość **can_persist** na **true** (domyślnie jest false), jak pokazano w [polu ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurowanie odtwarzacza Android do odtwarzania w trybie offline

Najprostszym sposobem na opracowanie aplikacji dla rodzimych graczy dla urządzeń z systemem Android jest użycie [SDK Google ExoPlayer](https://github.com/google/ExoPlayer), open-source video player SDK. ExoPlayer obsługuje funkcje, które nie są obecnie obsługiwane przez natywne api MediaPlayer systemu Android, w tym protokoły dostarczania MPEG-DASH i Microsoft Smooth Streaming.

ExoPlayer w wersji 2.6 lub wyższej zawiera wiele klas, które obsługują odtwarzanie widevine DRM w trybie offline. W szczególności offlineLicenseHelper klasy zapewnia funkcje narzędzia w celu ułatwienia korzystania z DefaultDrmSessionManager do pobierania, odnawiania i zwalniania licencji w trybie offline. Klasy dostępne w folderze SDK "library/core/src/main/java/com/google/android/exoplayer2/offline/" obsługują pobieranie zawartości wideo w trybie offline.

Poniższa lista klas ułatwia tryb offline w exoPlayer SDK dla systemu Android:

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

W przypadku niektórych starszych urządzeń z systemem Android należy ustawić wartości dla następujących właściwości **policy_overrides** (zdefiniowanych w [szablonie licencji Widevine:](widevine-license-template-overview.md) **rental_duration_seconds**, **playback_duration_seconds**i **license_duration_seconds**. Alternatywnie, można ustawić je na zero, co oznacza nieskończony / nieograniczony czas trwania.  

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

## <a name="faqs"></a>Często zadawane pytania

Aby uzyskać więcej informacji, zobacz [Widevine często zadawane pytania](frequently-asked-questions.md#widevine-streaming-for-android).

## <a name="additional-notes"></a>Uwagi dodatkowe

Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="summary"></a>Podsumowanie

W tym artykule omówiono sposób implementowania odtwarzania w trybie offline dla zawartości DASH chronionej przez Widevine na urządzeniach z systemem Android.  Odpowiedziała również na niektóre typowe pytania związane z przesyłaniem strumieniowe widevine w trybie offline.
