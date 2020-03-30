---
title: Ochrona zawartości za pomocą szyfrowania dynamicznego usługi Media Services w wersji 3
titleSuffix: Azure Media Services
description: Dowiedz się więcej o ochronie zawartości za pomocą szyfrowania dynamicznego, protokołów przesyłania strumieniowego i typów szyfrowania w usłudze Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461116"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Ochrona zawartości za pomocą szyfrowania dynamicznego usługi Media Services

Usługa Azure Media Services ułatwia zabezpieczenie multimediów od momentu opuszczenia komputera przez magazyn, przetwarzanie i dostarczanie. Usługa Media Services umożliwia dynamiczne szyfrowanie zawartości na żywo i na żądanie za pomocą standardu Advanced Encryption Standard (AES-128) lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Usługa Media Services zapewnia również usługę dostarczania licencji kluczy AES i DRM (PlayReady, Widevine i FairPlay) autoryzowanym klientom. Jeśli zawartość jest szyfrowana za pomocą czystego klucza AES i jest wysyłana za pośrednictwem protokołu HTTPS, nie jest w stanie wyczyścić, dopóki nie dotrze do klienta. 

W umiań Media Services w wersji 3 klucz zawartości jest skojarzony z lokalizatorem przesyłania strumieniowego (zobacz [ten przykład).](protect-with-aes128.md) Jeśli korzystasz z usługi dostarczania kluczy usługi Media Services, można umożliwić usługi Azure Media Services generowanie klucza zawartości dla Ciebie. Klucz zawartości powinien być generowany samodzielnie, jeśli korzystasz z usługi dostarczania kluczy lub jeśli musisz obsługiwać scenariusz wysokiej dostępności, w którym musisz mieć ten sam klucz zawartości w dwóch centrach danych.

Gdy odtwarzacz zażąda strumienia, usługa Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu czystego klucza AES lub szyfrowania DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy usługi Media Services lub określonej usługi dostarczania kluczy. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa ocenia zasady klucza zawartości określone dla klucza.

Za pomocą interfejsu API REST lub biblioteki klienta usługi Media Services można skonfigurować zasady autoryzacji i uwierzytelniania dla licencji i kluczy.

Na poniższej ilustracji przedstawiono przepływ pracy dla ochrony zawartości usługi Media Services:

![Przepływ pracy dla ochrony zawartości usługi Media Services](./media/content-protection/content-protection.svg)
  
*Szyfrowanie dynamiczne &#42; obsługuje klawisze Wyczyść AES-128, CBCS i CENC. Aby uzyskać szczegółowe informacje, zobacz [macierz obsługi](#streaming-protocols-and-encryption-types).*

W tym artykule opisano pojęcia i terminologię, które pomagają zrozumieć ochronę zawartości za pomocą usługi Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Główne elementy systemu ochrony zawartości

Aby pomyślnie ukończyć system ochrony zawartości, należy w pełni zrozumieć zakres wysiłku. W poniższych sekcjach przedstawiono przegląd trzech części, które należy zaimplementować.

> [!NOTE]
> Zalecamy skupienie i pełne przetestowanie każdej części w poniższych sekcjach przed przejściem do następnej części. Aby przetestować system ochrony zawartości, użyj narzędzi określonych w sekcjach.

### <a name="media-services-code"></a>Kod usługi Media Services
  
