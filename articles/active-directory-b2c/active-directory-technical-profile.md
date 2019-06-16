---
title: Definiowanie profilu technicznego usługi Azure Active Directory w przypadku zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W przypadku zasad niestandardowych w usłudze Azure Active Directory B2C, należy zdefiniować profil techniczny usługi Azure Active Directory.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b8bbe540d9e296b0f6a0c11a62d3b861e0115d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507446"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego usługi Azure Active Directory w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory (Azure AD) B2C umożliwia zarządzanie użytkownikami w usłudze Azure Active Directory. W tym artykule opisano szczegóły profilu technicznego do interakcji z dostawcy oświadczeń, który obsługuje ten standardowy protokół.

## <a name="protocol"></a>Protocol

**Nazwa** atrybutu **protokołu** element musi być równa `Proprietary`. **Obsługi** atrybutu musi zawierać w pełni kwalifikowana nazwa zestawu programu obsługi protokołu `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Obejmują wszystkie profile techniczne usługi Azure AD **typowe usługi AAD** profilu technicznego. Następujące profile techniczne nie określenie protokołu, ponieważ protokół jest skonfigurowany w **typowe usługi AAD** profilu technicznego:

- **Usługi AAD UserReadUsingAlternativeSecurityId** i **AAD-UserReadUsingAlternativeSecurityId — brak błędu** — poszukaj Konfigurowanie konta społecznościowego w katalogu.
- **AAD — UserWriteUsingAlternativeSecurityId** — Tworzenie nowego konta społecznościowego.
- **Usługi AAD UserReadUsingEmailAddress** — Szukaj w górę konto lokalne w katalogu. 
- **AAD — UserWriteUsingLogonEmail** — Utwórz nowe konto lokalne.
- **AAD — UserWritePasswordUsingObjectId** -aktualizacji hasła konta lokalnego.
- **AAD — UserWriteProfileUsingObjectId** — aktualizowanie profilu użytkownika konta lokalnego lub społecznościowych.
- **AAD — UserReadUsingObjectId** — odczytuj profil użytkownika konta lokalnego lub społecznościowych.
- **AAD — UserWritePhoneNumberUsingObjectId** -zapisu numer telefonu uwierzytelniania Wieloskładnikowego konto lokalne lub społecznościowych

W poniższym przykładzie przedstawiono **typowe usługi AAD** profilu technicznego:

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

## <a name="input-claims"></a>Oświadczeń wejściowych

Następujące profile techniczne zawierają **InputClaims** kont społecznościowych i lokalnym:

- Profile techniczne konta społecznościowego **AAD UserReadUsingAlternativeSecurityId** i **AAD UserWriteUsingAlternativeSecurityId** obejmuje **AlternativeSecurityId** oświadczenia. To oświadczenie zawiera identyfikator użytkownika konta w sieci społecznościowej.
- Profile techniczne konta lokalnego **AAD UserReadUsingEmailAddress** i **UserWriteUsingLogonEmail usługi AAD** obejmuje **e-mail** oświadczenia. To oświadczenie zawiera nazwę logowania konta lokalnego.
- Ujednolicone profile techniczne (lokalne i społecznościowych) **AAD UserReadUsingObjectId**, **AAD UserWritePasswordUsingObjectId**, **AAD UserWriteProfileUsingObjectId**, i **AAD UserWritePhoneNumberUsingObjectId** obejmuje **objectId** oświadczenia. Unikatowy identyfikator konta.

**InputClaimsTransformations** element może zawierać zbiór **InputClaimsTransformation** elementy, które są używane do modyfikowania danych wejściowych oświadczeń lub wygenerować nowe.

## <a name="output-claims"></a>Oświadczeń danych wyjściowych

**OutputClaims** element zawiera listę oświadczenia zwrócone przez profil techniczny usługi Azure AD. Może być konieczne mapowania nazwy oświadczenia, zdefiniowane w zasadach do nazwy zdefiniowane w usłudze Azure Active Directory. Możesz również uwzględnić oświadczenia, które nie są zwracane przez usługę Azure Active Directory, tak długo, jak można ustawić `DefaultValue` atrybutu.

**OutputClaimsTransformations** element może zawierać zbiór **OutputClaimsTransformation** elementy, które są używane do modyfikowania oświadczeń danych wyjściowych lub wygenerować nowe.

Na przykład **AAD UserWriteUsingLogonEmail** profilu technicznego tworzy konta lokalnego i zwraca następujące oświadczeń:

- **Identyfikator obiektu**, czyli identyfikator nowego konta
- **newUser**, która wskazuje, czy użytkownik ma nowy
- **authenticationSource**, która ustawia uwierzytelniania `localAccountAuthentication`
- **userPrincipalName**, czyli nazwę główną użytkownika nowego konta
- **signInNames.emailAddress**, który jest logowania nazwa konta, podobnie jak **e-mail** oświadczeń przychodzących

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

**PersistedClaims** element zawiera wszystkie wartości, które powinny zostać utrwalone przez usługę Azure AD przy użyciu informacji o mapowaniu możliwe między typem oświadczenia już zdefiniowane w sekcji ClaimsSchema w ramach zasad oraz atrybut usługi Azure AD Nazwa. 

**AAD UserWriteUsingLogonEmail** profilu technicznego, co powoduje utworzenie nowego konta lokalnego, będzie nadal występować po oświadczeń:

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

Nazwy oświadczenia jest nazwą atrybutu usługi Azure AD, chyba że **PartnerClaimType** atrybut jest określony, który zawiera nazwę usługi Azure AD.

## <a name="requirements-of-an-operation"></a>Wymagania dotyczące operacji

- Musi zawierać dokładnie jeden **oświadczenie InputClaim** elementu w zbiorze oświadczeń we wszystkich profilach techniczne usługi Azure AD. 
- Jeśli operacja się `Write` lub `DeleteClaims`, a następnie musi być w **PersistedClaims** elementu.
- Wartość **userPrincipalName** oświadczenia musi być w formacie `user@tenant.onmicrosoft.com`.
- **DisplayName** oświadczeń jest wymagana i nie może być pustym ciągiem.

## <a name="azure-ad-technical-provider-operations"></a>Operacji techniczne dostawcy w usłudze Azure AD

### <a name="read"></a>Odczyt

**Odczytu** operacji odczytuje dane dotyczące jednego konta użytkownika. Do odczytywania danych użytkownika, należy podać klucz jako oświadczenie wejściowe, takie jak **objectId**, **userPrincipalName**, **signInNames** (dowolnego typu, nazwy użytkownika i konto e-mail, na podstawie) lub **alternativeSecurityId**.  

Poniższy profil techniczny odczytuje dane dotyczące konta użytkownika, za pomocą identyfikator obiektu użytkownika:

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

### <a name="write"></a>Zapisywanie

**Zapisu** operacja tworzy lub aktualizuje jednego konta użytkownika. Można zapisać konta użytkownika, należy podać klucz jako oświadczenie wejściowe, takie jak **objectId**, **userPrincipalName**, **signInNames.emailAddress**, lub  **alternativeSecurityId**.  

Poniższy profil techniczny powoduje utworzenie nowego konta społecznościowego:

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

**DeleteClaims** operacja usuwa informacje z podanej listy oświadczeń. Do usuwania informacji z oświadczeń, należy podać klucz jako oświadczenie wejściowe, takie jak **objectId**, **userPrincipalName**, **signInNames.emailAddress** lub **alternativeSecurityId**.  

Poniższy profil techniczny usuwa oświadczeń:

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

**DeleteClaimsPrincipal** operacja usuwa pojedyncze konto użytkownika z katalogu. Aby usunąć konto użytkownika, należy podać klucz jako oświadczenie wejściowe, takie jak **objectId**, **userPrincipalName**, **signInNames.emailAddress** lub  **alternativeSecurityId**.  

Poniższy profil techniczny Usuwa konto użytkownika z katalogu przy użyciu głównej nazwy użytkownika:

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

Poniższy profil techniczny powoduje usunięcie konta społecznościowe użytkownika przy użyciu **alternativeSecurityId**:

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
| Operacja | Yes | Operacja do wykonania. Możliwe wartości: `Read`, `Write`, `DeleteClaims`, lub `DeleteClaimsPrincipal`. | 
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nie | Zgłoś błąd, jeśli obiekt użytkownika nie istnieje w katalogu. Możliwe wartości: `true` lub `false`. | 
| UserMessageIfClaimsPrincipalDoesNotExist | Nie | Jeśli błąd ma zostać wywołane (zobacz opis atrybutu RaiseErrorIfClaimsPrincipalDoesNotExist), określ wiadomość wyświetlana użytkownikowi, jeśli obiekt użytkownika nie istnieje. Wartość może być [zlokalizowane](localization.md).| 
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nie | Zgłoś błąd, jeśli obiekt użytkownika już istnieje. Możliwe wartości: `true` lub `false`.| 
| UserMessageIfClaimsPrincipalAlreadyExists | Nie | Jeśli błąd ma zostać wywołane (zobacz opis atrybutu RaiseErrorIfClaimsPrincipalAlreadyExists), określ komunikat do wyświetlenia dla użytkownika, jeśli obiekt użytkownika już istnieje. Wartość może być [zlokalizowane](localization.md).| 
| ApplicationObjectId | Nie | Identyfikator obiektu aplikacji rozszerzeń atrybuty. Wartość: Identyfikator obiektu aplikacji. Aby uzyskać więcej informacji, zobacz [Użyj atrybutów niestandardowych w niestandardowym profilu Edytuj zasady](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). | 
| ClientId | Nie | Identyfikator klienta do uzyskania dostępu do dzierżawy jako osobę trzecią. Aby uzyskać więcej informacji, zobacz [Użyj atrybutów niestandardowych w niestandardowym profilu edytowanie zasad](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) | 














