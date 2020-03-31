---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Określ element TechnicalProfiles zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5196615b6b935e4d37565298be03ad315163d132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264312"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **TechnicalProfiles** element zawiera zestaw profili technicznych obsługiwanych przez dostawcę oświadczeń. Każdy dostawca oświadczeń musi mieć jeden lub więcej profilów technicznych, które określają punkty końcowe i protokoły potrzebne do komunikowania się z dostawcą oświadczeń. Dostawca oświadczeń może mieć wiele profilów technicznych.

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

Element **TechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
|---------|---------|---------|
| Identyfikator | Tak | Unikatowy identyfikator profilu technicznego. Do profilu technicznego można odwoływać się przy użyciu tego identyfikatora z innych elementów w pliku zasad. Na przykład **OrchestrationSteps** i **ValidationTechnicalProfile**. |

**Profil techniczny** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Domain | 0:1 | Nazwa domeny profilu technicznego. Jeśli na przykład profil techniczny określa dostawcę tożsamości facebooka, nazwa domeny jest Facebook.com. |
| DisplayName | 1:1 | Nazwa profilu technicznego, który może być wyświetlany użytkownikom. |
| Opis | 0:1 | Opis profilu technicznego, który może być wyświetlany użytkownikom. |
| Protocol (Protokół) | 0:1 | Protokół używany do komunikacji z drugą stroną. |
| Metadane | 0:1 | Kolekcja par klucz/wartość, które są używane przez protokół do komunikowania się z punktem końcowym w trakcie transakcji. |
| InputTokenFormat | 0:1 | Format tokenu wejściowego. Możliwe `JSON`wartości: `JWT` `SAML11`, `SAML2`, , lub . Wartość `JWT` reprezentuje token sieci Web JSON zgodnie ze specyfikacją IETF. Wartość `SAML11` reprezentuje token zabezpieczający SAML 1.1 zgodnie ze specyfikacją OASIS.  Wartość `SAML2` reprezentuje token zabezpieczający SAML 2.0 zgodnie ze specyfikacją OASIS. |
| Format Danych | 0:1 | Format tokenu wyjściowego. Możliwe `JSON`wartości: `JWT` `SAML11`, `SAML2`, , lub . |
| Klucze kryptograficzne | 0:1 | Lista kluczy kryptograficznych, które są używane w profilu technicznym. |
| InputClaimsTransformacje | 0:1 | Lista uprzednio zdefiniowanych odwołań do przekształceń oświadczeń, które powinny zostać wykonane przed wysłaniem jakichkolwiek oświadczeń do dostawcy oświadczeń lub jednostki uzależniającej. |
| WejściaClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów oświadczeń, które są traktowane jako dane wejściowe w profilu technicznym. |
| PersistedClaims (Nieugięce roszczenia) | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów oświadczeń, które są zachowywane przez dostawcę oświadczeń, który odnosi się do profilu technicznego. |
| DisplayClaims (Wyświetlclaims) | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów oświadczeń, które są prezentowane przez dostawcę oświadczeń, która odnosi się do [samodzielnie potwierdzonego profilu technicznego](self-asserted-technical-profile.md). Funkcja DisplayClaims jest obecnie w **wersji zapoznawczej**. |
| OutputClaims (Roszczenia wyjściowe) | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów oświadczeń, które są traktowane jako dane wyjściowe w profilu technicznym. |
| OutputClaimsTransformacje | 0:1 | Lista wcześniej zdefiniowanych odwołań do przekształceń oświadczeń, które powinny być wykonywane po otrzymaniu oświadczeń od dostawcy oświadczeń. |
| ValidationTechnicalProfiles (Profesorowie technologii) | 0:n | Wykaz odniesień do innych profili technicznych, które profil techniczny wykorzystuje do celów walidacji. Aby uzyskać więcej informacji, zobacz [profil techniczny sprawdzania poprawności](validation-technical-profile.md)|
| ObiektNamingInfo | 0:1 | Steruje produkcją nazwy podmiotu w tokenach, w których nazwa podmiotu jest określona oddzielnie od oświadczeń. Na przykład OAuth lub SAML.  |
| Uwzględnij również Włącz | 0:1 |  Czy użycie tego profilu technicznego należy zastosować zachowanie logowania jednokrotnego (Logowanie jednokrotne) dla sesji, czy zamiast tego wymagają jawnej interakcji. Ten element jest prawidłowy tylko w profilach SelfAsserted używanych w profilu technicznym sprawdzania poprawności. Możliwe wartości: `true` (domyślnie) lub `false`. |
| IncludeClaimsFromProfiletechniczne | 0:1 | Identyfikator profilu technicznego, z którego mają być dodawane wszystkie oświadczenia wejściowe i wyjściowe do tego profilu technicznego. Profil techniczny, do którego istnieje odwołanie, musi być zdefiniowany w tym samym pliku zasad. |
| UwzględnijProfile technologii |0:1 | Identyfikator profilu technicznego, z którego mają być dodawane wszystkie dane do tego profilu technicznego. |
| UżyjTechnicalProfileForSessionManagement | 0:1 | Inny profil techniczny, który ma być używany do zarządzania sesjami. |
|EnabledForUserJourneys| 0:1 |Określa, czy profil techniczny jest wykonywany w podróży użytkownika.  |

