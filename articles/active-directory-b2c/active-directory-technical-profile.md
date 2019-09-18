---
title: Zdefiniuj profil techniczny Azure Active Directory w zasadach niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Zdefiniuj profil techniczny Azure Active Directory w zasadach niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4383980953147560b9e51e4ccab3032dd8173dd4
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064618"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny Azure Active Directory w Azure Active Directory B2C zasadach niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę Azure Active Directory zarządzania użytkownikami. W tym artykule opisano szczegóły dotyczące profilu technicznego dotyczącego współpracy z dostawcą oświadczeń obsługującym ten standardowy protokół.

## <a name="protocol"></a>Protocol

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary`. Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`programu obsługi protokołu.

Wszystkie profile techniczne usługi Azure AD obejmują wspólny profil techniczny w **usłudze AAD** . Następujące profile techniczne nie określają protokołu, ponieważ protokół jest skonfigurowany w profilu technicznym usługi **AAD — typowy** :

- **AAD-UserReadUsingAlternativeSecurityId** i **AAD-UserReadUsingAlternativeSecurityId-NOERROR** — wyszukiwanie konta społecznościowego w katalogu.
- **AAD-UserWriteUsingAlternativeSecurityId** — Tworzenie nowego konta społecznościowego.
- **AAD-UserReadUsingEmailAddress** — wyszukiwanie konta lokalnego w katalogu.
- **AAD-UserWriteUsingLogonEmail** — Tworzenie nowego konta lokalnego.
- **AAD-UserWritePasswordUsingObjectId** — aktualizuje hasło do konta lokalnego.
- **AAD-UserWriteProfileUsingObjectId** — aktualizowanie profilu użytkownika konta lokalnego lub społecznościowego.
- **AAD-UserReadUsingObjectId** — odczytywanie profilu użytkownika konta lokalnego lub społecznościowego.
- **AAD-UserWritePhoneNumberUsingObjectId** — Napisz numer telefonu MFA konta lokalnego lub społecznościowego

W poniższym przykładzie przedstawiono profil techniczny usługi **AAD-Common** :

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Następujące profile techniczne obejmują **InputClaims** kont społecznościowych i lokalnych:

- Profile techniczne konta społeczności **AAD-UserReadUsingAlternativeSecurityId** i **AAD-UserWriteUsingAlternativeSecurityId** zawierają zgłoszenie **AlternativeSecurityId** . To zgłoszenie zawiera identyfikator użytkownika konta społecznościowego.
- Profile techniczne dla konta lokalnego **AAD-UserReadUsingEmailAddress** i **AAD-UserWriteUsingLogonEmail** zawierają takie wnioski. To zgłoszenie zawiera nazwę logowania konta lokalnego.
- Ujednolicone (lokalne i społecznościowe) profile techniczne **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId**i **AAD-UserWritePhoneNumberUsingObjectId** obejmują identyfikator obiektu. Unikatowy identyfikator konta.

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych.

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez profil techniczny usługi Azure AD. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w Azure Active Directory. Można również uwzględnić oświadczenia, które nie są zwracane przez Azure Active Directory, o ile atrybut ten `DefaultValue` jest ustawiony.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

Na przykład profil techniczny usługi **AAD-UserWriteUsingLogonEmail** tworzy konto lokalne i zwraca następujące oświadczenia:

- **objectid**, który jest identyfikatorem nowego konta
- **newUser**, który wskazuje, czy użytkownik jest nowy
- **authenticationSource**, w którym są ustawiane uwierzytelnianie`localAccountAuthentication`
- **userPrincipalName**, czyli główna nazwa użytkownika nowego konta
- **signInNames. EmailAddress**, czyli nazwa logowania do konta, podobna do dodanego **adresu e-mail**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

Element **PersistedClaims** zawiera wszystkie wartości, które powinny być utrwalane przez usługę Azure AD, z możliwymi do mapowania informacjami o typie już zdefiniowanym w sekcji ClaimsSchema w zasadach i nazwie atrybutu usługi Azure AD.

Profil techniczny usługi **AAD-UserWriteUsingLogonEmail** , który tworzy nowe konto lokalne, utrzymuje następujące oświadczenia:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Nazwa tego żądania jest nazwą atrybutu usługi Azure AD, chyba że określono atrybut **PartnerClaimType** , który zawiera nazwę atrybutu usługi Azure AD.

