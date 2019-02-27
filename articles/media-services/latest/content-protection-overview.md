---
title: Ochrona zawartości przy użyciu usługi Media Services — Azure | Dokumentacja firmy Microsoft
description: W tym artykule, zapewniają Przegląd ochrony zawartości przy użyciu usługi Media Services.
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
ms.date: 02/20/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: e515f7c0587e8bbcba10bcd2d2a726c16401bfbd
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889782"
---
# <a name="content-protection-overview"></a>Omówienie ochrony zawartości

Usługa Azure Media Services umożliwia zabezpieczenie multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługa Media Services można dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

Na poniższym obrazie przedstawiono przepływ pracy usługi Media Services Ochrona zawartości: 

![Ochrona zawartości](./media/content-protection/content-protection.png)

&#42;*szyfrowania dynamicznego obsługuje klucza AES-128"Wyczyść", CBCS i CENC. Szczegółowe informacje można znaleźć macierz obsługi [tutaj](#streaming-protocols-and-encryption-types).*

W tym artykule opisano pojęcia i terminologia istotne dla zrozumienia, ochrony zawartości przy użyciu usługi Media Services. Artykuł również ma sekcji często zadawane pytania i zawiera łącza do artykułów, które pokazują, jak chronić zawartość. 

## <a name="main-components-of-a-content-protection-system"></a>Główne składniki systemu ochrony zawartości

Do pomyślnego ukończenia projektu systemu/aplikacji "content protection", należy całkowicie zrozumieniu zakresu nakładu pracy. Poniższa lista zawiera przegląd trzech części, które trzeba do zaimplementowania. 