Przykład [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) pokazuje, jak zaimplementować system multi-DRM z usługą Media Services w wersji 3 przy użyciu platformy .NET. Pokazuje również, jak korzystać z usługi dostarczania licencji/kluczy usługi Media Services.
  
Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Aby zobaczyć, co ma sens łączyć, zobacz [Protokoły przesyłania strumieniowego i typy szyfrowania](#streaming-protocols-and-encryption-types).

W przykładzie pokazano, jak:

1. Tworzenie i konfigurowanie [zasad klucza zawartości](content-key-policy-concept.md).

   Tworzenie zasad klucza zawartości w celu skonfigurowania sposobu dostarczania klucza zawartości (który zapewnia bezpieczny dostęp do zasobów) klientom końcowym:  

   * Zdefiniuj autoryzację dostarczania licencji. Określ logikę sprawdzania autoryzacji na podstawie oświadczeń w tokenie JSON Web Token (JWT).
   * Skonfiguruj licencje [PlayReady,](playready-license-template-overview.md) [Widevine](widevine-license-template-overview.md)i/lub [FairPlay.](fairplay-license-overview.md) Szablony umożliwiają konfigurowanie praw i uprawnień dla każdego z drmów.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Utwórz [lokalizator przesyłania strumieniowego](streaming-locators-concept.md) skonfigurowany do przesyłania strumieniowego zaszyfrowanego zasobu.
  
   Lokalizator przesyłania strumieniowego musi być skojarzony z [zasadami przesyłania strumieniowego](streaming-policy-concept.md). W tym przykładzie `StreamingLocator.StreamingPolicyName` ustawiliśmy zasady "Predefined_MultiDrmCencStreaming".

   Stosowane są szyfrowania PlayReady i Widevine, a klucz jest dostarczany do klienta odtwarzania na podstawie skonfigurowanych licencji DRM. Jeśli chcesz również zaszyfrować strumień za pomocą cbcs (FairPlay), użyj zasad "Predefined_MultiDrmStreaming".

   Lokalizator przesyłania strumieniowego jest również skojarzony z zdefiniowanymi zasadami klucza zawartości.

3. Utwórz token testowy.

   Metoda `GetTokenAsync` pokazuje, jak utworzyć token testowy.
4. Tworzenie adresu URL przesyłania strumieniowego.

   Metoda `GetDASHStreamingUrlAsync` pokazuje, jak utworzyć adres URL przesyłania strumieniowego. W takim przypadku adres URL strumieniuje zawartość DASH.

### <a name="player-with-an-aes-or-drm-client"></a>Odtwarzacz z klientem AES lub DRM

Aplikacja odtwarzacza wideo oparta na SDK gracza (natywnego lub przeglądarkowego) musi spełniać następujące wymagania:

* Zestaw SDK odtwarzacza obsługuje potrzebnych klientów DRM.
* Zestaw SDK odtwarzacza obsługuje wymagane protokoły przesyłania strumieniowego: Smooth, DASH i/lub HTTP Live Streaming (HLS).
* SDK gracza może obsłużyć przekazywanie tokenu JWT w żądaniu nabycia licencji.

Odtwarzacz można utworzyć za pomocą interfejsu [API programu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API Azure Media Player ProtectionInfo,](https://amp.azure.net/libs/amp/latest/docs/) aby określić, która technologia DRM ma być używana na różnych platformach DRM.

Do testowania zaszyfrowanej zawartości AES lub CENC (Widevine i/lub PlayReady) można użyć [programu Azure Media Player](https://aka.ms/azuremediaplayer). Upewnij się, że wybrano **opcje zaawansowane** i sprawdź opcje szyfrowania.

Jeśli chcesz przetestować zawartość zaszyfrowaną FairPlay, użyj [tego odtwarzacza testowego.](https://aka.ms/amtest) Odtwarzacz obsługuje widevine, PlayReady i FairPlay DRM, a także AES-128 czyste szyfrowanie klucza.

Wybierz odpowiednią przeglądarkę, aby przetestować różne drmy:

* Chrome, Opera lub Firefox dla Widevine.
* Microsoft Edge lub Internet Explorer 11 dla PlayReady.
* Safari na macOS dla FairPlay.

### <a name="security-token-service"></a>Usługa tokenu zabezpieczającego

Usługa tokenu zabezpieczającego (STS) wystawia JWT jako token dostępu do dostępu do zasobów zaplecza. Usługi Azure Media Services można użyć jako zasobu zaplecza usługi Azure Media Services. STS musi zdefiniować następujące rzeczy:

* Wystawcy i odbiorcy (lub zakres).
* Roszczenia, które są zależne od wymagań biznesowych w zakresie ochrony zawartości.
* Weryfikacja symetryczna lub asymetryczna w celu weryfikacji podpisu.
* Obsługa przerzucenia kluczy (jeśli to konieczne).

Za pomocą [tego narzędzia STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) można przetestować sts. Obsługuje wszystkie trzy typy kluczy weryfikacji: symetryczne, asymetryczne lub usługi Azure Active Directory (Azure AD) z przerzucania kluczy.

## <a name="streaming-protocols-and-encryption-types"></a>Protokoły przesyłania strumieniowego i typy szyfrowania

Za pomocą usługi Media Services można dynamicznie dostarczać zawartość za pomocą czystego klucza AES lub szyfrowania DRM przy użyciu funkcji PlayReady, Widevine lub FairPlay. Obecnie można szyfrować formaty HLS, MPEG DASH i Smooth Streaming. Każdy protokół obsługuje następujące metody szyfrowania.

### <a name="hls"></a>HLS

Protokół HLS obsługuje następujące formaty kontenerów i schematy szyfrowania:

|Format kontenera|Schemat szyfrowania|Przykład adresu URL|
|---|---|---|
|Wszystkie|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (w tym HEVC/H.265) jest obsługiwany na następujących urządzeniach:

* systemu iOS 11 lub nowszego.
* iPhone 8 lub nowszym.
* MacOS High Sierra z procesorem Intel 7. generacji.

### <a name="mpeg-dash"></a>MPEG-DASH

Protokół MPEG-DASH obsługuje następujące formaty kontenerów i schematy szyfrowania:

|Format kontenera|Schemat szyfrowania|Przykłady adresów URL
|---|---|---|
|Wszystkie|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CsF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Protokół Płynna transmisja strumieniowa obsługuje następujące formaty kontenerów i schematy szyfrowania.

|Protocol (Protokół)|Format kontenera|Schemat szyfrowania|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Przeglądarki

Typowe przeglądarki obsługują następujących klientów DRM:

|Przeglądarka|Szyfrowanie|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Kontrolowanie dostępu do zawartości

Możesz kontrolować, kto ma dostęp do zawartości, konfigurując zasady klucza zawartości. Usługa Media Services obsługuje wiele sposobów autoryzacji użytkowników, którzy tworzą żądania klucza. Klient (gracz) musi spełniać zasady, zanim klucz może zostać dostarczony do klienta. Zasady klucza zawartości mogą mieć *ograniczenia otwarcia* lub *tokenu.*

Zasady klucza zawartości z ograniczeniami dostępu mogą być używane, gdy chcesz wydać licencję każdemu bez autoryzacji. Jeśli na przykład przychody są oparte na reklamach, a nie na subskrypcji.  

W przypadku zasad klucza zawartości z ograniczeniami tokenu klucz zawartości jest wysyłany tylko do klienta, który przedstawia prawidłowy token JWT lub prosty token internetowy (SWT) w żądaniu licencji/klucza. Ten token musi być wystawiony przez STS.

Usługi Azure AD można używać jako usługi STS lub wdrażać [niestandardowy plik STS](#using-a-custom-sts). Sts musi być skonfigurowany do tworzenia tokenu podpisanego przy określonym kluczu i oświadczeń problem, które zostały określone w konfiguracji ograniczenia tokenu. Usługa dostarczania licencji/kluczy usługi Media Services zwraca żądaną licencję lub klucz do klienta, jeśli istnieją oba te warunki:

* Token jest prawidłowy.
* Oświadczenia w tokenie są zgodne z tymi skonfigurowanym dla licencji lub klucza.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacji, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, za pomocą którym token został podpisany. Wystawcą jest STS, który wystawia token. Odbiorcy, czasami nazywany zakres, opisuje intencji tokenu lub zasobu, który token autoryzuje dostęp do. Usługa dostarczania licencji/kluczy usługi Media Services sprawdza, czy te wartości w tokenie są zgodne z wartościami w szablonie.

### <a name="token-replay-prevention"></a>Zapobieganie powtarzaniu żetonów

Funkcja *zapobiegania powtarzaniu powtórzów tokenu* umożliwia klientom usługi Media Services ustawienie limitu liczby razy tego samego tokenu, który może być użyty do żądania klucza lub licencji. Klient może dodać oświadczenie `urn:microsoft:azure:mediaservices:maxuses` typu w tokenie, gdzie wartość jest liczba razy token może służyć do uzyskania licencji lub klucza. Wszystkie kolejne żądania z tym samym tokenem do dostarczania kluczy zwróci nieautoryzowaną odpowiedź. Zobacz, jak dodać oświadczenie w [przykładzie DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci muszą mieć kontrolę nad generowaniem tokenów. Oświadczenie musi być umieszczone w samym tokenie.
* Korzystając z tej funkcji, żądania z tokenami, których czas wygaśnięcia jest więcej niż jedną godzinę od momentu odebranie żądania są odrzucane z nieautoryzowaną odpowiedzią.
* Tokeny są jednoznacznie identyfikowane przez ich podpis. Wszelkie zmiany w ładunku (na przykład, aktualizacja do czasu wygaśnięcia lub oświadczenia) zmienia podpis tokenu i będzie liczony jako nowy token, że dostarczanie kluczy nie natknąć się wcześniej.
* Odtwarzanie kończy się niepowodzeniem, `maxuses` jeśli token przekroczył wartość ustawioną przez klienta.
* Ta funkcja może służyć do wszystkich istniejących zawartości chronionej (tylko token wystawione musi zostać zmieniony).
* Ta funkcja działa zarówno z JWT, jak i SWT.

## <a name="using-a-custom-sts"></a>Korzystanie z niestandardowego sts

Klient może użyć niestandardowego sts do dostarczania tokenów. Powody to:

* Dostawca tożsamości (IDP) używany przez klienta nie obsługuje usługi STS. W takim przypadku opcja może być niestandardowa sts.
* Klient może potrzebować bardziej elastycznej lub ściślejszej kontroli, aby zintegrować usługę STS z systemem rozliczeniowym subskrybenta klienta.

   Na przykład operator usługi [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) może oferować wiele pakietów subskrybentów, takich jak premium, podstawowe i sportowe. Operator może chcieć dopasować oświadczenia w tokenie z pakietem subskrybenta, dzięki czemu tylko zawartość w określonym pakiecie są udostępniane. W takim przypadku niestandardowy STS zapewnia niezbędną elastyczność i kontrolę.

* Aby uwzględnić oświadczenia niestandardowe w tokenie, aby wybrać między różnymi ContentKeyPolicyOptions z różnymi parametrami licencji DRM (licencja subskrypcyjna a licencja na wynajem).
* Aby dołączyć oświadczenie reprezentujące identyfikator klucza zawartości klucza, do których token udziela dostępu.

Podczas korzystania z niestandardowego STS należy wyw.

* Podczas konfigurowania usługi dostarczania licencji dla zasobu należy określić klucz zabezpieczeń używany do weryfikacji przez niestandardowy sts zamiast bieżącego klucza z usługi Azure AD.
* Podczas generowania tokenu JTW klucz zabezpieczeń jest określony zamiast klucza prywatnego bieżącego certyfikatu X509 w usłudze Azure AD.

Istnieją dwa typy kluczy zabezpieczeń:

* Klucz symetryczny: Ten sam klucz jest używany do generowania i weryfikacji JWT.
* Klucz asymetryczny: Para kluczy publiczno-prywatnych w certyfikacie X509 jest używana z kluczem prywatnym do szyfrowania/generowania JWT i przy użyciu klucza publicznego w celu zweryfikowania tokenu.

Jeśli używasz .NET Framework/C# jako platformy programistycznej, certyfikat X509 używany dla asymetrycznego klucza zabezpieczeń musi mieć długość klucza co najmniej 2048. Ta długość klucza jest wymaganiem klasy System.IdentityModel.Tokens.X509AsymmetricSecurityKey w .NET Framework. W przeciwnym razie zostanie zgłoszony następujący wyjątek: IDX10630: "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" dla podpisywania nie może być mniejszy niż bity "2048".

## <a name="custom-key-and-license-acquisition-url"></a>Niestandardowy klucz i adres URL pozyskiwania licencji

Użyj następujących szablonów, jeśli chcesz określić inną usługę dostarczania licencji/klucza (nie usługi Media Services). Dwa wymienne pola w szablonach są tam, dzięki czemu można udostępniać zasady przesyłania strumieniowego w wielu zasobach zamiast tworzyć zasady przesyłania strumieniowego na zasób. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Szablon adresu URL usługi niestandardowej, która dostarcza klucze do graczy użytkowników końcowych. Nie jest wymagane, gdy używasz usługi Azure Media Services do wystawiania kluczy. 

   Szablon obsługuje wymienne tokeny, które usługa będzie aktualizować w czasie wykonywania z wartością specyficzną dla żądania.  Aktualnie obsługiwane wartości tokenów to:
   * `{AlternativeMediaId}`, który jest zastępowany wartością StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, który jest zastępowany wartością identyfikatora żądanego klucza.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Szablon adresu URL usługi niestandardowej, która dostarcza licencje graczom końcowym. Nie jest wymagane, gdy używasz usługi Azure Media Services do wystawiania licencji.

   Szablon obsługuje wymienne tokeny, które usługa będzie aktualizować w czasie wykonywania z wartością specyficzną dla żądania. Aktualnie obsługiwane wartości tokenów to:  
   * `{AlternativeMediaId}`, który jest zastępowany wartością StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, który jest zastępowany wartością identyfikatora żądanego klucza. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Tak samo jak w poprzednim szablonie, tylko dla Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: tak samo jak w poprzednim szablonie, tylko dla FairPlay.  

Przykład:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`ma wartość żądanego klucza. Można użyć, `AlternativeMediaId` jeśli chcesz zamapować żądanie do jednostki po twojej stronie. Na przykład `AlternativeMediaId` może służyć do wyszukiwania uprawnień.

W przykładach REST, które używają niestandardowych adresów URL pozyskiwania licencji/kluczy, zobacz [Zasady przesyłania strumieniowego — tworzenie](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` zostanie wyświetlony błąd, upewnij się, że określisz odpowiednie zasady przesyłania strumieniowego.

Jeśli wystąpią błędy, `_NOT_SPECIFIED_IN_URL`które kończą się na , upewnij się, że określono format szyfrowania w adresie URL. Może to być na przykład `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Zobacz [Protokoły przesyłania strumieniowego i typy szyfrowania](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

* [Ochrona za pomocą szyfrowania AES](protect-with-aes128.md)
* [Ochrona przy użyciu technologii DRM](protect-with-drm.md)
* [Projektowanie systemu ochrony zawartości multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)
* [Szyfrowanie po stronie magazynu](storage-account-concept.md#storage-side-encryption)
* [Często zadawane pytania](frequently-asked-questions.md)
* [Program obsługi tokenów sieci Web JSON](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
