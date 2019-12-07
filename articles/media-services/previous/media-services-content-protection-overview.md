---
title: Ochrona zawartości za pomocą Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera omówienie ochrony zawartości w programie Azure Media Services V2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: b0d71a7b010e91776a28330cfc32278c7060aab6
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901254"
---
# <a name="content-protection-overview"></a>Omówienie ochrony zawartości 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Usługa Azure Media Services umożliwia zabezpieczenie multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługa Media Services umożliwia dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub jeden z systemów zarządzania (prawami cyfrowymi DRM) trzech głównych prawami cyfrowymi: PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

Na poniższym obrazie przedstawiono przepływ pracy usługi Media Services Ochrona zawartości: 

![Ochrona za pomocą PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

W tym artykule opisano pojęcia i terminologia istotne dla zrozumienia, ochrony zawartości przy użyciu usługi Media Services. Artykuł zawiera również linki do artykułów, w których omówiono sposób ochrony zawartości. 

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne
 Usługa Media Services umożliwia dostarczanie zawartości szyfrowane dynamicznie przy użyciu szyfrowania otwartym kluczem AES i technologii DRM szyfrowania za pomocą usług PlayReady, Widevine i FairPlay. Obecnie można zaszyfrować formatu HTTP Live Streaming (HLS), MPEG DASH i Smooth Streaming. Szyfrowanie przy pobieraniu progresywnym nie jest obsługiwane. Każda metoda szyfrowania obsługuje następujące protokoły przesyłania strumieniowego:

- AES: MPEG-KRESKa, Smooth Streaming i HLS
- PlayReady: MPEG-KRESKa, Smooth Streaming i HLS
- Widevine: MPEG-KRESKa
- FairPlay: HLS

Aby zaszyfrować zasób, należy skojarzyć klucz zawartości szyfrowania z zasobem, a także skonfigurować zasady autoryzacji klucza. Klucze zawartości mogą być określone lub generowane automatycznie przez Media Services.

Należy również skonfigurować zasady dostarczania elementu zawartości. Jeśli chcesz przesłać strumieniowo zasób szyfrowany przez magazyn, upewnij się, że określisz, w jaki sposób chcesz go dostarczyć przez skonfigurowanie zasad dostarczania elementów zawartości.

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania otwartym kluczem AES i szyfrowania DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy Media Services. Aby zdecydować, czy użytkownik ma uprawnienia do uzyskiwania klucza, usługa szacuje zasady autoryzacji określone dla klucza.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 Wyczyść klucz a DRM
Klienci często zastanawiasz się, czy należy używać szyfrowania AES lub systemu DRM. Główną różnicą między dwoma systemami to, że przy użyciu szyfrowania AES klucz zawartości jest wysyłany do klienta w niezaszyfrowanej postaci ("w Wyczyść"). W rezultacie klucz użyty do zaszyfrowania zawartości mogą być wyświetlane w śledzenia sieci na komputerze klienckim w postaci zwykłego tekstu. Szyfrowanie otwartym kluczem AES-128 nadaje się do zastosowań, gdzie przeglądarka jest zaufany (na przykład szyfrowanie firmowych filmów wideo dystrybuowane w obrębie firmy do przeglądania przez pracowników).

PlayReady, Widevine i FairPlay wszystkie zapewnia wyższy poziom szyfrowania w porównaniu do algorytmu AES-128 szyfrowania otwartym kluczem. Klucz zawartości jest przesyłany w postaci zaszyfrowanej. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniejsze, złośliwy użytkownik na ataki. DRM jest zalecana dla przypadków użycia, w której przeglądarka może nie być zaufaną stronę i wymagają najwyższy poziom zabezpieczeń.

## <a name="storage-encryption"></a>Szyfrowanie w usłudze Storage
Za pomocą szyfrowania magazynu można lokalnie zaszyfrować zawartość, używając szyfrowania AES 256-bitowego. Następnie można przekazać ją do usługi Azure Storage, gdzie jest przechowywana w stanie zaszyfrowanej. Zasoby chronione za pomocą szyfrowania magazynu są automatycznie odszyfrowane i umieszczane w zaszyfrowanym systemie plików przed kodowaniem. Zasoby są opcjonalnie ponownie szyfrowane przed przekazaniem ich jako nowego wyjściowego elementu zawartości. Podstawowym przypadkiem użycia szyfrowania magazynu jest Zabezpieczanie plików multimedialnych o wysokiej jakości z użyciem silnego szyfrowania na dysku.

Aby dostarczyć zasób szyfrowany przy użyciu magazynu, należy skonfigurować zasady dostarczania zasobów, aby Media Services wie, jak chcesz dostarczać zawartość. Przed przesłaniem strumieniowego zasobu serwer przesyłania strumieniowego odszyfrowuje i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania (na przykład AES, typowe szyfrowanie lub bez szyfrowania).

## <a name="types-of-encryption"></a>Typy szyfrowania
W przypadku oprogramowania PlayReady i Widevine wykorzystuje się typowe szyfrowanie (tryb AES). FairPlay wykorzystuje szyfrowanie AES w trybie CBC. Szyfrowanie przy nieszyfrowanej kluczu AES-128 wykorzystuje szyfrowanie kopert.

## <a name="licenses-and-keys-delivery-service"></a>Usługi dostarczania licencji i kluczy
Usługi Media Services udostępnia usługę dostarczania kluczy na potrzeby dostarczania licencji DRM (PlayReady, Widevine i FairPlay) i kluczy szyfrowania AES do autoryzowanych klientów. Aby skonfigurować zasady autoryzacji i uwierzytelniania dla licencji i kluczy, można użyć [Azure Portal](media-services-portal-protect-content.md), interfejsu API REST lub zestawu SDK Media Services dla platformy .NET.

## <a name="control-content-access"></a>Dostęp do zawartości kontrolki
Można kontrolować, kto ma dostęp do zawartości, konfigurując zasady autoryzacji klucza zawartości. Zasady autoryzacji klucza zawartości obsługują ograniczenie Open lub token.

### <a name="open-authorization"></a>Otwórz autoryzację
W przypadku otwartych zasad autoryzacji klucz zawartości jest wysyłany do dowolnego klienta (bez ograniczeń).

### <a name="token-authorization"></a>Autoryzacja tokenu
W przypadku zasad autoryzacji z ograniczeniami tokenu klucz zawartości jest wysyłany tylko do klienta, który przedstawia prawidłowy token sieci Web JSON (JWT) lub prosty token sieci Web (SWT) w żądaniu klucza/licencji. Ten token musi zostać wystawiony przez usługę tokenu zabezpieczającego (STS). Można użyć usługi Azure Active Directory jako Usługa STS lub wdrażanie niestandardowej usługi STS. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy Media Services zwraca żądany kluczy/licencji do klienta, jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z oświadczeniami skonfigurowanymi dla kluczy/licencji.

Po skonfigurowaniu zasadzie ograniczenia tokenu, należy określić podstawowy klucz weryfikacji wystawcy i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą. Wystawca jest usługa bezpiecznych tokenów, który wystawia token. Grupy odbiorców, czasami nazywane zakresu, opisuje przeznaczenie tokenu lub zasób tokenu autoryzuje dostępu do. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasuje do wartości w szablonie.

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Jeśli zasób został zaszyfrowany za pomocą więcej niż jednego obiektu DRM, Użyj znacznika szyfrowania w adresie URL przesyłania strumieniowego: (format = "3u8-AAPL", szyfrowanie = "XXX").

Obowiązują następujące zastrzeżenia:

* Nie można określić więcej niż jednego typu szyfrowania.
* Nie trzeba określać typu szyfrowania w adresie URL, jeśli do zasobu zastosowano tylko jedno szyfrowanie.
* W przypadku typu szyfrowania wielkość liter nie jest rozróżniana.
* Można określić następujące typy szyfrowania:
  * **Cenc**: w przypadku oprogramowania PlayReady lub Widevine (typowe szyfrowanie)
  * **cbcs-AAPL**: for FairPlay (szyfrowanie AES CBC)
  * **CBC**: na potrzeby szyfrowania kopert AES

## <a name="next-steps"></a>Następne kroki
Poniższe artykuły zawierają opis następnych kroków, które ułatwią rozpoczęcie pracy z usługą Content Protection:

* [Ochrona przy użyciu szyfrowania magazynu](media-services-rest-storage-encryption.md)
* [Ochrona przy użyciu szyfrowania AES](media-services-protect-with-aes128.md)
* [Ochrona za pomocą oprogramowania PlayReady i/lub Widevine](media-services-protect-with-playready-widevine.md)
* [Ochrona za pomocą FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Powiązane linki

* [Uwierzytelnianie tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrowanie Azure Media Services OWIN aplikacji opartych na MVC z Azure Active Directory i ograniczanie dostarczania kluczy zawartości na podstawie oświadczeń JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
