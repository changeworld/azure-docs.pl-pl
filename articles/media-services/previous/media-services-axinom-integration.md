---
title: Korzystanie z Axinom w celu dostarczania licencji Widevine do usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Azure Media Services (AMS) umożliwia dostarczanie dynamicznie szyfrowanych przez usługi AMS przy użyciu technologii PlayReady i Widevine protokołów DRM strumienia. Licencja PlayReady pochodzi z serwera licencji PlayReady usługi Media Services i licencji Widevine są dostarczane przez serwer licencji Axinom.
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
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 6714beae690e23c686fc08b88e93044ae3901c89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61245046"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Korzystanie z Axinom w celu dostarczania licencji Widevine do usługi Azure Media Services 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Omówienie
Usługa Azure Media Services (AMS) został dodany ochrony dynamiczne Google Widevine (zobacz [blog firmy Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) Aby uzyskać szczegółowe informacje). Ponadto usługi Azure Media Player (AMP) dodano również obsługę Widevine (zobacz [dokumentu AMP](https://amp.azure.net/libs/amp/latest/docs/) Aby uzyskać szczegółowe informacje). Jest to główna zakończenia w przesyłania strumieniowego DASH zawartość chroniona przez CENC przy użyciu wielu-native-DRM (PlayReady i Widevine) w nowoczesnych przeglądarkach, wyposażone w MSE i EME.

Począwszy od Media Services .NET SDK w wersji 3.5.2 usługi Media Services umożliwia konfigurowanie szablonu licencji Widevine i Uzyskaj licencje usługi widevine:. Można również użyć następujących partnerów usługi AMS aby łatwiej dostarczać licencje Widevine: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

W tym artykule opisano integracją i testowaniem zarządza Axinom serwera licencji Widevine. W szczególności obejmuje:  

* Konfigurowanie dynamicznego szyfrowania Common Encryption przy użyciu technologii multi-DRM (PlayReady i Widevine) przy użyciu odpowiedniego adresów URL pozyskiwania licencji;
* Generowanie tokenu JWT w celu spełnienia wymagań dotyczących licencji serwera;
* Tworzenie aplikacji usługi Azure Media Player obsługuje nabycie licencji przy użyciu uwierzytelniania tokenu JWT;

Całego systemu i przepływ klucz zawartości klucza Identyfikatora, klucza inicjatora, JTW token i jego oświadczeń można najlepiej opisać za pomocą poniższym diagramie:

![DASH i CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Do konfigurowania ochrony dynamiczne i zasady dostarczania kluczy, zobacz blog Mingfei firmy: [Konfigurowanie tworzenia pakietów Widevine przy użyciu usługi Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Możesz skonfigurować dynamiczne ochronę CENC przy użyciu technologii multi-DRM dla DASH przesyłania strumieniowego o z następujących czynności:

1. Objętych ochroną PlayReady i Microsoft Edge i IE11, które mogą mieć ograniczenia tokenu autoryzacji. Zasadzie ograniczenia tokenu musi towarzyszyć token wystawiony przez Secure Token Service (STS), takich jak Azure Active Directory
2. Widevine ochrony dla programu Chrome, jego może wymagać uwierzytelniania tokenu przy użyciu tokenu wystawionego przez inną usługę STS. 

Zobacz [generowania tokenów JWT](media-services-axinom-integration.md#jwt-token-generation) dlaczego usługi Azure Active Directory nie można użyć jako usługę STS serwera licencji Widevine przez Axinom w sekcji.

### <a name="considerations"></a>Zagadnienia do rozważenia
1. Należy użyć Axinom określony klucza inicjatora (8888000000000000000000000000000000000000) i usługi wygenerowany lub wybranego klucza identyfikator, aby wygenerować klucz zawartości konfigurowania usługi dostarczania kluczy. Serwer licencji Axinom wystawia wszystkie licencje zawierające kluczy zawartości, w oparciu o tej samej inicjatora klucza nadaje się do testowania i produkcji.
2. Adres URL pozyskiwania licencji Widevine do testowania: [ https://drm-widevine-licensing.axtest.net/AcquireLicense ](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP i HTTS są dozwolone.

## <a name="azure-media-player-preparation"></a>Usługa Azure Media Player przygotowania
AMP v1.4.0 obsługuje odtwarzanie zawartości usługi AMS, dynamicznie spakowanego za pomocą PlayReady i Widevine DRM.
Jeśli serwer licencji Widevine nie wymaga tokenu uwierzytelniania, nie ma nic dodatkową, należy wykonać, aby przetestować DASH zawartość chroniona przez Widevine. Na przykład zespół AMP zapewnia prosty [przykładowe](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), gdzie można go było wyświetlić pracy w programach Microsoft Edge i IE11 za pomocą usług PlayReady i Chrome przy użyciu metody Widevine.
Serwer licencji Widevine, które są dostarczane przez Axinom wymaga uwierzytelnienia tokenu JWT. JWT token musi można przesłać z żądaniem licencji za pośrednictwem nagłówek HTTP "X-AxDRM-Message". W tym celu należy dodać następujący kod javascript na stronie sieci web hostingu AMP przed ustawieniem źródła:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Pozostała część kodzie AMP jest standardowy AMP API tak jak w dokumencie AMP [tutaj](https://amp.azure.net/libs/amp/latest/docs/).

Powyższe javascript dla nagłówka autoryzacji niestandardowe ustawienie jest nadal krótkoterminowej podejście przed udostępnieniem oficjalne długoterminowego podejścia w AMP.

## <a name="jwt-token-generation"></a>Generowanie tokenów JWT
Serwer licencji Axinom Widevine do testowania wymaga uwierzytelnienia tokenu JWT. Ponadto jest jedno z oświadczeń tokenu JWT typu obiektu złożonego zamiast typów danych pierwotnych.

Niestety usługa Azure AD może wystawiać tokeny JWT z typami pierwotnymi. Podobnie interfejs API programu .NET Framework (Klasa System.IdentityModel.Tokens.SecurityTokenHandler i JwtPayload) pozwala na tylko obiektu złożonego typu danych wejściowych jako oświadczenia. Jednak oświadczenia nadal są serializowane jako ciąg. W związku z tym firma Microsoft nie może użyć dwóch podczas generowania tokenu JWT dla żądania licencji Widevine.

John Sheehan [pakietu JWT NuGet](https://www.nuget.org/packages/JWT) spełnia wymagania, dzięki czemu będziemy używać tego pakietu NuGet.

Poniżej znajduje się kod do generowania tokenu JWT roszczeń wymagane zgodnie z wymogami serwera licencji Axinom Widevine do testowania:

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
1. Mimo że wymaga usługi dostarczania licencji PlayReady usługi AMS "Bearer =" poprzedzających token uwierzytelniania, serwer licencji Axinom Widevine nie używa ich.
2. Klucz komunikacji Axinom jest używany jako klucz podpisywania. Klucz jest ciąg szesnastkowy, jednak ją muszą być traktowane jako serię bajtów nie ciągu podczas kodowania. Jest to osiągane przez metodę ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Trwa pobieranie identyfikator klucza
Być może zauważono, że w kodzie podczas generowania tokenu JWT token, key wymagany jest identyfikator. Ponieważ klucz tokenu JWT token musi być gotowy, zanim player ładowania AMP Identyfikatora musi można pobrać w celu wygenerowania tokenu JWT.

Oczywiście istnieje wiele sposobów, aby uzyskać przytrzymaj klawisz identyfikatora. Na przykład jeden może przechowywać Identyfikatora klucza wraz z zawartością metadanych w bazie danych. Lub można pobrać Identyfikatora z MPD DASH (opis nośnika prezentacji) pliku klucza. Poniższy kod jest dla niego.

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
Za pomocą najnowszy dodatek Widevine obsługi zarówno w przypadku usługi Azure Media Services Content Protection, jak i usługi Azure Media Player jesteśmy w stanie wdrożyć przesyłania strumieniowego DASH + wielu-native-DRM (PlayReady i Widevine) przy użyciu zarówno usługi licencji PlayReady usługi AMS i Widevine licencji Serwer z Axinom dla następujących nowoczesnych przeglądarek:

* Chrome
* Przeglądarka Microsoft Edge w systemie Windows 10
* IE 11 na Windows 8.1 i Windows 10
* Firefox (wersja klasyczna) i Safari dla komputerów Mac (nie z systemem iOS) są również obsługiwane za pośrednictwem programu Silverlight i tego samego adresu URL za pomocą usługi Azure Media Player

Następujące parametry są wymagane na serwerze licencji Axinom Widevine wykorzystaniem mini rozwiązania. Z wyjątkiem klucza Identyfikatora, pozostałe parametry są dostarczane przez Axinom w oparciu o ich konfiguracji serwera Widevine.

| Parametr | Jak są używane |
| --- | --- |
| Identyfikator klucza komunikacji |Może być dołączony jako wartość oświadczenia "com_key_id" w JWT token (zobacz [to](media-services-axinom-integration.md#jwt-token-generation) sekcji). |
| Klucz komunikacji |Musi być używany jako klucz podpisywania tokenu JWT (zobacz [to](media-services-axinom-integration.md#jwt-token-generation) sekcji). |
| Inicjator klucza |Może służyć do generowania klucza zawartości przy użyciu dowolnej podanej zawartości Identyfikatora klucza (zobacz [to](media-services-axinom-integration.md#content-protection) sekcji). |
| Adres URL pozyskiwania licencji Widevine |Należy użyć podczas konfigurowania zasad dostarczania elementów zawartości dla strumienia DASH (zobacz [to](media-services-axinom-integration.md#content-protection) sekcji). |
| Identyfikator klucza zawartości |Musi być dołączane jako część wartości oświadczeń wiadomości uprawnienia tokenu JWT (zobacz [to](media-services-axinom-integration.md#jwt-token-generation) sekcji). |

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Potwierdzenia
Chcielibyśmy potwierdzić poniższe osób, które przyczyniły się do tworzenia tego dokumentu: Kristjan Jõgi z Axinom Mingfei Yan i Rajput Amitowi.