## <a name="protocol"></a>Protocol (Protokół)

Element **Protokołu** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa prawidłowego protokołu obsługiwanego przez usługę Azure AD B2C, który jest używany jako część profilu technicznego. `OAuth1`Możliwe wartości: `OAuth2` `SAML2`, `OpenIdConnect` `Proprietary`, `None`, , , lub . |
| Program obsługi | Nie | Gdy nazwa protokołu jest `Proprietary`ustawiona na , określ w pełni kwalifikowaną nazwę zestawu, który jest używany przez usługę Azure AD B2C do określenia programu obsługi protokołu. |

## <a name="metadata"></a>Metadane

A **Metadata** element zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Element | 0:n | Metadane, które odnoszą się do profilu technicznego. Każdy typ profilu technicznego ma inny zestaw elementów metadanych. Aby uzyskać więcej informacji, zobacz sekcję typy profilów technicznych. |

### <a name="item"></a>Element

**Element elementu** **metadata** element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Klucz | Tak | Klucz metadanych. Zobacz każdy typ profilu technicznego, aby uzyskać listę elementów metadanych. |

## <a name="cryptographickeys"></a>Klucze kryptograficzne

**CryptographicKey element** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Klucz | 1:n | Klucz kryptograficzny używany w tym profilu technicznym. |

### <a name="key"></a>Klucz

Element **Key** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Nie | Unikatowy identyfikator określonej pary kluczy, do którego odwołuje się inne elementy w pliku zasad. |
| StorageReferenceId ( StorageReferenceId ) | Tak | Identifer kontenera klucza magazynu odwołuje się z innych elementów w pliku zasad. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformacje

Element **InputClaimsTransformations** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| InputClaimsTransformacja | 1:n | Identyfikator przekształcenia oświadczeń, które powinny być wykonywane przed wszelkie oświadczenia są wysyłane do dostawcy oświadczeń lub jednostki uzależniającej. Transformacja oświadczeń może służyć do modyfikowania istniejących oświadczeń ClaimsSchema lub generowania nowych. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformacja

Element **InputClaimsTransformation** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator transformacji oświadczeń zdefiniowany już w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="inputclaims"></a>WejściaClaims

Element **InputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| InputClaim (własnach wejściowych) | 1:n | Typ oczekiwanego oświadczenia wejściowego. |

### <a name="inputclaim"></a>InputClaim (własnach wejściowych)

**InputClaim** Element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Zastrzeżenie rodzaju żądań | Tak | Identyfikator typu oświadczenia zdefiniowanego już w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| Defaultvalue | Nie | Wartość domyślna używana do utworzenia oświadczenia, jeśli oświadczenie wskazane przez ClaimTypeReferenceId nie istnieje, tak aby wynikowe oświadczenie może być używane jako InputClaim przez profil techniczny. |
| Typ partnera | Nie | Identyfikator typu oświadczenia partnera zewnętrznego, do który jest mapowana określony typ oświadczenia zasad. Jeśli atrybut PartnerClaimType nie jest określony, określony typ oświadczenia zasad jest mapowany na typ oświadczenia partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu oświadczenia różni się od drugiej strony. Na przykład nazwa pierwszego oświadczenia to "givenName", podczas gdy partner używa oświadczenia o nazwie "first_name". |

## <a name="displayclaims"></a>DisplayClaims (Wyświetlclaims)

**DisplayClaims** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Displayclaim | 1:n | Typ oczekiwanego oświadczenia wejściowego. |

Funkcja DislayClaims jest obecnie w **wersji zapoznawczej**.

### <a name="displayclaim"></a>Displayclaim

**DisplayClaim** Element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Zastrzeżenie rodzaju żądań | Nie | Identyfikator typu oświadczenia zdefiniowanego już w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| DisplayControlReferenceId (DisplayControlReferenceId) | Nie | Identyfikator [formantu wyświetlania](display-controls.md) zdefiniowany już w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| Wymagany | Nie | Wskazuje, czy wymagane jest oświadczenie wyświetlania. |

**DisplayClaim** wymaga określenia `ClaimTypeReferenceId` albo . `DisplayControlReferenceId`

### <a name="persistedclaims"></a>PersistedClaims (Nieugięce roszczenia)

Element **PersistedClaims** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | Typ oświadczenia do persist. |

### <a name="persistedclaim"></a>PersistedClaim

