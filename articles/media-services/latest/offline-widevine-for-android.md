---
title: Stream Widevine Android Offline with Azure Media Services v3
description: This topic shows how to configure your Azure Media Services account for offline streaming of Widevine protected content.
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
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 55ed849b6083435e70d0943a359c83793ca0842d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705908"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Offline Widevine streaming for Android with Media Services v3

In addition to protecting content for online streaming, media content subscription and rental services offer downloadable content that works when you are not connected to the internet. You might need to download content onto your phone or tablet for playback in airplane mode when flying disconnected from the network. Additional scenarios, in which you might want to download content:

- Some content providers may disallow DRM license delivery beyond a country/region's border. If a user wants to watch content while traveling abroad, offline download is needed.
- In some countries/regions, Internet availability and/or bandwidth is limited. Users may choose to download content to be able to watch it in high enough resolution for satisfactory viewing experience.

This article discusses how to implement offline mode playback for DASH content protected by Widevine on Android devices. Offline DRM allows you to provide subscription, rental, and purchase models for your content, enabling customers of your services to easily take content with them when disconnected from the internet.

For building the Android player apps, we outline three options:

> [!div class="checklist"]
> * Build a player using the Java API of ExoPlayer SDK
> * Build a player using Xamarin binding of ExoPlayer SDK
> * Build a player using Encrypted Media Extension (EME) and Media Source Extension (MSE) in Chrome mobile browser v62 or later

The article also answers some common questions related to offline streaming of Widevine protected content.

> [!NOTE]
> Offline DRM is only billed for making a single request for a license when you download the content. Any errors are not billed.

## <a name="prerequisites"></a>Wymagania wstępne 

Before implementing offline DRM for Widevine on Android devices, you should first:

