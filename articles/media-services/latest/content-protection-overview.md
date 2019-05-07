---
title: Chronić zawartość przy użyciu szyfrowania dynamicznego usługi Media Services — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie ochrony zawartości w przypadku szyfrowania dynamicznego. Również opowiada o protokoły i typy szyfrowania przesyłania strumieniowego.
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
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: e13bcb7d4eeded691669277b64aba9048f3bbefa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150419"
---
# <a name="content-protection-with-dynamic-encryption"></a>Ochrona zawartości dzięki dynamicznemu szyfrowaniu

Usługa Azure Media Services umożliwia zabezpieczenie multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługa Media Services można dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

Na poniższym obrazie przedstawiono przepływ pracy usługi Media Services Ochrona zawartości: 

![Ochrona zawartości](./media/content-protection/content-protection.svg)

&#42;*szyfrowania dynamicznego obsługuje klucza AES-128"Wyczyść", CBCS i CENC. Szczegółowe informacje można znaleźć macierz obsługi [tutaj](#streaming-protocols-and-encryption-types).*

W tym artykule opisano pojęcia i terminologia istotne dla zrozumienia, ochrony zawartości przy użyciu usługi Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Główne składniki systemu ochrony zawartości

Do pomyślnego ukończenia projektu systemu/aplikacji "content protection", należy całkowicie zrozumieniu zakresu nakładu pracy. Poniższa lista zawiera przegląd trzech części, które trzeba do zaimplementowania. 

