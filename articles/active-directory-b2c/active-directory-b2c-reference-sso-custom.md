---
title: Zarządzanie sesjami logowania jednokrotnego przy użyciu zasad niestandardowych w programie Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak zarządzać sesjami logowania jednokrotnego przy użyciu zasad niestandardowych w programie Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ae30b316133b7479b66a69a3467497a7151dbc8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065393"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Zarządzanie sesjami logowania jednokrotnego w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa logowania jednokrotnego (SSO) w programie Azure Active Directory B2C (Azure AD B2C) umożliwia administratorowi sterowanie interakcją z użytkownikiem po jego uwierzytelnieniu. Administrator może na przykład określić, czy jest wyświetlany wybór dostawców tożsamości, czy też należy wprowadzić ponownie szczegóły konta lokalnego. W tym artykule opisano sposób konfigurowania ustawień logowania jednokrotnego dla Azure AD B2C.

Zarządzanie sesjami logowania jednokrotnego ma dwie części. Pierwsza z nich zajmuje się interakcją użytkownika bezpośrednio z Azure AD B2Cami i innymi pochodzącymi w interakcjach z użytkownikami zewnętrznymi, takimi jak Facebook. Azure AD B2C nie przesłania ani nie pomija sesji logowania jednokrotnego, które mogą być przechowywane przez strony zewnętrzne. Zamiast trasy do Azure AD B2C do strony zewnętrznej jest "zapamiętane", unikając konieczności monitowania użytkownika o wybranie dostawcy tożsamości społecznościowej lub korporacyjnej. Ostateczna decyzja dotycząca logowania jednokrotnego pozostaje ze stroną zewnętrzną.

Zarządzanie sesjami SSO korzysta z tej samej semantyki co inny profil techniczny w zasadach niestandardowych. Po wykonaniu kroku aranżacji do `UseTechnicalProfileForSessionManagement` odwołania zostanie odpytany profil techniczny skojarzony z tym krokiem. Jeśli taki istnieje, oznacza to, że dostawca sesji rejestracji jednokrotnej, którego dotyczy odwołanie, jest sprawdzany w celu sprawdzenia, czy użytkownik jest uczestnikiem sesji. W takim przypadku dostawca sesji logowania jednokrotnego jest używany do ponownego zapełnienia sesji. Podobnie po zakończeniu wykonywania kroku aranżacji dostawca jest używany do przechowywania informacji w sesji, jeśli określono dostawcę sesji logowania jednokrotnego.

Azure AD B2C zdefiniował wielu dostawców sesji rejestracji jednokrotnej, których można użyć:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Klasy zarządzania logowaniem jednokrotnym `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` są określane przy użyciu elementu profilu technicznego.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Jak nazywa się, ten dostawca niczego nie robi. Ten dostawca może służyć do pomijania zachowania logowania jednokrotnego dla określonego profilu technicznego.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Tego dostawcy można używać do przechowywania oświadczeń w sesji. Ten dostawca jest zwykle przywoływany w profilu technicznym używanym do zarządzania kontami lokalnymi. Korzystając z DefaultSSOSessionProvider do przechowywania oświadczeń w sesji, należy się upewnić, że wszelkie oświadczenia, które muszą zostać zwrócone do aplikacji lub używane przez warunki wstępne w kolejnych krokach, są przechowywane w sesji lub rozszerzane przez odczyt z profilu użytkownicy w katalogi. Zapewni to, że podróż z uwierzytelnianiem nie zakończy się niepowodzeniem w przypadku brakujących oświadczeń.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Aby dodać oświadczenia w sesji, użyj `<PersistedClaims>` elementu profilu technicznego. Gdy dostawca jest używany do ponownego wypełniania sesji, utrwalane oświadczenia są dodawane do zbioru oświadczeń. `<OutputClaims>`służy do pobierania oświadczeń z sesji.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ten dostawca służy do pomijania ekranu "wybieranie dostawcy tożsamości". Zwykle jest to przywoływane w profilu technicznym skonfigurowanym dla zewnętrznego dostawcy tożsamości, takiego jak Facebook.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ten dostawca służy do zarządzania Azure AD B2Cmi sesjami SAML między aplikacjami a zewnętrznymi dostawcami tożsamości SAML.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

W profilu technicznym znajdują się dwa elementy metadanych:

| Element | Default Value | Możliwe wartości | Opis
| --- | --- | --- | --- |
| IncludeSessionIndex | true | PRAWDA/FAŁSZ | Wskazuje dostawcę, który ma być przechowywany w indeksie sesji. |
| RegisterServiceProviders | true | PRAWDA/FAŁSZ | Wskazuje, że dostawca powinien rejestrować wszystkich dostawców usług SAML, którzy wystawiły potwierdzenie. |

W przypadku korzystania z dostawcy do przechowywania sesji dostawcy tożsamości SAML elementy powyżej powinny mieć wartość false. W przypadku korzystania z dostawcy do przechowywania sesji SAML B2C, powyższe elementy powinny być prawdziwe lub pomijane, ponieważ wartości domyślne są spełnione. Wylogowywanie sesji języka SAML wymaga `SessionIndex` zakończenia `NameID` i.