Element **PersistedClaim** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Zastrzeżenie rodzaju żądań | Tak | Identyfikator typu oświadczenia zdefiniowanego już w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| Defaultvalue | Nie | Wartość domyślna używana do utworzenia oświadczenia, jeśli oświadczenie wskazane przez ClaimTypeReferenceId nie istnieje, tak aby wynikowe oświadczenie może być używane jako InputClaim przez profil techniczny. |
| Typ partnera | Nie | Identyfikator typu oświadczenia partnera zewnętrznego, do który jest mapowana określony typ oświadczenia zasad. Jeśli atrybut PartnerClaimType nie jest określony, określony typ oświadczenia zasad jest mapowany na typ oświadczenia partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu oświadczenia różni się od drugiej strony. Na przykład nazwa pierwszego oświadczenia to "givenName", podczas gdy partner używa oświadczenia o nazwie "first_name". |

## <a name="outputclaims"></a>OutputClaims (Roszczenia wyjściowe)

**OutputClaims** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| WynikClaim | 1:n | Typ oczekiwanego oświadczenia wyjściowego. |

### <a name="outputclaim"></a>WynikClaim

**OutputClaim** element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Zastrzeżenie rodzaju żądań | Tak | Identyfikator typu oświadczenia zdefiniowanego już w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| Defaultvalue | Nie | Wartość domyślna używana do utworzenia oświadczenia, jeśli oświadczenie wskazane przez ClaimTypeReferenceId nie istnieje, tak aby wynikowe oświadczenie może być używane jako InputClaim przez profil techniczny. |
|Wartość AlwaysUseDefaultValue |Nie |Wymusz na użycie wartości domyślnej.  |
| Typ partnera | Nie | Identyfikator typu oświadczenia partnera zewnętrznego, do który jest mapowana określony typ oświadczenia zasad. Jeśli atrybut PartnerClaimType nie jest określony, określony typ oświadczenia zasad jest mapowany na typ oświadczenia partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu oświadczenia różni się od drugiej strony. Na przykład nazwa pierwszego oświadczenia to "givenName", podczas gdy partner używa oświadczenia o nazwie "first_name". |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformacje

**OutputClaimsTransformations** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| OutputClaimsTransformacja | 1:n | Identyfikatory przekształceń oświadczeń, które powinny zostać wykonane przed wysłaniem jakichkolwiek oświadczeń do dostawcy oświadczeń lub jednostki uzależniającej. Transformacja oświadczeń może służyć do modyfikowania istniejących oświadczeń ClaimsSchema lub generowania nowych. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformacja

**Element OutputClaimsTransformation** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator transformacji oświadczeń zdefiniowany już w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles (Profesorowie technologii)

Element **ValidationTechnicalProfiles** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile (Profiltechniczny) | 1:n | Identyfikatory profili technicznych, które są używane, potwierdzają niektóre lub wszystkie oświadczenia wyjściowe odwołującego się do profilu technicznego. Wszystkie oświadczenia wejściowe odniesienia do profilu technicznego, do którego istnieje odwołanie, muszą znajdować się w oświadczeń wyjściowych odwołującego się do profilu technicznego. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile (Profiltechniczny)

Element **ValidationTechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego zdefiniowanego już w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="subjectnaminginfo"></a>ObiektNamingInfo

**SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Claimtype | Tak | Identyfikator typu oświadczenia zdefiniowanego już w sekcji ClaimsSchema w pliku zasad. |

## <a name="includetechnicalprofile"></a>UwzględnijProfile technologii

**IncludeTechnicalProfile** element zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego zdefiniowanego już w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UżyjTechnicalProfileForSessionManagement

**UseTechnicalProfileForSessionManagement** element zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego zdefiniowanego już w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

**ClaimsProviderWybory** w podróży użytkownika definiuje listę opcji wyboru dostawcy oświadczeń i ich kolejność. Za pomocą **EnabledForUserJourneys** element filtrowania, który twierdzi, dostawca jest dostępny dla użytkownika. **EnabledForUserJourneys** element zawiera jedną z następujących wartości:

- **Zawsze,** wykonaj profil techniczny.
- **Nigdy,** pomiń profil techniczny.
- **OnClaimsExistence** wykonać tylko wtedy, gdy istnieje określone oświadczenie, określone w profilu technicznym.
- **OnItemExistenceInStringCollectionClaim**, wykonać tylko wtedy, gdy element istnieje w oświadczeniu kolekcji ciągów.
- **OnItemAbsenceInStringCollectionClaim** wykonać tylko wtedy, gdy element nie istnieje w oświadczeniu kolekcji ciągów.

Za pomocą **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** lub **OnItemAbsenceInStringCollectionClaim**, wymaga podania następujących metadanych: **ClaimTypeOnWhichToEnable** określa typ oświadczenia, który ma być oceniany, **ClaimValueOnWhichToEnable** określa wartość, która ma być porównywana.

Następujący profil techniczny jest wykonywany tylko wtedy, gdy kolekcja `facebook.com` **ciągów identityProviders** zawiera wartość:

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