1. Kod platformy Azure Media Services
  
   [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) przykładowych pokazano, jak zaimplementować multi-DRM systemu za pomocą usługi Media Services v3 i również użyć usługi dostarczania kluczy/licencji Media Services. Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [Streaming protocols and encryption types (Protokoły i typy szyfrowania przesyłania strumieniowego)](#streaming-protocols-and-encryption-types), aby sprawdzić, które rozwiązania warto łączyć.
  
   W przykładzie pokazano sposób:

   1. Tworzenie i konfigurowanie [zasad dotyczących zawartości klucza](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

      * Zdefiniuj autoryzacji dostarczania licencji, określając logika sprawdzania autoryzacji na podstawie oświadczeń w token JWT.
      * Konfigurowanie szyfrowania DRM, określając klucz zawartości.
      * Konfigurowanie [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), i/lub [FairPlay](fairplay-license-overview.md) licencji. Szablony pozwalają skonfigurować prawa i uprawnienia dla wszystkich używanych protokołów DRM.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Tworzenie [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) skonfigurowanego do przesyłania strumieniowego zawartości zaszyfrowanej. 
  
      **Lokalizatora przesyłania strumieniowego** musi być skojarzone z [przesyłania strumieniowego zasad](https://docs.microsoft.com/rest/api/media/streamingpolicies). W tym przykładzie ustawimy StreamingLocator.StreamingPolicyName z zasadami "Predefined_MultiDrmCencStreaming". Ta zasada wskazuje, że chcemy uzyskać dwa klucze zawartości (koperty i CENC), aby pobrać generowane i ustawiane na wskaźniku. Dlatego stosowane są szyfrowania typu koperta, PlayReady i Widevine (klucz jest dostarczany do klienta odtwarzania w oparciu o skonfigurowane licencje DRM). Jeśli chcesz także zaszyfrować strumień przy użyciu metody CBCS (FairPlay), użyj zasad „Predefined_MultiDrmStreaming”.
    
      Ponieważ chcemy zaszyfrować wideo, **zasad klucza zawartości** czy został skonfigurowany wcześniej również musi być skojarzone z **lokalizatora przesyłania strumieniowego**. 
    
   3. Utwórz token testu.

      **GetTokenAsync** metoda pokazuje, jak utworzyć test tokenu.
   4. Tworzenie adresu URL przesyłania strumieniowego.

      **GetDASHStreamingUrlAsync** metoda przedstawia sposób tworzenia adresu URL przesyłania strumieniowego. W tym przypadku strumieni adresu URL **DASH** zawartości.

2. Odtwarzacz przy użyciu technologii AES lub technologii DRM klienta. Aplikacja odtwarzacza wideo, oparte na odtwarzaczu zestawu SDK (natywny lub przeglądarki) musi spełniać następujące wymagania:
   * Odtwarzacz zestaw SDK obsługuje wymagane klientów DRM
   * Odtwarzacz zestaw SDK obsługuje wymagane protokołów przesyłania strumieniowego: Smooth, DASH lub HLS
   * Zestaw SDK gracz musi być w stanie obsługiwać przekazywanie tokenu JWT w żądaniu nabycie licencji
  
     Odtwarzacz można utworzyć za pomocą [interfejsu API usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API usługi Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) do określenia technologii DRM na różnych platformach DRM.

     Do testowania AES lub CENC (Widevine i/lub technologii PlayReady) zaszyfrowany zawartość, możesz użyć [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Upewnij się, kliknij pozycję "Opcje zaawansowane" i sprawdź opcje szyfrowania.

     Jeśli chcesz przetestować FairPlay zaszyfrowana zawartość, użyj [tego odtwarzacza testu](https://aka.ms/amtest). Odtwarzacz obsługuje Widevine, PlayReady, i protokołów technologii FairPlay DRM, a także AES-128 szyfrowania otwartym kluczem. 
    
     Musisz wybrać odpowiednie przeglądarce w celu przetestowania różnych protokołów DRM: Dla programu Chrome/Opera/Firefox Widevine, Microsoft Edge/IE11 dla technologii PlayReady, Safari w systemie macOS dla technologii FairPlay.

3. Secure Token Service (STS), która wystawia Token sieci Web JSON (JWT) jako token dostępu, aby uzyskać dostęp do zasobów w wewnętrznej bazie danych. Za pomocą usług dostarczania licencji usługi AMS jako zasobów wewnętrznej bazy danych. Usługa tokenu Zabezpieczającego musi definiują następujące elementy:

   * Wystawcy i odbiorców (lub zakres)
   * Oświadczenia, które są zależne od wymagań biznesowych w Ochrona zawartości
   * Weryfikacja konfiguracji symetrycznej lub asymetrycznej weryfikacji podpisu
   * Obsługa przerzucania kluczy (jeśli jest to konieczne)

     Możesz użyć [to narzędzie usługi STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) testu usługi STS, która obsługuje wszystkie typy 3 klucza weryfikacji: symetryczne, asymetryczne lub Azure AD przy użyciu przerzucania klucza. 

> [!NOTE]
> Zdecydowanie zaleca się skupić się i w pełni przetestować każdej części (opisane powyżej) przed przejściem do następnej części. Aby przetestować systemu "content protection", użyj narzędzia określonych na liście powyżej.  

## <a name="streaming-protocols-and-encryption-types"></a>Protokoły i typy szyfrowania przesyłania strumieniowego

Usługa Media Services umożliwia dostarczanie zawartości szyfrowane dynamicznie przy użyciu szyfrowania otwartym kluczem AES i technologii DRM szyfrowania za pomocą usług PlayReady, Widevine i FairPlay. Obecnie można zaszyfrować formatu HTTP Live Streaming (HLS), MPEG DASH i Smooth Streaming. Dla każdego protokołu obsługuje następujące metody szyfrowania:

### <a name="hls"></a>HLS

Protokół HLS obsługuje następujące formaty kontenera i schematy szyfrowania.

|Format kontenera|Schemat szyfrowania|Przykład adresu URL|
|---|---|---|
|Wszyscy|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) ||
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) ||
|CMAF(fmp4) |CENC (PlayReady) ||

HLS/CMAF oraz technologia FairPlay (w tym — HEVC / H.265) jest obsługiwane na następujących urządzeniach:

* v11 z systemem iOS lub nowszej 
* iPhone 8 lub nowszy
* System MacOS high Sierra oraz Intel 7 ogólnego użycia Procesora

### <a name="mpeg-dash"></a>MPEG-DASH

Protokołu MPEG-DASH obsługuje następujące formaty kontenera i schematy szyfrowania.

|Format kontenera|Schemat szyfrowania|Przykłady adresu URL
|---|---|---|
|Wszyscy|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)||

### <a name="smooth-streaming"></a>Smooth Streaming

Protokołu Smooth Streaming obsługuje następujące formaty kontenera i schematy szyfrowania.

|Protokół|Format kontenera|Schemat szyfrowania|
|---|---|---|
|fMP4|AES||
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Przeglądarki

Popularne przeglądarki obsługują następujących klientów DRM:

|Przeglądarka|Szyfrowanie|
|---|---|
|Chrome|Widevine|
|Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>Vs otwartym kluczem AES-128. DRM

Klienci często zastanawiasz się, czy należy używać szyfrowania AES lub systemu DRM. Główną różnicą między dwoma systemami to, że przy użyciu szyfrowania AES klucz zawartości jest wysyłany do klienta za pośrednictwem protokołu TLS tak, że klucz jest zaszyfrowany, podczas przesyłania, ale bez żadnych dodatkowych szyfrowania ("w Wyczyść"). W rezultacie klucz używany do odszyfrowywania zawartości jest dostępny dla odtwarzacz klienta i mogą być wyświetlane w śledzenia sieci na komputerze klienckim w postaci zwykłego tekstu. Szyfrowanie otwartym kluczem AES-128 nadaje się do zastosowań, gdzie przeglądarka jest zaufany (na przykład szyfrowanie firmowych filmów wideo dystrybuowane w obrębie firmy do przeglądania przez pracowników).

Systemami DRM, takich jak PlayReady, Widevine i FairPlay podane wszystkie dodatkowego poziomu szyfrowania na klucz używany do odszyfrowania zawartości w porównaniu do klucza niezaszyfrowanego AES-128. Klucz zawartości jest szyfrowany do klucza chronionego przez środowisko uruchomieniowe DRM w dodatkowej wszelkie transportu szyfrowania na poziomie obsługiwanego przez protokół TLS. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniejsze, złośliwy użytkownik na ataki. DRM jest zalecana dla przypadków użycia, w której przeglądarka może nie być zaufaną stronę i wymagają najwyższy poziom zabezpieczeń.

## <a name="dynamic-encryption-and-key-delivery-service"></a>Dynamiczne szyfrowanie i usługę dostarczania kluczy

W v3 usługi Media Services klucz zawartości jest skojarzony z lokalizatora przesyłania strumieniowego (zobacz [w tym przykładzie](protect-with-aes128.md)). Jeśli używana jest usługa dostarczania kluczy usługi Media Services, można pozwolić generowanie klucza zawartości dla Ciebie usługi Azure Media Services. Należy wygenerować klucz zawartości samodzielnie Jeśli używasz własnego klucza dostawy usług, czy wymagana jest obsługa scenariuszy wysokiej dostępności, którym musisz mieć ten sam klucz zawartości w dwóch centrach danych.

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania otwartym kluczem AES i szyfrowania DRM. Aby odszyfrować strumienia, gracz żąda klucza z usługę dostarczania kluczy usługi Media Services lub usługę dostarczania kluczy, wskazana. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zawartości zasad kluczy, które podane dla klucza.

Usługi Media Services udostępnia usługę dostarczania kluczy na potrzeby dostarczania licencji DRM (PlayReady, Widevine i FairPlay) i kluczy szyfrowania AES do autoryzowanych klientów. Interfejs API REST lub biblioteka klienta usługi Media Services umożliwia konfigurowanie zasad autoryzacji i uwierzytelniania dla licencji i kluczy.

### <a name="custom-key-and-license-acquisition-url"></a>Klucz i licencji nabycia adres URL niestandardowej

Jeśli chcesz określić inny klucz i licencji usługi dostarczania (nie Media Services), należy użyć poniższych szablonów. Wymienne dwa pola w szablonach czy istnieją tak, aby zasady przesyłania strumieniowego można udostępniać między wiele zasobów, zamiast tworzyć zasady przesyłania strumieniowego na zasób. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate — szablon dla adresu URL niestandardowej usługi dostarczania kluczy do odtwarzaczy przez użytkownika końcowego. Niewymagane w przypadku korzystania z usługi Azure Media Services do wystawiania kluczy. Szablon obsługuje wymienne tokenów, które usługa zostanie zaktualizowana w czasie wykonywania za pomocą wartości specyficzne dla żądania.  Obecnie obsługiwane wartości tokenu to {AlternativeMediaId}, jest on zastępowany przy użyciu wartości StreamingLocatorId.AlternativeMediaId i {ContentKeyId}, jest on zastępowany wartość identyfikatora klucza żądanej.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate — szablon dla adresu URL niestandardowej usługi dostarczania licencji do użytkowników końcowych graczy. Niewymagane w przypadku korzystania z usługi Azure Media Services do wystawiania licencji. Szablon obsługuje wymienne tokenów, które usługa zostanie zaktualizowana w czasie wykonywania za pomocą wartości specyficzne dla żądania. Obecnie obsługiwane wartości tokenu to {AlternativeMediaId}, jest on zastępowany przy użyciu wartości StreamingLocatorId.AlternativeMediaId i {ContentKeyId}, jest on zastępowany wartość identyfikatora klucza żądanej. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - sam, jak powyżej, tylko w przypadku Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - sam, jak powyżej, tylko w przypadku technologii FairPlay.  

Na przykład:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` Ma wartość klucza wymaganego i `AlternativeMediaId` można użyć, jeśli chcesz zmapować żądania do jednostki po swojej stronie. Na przykład `AlternativeMediaId` może służyć w celu wyszukania uprawnień.

Aby uzyskać przykłady REST, które używają niestandardowego klucza i adresy URL pozyskiwania licencji, zobacz [przesyłania strumieniowego zasad — tworzenie](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>Dostęp do zawartości kontrolki

Można kontrolować, kto ma dostęp do zawartości, konfigurując zasady klucza zawartości. Usługa Media Services obsługuje wiele sposobów autoryzacji użytkowników, którzy tworzą żądania klucza. Należy skonfigurować zasady kluczy zawartości. Klient (odtwarzacz) musi spełnić te zasady, aby klucz może dostarczony do klienta. Zasady klucza zawartości mogą mieć **Otwórz** lub **tokenu** ograniczeń. 

Za pomocą tokenu zawartości klucza zasad klucz zawartości jest wysyłane tylko do klienta, który stanowi prawidłowe JSON Web Token (JWT) lub prosty token sieci web (SWT) w żądaniu kluczy/licencji. Ten token musi zostać wystawiony przez usługę tokenu zabezpieczającego (STS). Można użyć usługi Azure Active Directory jako Usługa STS lub wdrażanie niestandardowej usługi STS. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy Media Services zwraca żądany kluczy/licencji do klienta, jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z oświadczeniami skonfigurowanymi dla kluczy/licencji.

Po skonfigurowaniu zasadzie ograniczenia tokenu, należy określić podstawowy klucz weryfikacji wystawcy i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą. Wystawca jest usługa bezpiecznych tokenów, który wystawia token. Grupy odbiorców, czasami nazywane zakresu, opisuje przeznaczenie tokenu lub zasób tokenu autoryzuje dostępu do. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasuje do wartości w szablonie.

Klienci często używają niestandardowej usługi STS do uwzględnienia niestandardowe oświadczenia w tokenie, aby wybrać inny ContentKeyPolicyOptions z innymi parametrami licencji DRM (subskrypcji licencji i licencji dzierżawy) lub w celu dołączenia oświadczenie reprezentujące klucza zawartości Identyfikator tokenu przyznaje dostęp do klucza.
 
## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w wersji 3 usługa Media Services:

|Opcja szyfrowania|Opis|Media Services v3|
|---|---|---|
|Szyfrowanie magazynu usługi Media Services| AES-256 szyfrowania kluczy zarządzanych przez usługę Media Services|Nieobsługiwane<sup>(1)</sup>|
|[Szyfrowanie usługi Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera, oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta, oferowane przez usługę Azure storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|

<sup>1</sup> Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest tylko obsługiwane dla zapewnienia zgodności gdy Twoje zasoby zostały utworzone za pomocą usługi Media Services v2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli otrzymasz `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` błąd, upewnij się, określ odpowiednie zasady przesyłania strumieniowego.

Jeśli występują błędy, które kończą się `_NOT_SPECIFIED_IN_URL`, upewnij się, że format szyfrowania w adresie URL. Na przykład `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Zobacz [protokoły i typy szyfrowania przesyłania strumieniowego](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

* [Ochrona przy użyciu szyfrowania AES](protect-with-aes128.md)
* [Ochrona za pomocą technologii DRM](protect-with-drm.md)
* [Projektowanie multi-DRM systemu ochrony zawartości przy użyciu kontroli dostępu](design-multi-drm-system-with-access-control.md)
* [Często zadawane pytania](frequently-asked-questions.md)

