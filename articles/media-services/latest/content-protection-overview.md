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
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5d31e4a523fdedf9907e33c70638f07a08461ed1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310314"
---
# <a name="content-protection-with-dynamic-encryption"></a>Ochrona zawartości przy użyciu szyfrowania dynamicznego

Usługa Azure Media Services umożliwia zabezpieczenie multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Za pomocą Media Services można dostarczyć zawartość dynamiczną i na żądanie zaszyfrowaną dynamicznie z Advanced Encryption Standard (AES-128) lub z jednego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

W Media Services v3 klucz zawartości jest skojarzony z lokalizatorem przesyłania strumieniowego (patrz [ten przykład](protect-with-aes128.md)). Jeśli korzystasz z usługi Media Services Key Delivery, możesz Azure Media Services wygenerować klucz zawartości. Klucz zawartości należy wygenerować samodzielnie, jeśli używasz własnej usługi dostarczania kluczy lub jeśli chcesz obsłużyć scenariusz wysokiej dostępności, w którym musisz mieć ten sam klucz zawartości w dwóch centrach danych.

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania otwartym kluczem AES i szyfrowania DRM. Aby odszyfrować strumienia, gracz żąda klucza z usługę dostarczania kluczy usługi Media Services lub usługę dostarczania kluczy, wskazana. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zawartości zasad kluczy, które podane dla klucza.

Interfejs API REST lub biblioteka klienta usługi Media Services umożliwia konfigurowanie zasad autoryzacji i uwierzytelniania dla licencji i kluczy.

Na poniższym obrazie przedstawiono przepływ pracy usługi Media Services Ochrona zawartości: 

![Ochrona zawartości](./media/content-protection/content-protection.svg)

&#42;*szyfrowania dynamicznego obsługuje klucza AES-128"Wyczyść", CBCS i CENC. Szczegółowe informacje można znaleźć macierz obsługi [tutaj](#streaming-protocols-and-encryption-types).*

W tym artykule opisano pojęcia i terminologia istotne dla zrozumienia, ochrony zawartości przy użyciu usługi Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Główne składniki systemu ochrony zawartości

Do pomyślnego ukończenia projektu systemu/aplikacji "content protection", należy całkowicie zrozumieniu zakresu nakładu pracy. Poniższa lista zawiera przegląd trzech części, które trzeba do zaimplementowania. 

