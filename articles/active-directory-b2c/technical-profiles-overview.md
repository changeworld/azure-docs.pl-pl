---
title: Przegląd profili technicznych w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak profile techniczne są używane w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057310"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Profile techniczne w zasadach niestandardowych usługi Azure Active Directory B2C — informacje

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny zapewnia strukturę z wbudowanym mechanizmem do komunikowania się z różnymi typami stron przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C (Azure AD B2C). Profile techniczne są używane do komunikowania się z dzierżawą usługi Azure AD B2C, do tworzenia użytkownika lub odczytu profilu użytkownika. Profil techniczny może być samodzielnie potwierdzony, aby umożliwić interakcję z użytkownikiem. Na przykład zebrać poświadczenia użytkownika, aby zalogować się, a następnie renderowania strony rejestracji lub strony resetowania hasła.

## <a name="type-of-technical-profiles"></a>Rodzaj profili technicznych

Profil techniczny umożliwia tego typu scenariusze:

- [Usługa Application Insights](application-insights-technical-profile.md) — wysyłanie danych zdarzeń do [usługi Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Usługa Azure Active Directory](active-directory-technical-profile.md) — zapewnia obsługę zarządzania użytkownikami usługi Azure Active Directory B2C.
- [Uwierzytelnianie wieloskładnikowe platformy Azure](multi-factor-auth-technical-profile.md) — zapewnia obsługę weryfikacji numeru telefonu przy użyciu usługi Azure Multi-Factor Authentication (MFA). 
- [Transformacja oświadczeń](claims-transformation-technical-profile.md) — wywołania przekształceń oświadczeń wyjściowych w celu manipulowania wartościami oświadczeń, sprawdzania poprawności oświadczeń lub ustawiania wartości domyślnych dla zestawu oświadczeń danych wyjściowych.
- [Wystawca tokenu JWT](jwt-issuer-technical-profile.md) — emituje token JWT, który jest zwracany z powrotem do aplikacji jednostki uzależnień.JWT token issuer - Emits a JWT token that is returned back to the relying party application.
- [OAuth1](oauth1-technical-profile.md) - Federacja z dowolnym dostawcą tożsamości protokołu OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) - Federacja z dowolnym dostawcą tożsamości protokołu OAuth 2.0.
- [Hasło jednorazowe](one-time-password-technical-profile.md) — zapewnia obsługę zarządzania generowaniem i weryfikacją hasła jednorazowego.
- [OpenID Connect](openid-connect-technical-profile.md) — federacja z dowolnym dostawcą tożsamości protokołu OpenID Connect.
- [Współczynnik telefonu](phone-factor-technical-profile.md) — obsługa rejestrowania i weryfikowania numerów telefonów.
- [DOSTAWCA RESTful](restful-technical-profile.md) — wywoływanie do usług interfejsu API REST, takich jak sprawdzanie poprawności danych wejściowych użytkownika, wzbogacanie danych użytkownika lub integracja z aplikacjami biznesowymi.
- [SAML2](saml-technical-profile.md) - Federacja z dowolnym dostawcą tożsamości protokołu SAML.
- [Wystawca tokenu SAML](saml-issuer-technical-profile.md) — emituje token SAML, który jest zwracany z powrotem do aplikacji jednostki uzależnień.
- [Self-Asserted](self-asserted-technical-profile.md) - Interakcja z użytkownikiem. Na przykład zebrać poświadczenia użytkownika, aby zalogować się, renderować stronę rejestracji lub resetowanie hasła.
- [Zarządzanie sesjami](custom-policy-reference-sso.md) — obsługa różnych typów sesji.

## <a name="technical-profile-flow"></a>Przepływ profilu technicznego

Wszystkie typy profili technicznych mają tę samą koncepcję. Wysyłasz oświadczenia wejściowe, uruchamiasz transformację oświadczeń i komunikujesz się ze skonfigurowaną stroną, taką jak dostawca tożsamości, interfejs API REST lub usługi katalogowe usługi Azure AD. Po zakończeniu procesu profil techniczny zwraca oświadczenia wyjściowe i może uruchomić transformację oświadczeń wyjściowych. Na poniższym diagramie przedstawiono sposób przetwarzania przekształceń i mapowań, do których odwołuje się profil techniczny. Niezależnie od strony profil techniczny współdziała z, po wykonaniu transformacji oświadczeń, oświadczeń wyjściowych z profilu technicznego są natychmiast przechowywane w torbie oświadczeń.

