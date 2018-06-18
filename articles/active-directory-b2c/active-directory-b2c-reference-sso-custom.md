---
title: Zarządzanie sesjami logowania jednokrotnego za pomocą niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Informacje o sposobie zarządzania sesjami logowania jednokrotnego za pomocą niestandardowych zasad w usłudze Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8e05b057f6d7bfe0d836bb31e3fc656c17d4f18e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34710179"
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Usługa Azure AD B2C: Pojedynczego logowania jednokrotnego (SSO) sesji zarządzania

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure AD B2C umożliwia administratorowi kontrolowania, jak usługi Azure AD B2C współdziała z użytkownikiem po użytkownik został już uwierzytelniony. Jest to realizowane za pośrednictwem zarządzania sesji rejestracji Jednokrotnej. Na przykład administrator może kontrolować, czy jest wyświetlany zaznaczenie dostawców tożsamości, lub określa, czy szczegóły konta lokalnego muszą zostać wprowadzone ponownie. W tym artykule opisano sposób konfigurowania ustawień rejestracji Jednokrotnej dla usługi Azure AD B2C.

## <a name="overview"></a>Przegląd

Zarządzanie sesjami logowania jednokrotnego ma dwie części. Pierwszy dotyczy interakcji użytkownika bezpośrednio z usługi Azure AD B2C i inne transakcje z interakcji użytkowników z zewnętrznymi, takimi jak Facebook. Usługa Azure AD B2C nie zastąpienia lub obejście sesji rejestracji Jednokrotnej, które może być przechowywany przez osoby trzecie. Zamiast Rozsyłanie za pomocą usługi Azure AD B2C, aby uzyskać dostęp do zewnętrznych strona jest "zapamiętany", uniknięcie reprompt użytkownikowi na wybranie ich społecznej lub organizacji dostawcy tożsamości. Ultimate decyzji rejestracji Jednokrotnej jest powiązana z firm zewnętrznych.

## <a name="how-does-it-work"></a>Jak to działa?

Zarządzanie sesjami logowania jednokrotnego używa tej samej semantyki jako inny profil techniczne w niestandardowych zasad. Po wykonaniu kroku aranżacji techniczne profil skojarzony z kroku zostanie zapytany o `UseTechnicalProfileForSessionManagement` odwołania. Jeśli istnieje, do którego istnieje odwołanie dostawcy sesji rejestracji Jednokrotnej jest sprawdzany czy użytkownik jest uczestnika sesji. Jeśli tak dostawcy sesji rejestracji Jednokrotnej jest używany do wypełnienia sesji. Podobnie po zakończeniu wykonywania kroku aranżacji dostawcy jest używany do przechowywania informacji w sesji, jeśli określono dostawcy sesji rejestracji Jednokrotnej.

Usługa Azure AD B2C zdefiniował wielu dostawców sesji logowania jednokrotnego, których można użyć:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Określono klasy zarządzania logowaniem Jednokrotnym przy użyciu `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` element techniczne profilu.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Jako nazwa decyduje, tego dostawcy nie działa. Ten dostawca może służyć do pomijania zachowanie rejestracji Jednokrotnej dla określonego profilu technicznych.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ten dostawca może służyć do przechowywania oświadczeń w sesji. Ten dostawca jest zwykle przywoływany w profilu techniczne używane do zarządzania kontami lokalnymi. 

> [!NOTE]
> Korzystając z DefaultSSOSessionProvider do przechowywania oświadczeń w sesji, musisz upewnij się, że wszelkie oświadczenia, które muszą być zwrócony do aplikacji lub używane przez warunki wstępne w kolejnych krokach, są przechowywane w sesji lub rozszerzony o odczytu z profilu użytkowników w katalog. Daje to pewność, że podróży uwierzytelnianie nie powiedzie się na brak oświadczeń.

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

Aby dodać oświadczeń w sesji, należy użyć `<PersistedClaims>` element techniczne profilu. Stosowania dostawcy do wypełnienia sesji utrwalonego oświadczenia są dodawane do zbioru oświadczeń. `<OutputClaims>` Służy do pobierania oświadczeń z sesji.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ten dostawca jest używany do pomijania ekranu "Wybierz dostawcy tożsamości". Jest zwykle przywoływany w profilu techniczne skonfigurowane dla dostawcy tożsamości zewnętrznych, takich jak Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ten dostawca jest używana do zarządzania sesjami SAML usługi Azure AD B2C między aplikacjami, a także zewnętrznych dostawców tożsamości SAML.

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

Istnieją dwa elementy metadanych w profilu techniczne:

| Element | Wartość domyślna | Możliwe wartości | Opis
| --- | --- | --- | --- |
| IncludeSessionIndex | true | wartość true, false | Wskazuje, aby dostawca indeksu sesji powinny być przechowywane. |
| RegisterServiceProviders | true | wartość true, false | Wskazuje, czy dostawca należy zarejestrować wszyscy dostawcy usług SAML, które zostały wydane potwierdzenia. |

Przy użyciu dostawcy do przechowywania sesji SAML dostawcy tożsamości, powyższych elementów powinny mieć wartość false. Korzystając z dostawcy do przechowywania sesji B2C SAML, powyższych elementów powinien być PRAWDA lub został pominięty wartości domyślne są spełnione.

>[!NOTE]
> Wymaga wylogowania sesji SAML `SessionIndex` i `NameID` do wykonania.

## <a name="next-steps"></a>Kolejne kroki

Można przyłączyć opinie i sugestie! Jeśli masz trudności w tym temacie, opublikuj wpis na przepełnienie stosu przy użyciu tagu ["azure-ad b2c"](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Dla żądania funkcji, Zagłosuj na ich w naszym [forum opinii](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

