---
title: Profili Technicalprofile | Dokumentacja firmy Microsoft
description: Określ element profili Technicalprofile zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 86f2a8fa11becdf24c0a10c0325893946a033c3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360133"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **profili Technicalprofile** element zawiera zbiór profilów Technical Preview obsługiwana przez dostawcę oświadczeń. Każdego dostawcy oświadczeń musi mieć co najmniej jeden profil techniczne określające punktów końcowych i protokoły wymagane do komunikowania się z dostawcą oświadczeń. Dostawcy oświadczeń mogą mieć wiele profilów Technical Preview.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**Profilu technicznego** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
|---------|---------|---------|
| Identyfikator | Yes | Unikatowy identyfikator profilu technicznego. Profil techniczny można się odwoływać przy użyciu tego identyfikatora, od innych elementów w pliku zasad. Na przykład **OrchestrationSteps** i **ValidationTechnicalProfile**. |

**Profilu technicznego** zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Domain | 0:1 | Nazwa domeny dla profilu technicznego. Na przykład jeśli profilu technicznego określa dostawcę tożsamości usługi Facebook, nazwa domeny jest Facebook.com. |
| Nazwa wyświetlana | 0:1 | Nazwa profilu technicznego, który może być widoczny dla użytkowników. |
| Opis | 0:1 | Opis profilu technicznego, który może być widoczny dla użytkowników. |
| Protokół | 0:1 | Protokół używany do komunikacji z drugiej strony. |
| Metadane | 0:1 | Kolekcja par klucz/wartość, które są wykorzystywane przez protokół komunikacji z punktem końcowym w toku transakcji. |
| InputTokenFormat | 0:1 | Format wejściowy tokenu. Możliwe wartości: `JSON`, `JWT`, `SAML11`, lub `SAML2`. `JWT` Wartość reprezentuje tokenu sieci Web JSON zgodnie z Specyfikacja IETF. `SAML11` Wartość reprezentuje token zabezpieczeń SAML 1.1, zgodnie z specyfikacją języka OASIS.  `SAML2` Wartość reprezentuje token zabezpieczeń SAML 2.0, zgodnie z specyfikacją języka OASIS. |
| OutputTokenFormat | 0:1 | Format tokenu danych wyjściowych. Możliwe wartości: `JSON`, `JWT`, `SAML11`, lub `SAML2`. |
| CryptographicKeys | 0:1 | Lista kluczy kryptograficznych, które są używane w profilu technicznym. |
| InputClaimsTransformations | 0:1 | Lista uprzednio zdefiniowany odwołania do przekształceń oświadczeń, które mają zostać wykonane przed wysłaniem żadnych oświadczeń na dostawcy oświadczeń lub jednostkę uzależnioną. |
| InputClaims | 0:1 | Lista uprzednio zdefiniowany odwołania do typów, które są wykonywane jako oświadczeń wejściowych w profilu technicznym. |
| PersistedClaims | 0:1 | Lista uprzednio zdefiniowany odwołania do typów, które są zachowywane przez dostawcę oświadczeń, które odnoszą się do profilu technicznego oświadczeń. |
| OutputClaims | 0:1 | Lista uprzednio zdefiniowany odwołania do typów, które są wykonywane jako dane wyjściowe w profilu technicznym oświadczeń. |
| OutputClaimsTransformations | 0:1 | Lista uprzednio zdefiniowany odwołania do przekształceń oświadczeń, które mają zostać wykonane po otrzymaniu oświadczenia od dostawcy oświadczeń. |
| ValidationTechnicalProfiles | 0: n | Lista odwołania do innych techniczne profilów, które korzysta z profilu technicznego do celów sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [profilu technicznego sprawdzania poprawności](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Steruje produkcji nazwę podmiotu w tokeny, których nazwa podmiotu jest określane osobno z oświadczeń. Na przykład uwierzytelniania OAuth lub SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | Identyfikator profilu technicznego, z którego będą wszystkie oświadczenia przychodzące i wychodzące ma zostać dodany do tego profilu technicznego. Profil techniczny odwołania musi być zdefiniowany w tym samym pliku zasad. |
| IncludeTechnicalProfile |0:1 | Identyfikator profilu technicznego, z którego ma wszystkie dane, które mają zostać dodane do tego profilu technicznego. Profil techniczny odwołania musi istnieć w tym samym pliku zasad. |
| UseTechnicalProfileForSessionManagement | 0:1 | Inny profil techniczny służący do zarządzania sesjami. |
|EnabledForUserJourneys| 0:1 |Kontroluje, czy profil techniczny jest wykonywany w podróży użytkownika.  |

### <a name="protocol"></a>Protokół

**Protokołu** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Name | Yes | Nazwa prawidłowe protokołu obsługiwanego przez usługę Azure AD B2C, która jest używana jako część profilu technicznego. Możliwe wartości: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `WsFed`, `WsTrust`, `Proprietary`, `session management`, `self-asserted`, lub `None`. |
| Program obsługi | Nie | Jeśli nazwa protokołu jest równa `Proprietary`, określ w pełni kwalifikowana nazwa zestawu, który jest używany przez usługę Azure AD B2C do określenia programu obsługi protokołu. |

### <a name="metadata"></a>Metadane

A **metadanych** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Element | 0: n | Metadane, które odnoszą się do profilu technicznego. Każdy typ profilu technicznego ma inny zbiór elementów metadanych. Patrz sekcja: typy profilu technicznego, aby uzyskać więcej informacji. |

#### <a name="item"></a>Element

**Elementu** elementu **metadanych** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Klucz | Yes | Klucz metadanych. Zobacz każdego typu profilu technicznego dla listy elementów metadanych. |

### <a name="cryptographickeys"></a>CryptographicKeys

**CryptographicKeys** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Klucz | 1: n | Klucz kryptograficzny używany w tym profilu technicznego. |

#### <a name="key"></a>Klucz

**Klucz** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Nie | Unikatowy identyfikator określonej pary kluczy przywoływany z innych elementów w pliku zasad. |
| StorageReferenceId | Yes | Identyfikator kontenera klucza magazynu przywoływany z innych elementów w pliku zasad. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

**InputClaimsTransformations** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identyfikator przekształcania oświadczeń, który ma być wykonany przed wysłaniem żadnych oświadczeń na dostawcy oświadczeń lub jednostkę uzależnioną. Przekształcanie oświadczeń może służyć do modyfikowania istniejących oświadczeń ClaimsSchema lub wygenerować nowe. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Yes | Identyfikator już zdefiniowana w pliku zasad lub nadrzędnego pliku zasad przekształcania oświadczeń. |

### <a name="inputclaims"></a>InputClaims

**InputClaims** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie InputClaim | 1: n | Typ oświadczenia oczekiwanych danych wejściowych. |

#### <a name="inputclaim"></a>Oświadczenie InputClaim

**Oświadczenie InputClaim** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Identyfikator typu oświadczenia już zdefiniowane w sekcji ClaimsSchema plik zasad lub nadrzędnego pliku zasad. |
| defaultValue | Nie | Wartość domyślną, służące do tworzenia oświadczeń, jeśli oświadczenie wskazywanym przez ClaimTypeReferenceId nie istnieje, tak aby wynikowe oświadczenia mogą być używane jako oświadczenie InputClaim przez profil techniczny. |
| PartnerClaimType | Nie | Identyfikator typu oświadczenia partnera zewnętrznych zasad określony typ oświadczenia mapuje. Jeśli nie określono atrybutu PartnerClaimType zasad określony typ oświadczenia jest mapowany na typ roszczenia partnera o takiej samej nazwie. Tej właściwości należy użyć, gdy Twoja nazwa typ oświadczenia różni się od innych firm. Na przykład imię oświadczeń jest "imię" oświadczenie o nazwie "imię" jest używana przez partnera. |

### <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Typ oświadczenia do innej witryny. |

#### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Identyfikator typu oświadczenia już zdefiniowane w sekcji ClaimsSchema plik zasad lub nadrzędnego pliku zasad. |
| defaultValue | Nie | Wartość domyślną, służące do tworzenia oświadczeń, jeśli oświadczenie wskazywanym przez ClaimTypeReferenceId nie istnieje, tak aby wynikowe oświadczenia mogą być używane jako oświadczenie InputClaim przez profil techniczny. |
| PartnerClaimType | Nie | Identyfikator typu oświadczenia partnera zewnętrznych zasad określony typ oświadczenia mapuje. Jeśli nie określono atrybutu PartnerClaimType zasad określony typ oświadczenia jest mapowany na typ roszczenia partnera o takiej samej nazwie. Tej właściwości należy użyć, gdy Twoja nazwa typ oświadczenia różni się od innych firm. Na przykład imię oświadczeń jest "imię" oświadczenie o nazwie "imię" jest używana przez partnera. |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| oświadczenie outputClaim | 1: n | Typ oświadczenia oczekiwanych danych wyjściowych. |

#### <a name="outputclaim"></a>oświadczenie outputClaim

**Oświadczenie OutputClaim** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Identyfikator typu oświadczenia już zdefiniowane w sekcji ClaimsSchema plik zasad lub nadrzędnego pliku zasad. |
| defaultValue | Nie | Wartość domyślną, służące do tworzenia oświadczeń, jeśli oświadczenie wskazywanym przez ClaimTypeReferenceId nie istnieje, tak aby wynikowe oświadczenia mogą być używane jako oświadczenie InputClaim przez profil techniczny. |
|AlwaysUseDefaultValue |Nie |Wymusić użycie wartości domyślnej.  |
| PartnerClaimType | Nie | Identyfikator typu oświadczenia partnera zewnętrznych zasad określony typ oświadczenia mapuje. Jeśli nie określono atrybutu PartnerClaimType zasad określony typ oświadczenia jest mapowany na typ roszczenia partnera o takiej samej nazwie. Tej właściwości należy użyć, gdy Twoja nazwa typ oświadczenia różni się od innych firm. Na przykład imię oświadczeń jest "imię" oświadczenie o nazwie "imię" jest używana przez partnera. |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

**OutputClaimsTransformations** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identyfikatory przekształceń oświadczeń, które mają zostać wykonane przed wysłaniem żadnych oświadczeń na dostawcy oświadczeń lub jednostkę uzależnioną. Przekształcanie oświadczeń może służyć do modyfikowania istniejących oświadczeń ClaimsSchema lub wygenerować nowe. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Yes | Identyfikator już zdefiniowana w pliku zasad lub nadrzędnego pliku zasad przekształcania oświadczeń. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Niektórych lub wszystkich oświadczeń danych wyjściowych odwołujący się profilu technicznego, sprawdź poprawność identyfikatorów profile techniczne, które są używane. Wszystkie oświadczeń wejściowych, do którego istnieje odwołanie profilu technicznego musi znajdować się w oświadczeń danych wyjściowych odwołujący się profilu technicznego. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Yes | Identyfikator profilu technicznego już zdefiniowana w pliku zasad lub nadrzędnego pliku zasad. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Typ oświadczenia | Yes | Identyfikator typu oświadczenia już zdefiniowane w sekcji ClaimsSchema w pliku zasad. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

**IncludeTechnicalProfile** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Yes | Identyfikator profilu technicznego już zdefiniowana w pliku zasad lub nadrzędnego pliku zasad. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

**UseTechnicalProfileForSessionManagement** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Yes | Identyfikator profilu technicznego już zdefiniowana w pliku zasad lub nadrzędnego pliku zasad. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
**ClaimsProviderSelections** podróży użytkownika definiuje listę opcji do wyboru dostawcy oświadczeń i ich kolejność. Za pomocą **EnabledForUserJourneys** elementu, można filtrować dostawcy oświadczeń, które jest dostępne dla użytkownika. **EnabledForUserJourneys** element zawiera jedną z następujących wartości:

- **Zawsze**, wykonaj profilu technicznego.
- **Nigdy nie**, Pomiń profilu technicznego.
- **OnClaimsExistence** wykonać tylko wtedy, gdy istnieje pewne oświadczenia, określona w profilu technicznym.
- **OnItemExistenceInStringCollectionClaim**, wykonywanie, gdy element istnieje tylko w oświadczenie kolekcji parametrów.
- **OnItemAbsenceInStringCollectionClaim** wykonać, tylko jeśli element nie istnieje oświadczenia kolekcji ciągów.

Za pomocą **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** lub **OnItemAbsenceInStringCollectionClaim**, wymaga podania następujących metadane: **ClaimTypeOnWhichToEnable** Określa typ oświadczenia, który ma zostać obliczone **ClaimValueOnWhichToEnable** określa wartość do porównania.

Następujące profilu technicznego jest wykonywany tylko wtedy, gdy **identityProviders** kolekcji ciągów zawiera wartość `facebook.com`:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
