---
title: Informacje o profilach technicznych w Azure Active Directory B2C zasadach niestandardowych | Microsoft Docs
description: Dowiedz się więcej o tym, jak profile techniczne są używane w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f3be6cebafb6d0f50b5ac9a9e40e5707202ea643
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849435"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informacje o profilach technicznych w Azure Active Directory B2C zasadach niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny zapewnia platformę z wbudowanym mechanizmem do komunikowania się z różnymi rodzajami stron przy użyciu zasad niestandardowych w Azure Active Directory (Azure AD) B2C. Profile techniczne są używane do komunikowania się z dzierżawcą Azure AD B2C, tworzenia użytkownika lub odczytywania profilu użytkownika. Profil techniczny może być samodzielnie potwierdzony w celu umożliwienia interakcji z użytkownikiem. Na przykład Zbierz poświadczenia użytkownika w celu zalogowania się, a następnie wyrenderowania strony rejestracji lub resetowania hasła.

## <a name="type-of-technical-profiles"></a>Typ profilów technicznych

Profil techniczny umożliwia realizację następujących typów scenariuszy:

- [Azure Active Directory](active-directory-technical-profile.md) — zapewnia obsługę Azure Active Directory B2C zarządzania użytkownikami.
- [Wystawca tokenu JWT](jwt-issuer-technical-profile.md) — emituje token JWT, który jest zwracany z powrotem do aplikacji jednostki uzależnionej.
- **Dostawca wskaźnika telefonu** — uwierzytelnianie wieloskładnikowe.
- [OAuth1](oauth1-technical-profile.md) -Federacja z dowolnym dostawcą tożsamości protokołu OAuth 1,0.
- [OAuth2](oauth2-technical-profile.md) -Federacja z dowolnym dostawcą tożsamości protokołu OAuth 2,0.
- [OpenIdConnect](openid-connect-technical-profile.md) -Federacja z dowolnym dostawcą tożsamości protokołu OpenID Connect Connect.
- [Przekształcanie oświadczeń](claims-transformation-technical-profile.md) — Wywołaj przekształcenia oświadczeń wyjściowych na potrzeby manipulowania wartościami oświadczeń, Weryfikuj oświadczenia lub ustaw wartości domyślne dla zestawu oświadczeń wyjściowych.
- [Dostawca RESTful](restful-technical-profile.md) — wywoływanie usług interfejsu API REST, takich jak sprawdzanie danych wejściowych użytkownika, Wzbogacanie danych użytkownika lub Integrowanie z aplikacjami biznesowymi.
- [SAML2](saml-technical-profile.md) -Federacja z dowolnym dostawcą tożsamości protokołu SAML.
- [Samodzielne](self-asserted-technical-profile.md) współpracujące z użytkownikiem. Na przykład Zbierz poświadczenia użytkownika w celu zalogowania się, renderowania strony rejestracji lub resetowania hasła.
- **WsFed** -Federacja z dowolnym dostawcą tożsamości protokołu WsFed.
- [Zarządzanie sesjami](active-directory-b2c-reference-sso-custom.md) — obsługa różnych typów sesji.
- **Application Insights**

## <a name="technical-profile-flow"></a>Przepływ profilu technicznego

Wszystkie typy profilów technicznych mają takie same koncepcje. Wysyłasz oświadczenia wejściowe, uruchamiaj transformację oświadczeń i Komunikuj się ze skonfigurowanymi stronami, takimi jak dostawca tożsamości, interfejs API REST lub usługi katalogowe Azure AD. Po zakończeniu procesu profil techniczny zwróci oświadczenia wyjściowe i może uruchomić transformację oświadczeń wyjściowych. Na poniższym diagramie pokazano, w jaki sposób przekształcenia i mapowania, do których odwołuje się profil techniczny, są przetwarzane. Bez względu na to, że profil techniczny współdziała z programem, po wykonaniu dowolnego przekształcenia oświadczeń dane wyjściowe z profilu technicznego są natychmiast przechowywane w zbiorze oświadczeń.

