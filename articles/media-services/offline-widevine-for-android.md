---
title: Konfigurowanie konta do trybu offline przesyłania strumieniowego zawartości Widevine chronione - Azure
description: W tym temacie przedstawiono sposób konfigurowania konta usługi Azure Media Services do przesyłania strumieniowego w trybie offline z Widevine chronione zawartości.
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
ms.date: 01/10/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: 158b58c13aee4d6241900db4a5e2b3fe8a45cc3c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32178468"
---
# <a name="offline-widevine-streaming-for-android"></a>W trybie offline Widevine przesyłania strumieniowego dla systemu Android

Oprócz ochrony zawartości online przesyłania strumieniowego, media zawartości subskrypcji i wynajem usług oferty do pobrania zawartości, która działa, gdy nie jest połączony z Internetem. Może być konieczne pobranie zawartości na Twój telefon lub tablet do odtwarzania w tryb samolotowy, gdy pod odłączony od sieci. Dodatkowe scenariusze, w których można pobrać zawartości:

- Niektórzy dostawcy zawartości może nie zezwalaj na dostarczania licencji DRM poza obramowania kraju. Jeśli użytkownik chce obejrzeć zawartość podczas podróży granicą, pobierania w trybie offline są wymagane.
- W niektórych krajach dostępności Internet i/lub przepustowości jest ograniczona. Użytkownicy mogą wybrać można pobrać zawartości, aby można było oglądać go w wystarczająco wysokiej rozdzielczości do wyświetlania zadowalające.

W tym artykule omówiono sposób wdrożenia w trybie offline odtwarzania zawartości DASH chronione przez Widevine na urządzeniach z systemem Android. W trybie offline DRM umożliwia podanie subskrypcji, wynajem i zakupu modeli dla zawartości, umożliwiając klientom usług łatwe przełączenie zawartości z nimi podczas połączenia z Internetem.

Do tworzenia aplikacji systemu Android player, możemy przedstawiają trzy opcje:

> [!div class="checklist"]
> * Tworzenie przy użyciu interfejsu API języka Java SDK ExoPlayer odtwarzacza
> * Tworzenie za pomocą powiązania Xamarin zestawu SDK ExoPlayer odtwarzacza
> * Tworzenie odtwarzacza przy użyciu szyfrowane nośnika rozszerzenia (EME) i rozszerzenia źródło nośnika (MSE) w v62 przenośnych przeglądarki Chrome lub nowszym

Artykuł zawiera również odpowiedzi na często zadawane pytania dotyczące przesyłania strumieniowego w trybie offline Widevine chronione zawartości.

## <a name="requirements"></a>Wymagania 

Przed wdrożeniem dla Widevine DRM w trybie offline, na urządzeniach z systemem Android, należy najpierw:

