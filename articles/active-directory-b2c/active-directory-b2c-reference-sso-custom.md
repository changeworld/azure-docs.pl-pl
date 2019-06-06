---
title: Zarządzanie sesji rejestracji jednokrotnej, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzanie sesjami logowania jednokrotnego za pomocą zasad niestandardowych w usłudze Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 246e00418c784ee463170d78543e4a9aae3d7da8
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509053"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Zarządzanie sesji rejestracji jednokrotnej w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po użytkownik już uwierzytelniony, pojedynczego logowania jednokrotnego (SSO) sesji zarządzania w usłudze Azure Active Directory (Azure AD) B2C umożliwia administratorowi kontrolki interakcji z użytkownikiem. Na przykład administrator może kontrolować, czy ma być wyświetlana wyboru dostawcy tożsamości lub tego, czy szczegóły konta lokalnego muszą zostać wprowadzone ponownie. W tym artykule opisano sposób konfigurowania ustawień logowania jednokrotnego dla usługi Azure AD B2C.

Zarządzanie sesjami logowania jednokrotnego ma dwie części. Pierwszy dotyczy interakcji użytkownika bezpośrednio w usłudze Azure AD B2C i inne transakcje z interakcji użytkownika z podmioty zewnętrzne, takie jak Facebook. Usługa Azure AD B2C nie zastąpić lub pominąć sesje logowania jednokrotnego, które mogą być przechowywane przez podmioty zewnętrzne. Zamiast tras za pomocą usługi Azure AD B2C, aby uzyskać dostęp do zewnętrznej strony "zapamiętywane jest", unikając konieczności reprompt użytkownikowi na wybranie jego dostawcy tożsamości społecznościowej lub przedsiębiorstwa. Ultimate decyzji rejestracji Jednokrotnej jest powiązana z firm zewnętrznych.

Zarządzanie sesjami logowania jednokrotnego korzysta z tą samą semantyką jako inne profil techniczny w zasadach niestandardowych. Po wykonaniu kroku aranżacji profilu technicznego skojarzone z kroku zostaje przesłane zapytanie `UseTechnicalProfileForSessionManagement` odwołania. Jeśli istnieje, do którego istnieje odwołanie dostawcy sesji logowania jednokrotnego jest sprawdzany można sprawdzić, czy użytkownik jest uczestnikiem sesji. Jeśli tak, dostawcy sesji logowania jednokrotnego jest używany do wypełnienia sesji. Podobnie po zakończeniu wykonywania kroku aranżacji dostawcy jest używany do przechowywania informacji w sesji, jeśli określono dostawcy sesji logowania jednokrotnego.

Usługa Azure AD B2C został zdefiniowany wiele dostawcy sesji logowania jednokrotnego, których można użyć:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Usługa rejestracji Jednokrotnej klasy zarządzania są określane za pomocą `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` elementu profilu technicznego.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Jako nazwę połączenia z opisywanym, tego dostawcy nic nie robi. Ten dostawca może służyć do pomijania zachowanie logowania jednokrotnego dla określonego profilu technicznego.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ten dostawca może służyć do przechowywania oświadczeń w sesji. Ten dostawca jest zwykle przywoływany w profilu technicznym używanym do zarządzania kontami lokalnymi. Korzystając z DefaultSSOSessionProvider do przechowywania oświadczeń w sesji, należy się upewnić, że żadnych oświadczeń, które muszą być zwrócone do aplikacji lub używane przez warunki wstępne w kolejnych krokach są przechowywane w sesji lub wzmacnia odczyt z profilów użytkowników w katalog. Pozwoli to zagwarantować, że podróż uwierzytelnianie zakończy się niepowodzeniem na brak oświadczeń.

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

Aby dodać oświadczeń w sesji, należy użyć `<PersistedClaims>` elementu profilu technicznego. Gdy dostawca służy do ponownie wypełnić sesji utrwalonych oświadczenia są dodawane do zbioru oświadczeń. `<OutputClaims>` Służy do pobierania oświadczeń z sesji.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ten dostawca jest używany do pomijania na ekranie "Wybieranie dostawcy tożsamości". Zazwyczaj odwołuje się do niego profil techniczny skonfigurowany do zewnętrznego dostawcy tożsamości, takie jak Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ten dostawca jest używana do zarządzania sesjami SAML usługi Azure AD B2C między aplikacje, a także zewnętrznego dostawcy tożsamości SAML.

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

Istnieją dwa elementy metadanych w profilu technicznym:

| Element | Wartość domyślna | Możliwe wartości | Opis
| --- | --- | --- | --- |
| IncludeSessionIndex | true | PRAWDA/FAŁSZ | Wskazuje, aby dostawca indeks sesji powinny być przechowywane. |
| RegisterServiceProviders | true | PRAWDA/FAŁSZ | Wskazuje, że dostawcy należy zarejestrować wszystkich dostawców usługi SAML, które zostały wydane potwierdzenie. |

Korzystając z dostawcy do przechowywania sesji SAML dostawcy tożsamości, powyższych elementów zarówno należy wartość false. Podczas przy użyciu dostawcy do przechowywania sesji B2C SAML, powyższych elementów powinna być prawdziwe lub pominięte jako wartości domyślne to true. Wylogowywanie sesji SAML wymaga `SessionIndex` i `NameID` do ukończenia.

