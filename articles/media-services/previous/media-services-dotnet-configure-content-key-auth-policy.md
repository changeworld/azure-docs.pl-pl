---
title: Skonfiguruj zasady autoryzacji klucza zawartości przy użyciu zestawu .NET SDK Media Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady autoryzacji klucza zawartości przy użyciu zestawu .NET SDK usługi multimediów.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: cfowler
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: ed919d8ac9bf88e8a9385930cafaf7b2bc4d2143
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788816"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Szyfrowania dynamicznego: Skonfiguruj zasady autoryzacji klucza zawartości
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Przegląd
 Azure Media Services umożliwia dostarczanie strumieni MPEG-DASH, Smooth Streaming i HTTP Live Streaming (HLS) chronionych z Standard AES (Advanced Encryption) przy użyciu kluczy szyfrowania 128-bitowego lub [PlayReady zarządzania prawami cyfrowymi (DRM) ](https://www.microsoft.com/playready/overview/). Z usługi Media Services również można dostarczać strumienie DASH zaszyfrowane za pomocą Widevine DRM. Obie usługi, PlayReady i Widevine, szyfrują dane zgodnie ze specyfikacją Common Encryption (ISO/IEC CENC 23001-7).

Usługa Media Services udostępnia również usługi dostarczania klucz/licencji, z których klienci mogą pobierać kluczy AES lub licencje PlayReady/Widevine, aby odtworzyć zaszyfrowaną zawartość.

Jeśli chcesz, aby usługi Media Services, aby zaszyfrować element zawartości, należy do skojarzenia klucza szyfrowania (CommonEncryption lub EnvelopeEncryption) z elementu zawartości. Aby uzyskać więcej informacji, zobacz [utworzyć ContentKeys z platformą .NET](media-services-dotnet-create-contentkey.md). Należy również skonfigurować zasady autoryzacji klucza (zgodnie z opisem w tym artykule).

Strumień zleconą przez odtwarzacz usługi Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania AES lub DRM. Aby odszyfrować strumienia, odtwarzacza żądań klucz z usługi dostarczania klucza. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Zasady autoryzacji klucza zawartości mogą mieć jeden lub więcej ograniczeń. Dostępne opcje to ograniczenie otwarte lub tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokenów w tokenie proste sieci web ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) format i tokenu Web JSON ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) format.

Usługa Media Services nie dostarcza usługi STS. Można utworzyć niestandardowe usługi STS lub użyj usługi kontroli dostępu platformy Azure do wydawania tokenów. Usługa tokenu Zabezpieczającego musi być skonfigurowana do utworzenia tokenu podpisany określone oświadczenia klucza i problem, określonych w konfiguracji ograniczenia tokenu (zgodnie z opisem w tym artykule). Jeśli token jest prawidłowy i oświadczenia w tokenie pasują do klucz zawartości, usługa Media Services klucza dostawy zwraca klucz szyfrowania do klienta.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Uwierzytelniania tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrowanie aplikacji usługi Azure Media Services OWIN MVC z usługą Azure Active Directory i ograniczenie klucza dostarczania zawartości na podstawie oświadczeń JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Zagadnienia do rozważenia
* Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z dynamicznego tworzenia pakietów i dynamicznego szyfrowania, punkt końcowy przesyłania strumieniowego musi być w stanie "Uruchomiona". 
* Zawartości musi zawierać zestaw o adaptacyjnej szybkości bitowej MP4s lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów. Aby uzyskać więcej informacji, zobacz [kodowanie elementu zawartości](media-services-encode-asset.md).
* Przekazywanie i kodowanie zasobów przy użyciu opcji AssetCreationOptions.StorageEncrypted.
* Jeśli planowane jest wiele kluczy zawartości, które wymagają taką samą konfigurację zasad, zaleca się tworzenie zasad autoryzacji pojedynczego i użyć go ponownie wiele kluczy zawartości.
* Usługa klucza dostawy buforuje ContentKeyAuthorizationPolicy i jego obiektów pokrewnych (opcje zasad i ograniczeń) przez 15 minut. Można utworzyć ContentKeyAuthorizationPolicy i określić ograniczenia tokenu, przetestować go, a następnie zaktualizuj zasady z otwartych ograniczenia. Ten proces trwa około 15 minut przed przełączniki zasad Otwórz wersję zasad.
* W przypadku dodania lub zaktualizowania zasad dostarczania elementów zawartości należy usunąć skojarzony lokalizator (jeśli istnieje) i utworzyć nowy.
* Obecnie nie można zaszyfrować pobierania progresywnego.
* Usługi Media Services punktu końcowego przesyłania strumieniowego ustawia wartość nagłówka "Access-Control-Allow-Origin" mechanizmu CORS w odpowiedzi dotyczące stanu wstępnego jako symbolu wieloznacznego "\*". Ta wartość działa prawidłowo w przypadku większości odtwarzaczy, w tym Azure Media Player, Roku i JWPlayer i inne. Jednak niektóre odtwarzacze, które używają dashjs nie działa, ponieważ wartość "Włącz" Tryb poświadczeń XMLHttpRequest w ich dashjs nie zezwala na symbol wieloznaczny "\*" jako wartości "Access-Control-Allow-Origin". Jako obejście tego ograniczenia w dashjs Jeśli host klienta z pojedynczą domenę usługi Media Services można określić tej domeny w nagłówku odpowiedzi dotyczące stanu wstępnego. Aby uzyskać pomoc Otwórz bilet pomocy technicznej za pośrednictwem portalu Azure.

