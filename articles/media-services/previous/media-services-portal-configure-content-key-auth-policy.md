---
title: Konfigurowanie zasad autoryzacji klucza zawartości przy użyciu witryny Azure Portal | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak skonfigurować zasady autoryzacji dla klucza zawartości.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 8580bafd4d68ef6567b09fefcaa01c682ae2cafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968803"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurowanie zasad autoryzacji klucza zawartości
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Omówienie
 Za pomocą usługi Azure Media Services można dostarczać strumienie MPEG-DASH, Smooth Streaming i HTTP Live Streaming (HLS) chronione za pomocą zaawansowanego standardu szyfrowania (AES) przy użyciu 128-bitowych kluczy szyfrowania lub [zarządzania prawami cyfrowymi PlayReady (DRM).](https://www.microsoft.com/playready/overview/) Za pomocą usługi Media Services można również dostarczać strumienie DASH zaszyfrowane za pomocą technologii Widevine DRM. Obie usługi, PlayReady i Widevine, szyfrują dane zgodnie ze specyfikacją Common Encryption (ISO/IEC CENC 23001-7).

Usługa Media Services zapewnia również usługę dostarczania kluczy/licencji, z której klienci mogą uzyskać klucze AES lub licencje PlayReady/Widevine do odtwarzania zaszyfrowanej zawartości.

W tym artykule pokazano, jak korzystać z witryny Azure Portal, aby skonfigurować zasady autoryzacji klucza zawartości. Klucz może być później używany do dynamicznego szyfrowania zawartości. Obecnie można szyfrować formaty HLS, MPEG-DASH i Smooth Streaming. Nie można szyfrować progresywnych pobrań.

Gdy odtwarzacz żąda strumienia, który jest skonfigurowany do dynamicznego szyfrowania, usługa Media Services używa skonfigurowanego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania AES lub DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa ocenia zasady autoryzacji określone dla klucza.

Jeśli planujesz mieć wiele kluczy zawartości lub chcesz określić adres URL usługi dostarczania kluczy/licencji inny niż usługa dostarczania kluczy usługi Media Services, użyj interfejsów API interfejsu US .NET usługi Media Services lub REST. Aby uzyskać więcej informacji, zobacz:

* [Konfigurowanie zasad autoryzacji klucza zawartości przy użyciu narzędzia Media Services . NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurowanie zasad autoryzacji klucza zawartości przy użyciu interfejsu API REST usługi Media Services](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Niektóre kwestie mają zastosowanie
* Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z dynamicznego pakowania i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego musi być w stanie "Uruchomiony". 
* Zasób musi zawierać zestaw adaptacyjnych plików MP4 o szybkości transmisji bitów lub adaptacyjnych plików płynnego przesyłania strumieniowego o szybkości transmisji bitów. Aby uzyskać więcej informacji, zobacz [Kodowanie zasobu](media-services-encode-asset.md).
* Usługa dostarczania kluczy buforuje ContentKeyAuthorizationPolicy i jej powiązane obiekty (opcje zasad i ograniczenia) przez 15 minut. Można utworzyć ContentKeyAuthorizationPolicy i określić, aby użyć ograniczenia tokenu, przetestować go, a następnie zaktualizować zasady do otwartego ograniczenia. Ten proces trwa około 15 minut, zanim zasady przełączy się do otwartej wersji.
* Punkt końcowy przesyłania strumieniowego usługi Media Services ustawia wartość nagłówka CORS Access-Control-Allow-Origin w odpowiedzi inspekcji wstępnej jako symbol wieloznaczny "\*". Ta wartość działa dobrze z większością graczy, w tym Azure Media Player, Roku i JWPlayer i innych. Jednak niektórzy gracze korzystający z dash.js nie działają, ponieważ z trybem poświadczeń ustawionym na "include", XMLHttpRequest w ich dash.js nie zezwala na symbol wieloznaczny "\*jako wartość access-control-allow-origin. Aby obejść to ograniczenie w dash.js, jeśli klient hostuje z jednej domeny, usługa Media Services może określić tę domenę w nagłówku odpowiedzi inspekcji wstępnej. Aby uzyskać pomoc, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure portal.

## <a name="configure-the-key-authorization-policy"></a>Konfigurowanie zasad autoryzacji kluczy
Aby skonfigurować zasady autoryzacji kluczy, wybierz stronę **OCHRONA ZAWARTOŚCI.**

Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy żądają kluczy. Zasady autoryzacji klucza zawartości mogą mieć ograniczenia autoryzacji otwarte, tokenowe lub IP. (Protokół IP można skonfigurować za pomocą restu lub sdk .NET).

### <a name="open-restriction"></a>Otwórz ograniczenie
Otwarte ograniczenie oznacza, że system dostarcza klucz każdemu, kto zgłosi żądanie klucza. To ograniczenie może być przydatne do celów testowych.

![Otwarta polityka][open_policy]

### <a name="token-restriction"></a>Ograniczenie tokenu
Aby wybrać zasady z ograniczeniami tokenu, wybierz przycisk **TOKEN.**

Zasady z ograniczeniami tokenów muszą być dołączona token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługi Media Services obsługują tokeny w formatach prostego tokenu sieci web[(SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)i JSON Web Token (JWT). Aby uzyskać więcej informacji, zobacz [uwierzytelnianie JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Usługi Media Services nie zapewniają usługi STS. Można utworzyć niestandardowy STS do wystawiania tokenów. Sts musi być skonfigurowany do tworzenia tokenu podpisanego przy określonym kluczu i oświadczeń problem, które zostały określone w konfiguracji ograniczenia tokenu. Jeśli token jest prawidłowy, a oświadczenia w tokenie są zgodne z tymi skonfigurowanym dla klucza zawartości, usługa dostarczania kluczy usługi Media Services zwraca klucz szyfrowania do klienta.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacji, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, za pomocą którym token został podpisany. Wystawcą jest STS, który wystawia token. Grupa odbiorców (czasami nazywana zakresem) opisuje intencję tokenu lub zasobu, do który token autoryzuje dostęp. Usługa dostarczania kluczy usługi Media Services sprawdza, czy te wartości w tokenie są zgodne z wartościami w szablonie.

### <a name="playready"></a>PlayReady
Podczas ochrony zawartości za pomocą PlayReady jedną z rzeczy, które należy określić w zasadach autoryzacji jest ciąg XML, który definiuje szablon licencji PlayReady. Domyślnie ustawiono następujące zasady:

    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

Można wybrać przycisk **xml zasad importu** i podać inny kod XML zgodny ze schematem XML zdefiniowanym w [przeglądzie szablonu licencji Programu Media Services PlayReady](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

