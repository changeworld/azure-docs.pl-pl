---
title: Konfigurowanie zasad autoryzacji klucza zawartości przy użyciu narzędzia Media Services .NET SDK | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady autoryzacji klucza zawartości przy użyciu narzędzia Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 58d52cd194ca4391c61f2477189984273df1198a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251208"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurowanie zasad autoryzacji klucza zawartości

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Omówienie
 Za pomocą usługi Azure Media Services można dostarczać strumienie MPEG-DASH, Smooth Streaming i HTTP Live Streaming (HLS) chronione za pomocą zaawansowanego standardu szyfrowania (AES) przy użyciu 128-bitowych kluczy szyfrowania lub [zarządzania prawami cyfrowymi PlayReady (DRM).](https://www.microsoft.com/playready/overview/) Za pomocą usługi Media Services można również dostarczać strumienie DASH zaszyfrowane za pomocą technologii Widevine DRM. Obie usługi, PlayReady i Widevine, szyfrują dane zgodnie ze specyfikacją Common Encryption (ISO/IEC CENC 23001-7).

Usługa Media Services zapewnia również usługę dostarczania kluczy/licencji, z której klienci mogą uzyskać klucze AES lub licencje PlayReady/Widevine do odtwarzania zaszyfrowanej zawartości.

Jeśli chcesz, aby usługa Media Services szyfrował zasób, musisz skojarzyć klucz szyfrowania (CommonEncryption lub EnvelopeEncryption) z zasobem. Aby uzyskać więcej informacji, zobacz [Tworzenie kluczy zawartości za pomocą pliku .NET](media-services-dotnet-create-contentkey.md). Należy również skonfigurować zasady autoryzacji dla klucza (zgodnie z opisem w tym artykule).

Gdy odtwarzacz zażąda strumienia, usługa Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania AES lub DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa ocenia zasady autoryzacji określone dla klucza.

Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Zasady autoryzacji klucza zawartości mogą mieć co najmniej jedno ograniczenie autoryzacji. Opcje są otwarte lub ograniczenie tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługi Media Services obsługują tokeny w formacie prostego tokenu internetowego[(SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)i formatu JSON Web Token[(JWT).](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)

Usługi Media Services nie zapewniają usługi STS. Można utworzyć niestandardowy sts lub użyć usługi Azure Access Control Service do wystawiania tokenów. Sts musi być skonfigurowany do tworzenia tokenu podpisanego przyconym określonym kluczem i oświadczeń problem, które zostały określone w konfiguracji ograniczenia tokenu (zgodnie z opisem w tym artykule). Jeśli token jest prawidłowy, a oświadczenia w tokenie są zgodne z tymi skonfigurowanym dla klucza zawartości, usługa dostarczania kluczy usługi Media Services zwraca klucz szyfrowania do klienta.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Uwierzytelnianie tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integracja aplikacji opartej na MVC usługi Azure Media Services z usługą Azure Active Directory i ograniczanie dostarczania kluczy zawartości na podstawie oświadczeń JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Niektóre kwestie mają zastosowanie
* Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z dynamicznego pakowania i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego musi być w stanie "Uruchomiony". 
* Zasób musi zawierać zestaw adaptacyjnych plików MP4 o szybkości transmisji bitów lub adaptacyjnych plików płynnego przesyłania strumieniowego o szybkości transmisji bitów. Aby uzyskać więcej informacji, zobacz [Kodowanie zasobu](media-services-encode-asset.md).
* Przekaż i zakoduj zasoby przy użyciu opcji AssetCreationOptions.StorageEncrypted.
* Jeśli planujesz mieć wiele kluczy zawartości, które wymagają tej samej konfiguracji zasad, zaleca się utworzenie jednej zasady autoryzacji i użyć go ponownie z wieloma kluczami zawartości.
* Usługa dostarczania kluczy buforuje ContentKeyAuthorizationPolicy i jej powiązane obiekty (opcje zasad i ograniczenia) przez 15 minut. Można utworzyć ContentKeyAuthorizationPolicy i określić, aby użyć ograniczenia tokenu, przetestować go, a następnie zaktualizować zasady do otwartego ograniczenia. Ten proces trwa około 15 minut, zanim zasady przełączy się do otwartej wersji zasad.
* W przypadku dodania lub zaktualizowania zasad dostarczania elementów zawartości należy usunąć skojarzony lokalizator (jeśli istnieje) i utworzyć nowy.
* Obecnie nie można szyfrować pobrań progresywnych.
* Punkt końcowy przesyłania strumieniowego usługi Media Services ustawia wartość nagłówka CORS "Access-Control-Allow-Origin" w odpowiedzi inspekcji wstępnej jako symbol wieloznaczny '\*. Ta wartość działa dobrze z większością graczy, w tym Azure Media Player, Roku i JWPlayer i innych. Jednak niektórzy gracze, którzy używają dashjs nie działają, ponieważ z trybem poświadczeń ustawionym na "include", XMLHttpRequest w ich dashjs nie zezwala na symbol wieloznaczny "\*jako wartość "Access-Control-Allow-Origin". Aby obejść to ograniczenie w dashjs, jeśli hostujesz klienta z jednej domeny, usługa Media Services może określić tę domenę w nagłówku odpowiedzi inspekcji wstępnej. Aby uzyskać pomoc, otwórz bilet pomocy technicznej za pośrednictwem witryny Azure portal.

## <a name="aes-128-dynamic-encryption"></a>Szyfrowanie dynamiczne AES-128
### <a name="open-restriction"></a>Otwórz ograniczenie
Otwarte ograniczenie oznacza, że system dostarcza klucz każdemu, kto zgłosi żądanie klucza. To ograniczenie może być przydatne do celów testowych.

Poniższy przykład tworzy zasady otwartej autoryzacji i dodaje go do klucza zawartości:
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

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Ograniczenie tokenu
W tej sekcji opisano sposób tworzenia zasad autoryzacji klucza zawartości i skojarzenia jej z kluczem zawartości. Zasady autoryzacji opisują wymagania dotyczące autoryzacji, aby ustalić, czy użytkownik jest upoważniony do odbierania klucza. Na przykład czy lista kluczy weryfikacji zawiera klucz, z jakim token został podpisany?

Aby skonfigurować opcję ograniczenia tokenu, należy użyć kodu XML do opisania wymagań autoryzacji tokenu. XML konfiguracji ograniczenia tokenu musi być zgodny z następującym schematem XML:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
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
Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacji, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, za pomocą którym token został podpisany. Wystawcą jest STS, który wystawia token. Grupa odbiorców (czasami nazywana zakresem) opisuje intencję tokenu lub zasobu, do który token autoryzuje dostęp. Usługa dostarczania kluczy usługi Media Services sprawdza, czy te wartości w tokenie są zgodne z wartościami w szablonie.

Korzystając z sdk usług multimedialnych dla platformy .NET, można użyć TokenRestrictionTemplate klasy do generowania tokenu ograniczenia.
Poniższy przykład tworzy zasady autoryzacji z ograniczeniem tokenu. W tym przykładzie klient musi przedstawić token, który zawiera klucz podpisywania (VerificationKey), wystawcy tokenu i wymagane oświadczenia.
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

        // Add ContentKeyAuthorizationPolicy to ContentKey
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
#### <a name="test-token"></a>Token testowy
Aby uzyskać token testowy na podstawie ograniczenia tokenu, który był używany dla zasad autoryzacji klucza, wykonaj następujące czynności:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>Szyfrowanie dynamiczne PlayReady
Za pomocą usługi Media Services można skonfigurować prawa i ograniczenia, które mają wymusić środowisko uruchomieniowe PlayReady DRM, gdy użytkownik próbuje odtworzyć zawartość chroniona. 

Podczas ochrony zawartości za pomocą programu PlayReady jedną z rzeczy, które należy określić w zasadach autoryzacji, jest ciąg XML definiujący [szablon licencji PlayReady](media-services-playready-license-template-overview.md). W programie Media Services SDK dla platformy .NET klasy PlayReadyLicenseResponseTemplate i PlayReadyLicenseTemplate ułatwiają definiowanie szablonu licencji PlayReady.

Aby dowiedzieć się, jak szyfrować zawartość za pomocą programów PlayReady i Widevine, zobacz Korzystanie z [dynamicznego szyfrowania dynamicznego PlayReady i/lub Widevine](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Otwórz ograniczenie
Otwarte ograniczenie oznacza, że system dostarcza klucz każdemu, kto zgłosi żądanie klucza. To ograniczenie może być przydatne do celów testowych.

Poniższy przykład tworzy zasady otwartej autoryzacji i dodaje go do klucza zawartości:

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

### <a name="token-restriction"></a>Ograniczenie tokenu
Aby skonfigurować opcję ograniczenia tokenu, należy użyć kodu XML do opisania wymagań autoryzacji tokenu. Konfiguracja ograniczenia tokenu XML musi być zgodna ze schematem XML pokazanym w sekcji "Schemat ograniczenia tokenu".

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

        // Add ContentKeyAuthorizationPolicy to ContentKey
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

Aby uzyskać token testowy na podstawie ograniczenia tokenu, który był używany dla zasad autoryzacji klucza, zobacz sekcję "[Token testu](#test-token)." 

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>Typy używane podczas definiowania contentkeyauthorizationPolicy
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a name="tokentype"></a><a id="TokenType"></a>Tokentype

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu zasad autoryzacji klucza zawartości zobacz [Konfigurowanie zasad dostarczania zasobów](media-services-dotnet-configure-asset-delivery-policy.md).