![Diagram ilustrujący przepływ profilu technicznego](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** — oświadczenia wejściowe dla każdej [transformacji oświadczeń](claimstransformations.md) wejściowych są pobierane z zbioru oświadczeń i po wykonaniu oświadczenia wyjściowe są umieszczane w zbiorze oświadczeń. Oświadczenia wyjściowe przekształcenia oświadczeń wejściowych mogą być oświadczeniami wejściowymi kolejnych transformacji oświadczeń wejściowych.
2. **InputClaims** — oświadczenia są pobierane z zbioru oświadczeń i są używane w profilu technicznym. Na przykład [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) używa oświadczeń wejściowych do wstępnego wypełniania oświadczeń wyjściowych dostarczanych przez użytkownika. Profil techniczny interfejsu API REST używa oświadczeń wejściowych do wysyłania parametrów wejściowych do punktu końcowego interfejsu API REST. Azure Active Directory używa jako unikatowego identyfikatora do odczytywania, aktualizowania lub usuwania konta.
3. **Wykonywanie profilu technicznego** — profil techniczny wymienia oświadczenia ze skonfigurowaną stroną. Przykład:
    - Przekieruj użytkownika do dostawcy tożsamości, aby zakończyć logowanie. Po pomyślnym zalogowaniu użytkownik wraca do tyłu, a wykonywanie profilu technicznego będzie kontynuowane.
    - Wywołaj interfejs API REST podczas wysyłania parametrów jako InputClaims i pobierania informacji z powrotem jako OutputClaims.
    - Utwórz lub zaktualizuj konto użytkownika.
    - Wysyła i weryfikuje wiadomość tekstową usługi MFA.
4. **ValidationTechnicalProfiles** — w przypadku [samodzielnego profilu technicznego](self-asserted-technical-profile.md)można wywołać [profil techniczny weryfikacji](validation-technical-profile.md)danych wejściowych. Profil techniczny weryfikacji weryfikuje dane profilowane przez użytkownika i zwraca komunikat o błędzie lub OK, z lub bez oświadczeń wyjściowych. Na przykład przed Azure AD B2C tworzenia nowego konta sprawdza, czy użytkownik już istnieje w usługach katalogowych. Aby dodać własną logikę biznesową, można wywołać profil techniczny interfejsu API REST.<p>Zakres oświadczeń wyjściowych dla profilu technicznego weryfikacji jest ograniczony do profilu technicznego, który wywołuje profil techniczny weryfikacji i inne profile techniczne weryfikacji w ramach tego samego profilu technicznego. Jeśli chcesz użyć oświadczeń wyjściowych w następnym kroku aranżacji, musisz dodać oświadczenia wyjściowe do profilu technicznego, który wywoła profil techniczny weryfikacji.
5. **OutputClaims** — oświadczenia są zwracane z powrotem do zbioru oświadczeń. Możesz użyć tych oświadczeń w następnych krokach aranżacji lub przekształceń oświadczeń wyjściowych.
6. **OutputClaimsTransformations** — oświadczenia wejściowe wszystkich przekształceń [oświadczeń](claimstransformations.md) wyjściowych są pobierane z zbioru oświadczeń. Oświadczenia wyjściowe profilu technicznego z poprzednich kroków mogą być oświadczeniami wejściowymi transformacji oświadczeń wyjściowych. Po wykonaniu oświadczenia wyjściowe są umieszczane w zbiorze oświadczeń. Oświadczenia wyjściowe transformacji oświadczeń wyjściowych mogą również być oświadczeniami wejściowymi dla kolejnej transformacji oświadczeń wyjściowych.
7.  - Usługa rejestracji jednokrotnej (SSO) Zarządzanie sesją logowania jednokrotnego —[Kontrola interakcji](active-directory-b2c-reference-sso-custom.md) z użytkownikiem po uwierzytelnieniu użytkownika. Administrator może na przykład określić, czy jest wyświetlany wybór dostawców tożsamości, czy też należy wprowadzić ponownie szczegóły konta lokalnego.

Profil techniczny może dziedziczyć z innego profilu technicznego, aby zmienić ustawienia lub dodać nową funkcję.  Element **IncludeTechnicalProfile** jest odwołaniem do podstawowego profilu technicznego, z którego pochodzi profil techniczny.

Na przykład profil techniczny **AAD-UserReadUsingAlternativeSecurityId-NOERROR** obejmuje usługi **AAD-UserReadUsingAlternativeSecurityId**. Ten profil techniczny ustawia element metadanych **RaiseErrorIfClaimsPrincipalDoesNotExist** na `true`i zgłasza błąd, jeśli konto społecznościowe nie istnieje w katalogu. **AAD-UserReadUsingAlternativeSecurityId-NOERROR** zastępuje to zachowanie i wyłącza komunikat o błędzie, jeśli użytkownik nie istniał.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

W przypadku `AAD-Common` usługi **AAD-UserReadUsingAlternativeSecurityId** uwzględniono profil techniczny.

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

W przypadku usługi AAD **-UserReadUsingAlternativeSecurityId-NOERROR** i **AAD-UserReadUsingAlternativeSecurityId** nie należy określać wymaganego elementu **protokołu** , ponieważ jest on określony w profilu technicznym usługi **AAD-Common** .

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Profil techniczny może obejmować lub dziedziczyć inny profil techniczny, który może zawierać kolejny z nich. Nie ma żadnego limitu liczby poziomów. W zależności od wymagań firmy, podróż użytkownika może wywołać **AAD-UserReadUsingAlternativeSecurityId** , który zgłasza błąd, jeśli konto społecznościowe użytkownika nie istnieje lub **AAD-UserReadUsingAlternativeSecurityId-NOERROR** , które nie Zgłoś błąd.











