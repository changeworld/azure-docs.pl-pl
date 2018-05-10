---
title: Na potrzeby dostarczania licencji Widevine do usługi Azure Media Services przy użyciu Axinom | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Azure Media Services (AMS) umożliwia dostarczanie dynamicznie szyfrowanych przez AMS z PlayReady i Widevine DRMs strumienia. Licencja PlayReady pochodzi z serwera licencji Media Services PlayReady i licencji Widevine jest dostarczany przez serwer licencji Axinom.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 0aaf0eea0414d234c9a24f707df5eed491a61c08
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Korzystanie z Axinom w celu dostarczania licencji Widevine do usługi Azure Media Services
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Przegląd
Azure Media Services (AMS) został dodany Google Widevine dynamiczną ochronę (zobacz [Mingfei w blogu](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) szczegółowe informacje). Ponadto usługi Azure Media Player (AMP) również została dodana obsługa Widevine (zobacz [dokumentu AMP](http://amp.azure.net/libs/amp/latest/docs/) szczegółowe informacje). W nowoczesnych przeglądarkach wyposażony MSE i EME jest głównych zakończenia w strumienia DASH zawartość chroniona przez CENC z kilku-native DRM (PlayReady i Widevine).

Począwszy od platformy .NET SDK usługi Media Services wersja 3.5.2 Media Services umożliwia skonfigurowanie szablonu licencji Widevine i uzyskać licencji Widevine. Licencje Widevine są dostępne również u następujących partnerów usługi AMS: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) i [castLabs](http://castlabs.com/company/partners/azure/).

W tym artykule opisano sposób integracji i przetestować serwer licencji Widevine zarządza Axinom. W szczególności obejmuje:  

* Konfigurowanie dynamicznego szyfrowania Common Encryption z wieloma DRM (PlayReady i Widevine) z odpowiednie adresy URL pozyskiwania licencji;
* Generowanie tokenu JWT w celu spełnienia wymagań dotyczących licencji serwera;
* Tworzenie aplikacji usługi Azure Media Player, która obsługuje nabycie licencji przy użyciu uwierzytelniania tokenu JWT;

Całego systemu i przepływ klucz zawartości klucz identyfikator, klucza inicjatora JTW token i jego oświadczeń może być najlepiej opisany przez poniższym diagramie:

![KRESKA i CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Do konfigurowania ochrony dynamiczne i zasady dostarczania klucza, zobacz Mingfei w blogu: [Konfigurowanie tworzenia pakietów Widevine w usłudze Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Można skonfigurować dynamiczne ochrony CENC z wieloma DRM DASH przesyłania strumieniowego o z następujących czynności:

1. Ochrona PlayReady MS Edge i IE11, mające ograniczenie tokenu autoryzacji. Zasadzie ograniczenia tokenu musi towarzyszyć token wystawiony przez Secure Token Service (STS), takich jak Azure Active Directory;
2. Widevine ochrony dla programu Chrome, ich wymaga tokenu uwierzytelniania z token wystawiony przez inną usługę STS. 

Zobacz [generowania tokenów JWT](media-services-axinom-integration.md#jwt-token-generation) sekcji dlaczego usługi Azure Active Directory nie można użyć jako tokenu Zabezpieczającego serwera licencji Widevine przez Axinom.

### <a name="considerations"></a>Zagadnienia do rozważenia
1. Należy użyć Axinom określony inicjatora klucza (8888000000000000000000000000000000000000) i z wygenerowany lub wybranego Identyfikatora do wygenerowania klucza zawartości do konfigurowania usługi dostarczania klucza klucza. Serwer licencji Axinom wystawia wszystkie licencje zawierający klucze zawartości oparte na tym samym inicjatora klucza jest prawidłowy dla testowania i produkcji.
2. Adres URL pozyskiwania licencji Widevine do testowania: [ https://drm-widevine-licensing.axtest.net/AcquireLicense ](https://drm-widevine-licensing.axtest.net/AcquireLicense). Protokół HTTP ani HTTS są dozwolone.

## <a name="azure-media-player-preparation"></a>Przygotowanie odtwarzacz multimediów Azure
AMP v1.4.0 obsługuje odtwarzanie AMS spakowanego dynamicznie z PlayReady i Widevine DRM.
Jeśli serwer licencji Widevine nie wymaga uwierzytelniania tokenu, nie ma dodatkowych, należy wykonać, aby przetestować DASH zawartość chroniona przez Widevine. Na przykład zespół AMP udostępnia prostą [próbki](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), w którym można obejrzeć go do pracy w Edge i IE11 za pomocą PlayReady i Chrome przy użyciu metody Widevine.
Serwer licencji Widevine dostarczonych przez Axinom wymaga uwierzytelnienia tokenu JWT. JWT token musi zostać przesłane z żądaniem licencji za pośrednictwem nagłówka HTTP "X-AxDRM-komunikat". W tym celu należy dodać następujący kod javascript hosting AMP przed ustawieniem źródło strony sieci web:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Pozostałe kodzie AMP to standardowa API AMP jak dokument AMP [tutaj](http://amp.azure.net/libs/amp/latest/docs/).

Powyżej javascript dla nagłówka autoryzacji niestandardowej ustawienie jest nadal krótkoterminowej podejście przed wydaniem oficjalnego długoterminowego podejścia w AMP.

## <a name="jwt-token-generation"></a>Generowanie tokenów JWT
Serwer licencji Axinom Widevine testowania wymaga uwierzytelnienia tokenu JWT. Ponadto jednym z oświadczeń w JWT token jest typu obiekt złożony zamiast typu danych pierwotnych.

Niestety usługi Azure AD może wystawiać tokeny JWT z typami pierwotnymi. Podobnie interfejsu API dla platformy .NET Framework (Klasa System.IdentityModel.Tokens.SecurityTokenHandler i JwtPayload) umożliwia tylko do wejściowego typu obiektu złożonego jako oświadczenia. Jednak oświadczenia nadal są serializowane jako ciąg. W związku z tym nie można użyć, dowolne dwa do generowania tokenu JWT dla żądania licencji Widevine.

Jan Sheehan [pakietu JWT NuGet](https://www.nuget.org/packages/JWT) zaspokoi potrzeby, dlatego firma Microsoft będzie używany ten pakiet NuGet.

Poniżej znajduje się kod generowania tokenu JWT z wymagane oświadczenia, co jest wymagane przez serwer licencji Axinom Widevine do testowania:

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

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Zagadnienia do rozważenia
1. Mimo że wymaga usługi dostarczania licencji AMS PlayReady "Bearer =" kolejnych tokenu uwierzytelniania serwera licencji Axinom Widevine nie używa ich.
2. Klucz komunikacji Axinom jest używany jako klucza podpisywania. Klucz jest ciąg znaków szesnastkowych, jednak ją muszą być traktowane jako serię bajtów nie ciągu podczas kodowania. Jest to osiągane przez metodę ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Pobieranie Identyfikatora klucza
Można zauważyć, że w kodzie generowania token JWT identyfikator tokenu, klucza jest wymagana. Ponieważ musi tokenu JWT jest gotowy, zanim player AMP ładowania klucza identyfikator musi być pobierane w celu wygenerowania tokenu JWT.

Oczywiście istnieje wiele sposobów uzyskania blokady klucza identyfikatora. Na przykład, co może przechowywać Identyfikatora klucza wraz z zawartością metadanych w bazie danych. Lub może pobrać Identyfikatora z MPD DASH (opis prezentacji nośnika) pliku klucza. Poniższy kod jest dla niego.

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
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
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
Z najnowszy dodatek Widevine obsługi zarówno w przypadku ochrony zawartości Azure Media Services, jak i usługi Azure Media Player możemy wdrożyć strumienia DASH + kilku-native DRM (PlayReady i Widevine) z usługą licencji PlayReady, zarówno w licencji AMS i Widevine Serwer z Axinom dla następujących nowoczesnymi przeglądarkami:

* Chrome
* Przeglądarka Microsoft Edge w systemie Windows 10
* IE 11 na Windows 8.1 i Windows 10
* Zarówno program Firefox (Desktop), jak i Safari dla komputerów Mac (nie iOS) są również obsługiwane za pośrednictwem programu Silverlight i do tego samego adresu URL usługi Azure Media Player

Następujące parametry są wymagane na serwerze licencji Axinom Widevine korzystanie z usług mini rozwiązania. Z wyjątkiem klucza identyfikator, pozostałe parametry są dostarczane przez Axinom na podstawie ich Widevine konfiguracji serwera.

| Parametr | Jak jest używany |
| --- | --- |
| Identyfikator klucza komunikacji |Musi być uwzględniony jako wartość oświadczenia "com_key_id" w JWT token (zobacz [to](media-services-axinom-integration.md#jwt-token-generation) sekcji). |
| Klucz komunikacji |Muszą być używane jako klucza podpisywania tokenu JWT (zobacz [to](media-services-axinom-integration.md#jwt-token-generation) sekcji). |
| Klucza inicjatora |Musi być używany do generowania klucza zawartości z zawartości danego Identyfikatora klucza (zobacz [to](media-services-axinom-integration.md#content-protection) sekcji). |
| Adres URL pozyskiwania licencji Widevine |Musi być używany w Konfigurowanie zasad dostarczania elementów zawartości dla strumienia DASH (zobacz [to](media-services-axinom-integration.md#content-protection) sekcji). |
| Identyfikator klucza zawartości |Musi być uwzględniony wartości oświadczenia komunikatów dotyczących tokenu JWT (zobacz [to](media-services-axinom-integration.md#jwt-token-generation) sekcji). |

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Potwierdzenia
Chcemy potwierdzić następujących osób, które przyczyniły się do tworzenia tego dokumentu: Kristjan Jõgi z Axinom, Mingfei Yan i Rajput Amitowi.