- Zapoznanie się z pojęciami opisanymi ochrony w trybie online zawartości przy użyciu Widevine DRM. To jest omówiona szczegółowo w dokumentach/próbek:
    - [Użyj usługi Azure Media Services do dostarczania licencji DRM lub kluczy AES](media-services-deliver-keys-and-licenses.md)
    - [Szyfrowanie CENC przy użyciu technologii Multi-DRM i kontroli dostępu: projekt wzorcowy i implementacja wzorcowa na platformie Azure i w usłudze Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Szyfrowanie PlayReady i Widevine dynamicznie wspólnego z platformą .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Użyj usługi Azure Media Services do dostarczania licencji PlayReady i Widevine z platformą .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Zapoznanie się z zestawu SDK ExoPlayer Google dla systemu Android, odtwarzacza wideo open source SDK może obsługiwać w trybie offline odtwarzania Widevine DRM. 
    - [Zestaw SDK ExoPlayer](https://github.com/google/ExoPlayer)
    - [Przewodnik dewelopera ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog deweloperów EoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfiguracja ochrony zawartości w usłudze Azure Media Services

Po skonfigurowaniu ochrony Widevine zasobów w usłudze Media Services, musisz utworzyć ContentKeyAuthorizationPolicyOption, które określone następujących czynności:

1. System DRM (Widevine)
2. Określanie jak zawartości klucza dostawy ContentKeyAuthorizationPolicyRestriction jest autoryzowany w usługi dostarczania licencji (otwarte lub tokenu autoryzacji)
3. Szablon licencji DRM (Widevine)

Aby włączyć **w trybie offline** tryb licencji Widevine, należy skonfigurować [szablonu licencji Widevine](media-services-widevine-license-template-overview.md). W **policy_overrides** obiekt, ustaw **can_persist** właściwości **true** (wartość domyślna to false). 

Poniższy przykładowy kod używa .NET, aby umożliwić **w trybie offline** tryb licencji Widevine. Kod jest oparta na [ za pomocą PlayReady i Widevine dynamicznego szyfrowania Common Encryption z platformą .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) próbki. 

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

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurowanie systemu Android player dla odtwarzania w trybie offline

Najprostszym sposobem opracowywanie aplikacji odtwarzacza macierzystego, urządzeń z systemem Android jest użycie [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), odtwarzacza wideo open source zestawu SDK. ExoPlayer obsługuje funkcje nie są obecnie obsługiwane przez firmy Android native MediaPlayer interfejs API, włącznie z protokołami dostarczania MPEG DASH i Microsoft Smooth Streaming.

ExoPlayer wersji version 2.6 lub nowszej zawiera wiele klas, które obsługują odtwarzania Widevine DRM w trybie offline. W szczególności klasa OfflineLicenseHelper udostępnia funkcje narzędzia ułatwiające użycie DefaultDrmSessionManager pobierania, odnowienia i zwolnienie licencji w trybie offline. Klasy w folderu zestawu SDK "Biblioteka/core/src/main/java/com/google/android/exoplayer2/offline /" obsługi w trybie offline wideo pobierania zawartości.

Poniższa lista klas ułatwia trybu offline w zestawie SDK ExoPlayer dla systemu Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- Library/Core/src/main/Java/com/Google/android/exoplayer2/offline/Downloader.Java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Deweloperzy powinien odwoływać [przewodnik dewelopera ExoPlayer](https://google.github.io/ExoPlayer/guide.html) i odpowiadający mu [Blog deweloperów](https://medium.com/google-exoplayer) podczas tworzenia aplikacji. Google nie została zwolniona kodu pełni udokumentowane odwołania implementacji lub przykładowej aplikacji ExoPlayer obsługi w tej chwili Widevine w trybie offline, więc informacje są ograniczone do przewodnik dla deweloperów i blog. 

### <a name="working-with-older-android-devices"></a>Praca z starszych urządzeń z systemem Android

Niektóre starsze urządzeń z systemem Android, należy ustawić wartości dla następujących **policy_overrides** właściwości (zdefiniowany w [szablonu licencji Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, i **license_duration_seconds**. Alternatywnie można je ustawić na zero, co oznacza, że czas nieskończone/nieokreślony.  

Wartości muszą ustawione w taki sposób, aby uniknąć błędów przepełnienie liczby całkowitej. Aby uzyskać więcej informacje o problemie, zobacz https://github.com/google/ExoPlayer/issues/3150 i https://github.com/google/ExoPlayer/issues/3112. <br/>Jeśli nie należy ustawiać wartości jawnie, bardzo dużej wartości **PlaybackDurationRemaining** i **LicenseDurationRemaining** zostanie przypisana, (na przykład 9223372036854775807, co jest maksymalną wartość dodatnią dla 64-bitowa liczba całkowita). W związku z tym licencji Widevine prawdopodobnie wygasłe i dlatego nie nastąpi odszyfrowywanie. 

Ten problem nie występuje w systemie Android interfejs typu lizak 5.0 lub nowszym od systemu Android 5.0 jest pierwsza wersja systemu Android zostało zaprojektowane do zapewnienia pełnej obsługi ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) i platform 64-bitowym, podczas Android 4.4 KitKat pierwotnie przeznaczony do obsługi ARMv7 i 32-bitowych platform jako innych starszych wersji systemu Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Tworzenie aplikacji systemu Android odtwarzania za pomocą platformy Xamarin

Powiązania Xamarin dla ExoPlayer można znaleźć przy użyciu następujących łączy:

- [Biblioteka powiązania Xamarin dla biblioteki Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Powiązania Xamarin dla ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Zobacz też następujące wątku: [powiązania Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome apps odtwarzacza dla systemu Android

Począwszy od wersji programu [Android v — Chrome. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), trwałe licencji w EME jest obsługiwana. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) jest teraz również obsługiwane w przeglądarce Chrome dla systemu Android. Dzięki temu można tworzyć aplikacje odtwarzania w trybie offline w przeglądarce Chrome, jeśli użytkownicy końcowi mają to (lub nowszej) wersji przeglądarki Chrome. 

Ponadto Google ma produkowane przykładowej aplikacji sieci Web progresywnego (PWA) i open-powierzając jej ich konserwację go: 

- [Kod źródłowy](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Wersja Google hostowanej](https://biograf-155113.appspot.com/ttt/episode-2/) (działa tylko w v Chrome 62 lub nowszej na urządzeniach z systemem Android)

Jeśli uaktualnienie przenośnych przeglądarki Chrome v62 (lub nowszej) na telefonach z systemem Android i testowania powyższych hostowanej przykładowej aplikacji, zostanie wyświetlone tej pracy odtwarzania online przesyłania strumieniowego i w trybie offline.

Powyżej aplikacja open source PWA został utworzony w środowisku Node.js. Jeśli chcesz udostępniać wersji na serwerze Ubuntu pamiętać następujące typowe problemy napotkano, uniemożliwiające odtwarzania:

1. Problem CORS: Przykładowe wideo w przykładowej aplikacji znajduje się w https://storage.googleapis.com/biograf-video-files/videos/. Google skonfigurował CORS dla wszystkich ich próbki hostowanej w zasobniku Google magazynu w chmurze. Są one obsługiwane z nagłówki CORS, jawne określenie wpis CORS: https://biograf-155113.appspot.com (ich próbki obsługuje domeną, w których google) zapobieganie dostępowi przez inne lokacje. Jeśli spróbujesz, zostanie wyświetlony następujący błąd HTTP: nie można załadować https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: Brak nagłówka "Access-Control-Allow-Origin" jest obecny dla żądanego zasobu. Pochodzenie "https://13.85.80.81:8080" nie jest w związku z tym prawa dostępu. Jeśli odpowiedź nieprzezroczyste spełnia Twoje potrzeby, ustaw tryb żądania "nie-cors" można pobrać zasobu z CORS wyłączone.
2. Certyfikat problem: począwszy od programu Chrome v 58, EME dla Widevine wymaga protokołu HTTPS. Dlatego należy do hostowania przykładowej aplikacji przy użyciu protokołu HTTPS z X509 certyfikatu. Certyfikat testowy zwykle nie działa z powodu następujących wymagań: należy uzyskać certyfikat spełnia następujące wymagania minimalne:
    - Chrome i Firefox wymagane ustawienie alternatywna nazwa podmiotu SAN istnieje w certyfikacie
    - Certyfikat musi być zaufany urząd certyfikacji i certyfikat z podpisem programowanie nie działa.
    - Certyfikat musi mieć CN, pasującego do nazwy DNS serwera sieci web lub bramy

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="question"></a>Pytanie

Jak może zapewnić stałe licencji (w trybie offline obsługą) dla niektórych klientów/użytkowników i nietrwałe licencji (w trybie offline wyłączona) dla innych użytkowników? Czy mają zduplikowane zawartości i używać oddzielnego klucz zawartości?

### <a name="answer"></a>Odpowiedź
Nie trzeba przeprowadzać zduplikowana zawartość. Możesz użyć pojedynczej kopii zawartości i jednego ContentKeyAuthorizationPolicy, ale dwa oddzielne ContentKeyAuthorizationPolicyOption firmy:

1. IContentKeyAuthorizationPolicyOption 1: używa trwałe licencji i 1 ContentKeyAuthorizationPolicyRestriction, który zawiera oświadczenia, takich jak license_type = "Trwała"
2. IContentKeyAuthorizationPolicyOption 2: używa nietrwałe licencji i 2 ContentKeyAuthorizationPolicyRestriction, która zawiera oświadczenia, takich jak license_type = "Nonpersistent"

Dzięki temu, gdy nadejdzie żądanie licencji z poziomu aplikacji klienta z żądania licencji nie ma żadnej różnicy. Jednak inne użytkownika/urządzenie, Usługa tokenu Zabezpieczającego powinny mieć logiki biznesowej w celu wystawienia innego tokenów JWT zawierających różne oświadczenia (jeden powyżej license_type dwóch). Wartość oświadczenia w JWT token będzie służyć do chce przez usługę licencji wystawić jakiego typu licencji: stałe i nietrwałe.

Oznacza to, że Secure Token Service (STS) musi mieć informacji logiki i klienta lub urządzeniu firm, aby dodać odpowiednią wartość oświadczenia w tokenie.

### <a name="question"></a>Pytanie

Widevine poziomów zabezpieczeń, w firmy Google [doc Przegląd architektury Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentacji definiuje trzech różnych poziomów ochrony. Jednak w [dokumentacja usługi Azure Media Services szablonu licencji Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), opisano pięciu różnych poziomów ochrony. Co to jest relacja lub mapowanie między dwoma zestawami różnych poziomów zabezpieczeń?

### <a name="answer"></a>Odpowiedź

W firmy Google [Przegląd architektury Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), definiuje następujących trzech poziomów ochrony:

1.  Poziom zabezpieczeń 1: Przetwarzanie zawartości, kryptografii i kontroli są wykonywane w środowisku zaufane wykonywanie (TEE). W niektórych modeli wdrażania przetwarzanie zabezpieczeń może być wykonana w różnych układów.
2.  Poziom zabezpieczeń 2: Wykonuje kryptografii (ale nie wideo przetwarzania) w ramach TEE: odszyfrowane buforów są zwracane do domeny aplikacji i przetwarzane za pomocą oddzielnego wideo sprzętu lub oprogramowania. Na poziomie 2, jednak kryptograficznych informacje są nadal przetwarzane tylko w obrębie TEE.
3.  Poziom zabezpieczeń 3 nie ma TEE na urządzeniu. Odpowiednie środki mogą chronić informacje kryptograficznych i odszyfrowania zawartości w systemie operacyjnym hosta. Implementacja poziom 3 mogą również obejmować aparatem kryptograficznych sprzętowej, ale który tylko zwiększa wydajność, nie zabezpieczeń.

Jednocześnie, w [dokumentacja usługi Azure Media Services szablonu licencji Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), właściwość security_level content_key_specs może mieć następujące pięciu różnych wartości (niezawodności wymagania dotyczące klienta dla odtwarzania):

1.  Programowa whitebox kryptograficznego jest wymagana.
2.  Kryptograficznego oprogramowania i zaciemnionego dekodera jest wymagana.
3.  Materiał klucza i operacje kryptograficzne, które muszą być wykonywane w sprzętu kopii TEE.
4.  Kryptograficznych i dekodowania zawartości musi zostać wykonane w sprzętu kopii TEE.
5.  Kryptograficzny dekodowania i wszystkie obsługi nośnika (skompresowanym i nieskompresowanym) musi być przetwarzanych w ramach sprzętu kopii TEE.

Zarówno poziomów zabezpieczeń są definiowane przez Google Widevine. Różnica polega na tym poziomie jego użycia: Architektura poziomie lub interfejsu API. Poziomy pięć zabezpieczeń są używane w interfejsie API Widevine. Obiekt content_key_specs, który zawiera security_level jest deserializacji i przekazywane do usługi Widevine globalne dostarczanie przez usługi Azure Media Services Widevine licencji usługi. W poniższej tabeli przedstawiono mapowanie między dwoma zestawami poziomów zabezpieczeń.

| **Poziomy zabezpieczeń zdefiniowane w architekturze Widevine** |**Poziomy zabezpieczeń używane w Widevine interfejsu API**|
|---|---| 
| **Poziom zabezpieczeń 1**: przetwarzanie zawartości, kryptografii i kontroli są wykonywane w środowisku zaufane wykonywanie (TEE). W niektórych modeli wdrażania przetwarzanie zabezpieczeń może być wykonana w różnych układów.|**security_level = 5**: kryptograficzny dekodowania i wszystkie obsługi nośnika (skompresowanym i nieskompresowanym) musi być przetwarzanych w ramach sprzętu kopii TEE.<br/><br/>**security_level = 4**: kryptograficznych i dekodowania zawartości musi zostać wykonane w sprzętu kopii TEE.|
**Poziom zabezpieczeń 2**: wykonuje kryptografii (ale nie wideo przetwarzania) w ramach TEE: odszyfrowane buforów są zwracane do domeny aplikacji i przetwarzane za pomocą oddzielnego wideo sprzętu lub oprogramowania. Na poziomie 2, jednak kryptograficznych informacje są nadal przetwarzane tylko w obrębie TEE.| **security_level = 3**: materiału klucza i operacje kryptograficzne, które muszą być wykonywane w sprzętu kopii TEE. |
| **Poziom zabezpieczeń 3**: nie ma TEE na urządzeniu. Odpowiednie środki mogą chronić informacje kryptograficznych i odszyfrowania zawartości w systemie operacyjnym hosta. Implementacja poziom 3 mogą również obejmować aparatem kryptograficznych sprzętowej, ale który tylko zwiększa wydajność, nie zabezpieczeń. | **security_level = 2**: kryptograficznego oprogramowania i zaciemnionego dekodera jest wymagana.<br/><br/>**security_level = 1**: programowe whitebox kryptograficznego jest wymagana.|

### <a name="question"></a>Pytanie

Dlaczego pobierania zawartości podąża tak długo?

### <a name="answer"></a>Odpowiedź

Istnieją dwa sposoby zwiększenia szybkości pobierania:

1.  Włącz CDN tak, aby użytkownicy końcowi będą prawdopodobnie trafień CDN zamiast punktu końcowego przesyłania strumieniowego/punkt początkowy dla pobierania zawartości. Jeśli użytkownik trafi punktu końcowego przesyłania strumieniowego, każdy HLS segment lub fragmentu DASH dynamicznie zostaje spakowany i szyfrowane. Nawet jeśli jest to Opóźnienie milisekund skalę dla każdego segmentu/fragmentu, jeśli masz godzinę długo wideo, skumulowany czas oczekiwania może być duży, co powoduje dłuższe pobierania.
2.  Należy zapewnić użytkownikom końcowym możliwość selektywnie pobierać warstwy jakości wideo i audio ścieżki, a nie całej zawartości. Dla trybu offline nie istnieje żaden punkt, aby pobrać wszystkie warstwy jakości. Istnieją dwa sposoby, w tym:
    1.  Klient kontrolowane: player aplikacji automatycznie wybiera albo użytkownik wybierze warstwy jakości wideo i audio ścieżki do pobrania;
    2.  Usługa kontrolowane: jeden funkcja dynamicznej manifestu w usłudze Azure Media Services do tworzenia filtru (globalne), która ogranicza listy odtwarzania HLS lub DASH MPD do warstwy pojedynczego jakości wideo i wybrane ścieżki audio. Następnie adresu URL pobierania widoczne dla użytkowników końcowych obejmuje tego filtru.

## <a name="summary"></a>Podsumowanie

W tym artykule omówiono sposób wdrożenia w trybie offline odtwarzania zawartości DASH chronione przez Widevine na urządzeniach z systemem Android.  Również odpowiedzi na często zadawane pytania dotyczące przesyłania strumieniowego w trybie offline Widevine chronione zawartości.
