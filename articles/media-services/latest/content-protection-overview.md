---
title: Ochrona zawartości przy użyciu Media Services szyfrowania dynamicznego — Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie ochrony zawartości przy użyciu szyfrowania dynamicznego. Informacje o protokołach przesyłania strumieniowego i typach szyfrowania można także uzyskać.
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
ms.date: 07/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a928640aa6d56f0a39011a2cabcf979b4d907a46
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561478"
---
# <a name="protect-your-content-by-using-media-services-dynamic-encryption"></a>Ochrona zawartości przy użyciu Media Services szyfrowania dynamicznego

Za pomocą Azure Media Services można zabezpieczyć nośnik od momentu, w którym komputer przejdzie przez magazyn, przetwarzanie i dostarczanie. Za pomocą Media Services można dostarczyć zawartość dynamiczną i na żądanie zaszyfrowaną dynamicznie z Advanced Encryption Standard (AES-128) lub z jednego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów.  

W Media Services v3 klucz zawartości jest skojarzony z lokalizatorem przesyłania strumieniowego (patrz [ten przykład](protect-with-aes128.md)). Jeśli korzystasz z usługi Media Services Key Delivery, możesz Azure Media Services wygenerować klucz zawartości. Klucz zawartości należy wygenerować samodzielnie, jeśli używasz własnej usługi dostarczania kluczy lub jeśli chcesz obsłużyć scenariusz wysokiej dostępności, w którym musisz mieć ten sam klucz zawartości w dwóch centrach danych.

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania otwartym kluczem AES i szyfrowania DRM. Aby odszyfrować strumienia, gracz żąda klucza z usługę dostarczania kluczy usługi Media Services lub usługę dostarczania kluczy, wskazana. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zawartości zasad kluczy, które podane dla klucza.

Interfejs API REST lub biblioteka klienta usługi Media Services umożliwia konfigurowanie zasad autoryzacji i uwierzytelniania dla licencji i kluczy.

Na poniższej ilustracji przedstawiono przepływ pracy Media Services ochrony zawartości:

![Przepływ pracy dla Media Services Content Protection](./media/content-protection/content-protection.svg)
  
&#42;*Szyfrowanie dynamiczne obsługuje algorytm AES-128 Clear Key, CBCS i Cenc. Aby uzyskać szczegółowe informacje, zobacz [Macierz obsługi](#streaming-protocols-and-encryption-types).*

W tym artykule wyjaśniono pojęcia i terminologia ułatwiająca zrozumienie ochrony zawartości za pomocą Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Główne składniki systemu ochrony zawartości

Aby pomyślnie zakończyć system ochrony zawartości, należy w pełni zrozumieć zakres nakładu pracy. Poniższe sekcje zawierają przegląd trzech części, które należy zaimplementować. 

> [!NOTE]
> Zdecydowanie zalecamy skoncentrowanie i pełne testowanie każdej części w poniższych sekcjach przed przejściem do następnej części. Aby przetestować system ochrony zawartości, użyj narzędzi określonych w sekcjach.

### <a name="media-services-code"></a>Kod Media Services
  
