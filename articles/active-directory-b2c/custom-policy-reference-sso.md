---
title: Zarządzanie sesjami logowania jednokrotnego przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zarządzać sesjami SSO przy użyciu zasad niestandardowych w usłudze Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246034"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Zarządzanie sesjami logowania jednokrotnego w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zarządzanie sesjami logowania jednokrotnego w usłudze Azure Active Directory B2C (Azure AD B2C) umożliwia administratorowi kontrolowanie interakcji z użytkownikiem po uwierzytelnienia użytkownika. Na przykład administrator może kontrolować, czy wybór dostawców tożsamości jest wyświetlany lub czy szczegóły konta lokalnego muszą zostać wprowadzone ponownie. W tym artykule opisano sposób konfigurowania ustawień logowania typu "SSO" dla usługi Azure AD B2C.

Zarządzanie sesjami SSO ma dwie części. Pierwszy dotyczy interakcji użytkownika bezpośrednio z usługą Azure AD B2C, a drugi dotyczy interakcji użytkownika z zewnętrznymi stronami, takimi jak Facebook. Usługa Azure AD B2C nie zastępuje ani nie pomija sesji jednośmiękowych, które mogą być przechowywane przez strony zewnętrzne. Zamiast trasy za pośrednictwem usługi Azure AD B2C, aby uzyskać dostęp do strony zewnętrznej jest "zapamiętany", unikając konieczności reprompt użytkownika, aby wybrać ich dostawcy tożsamości społecznej lub przedsiębiorstwa. Ostateczna decyzja o SSO pozostaje w przypadku strony zewnętrznej.

Zarządzanie sesjami jednojadłymi używa tej samej semantyki, co każdy inny profil techniczny w zasadach niestandardowych. Po wykonaniu kroku aranżacji profil techniczny skojarzony z tym `UseTechnicalProfileForSessionManagement` krokiem jest wyszukiwany w celu uzyskania odwołania. Jeśli taki istnieje, dostawca sesji jednośdziejowej, do którego istnieje odwołanie, jest sprawdzany, aby sprawdzić, czy użytkownik jest uczestnikiem sesji. Jeśli tak, dostawca sesji samouszczącej się jest używany do ponownego zapełnienia sesji. Podobnie po zakończeniu wykonywania kroku aranżacji dostawca jest używany do przechowywania informacji w sesji, jeśli określono dostawcę sesji logowania posoczeniucowego.

Usługa Azure AD B2C zdefiniowała liczbę dostawców sesji typu SSO, których można używać:

* NoopSSOSessionProvider
* DefaultSSSessionProvider
* ZewnętrznyloginSSoSessionProvider
* SamlSSOSessionProvider

Klasy zarządzania SSO `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` są określane przy użyciu elementu profilu technicznego.

## <a name="input-claims"></a>Oświadczenia wejściowe

Element `InputClaims` jest pusty lub nieobecny.

## <a name="persisted-claims"></a>Utrwalone roszczenia

Oświadczenia, które muszą zostać zwrócone do aplikacji lub używane przez warunki wstępne w kolejnych krokach, powinny być przechowywane w sesji lub rozszerzone przez odczyt z profilu użytkownika w katalogu. Za pomocą utrwalonych oświadczeń gwarantuje, że procesy uwierzytelniania nie zakończy się niepowodzeniem na brakujące oświadczenia. Aby dodać oświadczenia w sesji, użyj `<PersistedClaims>` elementu profilu technicznego. Gdy dostawca jest używany do ponownego zapełnienia sesji, utrwalone oświadczenia są dodawane do worka oświadczeń.

## <a name="output-claims"></a>Oświadczenia wyjściowe

Jest `<OutputClaims>` używany do pobierania oświadczeń z sesji.

## <a name="session-providers"></a>Dostawcy sesji

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Jak sama nazwa nakazuje, ten dostawca nic nie robi. Ten dostawca może służyć do wygaszanie zachowania SSO dla określonego profilu technicznego. Następujący `SM-Noop` profil techniczny znajduje się w [pakiecie startowym niestandardowych zasad](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSSessionProvider

Ten dostawca może służyć do przechowywania oświadczeń w sesji. Ten dostawca jest zazwyczaj odwołuje się w profilu technicznym używanym do zarządzania kontami lokalnymi. Następujący `SM-AAD` profil techniczny znajduje się w [pakiecie startowym niestandardowych zasad](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

Następujący `SM-MFA` profil techniczny znajduje się w [pakiecie startowym](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`niestandardowych zasad . Ten profil techniczny zarządza sesją uwierzytelniania wieloskładnikowego.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ZewnętrznyloginSSoSessionProvider

Ten dostawca jest używany do pomijania ekranu "wybierz dostawcę tożsamości". Zazwyczaj odwołuje się do niego w profilu technicznym skonfigurowanym dla zewnętrznego dostawcy tożsamości, takiego jak Facebook. Następujący `SM-SocialLogin` profil techniczny znajduje się w [pakiecie startowym niestandardowych zasad](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis|
| --- | --- | --- |
| ZawszeFetchClaimsFromProvider | Nie | Nie jest obecnie używany, mogą być ignorowane. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ten dostawca służy do zarządzania sesjami SAML usługi Azure AD B2C między aplikacją jednostki uzależnionej lub federowanym dostawcą tożsamości SAML. W przypadku korzystania z dostawcy usługi SSO do `RegisterServiceProviders` przechowywania sesji `false`dostawcy tożsamości SAML należy ustawić na . Poniższy `SM-Saml-idp` profil techniczny jest używany przez [profil techniczny SAML](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Podczas korzystania z dostawcy do przechowywania sesji SAML `RegisterServiceProviders` B2C, musi ustawić `true`. Wylogowanie sesji `SessionIndex` SAML wymaga i `NameID` do ukończenia.

Poniższy `SM-Saml-idp` profil techniczny jest używany przez [profil techniczny emitenta SAML](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis|
| --- | --- | --- |
| InsessionIndex (Index) | Nie | Nie jest obecnie używany, mogą być ignorowane.|
| Zarejestrujuchsługi | Nie | Wskazuje, że dostawca powinien zarejestrować wszystkich dostawców usług SAML, które zostały wystawione potwierdzenia. Możliwe wartości: `true` (domyślnie) lub `false`.|



