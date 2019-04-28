---
title: Skonfiguruj zasady autoryzacji klucza zawartości przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady autoryzacji klucza zawartości.
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
ms.openlocfilehash: b046ce5a8647abe601a6327667241d98445ce1e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61130559"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurowanie zasad autoryzacji klucza zawartości
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Omówienie
 Usługa Azure Media Services umożliwia dostarczanie strumieni MPEG-DASH, Smooth Streaming i HTTP Live Streaming (HLS) chronionych przy użyciu Advanced Encryption Standard (AES) przy użyciu kluczy szyfrowania 128-bitowego lub [PlayReady zarządzania prawami cyfrowymi (DRM)](https://www.microsoft.com/playready/overview/). Usługa Media Services można też dostarczać strumienie DASH szyfrowane przy użyciu usługi Widevine DRM. Obie usługi, PlayReady i Widevine, szyfrują dane zgodnie ze specyfikacją Common Encryption (ISO/IEC CENC 23001-7).

Media Services udostępnia również usługi dostarczania kluczy/licencji, z którego klienci mogą pobierać, kluczy szyfrowania AES lub licencji PlayReady/Widevine do odtwarzania zaszyfrowanej zawartości.

W tym artykule przedstawiono sposób konfigurowania zasad autoryzacji klucza zawartości przy użyciu witryny Azure portal. Klucz można później dynamiczne szyfrowanie zawartości. Obecnie można szyfrować HLS, MPEG-DASH i Smooth Streaming formatów. Nie można zaszyfrować pobierania progresywnego.

Gdy gracz zażąda strumienia, który jest ustawiony na był dynamicznie szyfrowany, Media Services używa skonfigurowanego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania AES lub technologii DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

Jeśli planowane jest używanie wielu kluczy zawartości lub aby określić adres URL usługi dostarczania kluczy/licencji innych niż usługa dostarczania kluczy usługi Media Services, należy użyć, Media Services .NET SDK lub interfejsów API REST. Aby uzyskać więcej informacji, zobacz:

* [Skonfiguruj zasady autoryzacji klucza zawartości przy użyciu zestawu SDK .NET usługi Media Services](media-services-dotnet-configure-content-key-auth-policy.md)
* [Skonfiguruj zasady autoryzacji klucza zawartości przy użyciu interfejsu API REST usługi Media Services](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Zagadnienia do rozważenia
* Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego musi być w stanie "Uruchomiona". 
* Element zawartości musi zawierać zestaw każdego pliku MP4 z adaptacyjną szybkością transmisji bitów lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów. Aby uzyskać więcej informacji, zobacz [kodowanie elementu zawartości](media-services-encode-asset.md).
* Usługa dostarczania kluczy buforuje ContentKeyAuthorizationPolicy i jej powiązane obiekty (opcje zasad i ograniczeń) przez 15 minut. Można tworzyć ContentKeyAuthorizationPolicy i określić, czy token ograniczenie użycia, przetestować go, a następnie zaktualizować zasady do ograniczenia otwarte. Ten proces trwa około 15 minut przed przełączników zasad do wersji open.
* Punkt końcowy przesyłania strumieniowego usługi Media Services ustawia wartość nagłówka CORS Access-Control-Allow-Origin w wstępnego odpowiedź jako symbolu wieloznacznego "\*". Ta wartość dobrze działa z większości odtwarzaczy, w tym usługi Azure Media Player, Roku i JWPlayer i inne. Jednak niektóre odtwarzacze, korzystających z implementacją dash.js nie działają, ponieważ w trybie poświadczeń ustawiona na "include" XMLHttpRequest w ich implementacją dash.js nie zezwala na symbol wieloznaczny "\*" z wartością Access-Control-Allow-Origin. Jako obejście tego ograniczenia w implementacją dash.js Jeśli Twój klient z jednej domeny są hostowane usługi Media Services można określić tej domeny w nagłówku odpowiedzi wstępnego. Aby uzyskać pomoc Otwórz bilet pomocy technicznej za pośrednictwem witryny Azure portal.

## <a name="configure-the-key-authorization-policy"></a>Konfigurowanie zasad autoryzacji klucza
Aby skonfigurować zasady autoryzacji klucza, wybierz **CONTENT PROTECTION** strony.

Usługa Media Services obsługuje wiele sposobów, aby uwierzytelniać użytkowników, którzy tworzą żądania klucza. Zasady autoryzacji klucza zawartości mogą mieć po otwarciu tokenu lub ograniczenia autoryzacji adresów IP. (IP można skonfigurować za pomocą REST lub zestawu .NET SDK).

### <a name="open-restriction"></a>Otwórz ograniczeń
Otwórz ograniczeń oznacza, że system udostępnia klucz dla każdego, kto wykonuje żądanie klucza. To ograniczenie może być przydatna do celów testowych.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Ograniczenia tokenu
Aby wybrać zasadzie ograniczenia tokenu, zaznacz **TOKENU** przycisku.

Zasadzie ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokenów w prosty token sieci web ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) i formatuje tokenu Web JSON (JWT). Aby uzyskać więcej informacji, zobacz [uwierzytelniania tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Usługa Media Services nie dostarcza usługi STS. Można tworzyć niestandardowych usług STS do wystawiania tokenów. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z oświadczeniami skonfigurowanymi dla klucza zawartości, usługa dostarczania kluczy usługi Media Services zwraca klucz szyfrowania do klienta.

Po skonfigurowaniu zasad ograniczony token, należy określić podstawowy klucz weryfikacji wystawcy i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą. Wystawca jest usługą STS, która wystawia token. Grupy odbiorców (nazywane również zakres) opisuje przeznaczenie tokenu lub zasób tokenu autoryzuje dostępu do. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasuje do wartości w szablonie.

### <a name="playready"></a>PlayReady
W przypadku ochrony zawartości przy użyciu technologii PlayReady, jedną z rzeczy, które należy określić w zasadach autoryzacji jest ciągu XML, który definiuje szablonu licencji technologii PlayReady. Domyślnie ustawiony są następujące zasady:

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

Możesz wybrać **zaimportować plik xml zasad** przycisk, a następnie podaj inny plik XML, który jest zgodny ze schematem XML zdefiniowane w [omówienie szablonu licencji PlayReady usługi Media Services](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