1. Kod platformy Azure Media Services
  
   Przykład [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) pokazuje, jak zaimplementować system wielodrm z Media Services v3 przy użyciu platformy .NET. Przedstawiono w nim również, jak korzystać z licencji Media Servicesej i usługi dostarczania kluczy. Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [Streaming protocols and encryption types (Protokoły i typy szyfrowania przesyłania strumieniowego)](#streaming-protocols-and-encryption-types), aby sprawdzić, które rozwiązania warto łączyć.
  
   W przykładzie pokazano sposób:

   1. Utwórz i skonfiguruj [Zasady kluczy zawartości](content-key-policy-concept.md). Tworzenie **zasad klucza zawartości** w celu skonfigurowania sposobu, w jaki klucz zawartości (zapewniający bezpieczny dostęp do zasobów) jest dostarczany do klientów końcowych.    

      * Definiowanie autoryzacji dostarczania licencji, określanie logiki kontroli autoryzacji na podstawie oświadczeń w JWT.
      * Skonfiguruj licencje [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)i/lub [FairPlay](fairplay-license-overview.md) . Szablony umożliwiają skonfigurowanie praw i uprawnień dla każdego z użytych protokołów DRM.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Utwórz [lokalizator przesyłania strumieniowego](streaming-locators-concept.md) skonfigurowany do przesyłania strumieniowego zaszyfrowanego zasobu. 
  
      **Lokalizator przesyłania strumieniowego** musi być skojarzony z [zasadami przesyłania strumieniowego](streaming-policy-concept.md). W tym przykładzie ustawimy StreamingLocator. StreamingPolicyName dla zasad "Predefined_MultiDrmCencStreaming". Szyfrowanie PlayReady i Widevine są stosowane, klucz jest dostarczany do klienta odtwarzania na podstawie skonfigurowanych licencji DRM. Jeśli chcesz także zaszyfrować strumień przy użyciu metody CBCS (FairPlay), użyj zasad „Predefined_MultiDrmStreaming”.
      
      Lokalizator przesyłania strumieniowego jest również skojarzony z **zasadami klucza zawartości** , które zostały zdefiniowane.
    
   3. Utwórz token testu.

      **GetTokenAsync** metoda pokazuje, jak utworzyć test tokenu.
   4. Tworzenie adresu URL przesyłania strumieniowego.

      **GetDASHStreamingUrlAsync** metoda przedstawia sposób tworzenia adresu URL przesyłania strumieniowego. W tym przypadku strumieni adresu URL **DASH** zawartości.

2. Odtwarzacz przy użyciu technologii AES lub technologii DRM klienta. Aplikacja odtwarzacza wideo, oparte na odtwarzaczu zestawu SDK (natywny lub przeglądarki) musi spełniać następujące wymagania:
   * Odtwarzacz zestaw SDK obsługuje wymagane klientów DRM
   * Zestaw SDK odtwarzacza obsługuje wymagane protokoły przesyłania strumieniowego: Gładki, KRESKa i/lub HLS
   * Zestaw SDK gracz musi być w stanie obsługiwać przekazywanie tokenu JWT w żądaniu nabycie licencji
  
     Odtwarzacz można utworzyć za pomocą [interfejsu API usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API usługi Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) do określenia technologii DRM na różnych platformach DRM.

     Do testowania AES lub CENC (Widevine i/lub technologii PlayReady) zaszyfrowany zawartość, możesz użyć [usługi Azure Media Player](https://aka.ms/azuremediaplayer). Upewnij się, kliknij pozycję "Opcje zaawansowane" i sprawdź opcje szyfrowania.

     Jeśli chcesz przetestować FairPlay zaszyfrowana zawartość, użyj [tego odtwarzacza testu](https://aka.ms/amtest). Odtwarzacz obsługuje Widevine, PlayReady, i protokołów technologii FairPlay DRM, a także AES-128 szyfrowania otwartym kluczem. 
    
     Musisz wybrać odpowiednią przeglądarkę, aby przetestować różne protokołów DRM: Chrome/Opera/Firefox for Widevine, Microsoft Edge/IE11 for PlayReady, Safari on macOS for FairPlay.

3. Secure Token Service (STS), która wystawia Token sieci Web JSON (JWT) jako token dostępu, aby uzyskać dostęp do zasobów w wewnętrznej bazie danych. Za pomocą usług dostarczania licencji usługi AMS jako zasobów wewnętrznej bazy danych. Usługa tokenu Zabezpieczającego musi definiują następujące elementy:

   * Wystawcy i odbiorców (lub zakres)
   * Oświadczenia, które są zależne od wymagań biznesowych w Ochrona zawartości
   * Weryfikacja konfiguracji symetrycznej lub asymetrycznej weryfikacji podpisu
   * Obsługa przerzucania kluczy (jeśli jest to konieczne)

     Za pomocą [tego narzędzia STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) można TESTOWAĆ usługę STS, która obsługuje wszystkie 3 typy kluczy weryfikacyjnych: symetryczne, asymetryczne lub Azure AD z przerzucaniem kluczy. 

> [!NOTE]
> Zdecydowanie zaleca się skupić się i w pełni przetestować każdej części (opisane powyżej) przed przejściem do następnej części. Aby przetestować systemu "content protection", użyj narzędzia określonych na liście powyżej.  

## <a name="streaming-protocols-and-encryption-types"></a>Protokoły i typy szyfrowania przesyłania strumieniowego

Usługa Media Services umożliwia dostarczanie zawartości szyfrowane dynamicznie przy użyciu szyfrowania otwartym kluczem AES i technologii DRM szyfrowania za pomocą usług PlayReady, Widevine i FairPlay. Obecnie można zaszyfrować formatu HTTP Live Streaming (HLS), MPEG DASH i Smooth Streaming. Dla każdego protokołu obsługuje następujące metody szyfrowania:

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

* System iOS v11 lub nowszy 
* iPhone 8 lub nowszy
* MacOS wysoka firma Sierra z procesorem Intel 7 gen

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
|Microsoft Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="control-content-access"></a>Dostęp do zawartości kontrolki

Można kontrolować, kto ma dostęp do zawartości, konfigurując zasady klucza zawartości. Usługa Media Services obsługuje wiele sposobów autoryzacji użytkowników, którzy tworzą żądania klucza. Należy skonfigurować zasady kluczy zawartości. Klient (odtwarzacz) musi spełnić te zasady, aby klucz może dostarczony do klienta. Zasady klucza zawartości mogą mieć **Otwórz** lub **tokenu** ograniczeń. 

Za pomocą tokenu zawartości klucza zasad klucz zawartości jest wysyłane tylko do klienta, który stanowi prawidłowe JSON Web Token (JWT) lub prosty token sieci web (SWT) w żądaniu kluczy/licencji. Ten token musi zostać wystawiony przez usługę tokenu zabezpieczającego (STS). Można użyć usługi Azure Active Directory jako Usługa STS lub wdrażanie niestandardowej usługi STS. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy Media Services zwraca żądany kluczy/licencji do klienta, jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z oświadczeniami skonfigurowanymi dla kluczy/licencji.

Po skonfigurowaniu zasadzie ograniczenia tokenu, należy określić podstawowy klucz weryfikacji wystawcy i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą. Wystawca jest usługa bezpiecznych tokenów, który wystawia token. Grupy odbiorców, czasami nazywane zakresu, opisuje przeznaczenie tokenu lub zasób tokenu autoryzuje dostępu do. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasuje do wartości w szablonie.

Klienci często używają niestandardowej usługi STS, aby uwzględnić niestandardowe oświadczenia w tokenie, aby wybrać różne ContentKeyPolicyOptions z różnymi parametrami licencji DRM (licencją subskrypcyjną a licencją najmu) lub dołączyć oświadczenie reprezentujące klucz zawartości Identyfikator klucza, do którego token przyznaje dostęp.

### <a name="token-replay-prevention"></a>Zapobieganie powtarzaniu tokenu

Funkcja *zapobiegania powtarzaniu tokenów* umożliwia Media Services klientom Ustawianie limitu, ile razy można użyć tego samego tokenu do żądania klucza lub licencji. Klient może dodać w tokenie roszczeń typu `urn:microsoft:azure:mediaservices:maxuses` , gdzie wartość jest liczbą przypadków, w których token może być używany w celu uzyskania licencji lub klucza. Wszystkie kolejne żądania o takim samym tokenie do dostarczania kluczy zwrócą nieautoryzowaną odpowiedź. Zobacz, jak dodać to zgłoszenie w [próbce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci muszą mieć kontrolę nad generowaniem tokenu. Należy umieścić to zastrzeżenie w samym tokenie.
* Korzystając z tej funkcji, żądania z tokenami, których czas wygaśnięcia wynosi więcej niż godzinę od momentu odebrania żądania, są odrzucane z nieautoryzowaną odpowiedzią.
* Tokeny są jednoznacznie identyfikowane przez ich sygnaturę. Wszelkie zmiany w ładunku (na przykład aktualizacja czasu wygaśnięcia lub roszczeń) zmienią sygnaturę tokenu i będą zliczane jako nowy token, którego dostarczenie klucza nie zostało wykryte.
* Odtwarzanie nie powiedzie się, jeśli token `maxuses` przekroczył wartość ustawioną przez klienta.
* Ta funkcja może być używana dla całej istniejącej zawartości chronionej (należy zmienić tylko token wystawiony).
* Ta funkcja działa z tokenami JWT i SWT.

## <a name="custom-key-and-license-acquisition-url"></a>Adres URL pozyskiwania klucza niestandardowego i licencji

Użyj następujących szablonów, jeśli chcesz określić inny klucz i usługę dostarczania licencji (nie Media Services). Dostępne są dwa pola, które można umieścić w szablonach, aby umożliwić udostępnianie zasad przesyłania strumieniowego w wielu zasobach zamiast tworzenia zasad przesyłania strumieniowego na element zawartości. 

* EnvelopeEncryption. CustomKeyAcquisitionUrlTemplate — szablon adresu URL usługi niestandardowej dostarczającej klucze do graczy użytkownika końcowego. Niewymagane w przypadku używania Azure Media Services do wystawiania kluczy. Szablon obsługuje tokeny wymienne, które usługa będzie aktualizować w czasie wykonywania, przy użyciu wartości właściwej dla żądania.  Aktualnie obsługiwane wartości tokenu to {AlternativeMediaId}, które są zastępowane wartością StreamingLocatorId. AlternativeMediaId i {ContentKeyId}, która jest zastępowana wartością identyfikatora żądanego klucza.
* StreamingPolicyPlayReadyConfiguration. CustomLicenseAcquisitionUrlTemplate — szablon adresu URL usługi niestandardowej dostarczającej licencje do graczy użytkowników końcowych. Niewymagane w przypadku używania Azure Media Services w celu wystawiania licencji. Szablon obsługuje tokeny wymienne, które usługa będzie aktualizować w czasie wykonywania, przy użyciu wartości właściwej dla żądania. Aktualnie obsługiwane wartości tokenu to {AlternativeMediaId}, które są zastępowane wartością StreamingLocatorId. AlternativeMediaId i {ContentKeyId}, która jest zastępowana wartością identyfikatora żądanego klucza. 
* StreamingPolicyWidevineConfiguration. CustomLicenseAcquisitionUrlTemplate — taka sama jak powyżej, tylko dla Widevine. 
* StreamingPolicyFairPlayConfiguration. CustomLicenseAcquisitionUrlTemplate — taka sama jak powyżej, tylko dla FairPlay.  

Przykład:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

Ma wartość żądanego klucza i może być użyta, `AlternativeMediaId` Jeśli chcesz zmapować żądanie do jednostki po stronie użytkownika. `ContentKeyId` Na przykład `AlternativeMediaId` może służyć do wyszukiwania uprawnień.

Aby zapoznać się z przykładami użycia niestandardowych adresów URL dotyczących kluczy i licencji, zobacz [zasady przesyłania strumieniowego — tworzenie](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` Jeśli wystąpi błąd, upewnij się, że określono odpowiednie zasady przesyłania strumieniowego.

Jeśli pojawią się błędy `_NOT_SPECIFIED_IN_URL`, upewnij się, że w adresie URL został określony format szyfrowania. Na przykład `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Zobacz [protokoły przesyłania strumieniowego i typy szyfrowania](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

* [Ochrona przy użyciu szyfrowania AES](protect-with-aes128.md)
* [Ochrona za pomocą technologii DRM](protect-with-drm.md)
* [Projektowanie systemu ochrony zawartości z obsługą technologii DRM z użyciem kontroli dostępu](design-multi-drm-system-with-access-control.md)
* [Szyfrowanie po stronie magazynu](storage-account-concept.md#storage-side-encryption)
* [Często zadawane pytania](frequently-asked-questions.md)

