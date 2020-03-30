---
title: Definiowanie profilu technicznego usługi Azure AD w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny usługi Azure Active Directory w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330386"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego usługi Azure Active Directory w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę zarządzania użytkownikami usługi Azure Active Directory. W tym artykule opisano specyfikę profilu technicznego do interakcji z dostawcą oświadczeń, który obsługuje ten znormalizowany protokół.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`obsługi protokołu.

Następujące [niestandardowe zasady starter pack](custom-policy-get-started.md#custom-policy-starter-pack) Azure AD profile techniczne obejmują profil techniczny **AAD-Common.** Profile techniczne usługi Azure AD nie określają protokołu, ponieważ protokół jest skonfigurowany w profilu **technicznym AAD-Common:**
 
- **AAD-UserReadUsingAlternativeSecurityId** i **AAD-UserReadUsingAlternativeSecurityId-NoError** — wyszukuj konto społecznościowe w katalogu.
- **AAD-UserWriteUsingAlternativeSecurityId** — utwórz nowe konto społecznościowe.
- **AAD-UserReadUsingEmailAddress** — wyszukuj konto lokalne w katalogu.
- **AAD-UserWriteUsingLogonEmail** — tworzenie nowego konta lokalnego.
- **AAD-UserWritePasswordUsingObjectId** — aktualizowanie hasła konta lokalnego.
- **AAD-UserWriteProfileUsingObjectId** — aktualizowanie profilu użytkownika konta lokalnego lub społecznego.
- **AAD-UserReadUsingObjectId** — odczyt profilu użytkownika konta lokalnego lub społecznościowego.
- **AAD-UserWritePhoneNumberUsingObjectId** - Napisz numer telefonu usługi MFA konta lokalnego lub społecznego

W poniższym przykładzie przedstawiono profil techniczny **AAD-Common:**

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

## <a name="inputclaims"></a>WejściaClaims

InputClaims element zawiera oświadczenie, który jest używany do wyszukiwania konta w katalogu lub utworzyć nowy. Musi istnieć dokładnie jeden element InputClaim w kolekcji oświadczeń wejściowych dla wszystkich profilów technicznych usługi Azure AD. Może być konieczne mapowanie nazwy oświadczenia zdefiniowanego w zasadach na nazwę zdefiniowaną w usłudze Azure Active Directory.

Aby odczytać, zaktualizować lub usunąć istniejące konto użytkownika, oświadczenie wejściowe jest kluczem, który jednoznacznie identyfikuje konto w katalogu usługi Azure AD. Na przykład **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName**lub **alternativeSecurityId**. 

Aby utworzyć nowe konto użytkownika, oświadczenie wejściowe jest kluczem, który jednoznacznie identyfikuje konto lokalne lub federacyjne. Na przykład konto lokalne: **signInNames.emailAddress**lub **signInNames.userName**. Dla konta federacyjnego: **alternativeSecurityId**.

[InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) element może zawierać kolekcję składników transformacji oświadczeń wejściowych, które są używane do modyfikowania oświadczenia wejściowego lub generowania nowego.

## <a name="outputclaims"></a>OutputClaims (Roszczenia wyjściowe)

**OutputClaims** element zawiera listę oświadczeń zwróconych przez profil techniczny usługi Azure AD. Może być konieczne mapowanie nazwy oświadczenia zdefiniowanego w zasadach na nazwę zdefiniowaną w usłudze Azure Active Directory. Można również dołączyć oświadczenia, które nie są zwracane przez usługę Azure `DefaultValue` Active Directory, tak długo, jak ustawić atrybut.

[OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

Na przykład profil techniczny **AAD-UserWriteUsingLogonEmail** tworzy konto lokalne i zwraca następujące oświadczenia:

- **objectId**, który jest identyfikatorem nowego konta
- **newUser**, który wskazuje, czy użytkownik jest nowy
- **authenticationSource**, który ustawia uwierzytelnianie na`localAccountAuthentication`
- **userPrincipalName**, która jest główną nazwą użytkownika nowego konta
- **signInNames.emailAddress**, czyli nazwa logowania do konta, podobna do oświadczenia o wejsciu **wiadomości e-mail**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims (Nieugięce roszczenia)

Element **PersistedClaims** zawiera wszystkie wartości, które powinny być utrwalone przez usługę Azure AD z możliwymi informacjami mapowania między typem oświadczenia już zdefiniowanym w sekcji [ClaimsSchema](claimsschema.md) w zasadach i nazwą atrybutu usługi Azure AD.

Profil techniczny **AAD-UserWriteUsingLogonEmail,** który tworzy nowe konto lokalne, nadal występuje następujące oświadczenia:

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

Nazwa oświadczenia jest nazwą atrybutu usługi Azure AD, chyba że określono atrybut **PartnerClaimType,** który zawiera nazwę atrybutu usługi Azure AD.

## <a name="requirements-of-an-operation"></a>Wymagania dotyczące operacji

- Musi istnieć dokładnie jeden element **InputClaim** w torbie oświadczeń dla wszystkich profilów technicznych usługi Azure AD.
- [W artykule atrybuty profilu użytkownika](user-profile-attributes.md) opisano obsługiwane atrybuty profilu użytkownika usługi Azure AD B2C, których można używać w zgłoszeniach wejściowych, oświadczeń danych wyjściowych i utrwalonych oświadczeń. 
- Jeśli operacja `Write` jest `DeleteClaims`lub , a następnie musi również pojawić się w **PersistedClaims** elementu.
- Wartość oświadczenia **userPrincipalName** musi być w `user@tenant.onmicrosoft.com`formacie .
- Oświadczenie **displayName** jest wymagane i nie może być pustym ciągiem.

## <a name="azure-ad-technical-provider-operations"></a>Operacje dostawców technicznych usługi Azure AD

### <a name="read"></a>Odczyt

Operacja **Odczyt** odczytuje dane dotyczące jednego konta użytkownika. Następujący profil techniczny odczytuje dane dotyczące konta użytkownika przy użyciu identyfikatora objectId użytkownika:

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

Operacja **Write** tworzy lub aktualizuje jedno konto użytkownika. Następujący profil techniczny tworzy nowe konto społecznościowe:

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

### <a name="deleteclaims"></a>Usuń roszczenia

**Operacja DeleteClaims** czyści informacje z podanej listy oświadczeń. Następujący profil techniczny usuwa oświadczenia:

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

### <a name="deleteclaimsprincipal"></a>UsuńClaimsPrincipal

**Operacja DeleteClaimsPrincipal** usuwa pojedyncze konto użytkownika z katalogu. Następujący profil techniczny usuwa konto użytkownika z katalogu przy użyciu głównej nazwy użytkownika:

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

Następujący profil techniczny usuwa konto użytkownika społecznościowego przy użyciu **alternativeSecurityId:**

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

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Operacja do wykonania. Możliwe `Read`wartości: `Write` `DeleteClaims`, `DeleteClaimsPrincipal`, , lub . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nie | Podnieś błąd, jeśli obiekt użytkownika nie istnieje w katalogu. Możliwe `true` wartości: `false`lub . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nie | Podnieś błąd, jeśli obiekt użytkownika już istnieje. Możliwe `true` wartości: `false`lub .|
| Identyfikatorobiekty aplikacji | Nie | Identyfikator obiektu aplikacji dla atrybutów rozszerzenia. Wartość: Identyfikator obiektu aplikacji. Aby uzyskać więcej informacji, zobacz [Używanie atrybutów niestandardowych w zasadach edycji profilu niestandardowego](custom-policy-custom-attributes.md). |
| ClientId | Nie | Identyfikator klienta dostępu do dzierżawy jako strony trzeciej. Aby uzyskać więcej informacji, zobacz [Używanie atrybutów niestandardowych w zasadach edycji profilu niestandardowego](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy [rozpoznawanie oświadczeń](claim-resolver-overview.md) jest uwzględnione w profilu technicznym. Możliwe wartości: `true` `false`  , lub (domyślnie). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw to na `true`. |

### <a name="ui-elements"></a>Elementy interfejsu użytkownika
 
Do skonfigurowania komunikatu o błędzie wyświetlanego po awarii można użyć następujących ustawień. Metadane powinny być skonfigurowane w [samodzielnie potwierdzonym](self-asserted-technical-profile.md) profilu technicznym. Komunikaty o błędach mogą być [zlokalizowane](localization.md).

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists UserMessageIfClaimsPrincipalAlreadyExists UserMessageIfClaimsPrincipalAlreadyExists UserM | Nie | Jeśli ma zostać zgłoszony błąd (zobacz RaiseErrorIfClaimsPrincipalAlreadyExists opis atrybutu), określ komunikat, który ma być wyświetlany użytkownikowi, jeśli obiekt użytkownika już istnieje. |
| UserMessageIfClaimsPrincipalDoesNotExist | Nie | Jeśli ma zostać zgłoszony błąd (zobacz opis atrybutu RaiseErrorIfClaimsPrincipalDoesNotExist), określ komunikat, który ma być wyświetlany użytkownikowi, jeśli obiekt użytkownika nie istnieje. |


## <a name="next-steps"></a>Następne kroki

Zobacz następujący artykuł, na przykład przy użyciu profilu technicznego usługi Azure AD:

- [Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-configure-user-input.md)