## <a name="aes-128-dynamic-encryption"></a>Dynamicznego szyfrowania AES-128
### <a name="open-restriction"></a>Ograniczenie otwarte
Otwórz ograniczeń oznacza, że system dostarcza klucza do każdego, kto wysyła żądanie klucza. To ograniczenie może być przydatna do celów testowych.

Poniższy przykład tworzy zasady autoryzacji otwarte i dodaje go do klucz zawartości:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Ograniczenia tokenu
W tej sekcji opisano sposób tworzenia zasady autoryzacji klucza zawartości i skojarz ją z klucz zawartości. Zasady autoryzacji opisano, jakie wymagania autoryzacji muszą być spełnione do określenia, czy użytkownik jest autoryzowany do odbierania klucza. Na przykład listę kluczy weryfikacji zawiera klucz, który został podpisany token, z?

Aby skonfigurować opcję ograniczenia tokenu, należy użyć XML opisujący wymagań autoryzacji tokenu. Plik XML konfiguracji ograniczenia tokenu musi być zgodna z następującego schematu XML:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Po skonfigurowaniu zasad ograniczonej token, należy określić klucz podstawowy weryfikacji, wystawcy i parametry odbiorców. Klucz podstawowy weryfikacji zawiera klucz, który został podpisany token, z. Wystawca jest usługa tokenu Zabezpieczającego, które wystawia token. Odbiorców (nazywane również zakres) opisano celem tokenu lub zasobu tokenu zezwala na dostęp do. Usługa Media Services klucza dostawy weryfikuje, czy te wartości w tokenie pasują do wartości w szablonie.

Gdy używasz SDK usługi Media Services dla platformy .NET, można użyć klasy TokenRestrictionTemplate do wygenerowania tokenu ograniczeń.
Poniższy przykład tworzy zasady autoryzacji z ograniczenia tokenu. W tym przykładzie klient musi przedstawić token, który zawiera klucz podpisujący (VerificationKey), wystawcy tokenów i oświadczeń wymagane.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Test tokenu
Aby uzyskać token testu w oparciu ograniczenia tokenu użyto zasad autoryzacji klucza, wykonaj następujące czynności:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>Szyfrowania dynamicznego PlayReady
Usługi Media Services umożliwiają skonfigurowanie uprawnień i ograniczeń, które chcesz wymusić, gdy użytkownik próbuje odtworzyć środowiska uruchomieniowego PlayReady DRM chronionej zawartości. 

W przypadku ochrony zawartości przy użyciu PlayReady, jest jednym z elementów, należy określić w zasadach autoryzacji ciągu XML, który definiuje [szablon licencji PlayReady](media-services-playready-license-template-overview.md). W zestawie SDK usługi multimediów dla platformy .NET klasy PlayReadyLicenseResponseTemplate i PlayReadyLicenseTemplate pomagają zdefiniować szablon licencji PlayReady.

Aby dowiedzieć się, jak szyfrowanie zawartości przy użyciu PlayReady i Widevine, zobacz [Użyj PlayReady i Widevine dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Ograniczenie otwarte
Otwórz ograniczeń oznacza, że system dostarcza klucza do każdego, kto wysyła żądanie klucza. To ograniczenie może być przydatna do celów testowych.

Poniższy przykład tworzy zasady autoryzacji otwarte i dodaje go do klucz zawartości:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Ograniczenia tokenu
Aby skonfigurować opcję ograniczenia tokenu, należy użyć XML opisujący wymagań autoryzacji tokenu. Konfiguracja ograniczenia tokenu XML muszą być zgodne ze schematem XML pokazano "[schematu ograniczenia tokenu](#token-restriction-schema)" sekcji.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Aby uzyskać token testu w oparciu ograniczenia tokenu użyto zasad autoryzacji klucza, zobacz "[token testu](#test-token)" sekcji. 

## <a id="types"></a>Typy używane podczas definiowania ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>tokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy skonfigurowano zasady autoryzacji klucza zawartości, zobacz [Skonfiguruj zasady dostarczania zasobu](media-services-dotnet-configure-asset-delivery-policy.md).