- Become familiar with the concepts introduced for online content protection using Widevine DRM. This is covered in detail in the following documents/samples:
    - [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)
    - [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
- Klonowanie https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    You will need to modify the code in [Encrypt with DRM using .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) to add Widevine configurations.  
- Zapoznaj się z zestawem SDK usługi Google ExoPlayer dla systemu Android — zestawem SDK odtwarzacza wideo typu open source, który umożliwia obsługę odtwarzania w trybie offline Widevine funkcji DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Przewodnik dewelopera ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog dotyczący deweloperów EoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurowanie ochrony zawartości w Azure Media Services

W metodzie [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) są obecne następujące niezbędne kroki:

1. Określ sposób autoryzowania dostarczania klucza zawartości w usłudze dostarczania licencji: 

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
2. Skonfiguruj szablon licencji Widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Utwórz ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Włącz tryb offline

Aby włączyć tryb **offline** dla licencji Widevine, należy skonfigurować [szablon licencji Widevine](widevine-license-template-overview.md). W obiekcie **policy_overrides** ustaw właściwość **can_persist** na **true** (wartość domyślna to false), jak pokazano w [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurowanie odtwarzacza systemu Android na potrzeby odtwarzania w trybie offline

Najprostszym sposobem na tworzenie natywnej aplikacji odtwarzacza dla urządzeń z systemem Android jest użycie [zestawu SDK usługi Google ExoPlayer](https://github.com/google/ExoPlayer)z zestawem SDK odtwarzacza wideo typu open source. Program ExoPlayer obsługuje funkcje, które nie są obecnie obsługiwane przez natywny interfejs API MediaPlayer systemu Android, w tym protokół MPEG-KRESKa i protokoły dostarczania Microsoft Smooth Streaming.

ExoPlayer w wersji 2,6 lub nowszej zawiera wiele klas, które obsługują odtwarzanie w trybie offline Widevine. W szczególności Klasa OfflineLicenseHelper oferuje funkcje narzędziowe ułatwiające korzystanie z DefaultDrmSessionManager do pobierania, odnawiania i zwalniania licencji w trybie offline. Klasy udostępnione w folderze zestawu SDK "Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/offline/" obsługują pobieranie zawartości wideo w trybie offline.

Poniższa lista klas ułatwia tryb offline w ExoPlayer SDK dla systemu Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/offline/narzędzia do pobierania. Java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Deweloperzy powinni odwoływać się do [przewodnika dla deweloperów ExoPlayer](https://google.github.io/ExoPlayer/guide.html) oraz odpowiedniego [blogu dla deweloperów](https://medium.com/google-exoplayer) podczas opracowywania aplikacji. Firma Google nie wyłączyła w pełni udokumentowanej implementacji referencyjnej lub przykładowego kodu dla aplikacji ExoPlayer obsługującej Widevine w trybie offline, więc informacje są ograniczone do podręcznika i bloga deweloperów. 

### <a name="working-with-older-android-devices"></a>Praca ze starszymi urządzeniami z systemem Android

W przypadku niektórych starszych urządzeń z systemem Android należy ustawić wartości następujących **policy_overrides** właściwości (zdefiniowane w [szablonie licencji Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**i **license_duration_seconds**. Alternatywnie można ustawić je na zero, co oznacza nieskończony/nieograniczony czas trwania.  

Wartości muszą być ustawione tak, aby uniknąć błędów przepełnienia liczby całkowitej. Aby uzyskać więcej informacji o tym problemie, zobacz https://github.com/google/ExoPlayer/issues/3150 i https://github.com/google/ExoPlayer/issues/3112. <br/>Jeśli nie ustawisz jawnie wartości, zostaną przypisane bardzo duże wartości dla **PlaybackDurationRemaining** i **LicenseDurationRemaining** , (na przykład 9223372036854775807, która jest maksymalną wartością dodatnią dla 64-bitowej liczby całkowitej). W związku z tym licencja Widevine zostanie wyświetlona, a więc odszyfrowywanie nie zostanie wykonane. 

Ten problem nie występuje w przypadku systemu Android 5,0 Lizak lub nowszego, ponieważ system Android 5,0 to pierwsza wersja systemu Android, która została zaprojektowana w celu zapewnienia pełnej obsługi ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) i 64-bitowych platform, podczas gdy system Android 4,4 KitKat został pierwotnie zaprojektowany do obsługi platform architektury armv7 i 32-bit, jak w przypadku innych starszych wersji systemu Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Tworzenie aplikacji do odtwarzania systemu Android za pomocą platformy Xamarin

Powiązania platformy Xamarin dla ExoPlayer można znaleźć, korzystając z następujących linków:

- [Biblioteka powiązań platformy Xamarin dla biblioteki Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Powiązania Xamarin dla narzędzia NuGet ExoPlayer](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Zobacz też następujący wątek: [powiązanie Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplikacje odtwarzacza Chrome dla systemu Android

Począwszy od wersji programu [Chrome dla systemu Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), jest obsługiwana licencja trwała w eme. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) jest teraz również obsługiwane w programie Chrome dla systemu Android. Dzięki temu można tworzyć aplikacje do odtwarzania w trybie offline w programie Chrome, jeśli użytkownicy końcowi mają tę wersję programu Chrome (lub wyższą). 

Ponadto firma Google wygenerowała progresywną aplikację sieci Web (PWA) i Źródło "open source": 

- [Kod źródłowy](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Hostowana wersja usługi Google](https://biograf-155113.appspot.com/ttt/episode-2/) (działa tylko w programie Chrome v 62 i nowszych na urządzeniach z systemem Android)

W przypadku uaktualnienia przeglądarki Mobile Chrome do v62 (lub nowszej) na telefonie z systemem Android i przetestowania powyższej przykładowej aplikacji można zobaczyć, że dane przesyłane strumieniowo online i odtwarzanie w trybie offline zostaną wykonane.

Powyższa aplikacja PWA Open Source została utworzona w programie Node. js. Jeśli chcesz hostować własną wersję na serwerze Ubuntu, pamiętaj o następujących typowych problemach, które mogą uniemożliwić odtwarzanie:

1. Problem CORS: Przykładowe wideo w przykładowej aplikacji jest hostowane w https://storage.googleapis.com/biograf-video-files/videos/. Firma Google skonfigurował mechanizm CORS dla wszystkich próbek testowych hostowanych w przedziale magazynu Google Cloud. Są one obsługiwane z nagłówkami CORS, określając jawnie wpis CORS: https://biograf-155113.appspot.com (domena, w której znajduje się przykład Google), uniemożliwiając dostęp przez inne lokacje. Jeśli spróbujesz, zobaczysz następujący błąd HTTP: nie powiodło się załadowanie https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie. W związku z tym nie można uzyskać dostępu do źródła "https:\//13.85.80.81:8080". Jeśli nieprzezroczysta odpowiedź spełnia Twoje wymagania, ustaw tryb żądania na wartość "No-CORS", aby pobrać zasób z wyłączonym mechanizmem CORS.
2. Problem z certyfikatem: począwszy od programu Chrome v 58, EME for Widevine wymaga protokołu HTTPS. W związku z tym należy hostować przykładową aplikację za pośrednictwem protokołu HTTPS z certyfikatem x509. Zwykły certyfikat testowy nie działa z powodu następujących wymagań: należy uzyskać certyfikat spełniający następujące wymagania minimalne:
    - Przeglądarki Chrome i Firefox wymagają ustawienia alternatywnej nazwy podmiotu (SAN) w certyfikacie
    - Certyfikat musi mieć zaufany urząd certyfikacji, a certyfikat programistyczny z podpisem własnym nie działa
    - Certyfikat musi mieć nazwę POSPOLITą zgodną z nazwą DNS serwera sieci Web lub bramy

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="question"></a>Pytanie

Jak można dostarczać trwałe licencje (z włączoną obsługą trybu offline) dla niektórych klientów i nietrwałych licencji (wyłączone w trybie offline) dla innych użytkowników? Czy muszę duplikować zawartość i używać oddzielnego klucza zawartości?

### <a name="answer"></a>Odpowiedź
Ponieważ Media Services wersja 3 zezwala, aby element zawartości miał wiele StreamingLocators. Możesz mieć

1.  Jeden ContentKeyPolicy z license_type = "persistent", ContentKeyPolicyRestriction z elementem "persistent" i jego StreamingLocator;
2.  Inny ContentKeyPolicy z license_type = "nietrwałe", ContentKeyPolicyRestriction z usługą Claim na "nietrwałe" i jego StreamingLocator.
3.  Dwa StreamingLocators mają różne ContentKey.

W zależności od logiki biznesowej niestandardowej usługi STS różne oświadczenia są wystawiane w tokenie JWT. Za pomocą tokenu można uzyskać tylko odpowiednią licencję i można odtworzyć tylko odpowiedni adres URL.

### <a name="question"></a>Pytanie

W przypadku poziomów zabezpieczeń Widevine w dokumentacji doc dotyczącej [architektury DRM Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) firmy Google definiujemy trzy różne poziomy zabezpieczeń. Jednak w [Azure Media Services dokumentacji dotyczącej szablonu licencji Widevine](widevine-license-template-overview.md)są podane pięć różnych poziomów zabezpieczeń. Jaka jest relacja lub mapowanie między dwoma różnymi zestawami poziomów zabezpieczeń?

### <a name="answer"></a>Odpowiedź

W temacie [Omówienie architektury DRM Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)firmy Google definiujemy następujące trzy poziomy zabezpieczeń:

1.  Poziom zabezpieczeń 1: cała zawartość, Kryptografia i kontrola są wykonywane w ramach zaufanego środowiska wykonawczego (TEE). In some implementation models, security processing may be performed in different chips.
2.  Poziom zabezpieczeń 2: wykonuje kryptografię (ale nie przetwarzanie wideo) w TEE: odszyfrowane bufory są zwracane do domeny aplikacji i przetwarzane przez oddzielnego sprzętu wideo lub oprogramowania. At level 2, however, cryptographic information is still processed only within the TEE.
3.  Poziom zabezpieczeń 3 nie ma TEE na urządzeniu. Appropriate measures may be taken to protect the cryptographic information and decrypted content on host operating system. A Level 3 implementation may also include a hardware cryptographic engine, but that only enhances performance, not security.

W tym samym czasie, w [Azure Media Services dokumentacji dotyczącej szablonu licencji Widevine](widevine-license-template-overview.md), właściwość security_level content_key_specs może mieć następujące pięć różnych wartości (wymagania dotyczące niezawodności klienta na potrzeby odtwarzania):

1.  Wymagana jest Kryptografia Whitebox oparta na oprogramowaniu.
2.  Wymagane jest Kryptografia oprogramowania i niesłonięty dekoder.
3.  Materiał klucza i operacje kryptograficzne muszą być wykonywane w ramach sprzętowej TEE kopii zapasowej.
4.  Kryptograficzne i dekodowanie zawartości należy wykonać w ramach sprzętowej TEE kopii zapasowej.
5.  Kryptografia, dekodowanie i wszystkie obsługujące nośniki (skompresowane i nieskompresowane) muszą być obsługiwane w ramach sprzętowej TEE kopii zapasowej.

Oba poziomy zabezpieczeń są definiowane przez firmę Google Widevine. Różnica jest na poziomie użycia: poziom architektury lub poziom interfejsu API. W interfejsie API Widevine są używane pięć poziomów zabezpieczeń. Obiekt content_key_specs, który zawiera security_level, jest deserializowany i przeszedł do usługi dostarczania globalnego Widevine przez Azure Media Services usługę licencji Widevine. W poniższej tabeli przedstawiono mapowanie między dwoma zestawami poziomów zabezpieczeń.

| **Poziomy zabezpieczeń zdefiniowane w architekturze Widevine** |**Security Levels Used in Widevine API**|
|---|---| 
| **Security Level 1**: All content processing, cryptography, and control are performed within the Trusted Execution Environment (TEE). In some implementation models, security processing may be performed in different chips.|**security_level=5**: The crypto, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware backed TEE.<br/><br/>**security_level=4**: The crypto and decoding of content must be performed within a hardware backed TEE.|
**Security Level 2**: Performs cryptography (but not video processing) within the TEE: decrypted buffers are returned to the application domain and processed through separate video hardware or software. At level 2, however, cryptographic information is still processed only within the TEE.| **security_level=3**: The key material and crypto operations must be performed within a hardware backed TEE. |
| **Security Level 3**: Does not have a TEE on the device. Appropriate measures may be taken to protect the cryptographic information and decrypted content on host operating system. A Level 3 implementation may also include a hardware cryptographic engine, but that only enhances performance, not security. | **security_level=2**: Software crypto and an obfuscated decoder are required.<br/><br/>**security_level=1**: Software-based whitebox crypto is required.|

### <a name="question"></a>Pytanie

Why does content download take so long?

### <a name="answer"></a>Odpowiedź

There are two ways to improve download speed:

1.  Enable CDN so that end users are more likely to hit CDN instead of origin/streaming endpoint for content download. If user hits streaming endpoint, each HLS segment or DASH fragment is dynamically packaged and encrypted. Even though this latency is in millisecond scale for each segment/fragment, when you have an hour long video, the accumulated latency can be large causing longer download.
2.  Provide end users the option to selectively download video quality layers and audio tracks instead of all contents. For offline mode, there is no point to download all of the quality layers. There are two ways to achieve this:
    1.  Client controlled: either player app auto selects or user selects video  quality layer and audio tracks to download;
    2.  Service controlled: one can use Dynamic Manifest feature in Azure Media Services to create a (global) filter, which limits HLS playlist or DASH MPD to a single video quality layer and selected audio tracks. Then the download URL presented to end users will include this filter.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine is a service provided by Google Inc. and subject to the terms of service and Privacy Policy of Google, Inc.

## <a name="summary"></a>Podsumowanie

This article discussed how to implement offline mode playback for DASH content protected by Widevine on Android devices.  It also answered some common questions related to offline streaming of Widevine protected content.