W [przykładzie technologii DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) przedstawiono sposób implementacji systemu wieloskładnikowego z Media Services v3 przy użyciu platformy .NET. Przedstawiono w nim również, jak korzystać z usługi Media Services License/Delivery Key.   
  
Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Aby zobaczyć, co ma być przydatne, zobacz [protokoły przesyłania strumieniowego i typy szyfrowania](#streaming-protocols-and-encryption-types).

W przykładzie pokazano sposób:

1. Utwórz i skonfiguruj [zasady klucza zawartości](content-key-policy-concept.md).    

   Tworzenie zasad klucza zawartości w celu skonfigurowania sposobu, w jaki klucz zawartości (zapewniający bezpieczny dostęp do zasobów) jest dostarczany do klientów końcowych:  
 
   * Zdefiniuj autoryzację dostarczania licencji. Określ logikę sprawdzania autoryzacji na podstawie oświadczeń w tokenie sieci Web JSON (JWT).
   * Skonfiguruj licencje [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)i/lub [FairPlay](fairplay-license-overview.md) . Szablony umożliwiają skonfigurowanie praw i uprawnień dla każdego z protokołów DRM.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```
2. Utwórz [lokalizator przesyłania strumieniowego](streaming-locators-concept.md) skonfigurowany do przesyłania strumieniowego zaszyfrowanego zasobu. 
  
   Lokalizator przesyłania strumieniowego musi być skojarzony z [zasadami przesyłania strumieniowego](streaming-policy-concept.md). W tym przykładzie ustawimy `StreamingLocator.StreamingPolicyName` zasady "Predefined_MultiDrmCencStreaming". 
      
   Są stosowane szyfrowanie PlayReady i Widevine, a klucz jest dostarczany do klienta odtwarzania na podstawie skonfigurowanych licencji DRM. Jeśli chcesz również zaszyfrować strumień przy użyciu CBCS (FairPlay), użyj zasad "Predefined_MultiDrmStreaming".

   Lokalizator przesyłania strumieniowego jest również skojarzony z zdefiniowanymi zasadami klucza zawartości.
3. Utwórz token testu.

   `GetTokenAsync` Metoda pokazuje, jak utworzyć token testowy.
4. Tworzenie adresu URL przesyłania strumieniowego.

   `GetDASHStreamingUrlAsync` Metoda pokazuje, jak utworzyć adres URL przesyłania strumieniowego. W takim przypadku adres URL strumieniuje zawartość KRESKi.

### <a name="player-with-an-aes-or-drm-client"></a>Odtwarzacz z klientem AES lub DRM 

Aplikacja odtwarzacza wideo, oparte na odtwarzaczu zestawu SDK (natywny lub przeglądarki) musi spełniać następujące wymagania:

* Zestaw SDK odtwarzacza obsługuje wymaganych klientów DRM.
* Zestaw SDK odtwarzacza obsługuje wymagane protokoły przesyłania strumieniowego: Gładki, KRESKa i/lub HLS.
* Zestaw SDK odtwarzacza może obsłużyć przekazywanie tokenu JWT w żądaniu pozyskiwania licencji.

Odtwarzacz można utworzyć za pomocą [interfejsu API usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API usługi Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) do określenia technologii DRM na różnych platformach DRM.

Do testowania AES lub CENC (Widevine i/lub technologii PlayReady) zaszyfrowany zawartość, możesz użyć [usługi Azure Media Player](https://aka.ms/azuremediaplayer). Upewnij się, że wybrano **Opcje zaawansowane** i Sprawdź opcje szyfrowania.

Jeśli chcesz przetestować FairPlay zaszyfrowana zawartość, użyj [tego odtwarzacza testu](https://aka.ms/amtest). Odtwarzacz obsługuje Widevine, PlayReady i FairPlay protokołów DRM oraz szyfrowanie za pomocą klucza AES-128. 

Wybierz odpowiednią przeglądarkę, aby przetestować różne protokołów DRM:

* Chrome, Opera lub Firefox for Widevine
* Microsoft Edge lub Internet Explorer 11 dla oprogramowania PlayReady
* Safari on macOS for FairPlay

### <a name="security-token-service"></a>Usługa tokenu zabezpieczającego

Usługa tokenu zabezpieczającego (STS) wystawia token dostępu dla dostępu do zasobów zaplecza. Jako zasobu zaplecza można użyć usługi licencjonowania Azure Media Services/klucza dostawy. Usługa tokenu Zabezpieczającego musi definiują następujące elementy:

* Wystawcy i odbiorców (lub zakres)
* Oświadczenia, które są zależne od wymagań biznesowych w Ochrona zawartości
* Weryfikacja konfiguracji symetrycznej lub asymetrycznej weryfikacji podpisu
* Obsługa przerzucania kluczy (jeśli jest to konieczne)

Możesz użyć [tego narzędzia STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) do przetestowania usługi STS. Obsługuje ona wszystkie trzy typy kluczy weryfikacyjnych: symetryczne, asymetryczne lub Azure Active Directory (Azure AD) z przerzucaniem kluczy. 

## <a name="streaming-protocols-and-encryption-types"></a>Protokoły i typy szyfrowania przesyłania strumieniowego

Usługa Media Services umożliwia dostarczanie zawartości szyfrowane dynamicznie przy użyciu szyfrowania otwartym kluczem AES i technologii DRM szyfrowania za pomocą usług PlayReady, Widevine i FairPlay. Obecnie można zaszyfrować formatu HTTP Live Streaming (HLS), MPEG DASH i Smooth Streaming. Każdy protokół obsługuje następujące metody szyfrowania.

### <a name="hls"></a>HLS

Protokół HLS obsługuje następujące formaty kontenerów i schematy szyfrowania.

|Format kontenera|Schemat szyfrowania|Przykład adresu URL|
|---|---|---|
|Wszyscy|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (w tym HEVC/H. 265) są obsługiwane na następujących urządzeniach:

* System iOS 11 lub nowszy 
* iPhone 8 lub nowszy
* MacOS wysoka firma Sierra z procesorem Intel siódmej generacji

### <a name="mpeg-dash"></a>MPEG-DASH

Protokół MPEG-KRESKOWANY obsługuje następujące formaty kontenerów i schematy szyfrowania.

|Format kontenera|Schemat szyfrowania|Przykłady adresów URL
|---|---|---|
|Wszyscy|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Protokół Smooth Streaming obsługuje następujące formaty kontenerów i schematy szyfrowania.

|Protocol|Format kontenera|Schemat szyfrowania|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Przeglądarki

Popularne przeglądarki obsługują następujących klientów DRM:

|Browser|Szyfrowanie|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Kontrolowanie dostępu do zawartości

Można kontrolować, kto ma dostęp do zawartości, konfigurując zasady klucza zawartości. Usługa Media Services obsługuje wiele sposobów autoryzacji użytkowników, którzy tworzą żądania klucza. Należy skonfigurować zasady kluczy zawartości. Klient (odtwarzacz) musi spełnić te zasady, aby klucz może dostarczony do klienta. Zasady klucza zawartości mogą mieć *Otwórz* lub *tokenu* ograniczeń. 

Zasady klucza zawartości z ograniczeniami (Open) mogą być używane do wystawiania licencji osobom bez autoryzacji. Na przykład, jeśli przychód jest oparty na usłudze AD, a nie na podstawie subskrypcji.  

Przy użyciu zasad klucza zawartości z ograniczeniami tokenu klucz zawartości jest wysyłany tylko do klienta, który przedstawia prawidłowy token JWT lub prosty token sieci Web (SWT) w żądaniu licencji/klucza. Ten token musi zostać wystawiony przez usługę STS. 

Możesz użyć usługi Azure AD jako usług STS lub wdrożyć niestandardową usługę STS. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Media Services licencji/usługi dostarczania kluczy zwraca żądaną licencję lub klucz do klienta w przypadku istnienia obu następujących warunków:

* Token jest prawidłowy. 
* Oświadczenia w tokenie są zgodne z tymi skonfigurowanymi dla licencji lub klucza.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacyjny, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą. Wystawca to usługa STS, która wystawia token. Odbiorcy, czasami nazywane zakresem, opisują zamiar tokenu lub zasobu, do którego token autoryzuje dostęp. Usługa Media Services License/Key Delivery sprawdza, czy te wartości w tokenie pasują do wartości w szablonie.

### <a name="token-replay-prevention"></a>Zapobieganie powtarzaniu tokenu

Funkcja *zapobiegania powtarzaniu tokenów* umożliwia Media Services klientom Ustawianie limitu, ile razy można użyć tego samego tokenu do żądania klucza lub licencji. Klient może dodać w tokenie roszczeń typu `urn:microsoft:azure:mediaservices:maxuses` , gdzie wartość jest liczbą przypadków, w których token może być używany w celu uzyskania licencji lub klucza. Wszystkie kolejne żądania o takim samym tokenie do dostarczania kluczy zwrócą nieautoryzowaną odpowiedź. Zobacz, jak dodać to zgłoszenie w [próbce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci muszą mieć kontrolę nad generowaniem tokenu. Należy umieścić to zastrzeżenie w samym tokenie.
* Korzystając z tej funkcji, żądania z tokenami, których czas wygaśnięcia wynosi więcej niż godzinę od momentu odebrania żądania, są odrzucane z nieautoryzowaną odpowiedzią.
* Tokeny są jednoznacznie identyfikowane przez ich sygnaturę. Wszelkie zmiany w ładunku (na przykład aktualizacja czasu wygaśnięcia lub roszczeń) zmienią sygnaturę tokenu i będą zliczane jako nowy token, którego dostarczenie klucza nie zostało wykryte.
* Odtwarzanie nie powiedzie się, jeśli token `maxuses` przekroczył wartość ustawioną przez klienta.
* Ta funkcja może być używana dla całej istniejącej zawartości chronionej (należy zmienić tylko token wystawiony).
* Ta funkcja działa z tokenami JWT i SWT.

## <a name="using-a-custom-sts"></a>Korzystanie z niestandardowej usługi STS

Klient może zdecydować się na użycie niestandardowej usługi STS w celu zapewnienia tokenów. Przyczyny:

* Dostawca tożsamości (dostawcy tożsamości) używany przez klienta nie obsługuje usługi STS. W tym przypadku niestandardowej usługi STS może być opcją.
* Klient może być konieczne bardziej elastyczne większego formantu lub do integracji usługi STS z subskrypcją klienta z systemem rozliczeniowym. 

   Na przykład operator usługi [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) może oferować wiele pakietów subskrybentów, takich jak Premium, Basic i sport. Operator może być zgodne oświadczenia w tokenie pakietem subskrybenta, aby są udostępniane tylko zawartości określonego pakietu. W tym przypadku niestandardowej usługi STS zapewnia wymagane elastyczność i kontrolę.
* Aby uwzględnić niestandardowe oświadczenia w tokenie, aby wybrać różne ContentKeyPolicyOptions z różnymi parametrami licencji DRM (Licencja subskrypcyjna w porównaniu z licencją dzierżawy).
* Aby uwzględnić w nim zastrzeżenie reprezentujące identyfikator klucza zawartości klucza, do którego token przyznaje dostęp.

Jeśli używasz niestandardowej usługi STS, przeprowadza się dwie zmiany:

* Podczas konfigurowania usługi dostarczania licencji dla zasobu, należy określić klucz zabezpieczeń używane na potrzeby weryfikacji przez niestandardowej usługi STS, zamiast bieżącego klucza z usługi Azure AD.
* Podczas generowania tokenu JTW określono klucza zabezpieczeń, zamiast X509 bieżącego klucza prywatnego certyfikatu w usłudze Azure AD.

Istnieją dwa typy kluczy zabezpieczeń:

* Klucz symetryczny: Ten sam klucz jest używany do generowania i weryfikowania tokenu JWT.
* Klucz asymetryczny: Para kluczy publiczny-prywatny w certyfikacie x509 jest używana z kluczem prywatnym do szyfrowania/generowania tokenu JWT oraz klucza publicznego do weryfikowania tokenów.

Jeśli używasz środowiska .NET Framework / C# jako platformy projektowej, X509 certyfikat używany dla klucza asymetrycznego zabezpieczeń musi mieć klucz o długości co najmniej 2048. Jest to wymagane klasy System.IdentityModel.Tokens.X509AsymmetricSecurityKey w programie .NET Framework. W przeciwnym razie jest zgłaszany następujący wyjątek: IDX10630: Element "System. IdentityModel. Tokens. X509AsymmetricSecurityKey" dla podpisywania nie może być mniejszy niż "2048" bitów.

## <a name="custom-key-and-license-acquisition-url"></a>Adres URL pozyskiwania klucza niestandardowego i licencji

Użyj następujących szablonów, jeśli chcesz określić inną licencję/usługę dostarczania kluczy (nie Media Services). Dostępne są dwa pola, które można umieścić w szablonach, aby umożliwić udostępnianie zasad przesyłania strumieniowego w wielu zasobach zamiast tworzenia zasad przesyłania strumieniowego na element zawartości. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Szablon adresu URL usługi niestandardowej, która dostarcza klucze do graczy użytkowników końcowych. Nie jest to wymagane, jeśli używasz Azure Media Services do wystawiania kluczy. 

   Szablon obsługuje tokeny wymienne, które usługa będzie aktualizować w czasie wykonywania, przy użyciu wartości właściwej dla żądania.  Obecnie obsługiwane są następujące wartości tokenu:
   * `{AlternativeMediaId}`, który jest zastępowany przez wartość StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, która jest zastępowana wartością identyfikatora żądanego klucza.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Szablon adresu URL usługi niestandardowej, która dostarcza licencje dla graczy użytkowników końcowych. Nie jest to wymagane w przypadku korzystania z Azure Media Services w celu wystawiania licencji. 

   Szablon obsługuje tokeny wymienne, które usługa będzie aktualizować w czasie wykonywania, przy użyciu wartości właściwej dla żądania. Obecnie obsługiwane są następujące wartości tokenu:  
   * `{AlternativeMediaId}`, który jest zastępowany przez wartość StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, która jest zastępowana wartością identyfikatora żądanego klucza. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Tak samo jak w przypadku poprzedniego szablonu, tylko dla Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Tak samo jak w przypadku poprzedniego szablonu, tylko dla FairPlay.  

Na przykład:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`ma wartość żądanego klucza. Możesz użyć `AlternativeMediaId` , jeśli chcesz zmapować żądanie do jednostki po stronie. Na przykład `AlternativeMediaId` może służyć do wyszukiwania uprawnień.

 Aby zapoznać się z przykładami użycia niestandardowych licencji/adresów URL pozyskiwania kluczy, zobacz [zasady przesyłania strumieniowego — tworzenie](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` Jeśli wystąpi błąd, upewnij się, że określono odpowiednie zasady przesyłania strumieniowego.

Jeśli pojawią się błędy `_NOT_SPECIFIED_IN_URL`, upewnij się, że w adresie URL został określony format szyfrowania. Może to być na przykład `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Zobacz [protokoły przesyłania strumieniowego i typy szyfrowania](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

* [Ochrona przy użyciu szyfrowania AES](protect-with-aes128.md)
* [Ochrona za pomocą technologii DRM](protect-with-drm.md)
* [Projektowanie systemu ochrony zawartości z obsługą technologii DRM z użyciem kontroli dostępu](design-multi-drm-system-with-access-control.md)
* [Szyfrowanie po stronie magazynu](storage-account-concept.md#storage-side-encryption)
* [Często zadawane pytania](frequently-asked-questions.md)