![Wykres przedstawiający przepływ profilu technicznego](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Zarządzanie sesjami logowania jednokrotnego —** przywraca stan sesji profilu technicznego przy użyciu [zarządzania sesjami logowania jednokrotnego](custom-policy-reference-sso.md).
1. **Transformacja oświadczeń wejściowych** — oświadczenia wejściowe każdej [transformacji oświadczeń](claimstransformations.md) wejściowych są pobierane z worka oświadczeń.  Oświadczenia wyjściowe transformacji oświadczeń wejściowych mogą być oświadczeniami wejściowymi późniejszego przekształcenia oświadczeń wejściowych.
1. **Oświadczenia wejściowe** — oświadczenia są pobierane z worka z roszczeniami i są używane dla profilu technicznego. Na przykład [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) używa oświadczeń wejściowych do wstępnego wypełniania oświadczeń danych wyjściowych, które użytkownik dostarcza. Profil techniczny interfejsu API REST używa oświadczeń wejściowych do wysyłania parametrów wejściowych do punktu końcowego interfejsu API REST. Usługa Azure Active Directory używa oświadczenia wejściowego jako unikatowego identyfikatora do odczytu, aktualizacji lub usunięcia konta.
1. **Wykonanie profilu technicznego** — profil techniczny wymienia oświadczenia ze skonfigurowaną stroną. Przykład:
    - Przekieruj użytkownika do dostawcy tożsamości, aby zakończyć logowanie. Po pomyślnym zalogowaniu użytkownik powraca, a wykonanie profilu technicznego jest kontynuowane.
    - Wywołanie interfejsu API REST podczas wysyłania parametrów jako InputClaims i uzyskiwania informacji z powrotem jako OutputClaims.
    - Utwórz lub zaktualizuj konto użytkownika.
    - Wysyła i weryfikuje wiadomość tekstową usługi MFA.
1. **Sprawdzanie poprawności profili technicznych** — [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) może wywołać profile techniczne sprawdzania [poprawności](validation-technical-profile.md). Profil techniczny sprawdzania poprawności sprawdza poprawność danych profilowanych przez użytkownika i zwraca komunikat o błędzie lub Ok, z lub bez oświadczeń wyjściowych. Na przykład przed azure AD B2C tworzy nowe konto, sprawdza, czy użytkownik już istnieje w usługach katalogowych. Można wywołać profil techniczny interfejsu API REST, aby dodać własną logikę biznesową.<p>Zakres oświadczeń wyjściowych profilu technicznego sprawdzania poprawności jest ograniczony do profilu technicznego, który wywołuje profil techniczny sprawdzania poprawności. i innych profili technicznych walidacji o tym samym profilu technicznym. Jeśli chcesz użyć oświadczeń danych wyjściowych w następnym kroku aranżacji, należy dodać oświadczenia wyjściowe do profilu technicznego, który wywołuje profil techniczny sprawdzania poprawności.
1. **Oświadczenia wyjściowe** — oświadczenia są zwracane z powrotem do worka oświadczeń. Można użyć tych oświadczeń w następnym kroku aranżacji lub przekształcenia oświadczeń wyjściowych.
1. **Transformacje oświadczeń wyjściowych** — oświadczenia wejściowe każdej [transformacji oświadczeń](claimstransformations.md) wyjściowych są pobierane z worka oświadczeń. Oświadczenia wyjściowe profilu technicznego z poprzednich kroków mogą być oświadczeń wejściowych transformacji oświadczeń danych wyjściowych. Po wykonaniu oświadczeń wyjściowych są umieszczane z powrotem w torbie oświadczeń. Oświadczenia wyjściowe transformacji oświadczeń wyjściowych mogą być również oświadczenia wejściowe kolejnych przekształceń oświadczeń danych wyjściowych.
1. **Zarządzanie sesjami logowania jednokrotnego —** dane profilu technicznego z sesji , przy użyciu [zarządzania sesjami logowania jednokrotnego](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Włączenie profilu technicznego

Profil techniczny może zawierać inny profil techniczny, aby zmienić ustawienia lub dodać nowe funkcje.  Element `IncludeTechnicalProfile` jest odniesieniem do podstawowego profilu technicznego, z którego pochodzi profil techniczny. Nie ma ograniczeń co do liczby poziomów.

Na przykład profil techniczny **AAD-UserReadUsingAlternativeSecurityId-NoError** zawiera identyfikator techniczny **AAD-UserReadUsingAlternativeSecurityId**. Ten profil techniczny `RaiseErrorIfClaimsPrincipalDoesNotExist` ustawia element `true`metadanych na , i wywołuje błąd, jeśli konto społecznościowe nie istnieje w katalogu. **AAD-UserReadUsingAlternativeSecurityId-NoError** zastępuje to zachowanie i wyłącza ten komunikat o błędzie.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** zawiera `AAD-Common` profil techniczny.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Zarówno **AAD-UserReadUsingAlternativeSecurityId-NoError** i **AAD-UserReadUsingAlternativeSecurityId** nie określają wymagany element **protokołu,** ponieważ jest określony w profilu **technicznym AAD-Common.**

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