## <a name="requirements-of-an-operation"></a>Wymagania operacji

- W zbiorze oświadczeń musi być dokładnie jeden element **oświadczenie inputclaim** dla wszystkich profilów technicznych usługi Azure AD.
- Jeśli operacja jest `Write` lub `DeleteClaims`, to musi także występować w elemencie **PersistedClaims** .
- Wartość żądania **userPrincipalName** musi mieć format `user@tenant.onmicrosoft.com`.
- Żądanie **DisplayName** jest wymagane i nie może być pustym ciągiem.

## <a name="azure-ad-technical-provider-operations"></a>Operacje związane z dostawcą technicznym usługi Azure AD

### <a name="read"></a>Odczyt

Operacja **odczytu** odczytuje dane dotyczące jednego konta użytkownika. Aby odczytać dane użytkownika, należy podać klucz jako rolę wejściową, na przykład **objectid**, **userPrincipalName**, **signInNames** (dowolny typ, nazwa użytkownika i konto e-mail) lub **alternativeSecurityId**.

Poniższy profil techniczny odczytuje dane dotyczące konta użytkownika przy użyciu identyfikatora obiektu użytkownika:

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Zapis

Operacja **zapisu** tworzy lub aktualizuje pojedyncze konto użytkownika. Aby napisać konto użytkownika, należy podać klucz jako rolę wejściową, taką jak **objectid**, **userPrincipalName**, **signInNames. EmailAddress**lub **alternativeSecurityId**.

Poniższy profil techniczny tworzy nowe konto społecznościowe:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

Operacja **DeleteClaims** czyści informacje z podanej listy oświadczeń. Aby usunąć informacje z oświadczeń, należy podać klucz jako oświadczenie wejściowe, takie jak **objectid**, **userPrincipalName**, **signInNames. EmailAddress** lub **alternativeSecurityId**.

Poniższy profil techniczny usuwa oświadczenia:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Operacja **DeleteClaimsPrincipal** usuwa jedno konto użytkownika z katalogu. Aby usunąć konto użytkownika, należy podać klucz jako rolę wejściową, taką jak **objectid**, **userPrincipalName**, **signInNames. EmailAddress** lub **alternativeSecurityId**.

Poniższy profil techniczny usuwa konto użytkownika z katalogu przy użyciu głównej nazwy użytkownika:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Poniższy profil techniczny usuwa konto użytkownika społecznościowego przy użyciu **alternativeSecurityId**:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Operacja do wykonania. Możliwe wartości: `Read`, `Write`, `DeleteClaims`lub. `DeleteClaimsPrincipal` |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nie | Zgłoś błąd, jeśli obiekt użytkownika nie istnieje w katalogu. Możliwe wartości: `true` lub `false`. |
| UserMessageIfClaimsPrincipalDoesNotExist | Nie | Jeśli błąd ma zostać podniesiony (zobacz Opis atrybutu RaiseErrorIfClaimsPrincipalDoesNotExist), określ komunikat, który ma być wyświetlany użytkownikowi, jeśli obiekt użytkownika nie istnieje. Wartość może być [zlokalizowana](localization.md).|
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nie | Zgłoś błąd, jeśli obiekt użytkownika już istnieje. Możliwe wartości: `true` lub `false`.|
| UserMessageIfClaimsPrincipalAlreadyExists | Nie | Jeśli błąd ma zostać podniesiony (zobacz Opis atrybutu RaiseErrorIfClaimsPrincipalAlreadyExists), określ komunikat, który ma być wyświetlany użytkownikowi, jeśli obiekt użytkownika już istnieje. Wartość może być [zlokalizowana](localization.md).|
| ApplicationObjectId | Nie | Identyfikator obiektu aplikacji dla atrybutów rozszerzenia. Wartość: Identyfikator obiektu aplikacji. Aby uzyskać więcej informacji, zobacz [Używanie atrybutów niestandardowych w niestandardowych zasadach edytowania profilu](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). |
| ClientId | Nie | Identyfikator klienta służący do uzyskiwania dostępu do dzierżawy jako osoba trzecia. Aby uzyskać więcej informacji, zobacz [Używanie atrybutów niestandardowych w niestandardowych zasadach edytowania profilu](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) |














