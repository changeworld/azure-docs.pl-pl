---
title: Chroń swoją zawartość za pomocą usługi Azure Media Services | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie ochrony zawartości za pomocą usługi Azure Media Services w wersji 2.
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
ms.openlocfilehash: 88e0e1c18722fd86e79fc1fa7722b59b3cb8966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460963"
---
# <a name="content-protection-overview"></a>Omówienie ochrony zawartości 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Usługi Azure Media Services można używać do zabezpieczania multimediów od momentu opuszczenia komputera za pośrednictwem magazynu, przetwarzania i dostarczania. Usługa Media Services umożliwia dynamiczne szyfrowanie zawartości na żywo i na żądanie za pomocą standardu Advanced Encryption Standard (AES-128) lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Usługa Media Services zapewnia również usługę dostarczania licencji kluczy AES i DRM (PlayReady, Widevine i FairPlay) autoryzowanym klientom. 

Na poniższej ilustracji przedstawiono przepływ pracy ochrony zawartości usługi Media Services: 

![Ochrona za pomocą PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

W tym artykule opisano pojęcia i terminologii istotne dla zrozumienia ochrony zawartości z usługi Media Services. Artykuł zawiera również łącza do artykułów, które omawiają sposób ochrony zawartości. 

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

Za pomocą usługi Media Services można dynamicznie dostarczać zawartość za pomocą czystego klucza AES lub szyfrowania DRM przy użyciu funkcji PlayReady, Widevine lub FairPlay. Jeśli zawartość jest szyfrowana za pomocą czystego klucza AES i jest wysyłana za pośrednictwem protokołu HTTPS, nie jest w stanie wyczyścić, dopóki nie dotrze do klienta. 

Każda metoda szyfrowania obsługuje następujące protokoły przesyłania strumieniowego:
 
- AES: MPEG-DASH, Płynne przesyłanie strumieniowe i HLS
- PlayReady: MPEG-DASH, Płynne przesyłanie strumieniowe i HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Szyfrowanie podczas pobierania progresywnego nie jest obsługiwane. 

Aby zaszyfrować zasób, należy skojarzyć klucz zawartości szyfrowania z zasobem, a także skonfigurować zasady autoryzacji klucza. Klucze zawartości mogą być określone lub generowane automatycznie przez usługi Media Services.

Należy również skonfigurować zasady dostarczania zasobu. Jeśli chcesz przesyłać strumieniowo zasób zaszyfrowany magazynem, określ sposób jego dostarczenia, konfigurując zasady dostarczania zasobów.

Gdy odtwarzacz zażąda strumienia, usługa Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu czystego klucza AES lub szyfrowania DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy usługi Media Services. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa ocenia zasady autoryzacji określone dla klucza.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 wyczyść klucz vs. DRM
Klienci często zastanawiają się, czy powinni używać szyfrowania AES, czy systemu DRM. Podstawową różnicą między tymi dwoma systemami jest to, że przy szyfrowaniu AES klucz zawartości jest przesyłany do klienta w formacie niezaszyfrowanym ("w wyczyściu"). W rezultacie klucz używany do szyfrowania zawartości można wyświetlić w śledzenia sieci na kliencie w postaci zwykłego tekstu. Szyfrowanie klucza ściąglowego AES-128 jest odpowiednie w przypadkach użycia, w których widz jest stroną zaufaną (na przykład szyfrowanie firmowych filmów wideo dystrybuowanych w firmie, które mają być wyświetlane przez pracowników).

PlayReady, Widevine i FairPlay zapewniają wyższy poziom szyfrowania w porównaniu do szyfrowania klucza Wyczyszczanego AES-128. Klucz zawartości jest przesyłany w zaszyfrowanym formacie. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniejsze dla złośliwego użytkownika do ataku. DrM jest zalecane w przypadkach użycia, w których przeglądarka może nie być zaufaną stroną i wymagają najwyższego poziomu zabezpieczeń.

## <a name="storage-encryption"></a>Szyfrowanie w usłudze Storage
Szyfrowanie pamięci masowej umożliwia szyfrowanie wyczyszczanych zawartości lokalnie przy użyciu szyfrowania 256-bitowego AES. Następnie można przekazać go do usługi Azure Storage, gdzie jest przechowywany zaszyfrowany w spoczynku. Zasoby chronione szyfrowaniem magazynu są automatycznie niezaszyfrowane i umieszczane w zaszyfrowanym systemie plików przed kodowaniem. Zasoby są opcjonalnie ponownie szyfrowane przed przekazaniem z powrotem jako nowego zasobu wyjściowego. Podstawowym przypadkiem użycia szyfrowania magazynu jest, gdy chcesz zabezpieczyć wysokiej jakości pliki multimedialne wejściowe z silnym szyfrowaniem w spoczynku na dysku.

Aby dostarczyć zasób zaszyfrowany magazynem, należy skonfigurować zasady dostarczania zasobu, aby program Media Services wiedział, jak chcesz dostarczać zawartość. Przed przesyłaniem strumieniowego zasobu serwer przesyłania strumieniowego odszyfrowuje i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania (na przykład AES, szyfrowania wspólnego lub braku szyfrowania).

## <a name="types-of-encryption"></a>Typy szyfrowania
PlayReady i Widevine wykorzystują typowe szyfrowanie (tryb CTR AES). FairPlay wykorzystuje szyfrowanie w trybie CBC AES. Szyfrowanie klucza wyczyszczanego AES-128 wykorzystuje szyfrowanie kopert.

## <a name="licenses-and-keys-delivery-service"></a>Usługi dostarczania licencji i kluczy
Usługa Media Services zapewnia usługę dostarczania kluczy do dostarczania licencji DRM (PlayReady, Widevine, FairPlay) i kluczy AES autoryzowanym klientom. Za pomocą [witryny Azure portal,](media-services-portal-protect-content.md)interfejsu API REST lub narzędzia Media Services SDK dla platformy .NET można skonfigurować zasady autoryzacji i uwierzytelniania dla licencji i kluczy.

## <a name="control-content-access"></a>Kontrolowanie dostępu do zawartości
Możesz kontrolować, kto ma dostęp do zawartości, konfigurując zasady autoryzacji klucza zawartości. Zasady autoryzacji klucza zawartości obsługuje ograniczenie otwierania lub tokenu.

### <a name="open-authorization"></a>Otwarta autoryzacja
W przypadku zasad otwartej autoryzacji klucz zawartości jest wysyłany do dowolnego klienta (bez ograniczeń).

### <a name="token-authorization"></a>Autoryzacja tokenu
W przypadku zasad autoryzacji z ograniczeniami tokenu klucz zawartości jest wysyłany tylko do klienta, który przedstawia prawidłowy token JSON Web Token (JWT) lub prosty token internetowy (SWT) w żądaniu klucza/licencji. Ten token musi być wystawiony przez usługę tokenu zabezpieczającego (STS). Usługi Azure Active Directory można używać jako usługi STS lub wdrażać niestandardowy sts. Sts musi być skonfigurowany do tworzenia tokenu podpisanego przy określonym kluczu i oświadczeń problem, które zostały określone w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy usługi Media Services zwraca żądany klucz/licencję do klienta, jeśli token jest prawidłowy, a oświadczenia w tokenie są zgodne z tymi skonfigurowanym dla klucza/licencji.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacji, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, za pomocą którym token został podpisany. Wystawca jest usługą bezpiecznego tokenu, która wystawia token. Odbiorcy, czasami nazywany zakres, opisuje intencji tokenu lub zasobu tokenu autoryzuje dostęp do. Usługa dostarczania kluczy usługi Media Services sprawdza, czy te wartości w tokenie są zgodne z wartościami w szablonie.

### <a name="token-replay-prevention"></a>Zapobieganie powtarzaniu żetonów

Funkcja *zapobiegania powtarzaniu powtórzów tokenu* umożliwia klientom usługi Media Services ustawienie limitu liczby razy tego samego tokenu, który może być użyty do żądania klucza lub licencji. Klient może dodać oświadczenie `urn:microsoft:azure:mediaservices:maxuses` typu w tokenie, gdzie wartość jest liczba razy token może służyć do uzyskania licencji lub klucza. Wszystkie kolejne żądania z tym samym tokenem do dostarczania kluczy zwróci nieautoryzowaną odpowiedź. Zobacz, jak dodać oświadczenie w [przykładzie DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci muszą mieć kontrolę nad generowaniem tokenów. Oświadczenie musi być umieszczone w samym tokenie.
* Korzystając z tej funkcji, żądania z tokenami, których czas wygaśnięcia jest więcej niż jedną godzinę od momentu odebranie żądania są odrzucane z nieautoryzowaną odpowiedzią.
* Tokeny są jednoznacznie identyfikowane przez ich podpis. Wszelkie zmiany w ładunku (na przykład, aktualizacja do czasu wygaśnięcia lub oświadczenia) zmienia podpis tokenu i będzie liczony jako nowy token, że dostarczanie kluczy nie natknąć się wcześniej.
* Odtwarzanie kończy się niepowodzeniem, `maxuses` jeśli token przekroczył wartość ustawioną przez klienta.
* Ta funkcja może służyć do wszystkich istniejących zawartości chronionej (tylko token wystawione musi zostać zmieniony).
* Ta funkcja działa zarówno z JWT, jak i SWT.

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Jeśli twój zasób został zaszyfrowany za pomocą więcej niż jednego drm, użyj tagu szyfrowania w adresie URL przesyłania strumieniowego: (format='m3u8-aapl', encryption='xxx').

Obowiązują następujące zastrzeżenia:

* Można określić nie więcej niż jeden typ szyfrowania.
* Typ szyfrowania nie musi być określony w adresie URL, jeśli tylko jedno szyfrowanie zostało zastosowane do zasobu.
* Typ szyfrowania jest bez uwzględniania wielkości liter.
* Można określić następujące typy szyfrowania:

  * **cenc**: Dla PlayReady lub Widevine (wspólne szyfrowanie)
  * **cbcs-aapl**: Dla FairPlay (szyfrowanie AES CBC)
  * **cbc**: Do szyfrowania kopert AES

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach opisano kolejne kroki ułatwiające rozpoczęcie pracy z ochroną zawartości:

* [Ochrona za pomocą szyfrowania pamięci masowej](media-services-rest-storage-encryption.md)
* [Ochrona za pomocą szyfrowania AES](media-services-protect-with-aes128.md)
* [Ochrona za pomocą PlayReady i/lub Widevine](media-services-protect-with-playready-widevine.md)
* [Chroń za pomocą FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Powiązane linki

* [Uwierzytelnianie tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integracja aplikacji opartej na MVC usługi Azure Media Services z usługą Azure Active Directory i ograniczanie dostarczania kluczy zawartości na podstawie oświadczeń JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
