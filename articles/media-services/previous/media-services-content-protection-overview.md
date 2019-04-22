---
title: Ochrona zawartości przy użyciu usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym artykule, zapewniają Przegląd ochrony zawartości przy użyciu usługi Media Services.
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
ms.openlocfilehash: 8259b58c7f30b63084e970bd9aed99642a43226f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58805741"
---
# <a name="content-protection-overview"></a>Omówienie ochrony zawartości 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Usługa Azure Media Services umożliwia zabezpieczenie multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługa Media Services można dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

Na poniższym obrazie przedstawiono przepływ pracy usługi Media Services Ochrona zawartości: 

![Ochrona za pomocą PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

W tym artykule opisano pojęcia i terminologia istotne dla zrozumienia, ochrony zawartości przy użyciu usługi Media Services. Artykuł zawiera również linki do artykułów opisujących sposób ochrony zawartości. 

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne
 Usługa Media Services umożliwia dostarczanie zawartości szyfrowane dynamicznie przy użyciu szyfrowania otwartym kluczem AES i technologii DRM szyfrowania za pomocą usług PlayReady, Widevine i FairPlay. Obecnie można zaszyfrować formatu HTTP Live Streaming (HLS), MPEG DASH i Smooth Streaming. Szyfrowanie na pobierania progresywnego nie jest obsługiwane. Każda metoda szyfrowania obsługuje następujące protokoły przesyłania strumieniowego:

- AES: MPEG-DASH, Smooth Streaming i HLS
- PlayReady: MPEG-DASH, Smooth Streaming i HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Aby zaszyfrować element zawartości, musisz skojarzyć klucz szyfrowania zawartości z elementów zawartości, a także skonfigurować zasady autoryzacji klucza. Kluczy zawartości można określić lub automatycznie generowane przez usługę Media Services.

Należy również skonfigurować zasady dostarczania elementu zawartości. Jeśli chcesz przesyłać strumieniowo do zasobu szyfrowany magazyn, upewnij się, można określić sposób do dostarczenia go przez skonfigurowanie zasad dostarczania elementów zawartości.

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania otwartym kluczem AES i szyfrowania DRM. Aby odszyfrować strumienia, gracz żądań klucz usługi dostarczania kluczy usługi Media Services. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

## <a name="aes-128-clear-key-vs-drm"></a>Vs otwartym kluczem AES-128. DRM
Klienci często zastanawiasz się, czy należy używać szyfrowania AES lub systemu DRM. Główną różnicą między dwoma systemami to, że przy użyciu szyfrowania AES klucz zawartości jest wysyłany do klienta w niezaszyfrowanej postaci ("w Wyczyść"). W rezultacie klucz użyty do zaszyfrowania zawartości mogą być wyświetlane w śledzenia sieci na komputerze klienckim w postaci zwykłego tekstu. Szyfrowanie otwartym kluczem AES-128 nadaje się do zastosowań, gdzie przeglądarka jest zaufany (na przykład szyfrowanie firmowych filmów wideo dystrybuowane w obrębie firmy do przeglądania przez pracowników).

PlayReady, Widevine i FairPlay wszystkie zapewnia wyższy poziom szyfrowania w porównaniu do algorytmu AES-128 szyfrowania otwartym kluczem. Klucz zawartości jest przesyłany w postaci zaszyfrowanej. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniejsze, złośliwy użytkownik na ataki. DRM jest zalecana dla przypadków użycia, w której przeglądarka może nie być zaufaną stronę i wymagają najwyższy poziom zabezpieczeń.

## <a name="storage-encryption"></a>Szyfrowanie w usłudze Storage
Szyfrowanie magazynu umożliwia szyfrowanie zawartości lokalnie, za pomocą 256-bitowym szyfrowaniem AES. Możesz następnie przekazać go do usługi Azure Storage, gdzie są przechowywane szyfrowane w stanie spoczynku. Elementy zawartości chronione przy użyciu szyfrowania magazynu są niezaszyfrowane i automatycznie umieszczane w systemie szyfrowania plików przed kodowaniem. Zasoby są opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Głównym zastosowaniem szyfrowania magazynu jest, gdy chcesz zabezpieczyć swoje wysokiej jakości multimedialnych plików wejściowych za pomocą silnego szyfrowania magazynowanych na dysku.

Do dostarczania elementu zawartości szyfrowany magazyn, należy skonfigurować zasady dostarczania elementu zawartości, tak, aby usługi Media Services wie, jak chcesz dostarczania zawartości. Zanim może być przesyłany strumieniowo element zawartości, serwer przesyłania strumieniowego odszyfrowuje i przesyła strumieniowo zawartość za pomocą zasad określony dostarczania (na przykład, AES, stosowanie szyfrowania common encryption lub bez szyfrowania).

## <a name="types-of-encryption"></a>Typy szyfrowania
Technologie PlayReady i Widevine korzystanie z szyfrowania common encryption (tryb Ewidencyjne AES). Technologia FairPlay korzysta z szyfrowania w trybie CBC AES. Szyfrowanie otwartym kluczem AES-128 korzysta z szyfrowania koperty.

## <a name="licenses-and-keys-delivery-service"></a>Usługi dostarczania licencji i kluczy
Usługi Media Services udostępnia usługę dostarczania kluczy na potrzeby dostarczania licencji DRM (PlayReady, Widevine i FairPlay) i kluczy szyfrowania AES do autoryzowanych klientów. Możesz użyć [witryny Azure portal](media-services-portal-protect-content.md), interfejs API REST lub Media Services SDK dla platformy .NET, aby skonfigurować zasady autoryzacji i uwierzytelniania dla Twojej licencji i kluczy.

## <a name="control-content-access"></a>Dostęp do zawartości kontrolki
Można kontrolować, kto ma dostęp do zawartości, konfigurując zasady autoryzacji klucza zawartości. Zasady autoryzacji klucza zawartości obsługują ograniczenie otwarte lub tokenu.

### <a name="open-authorization"></a>Otwórz autoryzacji
Przy użyciu zasad usługi open autoryzacji klucza zawartości jest wysyłany do dowolnego klienta (nie ograniczenia).

### <a name="token-authorization"></a>Token autoryzacji
Za pomocą zasad ograniczony token autoryzacji klucza zawartości jest wysyłane tylko do klienta, który stanowi prawidłowe JSON Web Token (JWT) lub prosty token sieci web (SWT) w żądaniu kluczy/licencji. Ten token musi zostać wystawiony przez usługę tokenu zabezpieczającego (STS). Można użyć usługi Azure Active Directory jako Usługa STS lub wdrażanie niestandardowej usługi STS. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy Media Services zwraca żądany kluczy/licencji do klienta, jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z oświadczeniami skonfigurowanymi dla kluczy/licencji.

Po skonfigurowaniu zasadzie ograniczenia tokenu, należy określić podstawowy klucz weryfikacji wystawcy i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą. Wystawca jest usługa bezpiecznych tokenów, który wystawia token. Grupy odbiorców, czasami nazywane zakresu, opisuje przeznaczenie tokenu lub zasób tokenu autoryzuje dostępu do. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasuje do wartości w szablonie.

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Jeśli element zawartości został zaszyfrowany za pomocą więcej niż jeden DRM, użycie znacznika szyfrowania w adresu URL przesyłania strumieniowego: (format = "m3u8-aapl" szyfrowania = "xxx").

Mają zastosowanie następujące kwestie:

* Można określić maksymalnie jeden typ szyfrowania.
* Typ szyfrowania nie ma określonego w adresie URL, jeśli tylko jeden szyfrowania została zastosowana do zasobu.
* Typ szyfrowania jest uwzględniana wielkość liter.
* Można określić następujące typy szyfrowania:
  * **cenc**: Aby uzyskać PlayReady lub Widevine (common encryption)
  * **cbcs-aapl**: Dla technologii FairPlay (szyfrowanie AES-CBC)
  * **cbc**: Do szyfrowania koperty AES

## <a name="next-steps"></a>Kolejne kroki
Następujące artykuły opisano kolejne kroki, aby pomóc Ci rozpocząć pracę z usługą content protection:

* [Ochrona przy użyciu szyfrowania magazynu](media-services-rest-storage-encryption.md)
* [Ochrona przy użyciu szyfrowania AES](media-services-protect-with-aes128.md)
* [Ochrona za pomocą usług PlayReady i Widevine](media-services-protect-with-playready-widevine.md)
* [Ochrona z użyciem technologii FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Powiązane linki

* [Uwierzytelnianie przy użyciu tokenów JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integracja aplikacji opartych na usłudze Azure Media Services OWIN MVC za pomocą usługi Azure Active Directory i ograniczenie klucza dostarczania zawartości na podstawie oświadczeń JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
