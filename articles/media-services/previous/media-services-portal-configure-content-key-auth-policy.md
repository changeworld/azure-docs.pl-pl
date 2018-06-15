---
title: Skonfiguruj zasady autoryzacji klucza zawartości przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania zasad autoryzacji klucza zawartości.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: db0117b0b4ddee002fc69d71e78eca2b9008e4f6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941032"
---
# <a name="configure-a-content-key-authorization-policy"></a>Skonfiguruj zasady autoryzacji klucza zawartości
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Przegląd
 Azure Media Services umożliwia dostarczanie strumieni MPEG-DASH, Smooth Streaming i HTTP Live Streaming (HLS) chronionych z Standard AES (Advanced Encryption) przy użyciu kluczy szyfrowania 128-bitowego lub [PlayReady zarządzania prawami cyfrowymi (DRM)](https://www.microsoft.com/playready/overview/). Z usługi Media Services również można dostarczać strumienie DASH zaszyfrowane za pomocą Widevine DRM. Obie usługi, PlayReady i Widevine, szyfrują dane zgodnie ze specyfikacją Common Encryption (ISO/IEC CENC 23001-7).

Usługa Media Services udostępnia również usługi dostarczania klucz/licencji, z których klienci mogą pobierać kluczy AES lub licencje PlayReady/Widevine, aby odtworzyć zaszyfrowaną zawartość.

W tym artykule pokazano, jak skonfigurować zasady autoryzacji klucza zawartości przy użyciu portalu Azure. Klucz później może służyć do dynamicznego szyfrowania zawartości. Obecnie można zaszyfrować formatów Smooth Streaming, MPEG DASH i HLS. Nie można zaszyfrować pobierania progresywnego.

Gdy odtwarzacza zażąda strumienia, który ma ustawioną wartość był dynamicznie szyfrowany, Media Services używa klucza skonfigurowanych do dynamicznego szyfrowania zawartości przy użyciu szyfrowania AES lub DRM. Aby odszyfrować strumienia, odtwarzacza żądań klucz z usługi dostarczania klucza. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

Jeśli planujesz mieć wiele kluczy zawartości lub chcesz określać adresu URL usługi dostarczania klucz/licencji, niż usługa Media Services klucza dostawy, należy użyć zestawu .NET SDK usługi Media lub interfejsów API REST. Aby uzyskać więcej informacji, zobacz:

* [Skonfiguruj zasady autoryzacji klucza zawartości przy użyciu zestawu .NET SDK usługi multimediów](media-services-dotnet-configure-content-key-auth-policy.md)
* [Skonfiguruj zasady autoryzacji klucza zawartości przy użyciu interfejsu API REST usługi multimediów](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Zagadnienia do rozważenia
* Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z dynamicznego tworzenia pakietów i dynamicznego szyfrowania, punkt końcowy przesyłania strumieniowego musi być w stanie "Uruchomiona". 
* Zawartości musi zawierać zestaw o adaptacyjnej szybkości bitowej MP4s lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów. Aby uzyskać więcej informacji, zobacz [kodowanie elementu zawartości](media-services-encode-asset.md).
* Usługa klucza dostawy buforuje ContentKeyAuthorizationPolicy i jego obiektów pokrewnych (opcje zasad i ograniczeń) przez 15 minut. Można utworzyć ContentKeyAuthorizationPolicy i określić ograniczenia tokenu, przetestować go, a następnie zaktualizuj zasady z otwartych ograniczenia. Ten proces trwa około 15 minut przed przełączniki zasad Otwórz wersję.
* Usługi Media Services punktu końcowego przesyłania strumieniowego ustawia wartość nagłówka CORS Access-Control-Allow-Origin w odpowiedzi dotyczące stanu wstępnego jako symbolu wieloznacznego "\*". Ta wartość działa prawidłowo w przypadku większości odtwarzaczy, w tym Azure Media Player, Roku i JWPlayer i inne. Jednak niektóre odtwarzacze, które używają dash.js nie działa, ponieważ poświadczenia w trybie ustawioną wartość "Włącz" XMLHttpRequest w ich dash.js nie zezwala na symbol wieloznaczny "\*" jako wartości Access-Control-Allow-Origin. Jako obejście tego ograniczenia w dash.js Jeśli host klienta z pojedynczą domenę usługi Media Services można określić tej domeny w nagłówku odpowiedzi dotyczące stanu wstępnego. Aby uzyskać pomoc Otwórz bilet pomocy technicznej za pośrednictwem portalu Azure.

## <a name="configure-the-key-authorization-policy"></a>Skonfiguruj zasady autoryzacji klucza
Aby skonfigurować zasady autoryzacji klucza, wybierz **ochrony zawartości** strony.

Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Zasady autoryzacji klucza zawartości mogą mieć otwarty, tokenu lub ograniczenia autoryzacji adresów IP. (IP można skonfigurować z REST lub zestawu .NET SDK).

### <a name="open-restriction"></a>Ograniczenie otwarte
Otwórz ograniczeń oznacza, że system dostarcza klucza do każdego, kto wysyła żądanie klucza. To ograniczenie może być przydatna do celów testowych.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Ograniczenia tokenu
Aby wybrać zasadzie ograniczenia tokenu, wybierz **TOKENU** przycisku.

Zasadzie ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokenów w tokenie proste sieci web ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) i formatuje tokenu Web JSON (JWT). Aby uzyskać więcej informacji, zobacz [uwierzytelniania JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Usługa Media Services nie dostarcza usługi STS. Można tworzyć niestandardowe STS do wystawiania tokenów. Usługa tokenu Zabezpieczającego musi być skonfigurowana do utworzenia tokenu podpisany z określonego klucza i problem oświadczenia określony w konfiguracji ograniczenia tokenu. Jeśli token jest prawidłowy i oświadczenia w tokenie pasują do klucz zawartości, usługa Media Services klucza dostawy zwraca klucz szyfrowania do klienta.

Po skonfigurowaniu zasad ograniczonej token, należy określić klucz podstawowy weryfikacji, wystawcy i parametry odbiorców. Klucz podstawowy weryfikacji zawiera klucz, który został podpisany token, z. Wystawca jest usługa tokenu Zabezpieczającego, które wystawia token. Odbiorców (nazywane również zakres) opisano celem tokenu lub zasobu tokenu zezwala na dostęp do. Usługa Media Services klucza dostawy weryfikuje, czy te wartości w tokenie pasują do wartości w szablonie.

### <a name="playready"></a>PlayReady
W przypadku ochrony zawartości przy użyciu PlayReady, jednym z czynników, które należy określić w zasadach autoryzacji jest ciąg XML, który definiuje szablon licencji PlayReady. Domyślnie jest ustawiona następujące zasady:

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
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

Możesz wybrać **zaimportować pliku xml zasady** przycisk i podaj inny kod XML, który jest zgodny ze schematem XML zdefiniowane w [omówienie szablon licencji PlayReady usług Media](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

