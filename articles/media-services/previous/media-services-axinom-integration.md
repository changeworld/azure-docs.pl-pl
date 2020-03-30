---
title: Używanie usługi Axinom do dostarczania licencji Widevine do usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak można użyć usługi Azure Media Services (AMS) do dostarczania strumienia, który jest dynamicznie szyfrowany przez usługę AMS za pomocą technologii DrM PlayReady i Widevine. Licencja PlayReady pochodzi z serwera licencji Media Services PlayReady, a licencja Widevine jest dostarczana przez serwer licencji Axinom.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 2ec3276b9b02c29b80d46e5fd31298c909857182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197168"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Korzystanie z Axinom w celu dostarczania licencji Widevine do usługi Azure Media Services 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Omówienie
Usługa Azure Media Services (AMS) dodała dynamiczną ochronę Google Widevine (szczegółowe informacje można znaleźć w [blogu Mingfei).](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) Ponadto usługa Azure Media Player (AMP) dodała również obsługę widevine (szczegółowe informacje można znaleźć w [dokumencie AMP).](https://amp.azure.net/libs/amp/latest/docs/) Jest to duże osiągnięcie w streamingu treści DASH chronionych przez CENC z multi-native-DRM (PlayReady i Widevine) w nowoczesnych przeglądarkach wyposażonych w MSE i EME.

Począwszy od programu Media Services .NET SDK w wersji 3.5.2, program Media Services umożliwia skonfigurowanie szablonu licencji Widevine i uzyskanie licencji Widevine. Licencje Widevine są dostępne również u następujących partnerów usługi AMS: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) i [castLabs](https://castlabs.com/company/partners/azure/).

W tym artykule opisano sposób integracji i testowania serwera licencji Widevine zarządzanego przez axinom. W szczególności obejmuje ona:  

* Konfigurowanie dynamicznego szyfrowania wspólnego z wieloma drm (PlayReady i Widevine) z odpowiednimi adresami URL nabycia licencji;
* Generowanie tokenu JWT w celu spełnienia wymagań serwera licencji;
* Tworzenie aplikacji azure media player, która obsługuje pozyskiwanie licencji za pomocą uwierzytelniania tokenu JWT;

Kompletny system i przepływ klucza zawartości, identyfikator klucza, klucz siewny, token JTW i jego oświadczenia można najlepiej opisać na poniższym diagramie:

![DASH i CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Aby dowiedzieć się, jak skonfigurować zasady ochrony dynamicznej i dostarczania kluczy, zobacz blog Mingfei: [Jak skonfigurować opakowanie Widevine za pomocą usługi Azure Media Services.](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

Można skonfigurować dynamiczną ochronę CENC z multi-DRM dla strumieniowania DASH, mając obie następujące właściwości:

1. Ochrona PlayReady dla microsoft edge i IE11, które mogą mieć ograniczenie autoryzacji tokenu. Zasady z ograniczeniami tokenów muszą być dołączona token wystawiony przez usługę bezpiecznego tokenu (STS), taką jak usługa Azure Active Directory;
2. Ochrona Widevine dla Chrome, może wymagać uwierzytelniania tokenu za pomocą tokenu wydanego przez inny STS. 

Zobacz sekcję [Generowanie tokenów JWT,](media-services-axinom-integration.md#jwt-token-generation) dlaczego usługa Azure Active Directory nie może być używana jako usługa STS dla serwera licencji Widevine firmy Axinom.

### <a name="considerations"></a>Zagadnienia do rozważenia
1. Aby wygenerować klucz zawartości do konfigurowania usługi dostarczania kluczy, należy użyć pliku siewnego określonego przez system Axinom (8888000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 Serwer licencji Axinom wystawia wszystkie licencje zawierające klucze zawartości oparte na tym samym pliku danych źródłowych kluczy, który jest ważny zarówno w przypadku testowania, jak i produkcji.
2. Adres URL nabycia licencji Widevine do testowania: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). Dozwolone są zarówno protokół HTTP, jak i HTTS.

## <a name="azure-media-player-preparation"></a>Przygotowanie programu Azure Media Player
AMP v1.4.0 obsługuje odtwarzanie zawartości AMS, która jest dynamicznie pakowany zarówno z PlayReady i Widevine DRM.
Jeśli serwer licencji Widevine nie wymaga uwierzytelniania tokenu, nie ma nic dodatkowego, co należy zrobić, aby przetestować zawartość DASH chronioną przez Widevine. Na przykład zespół AMP udostępnia prosty [przykład,](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)w którym można zobaczyć, że działa w Microsoft Edge i IE11 z PlayReady i Chrome z Widevine.
Serwer licencji Widevine dostarczony przez axinom wymaga uwierzytelniania tokenu JWT. Token JWT musi zostać przesłany z żądaniem licencji za pośrednictwem nagłówka HTTP "X-AxDRM-Message". W tym celu należy dodać następujący javascript na stronie internetowej hosta AMP przed ustawieniem źródła:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Reszta kodu AMP to standardowy interfejs API AMP, jak w dokumencie [AMP.](https://amp.azure.net/libs/amp/latest/docs/)

Powyższy javascript do ustawiania niestandardowego nagłówka autoryzacji jest nadal krótkoterminowym podejściem przed wydaniem oficjalnego długoterminowego podejścia w AMP.

## <a name="jwt-token-generation"></a>Generowanie tokenów JWT
Serwer licencji Axinom Widevine do testowania wymaga uwierzytelniania tokenu JWT. Ponadto jednym z oświadczeń w tokenie JWT jest typu obiektu złożonego zamiast pierwotnego typu danych.

Niestety usługa Azure AD może wydawać tylko tokeny JWT z typami pierwotnymi. Podobnie .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler i JwtPayload) umożliwia wprowadzanie złożonego typu obiektu jako oświadczeń. Jednak oświadczenia są nadal serializowane jako ciąg. W związku z tym nie możemy użyć żadnego z dwóch do generowania tokenu JWT dla żądania licencji Widevine.

[Pakiet JWT NuGet](https://www.nuget.org/packages/JWT) Johna Sheehana spełnia potrzeby, więc będziemy używać tego pakietu NuGet.

Poniżej znajduje się kod do generowania tokenu JWT z wymaganymi oświadczeniami wymaganymi przez serwer licencji Axinom Widevine do testowania:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Serwer licencji Axinom Widevine

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Zagadnienia do rozważenia
1. Mimo że usługa dostarczania licencji AMS PlayReady wymaga "Bearer=" poprzedzającego token uwierzytelniania, serwer licencji Axinom Widevine nie używa go.
2. Klucz komunikacji Axinom jest używany jako klucz podpisywania. Klucz jest ciągiem szesnastkowym, jednak musi być traktowany jako seria bajtów, a nie ciąg podczas kodowania. Osiąga się to za pomocą metody ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Pobieranie identyfikatora klucza
Być może zauważyłeś, że w kodzie do generowania tokenu JWT wymagany jest identyfikator klucza. Ponieważ token JWT musi być gotowy przed załadowaniem odtwarzacza AMP, identyfikator klucza musi zostać pobrany, aby wygenerować token JWT.

Oczywiście istnieje wiele sposobów na zdobycie identyfikatora klucza. Na przykład można przechowywać identyfikator klucza wraz z metadanymi zawartości w bazie danych. Możesz też pobrać identyfikator klucza z pliku DASH MPD (Media Presentation Description). Poniższy kod dotyczy tego ostatniego.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Podsumowanie

Dzięki najnowszemu dodaniu obsługi Widevine zarówno w usłudze Azure Media Services Content Protection, jak i usłudze Azure Media Player, jesteśmy w stanie zaimplementować przesyłanie strumieniowe dash + multi-native-DRM (PlayReady + Widevine) zarówno z usługą licencji PlayReady w ams, jak i licencją Widevine serwera firmy Axinom dla następujących nowoczesnych przeglądarek:

* Chrome
* Microsoft Edge w systemie Windows 10
* IE 11 w systemach Windows 8.1 i Windows 10
* Zarówno Firefox (Desktop) i Safari na Mac (nie iOS) są również obsługiwane przez Silverlight i ten sam adres URL z azure media player

W mini-rozwiązaniu wymagane są następujące parametry wykorzystujące serwer licencji Axinom Widevine. Z wyjątkiem identyfikatora klucza, pozostałe parametry są dostarczane przez Axinom na podstawie ich konfiguracji serwera Widevine.

| Parametr | Jak jest on używany |
| --- | --- |
| Identyfikator klucza komunikacji |Musi być uwzględniony jako wartość oświadczenia "com_key_id" w tokenie JWT (zobacz [tę](media-services-axinom-integration.md#jwt-token-generation) sekcję). |
| Klucz komunikacji |Musi być używany jako klucz podpisywania tokenu JWT (zobacz [tę](media-services-axinom-integration.md#jwt-token-generation) sekcję). |
| Materiał siewny klucza |Musi być używany do generowania klucza zawartości z dowolnym identyfikatorem klucza zawartości (zobacz [tę](media-services-axinom-integration.md#content-protection) sekcję). |
| Adres URL nabycia licencji Widevine |Musi być używany do konfigurowania zasad dostarczania zasobów dla przesyłania strumieniowego DASH (zobacz [tę](media-services-axinom-integration.md#content-protection) sekcję). |
| Identyfikator klucza zawartości |Musi być uwzględniony jako część wartości żądania komunikatu uprawnień tokenu JWT (zobacz [tę](media-services-axinom-integration.md#jwt-token-generation) sekcję). |

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Podziękowania
Pragniemy podziękować następującym osobom, które przyczyniły się do stworzenia tego dokumentu: Kristjan Jõgi z Axinom, Mingfei Yan i Amit Rajput.