1. Kod platformy Azure Media Services
  
  [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) przykładowych pokazano, jak zaimplementować multi-DRM systemu za pomocą usługi Media Services v3 i również użyć usługi dostarczania kluczy/licencji Media Services. Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [Streaming protocols and encryption types (Protokoły i typy szyfrowania przesyłania strumieniowego)](#streaming-protocols-and-encryption-types), aby sprawdzić, które rozwiązania warto łączyć.
  
  W przykładzie pokazano sposób:

  1. Tworzenie i konfigurowanie ContentKeyPolicies.

    * Zdefiniuj autoryzacji dostarczania licencji, określając logika sprawdzania autoryzacji na podstawie oświadczeń w token JWT.
    * Konfigurowanie szyfrowania DRM, określając klucz zawartości.
    * Konfigurowanie [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), i [FairPlay](fairplay-license-overview.md) licencji. Szablony pozwalają skonfigurować prawa i uprawnienia dla wszystkich używanych protokołów DRM.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
  2. Utwórz StreamingLocator, który jest skonfigurowany do przesyłania strumieniowego element zawartości zaszyfrowanej. 

    Na przykład można ustawić StreamingLocator.StreamingPolicyName z zasadami "Predefined_MultiDrmCencStreaming". Te zasady wskazują, że chcesz wygenerować dwa klucze zawartości (koperta i CENC) i ustawić je w lokalizatorze. Dlatego stosowane są szyfrowania typu koperta, PlayReady i Widevine (klucz jest dostarczany do klienta odtwarzania w oparciu o skonfigurowane licencje DRM). Jeśli chcesz także zaszyfrować strumień przy użyciu metody CBCS (FairPlay), użyj zasad „Predefined_MultiDrmStreaming”.
  3. Utwórz token testu.

    **GetTokenAsync** metoda pokazuje, jak utworzyć test tokenu.
  4. Tworzenie adresu URL przesyłania strumieniowego.

    **GetDASHStreamingUrlAsync** metoda przedstawia sposób tworzenia adresu URL przesyłania strumieniowego. W tym przypadku strumieni adresu URL **DASH** zawartości.

2. Odtwarzacz przy użyciu technologii AES lub technologii DRM klienta. Aplikacja odtwarzacza wideo, oparte na odtwarzaczu zestawu SDK (natywny lub przeglądarki) musi spełniać następujące wymagania:
  * Odtwarzacz zestaw SDK obsługuje wymagane klientów DRM
  * Odtwarzacz zestaw SDK obsługuje wymagane protokołów przesyłania strumieniowego: Smooth, DASH lub HLS
  * Zestaw SDK gracz musi być w stanie obsługiwać przekazywanie tokenu JWT w żądaniu nabycie licencji
  
    Odtwarzacz można utworzyć za pomocą [interfejsu API usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API usługi Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) do określenia technologii DRM na różnych platformach DRM.

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

|Protokół|Format kontenera|Schemat szyfrowania|
|---|---|---|---|
|MPEG-DASH|Wszyscy|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Wszyscy|AES|
||MPG2-TS |CBCS (Fairplay) |
||MPG2-TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

W v3 usługi Media Services klucz zawartości jest skojarzony z StreamingLocator (zobacz [w tym przykładzie](protect-with-aes128.md)). Jeśli używana jest usługa dostarczania kluczy usługi Media Services, powinien automatycznie wygenerować klucz zawartości. Należy wygenerować klucz zawartości samodzielnie Jeśli używasz własnego klucza dostawy usług, czy wymagana jest obsługa scenariuszy wysokiej dostępności, którym musisz mieć ten sam klucz zawartości w dwóch centrach danych.

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania otwartym kluczem AES i szyfrowania DRM. Aby odszyfrować strumienia, gracz żąda klucza z usługę dostarczania kluczy usługi Media Services lub usługę dostarczania kluczy, wskazana. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zawartości zasad kluczy, które podane dla klucza.

## <a name="aes-128-clear-key-vs-drm"></a>Vs otwartym kluczem AES-128. DRM

Klienci często zastanawiasz się, czy należy używać szyfrowania AES lub systemu DRM. Główną różnicą między dwoma systemami to, że przy użyciu szyfrowania AES klucz zawartości jest wysyłany do klienta w niezaszyfrowanej postaci ("w Wyczyść"). W rezultacie klucz użyty do zaszyfrowania zawartości mogą być wyświetlane w śledzenia sieci na komputerze klienckim w postaci zwykłego tekstu. Szyfrowanie otwartym kluczem AES-128 nadaje się do zastosowań, gdzie przeglądarka jest zaufany (na przykład szyfrowanie firmowych filmów wideo dystrybuowane w obrębie firmy do przeglądania przez pracowników).

PlayReady, Widevine i FairPlay wszystkie zapewnia wyższy poziom szyfrowania w porównaniu do algorytmu AES-128 szyfrowania otwartym kluczem. Klucz zawartości jest przesyłany w postaci zaszyfrowanej. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniejsze, złośliwy użytkownik na ataki. DRM jest zalecana dla przypadków użycia, w której przeglądarka może nie być zaufaną stronę i wymagają najwyższy poziom zabezpieczeń.

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w wersji 3 usługa Media Services:

|Opcja szyfrowania|Opis|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usługi Media Services| AES-256 szyfrowania kluczy zarządzanych przez usługę Media Services|Nieobsługiwane<sup>(1)</sup>|
|[Szyfrowanie usługi Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera, oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta, oferowane przez usługę Azure storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|

<sup>1</sup> Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest tylko obsługiwane dla zapewnienia zgodności gdy Twoje zasoby zostały utworzone za pomocą usługi Media Services v2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

## <a name="licenses-and-keys-delivery-service"></a>Usługi dostarczania licencji i kluczy

Usługi Media Services udostępnia usługę dostarczania kluczy na potrzeby dostarczania licencji DRM (PlayReady, Widevine i FairPlay) i kluczy szyfrowania AES do autoryzowanych klientów. Interfejs API REST lub biblioteka klienta usługi Media Services umożliwia konfigurowanie zasad autoryzacji i uwierzytelniania dla licencji i kluczy.

## <a name="control-content-access"></a>Dostęp do zawartości kontrolki

Można kontrolować, kto ma dostęp do zawartości, konfigurując zasady klucza zawartości. Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Należy skonfigurować zasady kluczy zawartości. Klient (odtwarzacz) musi spełnić te zasady, aby klucz może dostarczony do klienta. Zasady klucza zawartości mogą mieć **Otwórz** lub **tokenu** ograniczeń. 

Za pomocą tokenu zawartości klucza zasad klucz zawartości jest wysyłane tylko do klienta, który stanowi prawidłowe JSON Web Token (JWT) lub prosty token sieci web (SWT) w żądaniu kluczy/licencji. Ten token musi zostać wystawiony przez usługę tokenu zabezpieczającego (STS). Można użyć usługi Azure Active Directory jako Usługa STS lub wdrażanie niestandardowej usługi STS. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy Media Services zwraca żądany kluczy/licencji do klienta, jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z oświadczeniami skonfigurowanymi dla kluczy/licencji.

Po skonfigurowaniu zasadzie ograniczenia tokenu, należy określić podstawowy klucz weryfikacji wystawcy i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą. Wystawca jest usługa bezpiecznych tokenów, który wystawia token. Grupy odbiorców, czasami nazywane zakresu, opisuje przeznaczenie tokenu lub zasób tokenu autoryzuje dostępu do. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasuje do wartości w szablonie.

## <a name="next-steps"></a>Kolejne kroki

* [Ochrona przy użyciu szyfrowania AES](protect-with-aes128.md)
* [Ochrona za pomocą technologii DRM](protect-with-drm.md)
* [Projektowanie technologii multi-drm systemu ochrony zawartości przy użyciu kontroli dostępu](design-multi-drm-system-with-access-control.md)
* [Często zadawane pytania](frequently-asked-questions.md)


