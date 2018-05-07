---
title: "Ochrona zawartości przy użyciu usługi Azure Media Services | Dokumentacja firmy Microsoft"
description: "W tym artykule nadaj Omówienie ochrony zawartości z usługi Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 13447fd9193374d80ed5c2e6af8543f11b95e709
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="content-protection-overview"></a>Omówienie ochrony zawartości
 Usługi Azure Media Services służy do zabezpieczania od momentu, gdy opuszczą komputera za pośrednictwem przechowywania, przetwarzania i dostarczania multimediów. Z usługi Media Services można dostarczania zawartości na żywo i na żądanie dynamicznie szyfrowany za pomocą Advanced Encryption Standard (AES-128) lub jeden z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): Microsoft PlayReady, Google Widevine i FairPlay firmy Apple. Usługa Media Services udostępnia usługę dostarczania kluczy AES i DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

Na poniższym obrazie przedstawiono przepływu pracy ochrony zawartości Media Services: 

![Ochrona za pomocą PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

W tym artykule opisano pojęcia i terminologię istotne dla zrozumienia ochrony zawartości z usługi Media Services. Artykuł również zawiera łącza do artykułów, które zawierają opis do ochrony zawartości. 

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne
 Usługi Media Services umożliwia dostarczanie dynamicznie zaszyfrowany wyczyść klucza AES lub szyfrowania DRM, za pomocą PlayReady i Widevine, FairPlay zawartości. Obecnie można zaszyfrować formatów HTTP Live Streaming (HLS), MPEG DASH i Smooth Streaming. Szyfrowanie na pobierania progresywnego nie jest obsługiwane. Każda metoda szyfrowania obsługuje następujących protokołów:

- AES: MPEG-DASH, Smooth Streaming i HLS
- PlayReady: MPEG-DASH, Smooth Streaming i HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Aby zaszyfrować element zawartości, należy skojarzyć klucz szyfrowania zawartości z zawartości, a także skonfigurować zasady autoryzacji klucza. Kluczy zawartości można określić lub generowane automatycznie przez usługę Media Services.

Należy również skonfigurować zasady dostarczania elementu zawartości. Jeśli ma być przesyłana strumieniowo zaszyfrowana magazynu trwałego, upewnij się, że Określ sposób dostarczania go przez skonfigurowanie zasad dostarczania elementów zawartości.

Strumień zleconą przez odtwarzacz usługi Media Services używa określonego klucza do dynamicznego szyfrowania zawartości za pomocą klucza niezaszyfrowanego AES lub szyfrowania DRM. Aby odszyfrować strumienia, odtwarzacza zażąda klucza od usługi klucza dostawy Media Services. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

## <a name="aes-128-clear-key-vs-drm"></a>Vs wyczyść klucza AES-128. DRM
Klienci często zastanawiasz się, czy należy używać szyfrowania AES lub DRM system. Główną różnicą między tymi dwoma systemami jest, że z szyfrowania AES klucz zawartości są przesyłane do klientów w niezaszyfrowanej postaci ("w Wyczyść"). W związku z tym klucz używany do szyfrowania zawartości można wyświetlić w śledzenia sieci na komputerze klienckim w postaci zwykłego tekstu. Wyczyść klucza szyfrowania AES-128 jest odpowiednia dla przypadków użycia, gdy przeglądarka jest zaufany (na przykład szyfrowania firmowej filmy wideo rozproszonych w obrębie firmy będą wyświetlane przez pracowników).

PlayReady, Widevine i FairPlay wszystkie zapewnia wyższy poziom szyfrowania w porównaniu do AES-128 wyczyść klucza szyfrowania. Klucz zawartości jest przekazywane w postaci zaszyfrowanej. Ponadto odszyfrowywania jest obsługiwany w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniej złośliwemu użytkownikowi na ataki. DRM zaleca się użycie zgłoszenia Podgląd może nie być zaufany wymagają najwyższy poziom zabezpieczeń.

## <a name="storage-encryption"></a>Szyfrowanie w usłudze Storage
Szyfrowanie magazynu służy do szyfrowania zawartości lokalnie, przy użyciu szyfrowania AES 256-bitowego. Następnie można przekazać go do magazynu Azure, gdzie jest przechowywana szyfrowane, gdy. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie bez szyfrowania i umieszczane w systemie szyfrowania plików przed kodowaniem. Zasoby są opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest, gdy chcesz zabezpieczyć Twoje wysokiej jakości multimedialnych plików wejściowych z silne szyfrowanie przechowywanych na dysku.

Aby dostarczyć zasób szyfrowane magazynu, należy skonfigurować zasady dostarczania elementu zawartości, aby usługi Media Services znała sposób dostarczania zawartości. Aby mogła być przesłana strumieniowo zawartości, serwer przesyłania strumieniowego odszyfrowuje i strumieni zawartości za pomocą zasad określonym dostarczania (na przykład AES, wspólnego szyfrowania lub bez szyfrowania).

## <a name="types-of-encryption"></a>Typy szyfrowania
PlayReady i Widevine korzystać z szyfrowania common encryption (tryb Ewidencyjne AES). FairPlay używa szyfrowania w trybie CBC AES. Wyczyść klucza szyfrowania AES-128 używa szyfrowania koperty.

## <a name="licenses-and-keys-delivery-service"></a>Usługi dostarczania licencji i kluczy
Usługi Media Services udostępnia usługę dostarczania kluczy do dostarczania licencji DRM (PlayReady, Widevine, FairPlay) i kluczy AES do autoryzowanych klientów. Można użyć [portalu Azure](media-services-portal-protect-content.md), interfejsu API REST lub SDK usługi Media Services dla platformy .NET, aby skonfigurować zasady autoryzacji i uwierzytelniania dla platformy licencji i kluczy.

## <a name="control-content-access"></a>Kontrola dostępu do zawartości
Można kontrolować, kto ma dostęp do zawartości przez skonfigurowanie zasad autoryzacji klucza zawartości. Zasady autoryzacji klucza zawartości obsługuje ograniczenie otwarte lub tokenu.

### <a name="open-authorization"></a>Otwórz autoryzacji
Przy użyciu zasad autoryzacji Otwórz klucz zawartości jest wysyłane do dowolnego klienta (nie ograniczenia).

### <a name="token-authorization"></a>Token autoryzacji
Za pomocą zasad ograniczonej token autoryzacji klucza zawartości jest wysyłane tylko do klienta, który stanowi prawidłowe JSON Web Token (JWT) lub simple web token (SWT) w żądaniu klucz/licencji. Token ten musi być wystawiony przez usługę tokenu zabezpieczającego (STS). Można użyć usługi Azure Active Directory jako tokenu Zabezpieczającego lub wdrożyć niestandardowe STS. Usługa tokenu Zabezpieczającego musi być skonfigurowana do utworzenia tokenu podpisany z określonego klucza i problem oświadczenia określony w konfiguracji ograniczenia tokenu. Usługa Media Services klucza dostawy zwraca klucz żądanej/licencji do klienta, jeśli token jest prawidłowy, a oświadczenia w tokenie pasują do klucza/licencji.

Po skonfigurowaniu zasadzie ograniczenia tokenu, należy określić klucz podstawowy weryfikacji, wystawcy i parametry odbiorców. Klucz podstawowy weryfikacji zawiera klucz, który został podpisany token, z. Wystawca jest bezpieczne usługi tokenu, który wystawia token. Odbiorców, nazywane również zakres, w tym artykule opisano celem tokenu lub zasobu tokenu zezwala na dostęp do. Usługa Media Services klucza dostawy weryfikuje, czy te wartości w tokenie pasują do wartości w szablonie.

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Jeśli zawartości została zaszyfrowana z więcej niż jeden DRM, użyj tagu szyfrowania w adresie URL przesyłania strumieniowego: (format = "m3u8-aapl, szyfrowanie = 'xxx').

Następujące kwestie:

* Można określić maksymalnie jeden typ szyfrowania.
* Typ szyfrowania nie ma określonego w adresie URL, jeśli tylko jeden szyfrowania została zastosowana do zasobu.
* Typ szyfrowania jest uwzględniana wielkość liter.
* Można określić następujące typy szyfrowania:
  * **cenc**: dla PlayReady lub Widevine (szyfrowania common encryption)
  * **cbcs-aapl**: dla FairPlay (szyfrowanie AES CBC)
  * **CBC**: dla AES envelope szyfrowania

## <a name="next-steps"></a>Kolejne kroki
Poniższe artykuły zawierają następne kroki ułatwiające rozpoczęcie pracy z ochrony zawartości:

* [Ochrona przy użyciu szyfrowania magazynu](media-services-rest-storage-encryption.md)
* [Ochrona przy użyciu szyfrowania AES](media-services-protect-with-aes128.md)
* [Chroń za pomocą PlayReady i Widevine](media-services-protect-with-playready-widevine.md)
* [Chroń za pomocą FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Powiązane linki

* [Wyjaśniono cennik dostarczania licencji PlayReady usługi multimediów Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)
* [Debugowanie AES strumienia zaszyfrowane w usłudze Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)
* [Uwierzytelniania tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrowanie aplikacji usługi Azure Media Services OWIN MVC z usługą Azure Active Directory i ograniczenie klucza dostarczania zawartości na podstawie oświadczeń JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
