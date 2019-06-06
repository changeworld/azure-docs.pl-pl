---
title: Informacje o profilach techniczne w zasadach niestandardowych usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat profili sposobu techniczne są używane w przypadku zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 80b8969ba657506705db2b1a3bbc5b389d0a992c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512454"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informacje o profilach techniczne w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny zapewnia platforma wbudowany mechanizm do komunikowania się z innego typu strony za pomocą zasad niestandardowych w usłudze Azure Active Directory (Azure AD) B2C. Profile techniczne są używane do komunikowania się z dzierżawą usługi Azure AD B2C, aby utworzyć użytkownika lub odczytuj profil użytkownika. Profil techniczny można samodzielnie, aby umożliwić interakcję z użytkownikiem. Na przykład zbieranie poświadczeń użytkownika do logowania i renderowania strony tworzenia konta lub strony resetowania hasła. 

## <a name="type-of-technical-profiles"></a>Typ profile techniczne

Profil techniczny umożliwia tego rodzaju scenariuszy:

- [Usługa Azure Active Directory](active-directory-technical-profile.md) — zapewnia obsługę zarządzania użytkownikami w usłudze Azure Active Directory B2C.
- [Wystawca tokenu JWT](jwt-issuer-technical-profile.md) -emituje token JWT, która jest zwracana do aplikacji jednostki uzależnionej. 
- **Dostawcy usług dwuskładnikowych Phone** — uwierzytelnianie wieloskładnikowe.
- [OAuth1](oauth1-technical-profile.md) -Federacji za pomocą dowolnego dostawcy tożsamości protokołu OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) -Federacji za pomocą dowolnego dostawcy tożsamości protokołu OAuth 2.0.
- [OpenIdConnect](openid-connect-technical-profile.md) -Federacji za pomocą dowolnego dostawcy tożsamości protokołu OpenId Connect.
- [Przekształcanie oświadczeń](claims-transformation-technical-profile.md) — przekształcenia oświadczeń danych wyjściowych wywołania do manipulowania oświadczeń wartości, weryfikowania oświadczeń lub ustawić wartości domyślne dla zestawu oświadczeń wyjściowych.
- [Dostawca typu restful](restful-technical-profile.md) — wywołania interfejsu API REST usług, takich jak sprawdzanie poprawności danych wejściowych użytkownika, Wzbogacanie danych użytkownika lub integrowanie ich z aplikacji — biznesowych.
- [SAML2](saml-technical-profile.md) -Federacji za pomocą dowolnego dostawcy tożsamości protokołu SAML.
- [Samodzielnie określonych](self-asserted-technical-profile.md) -interakcji z użytkownikiem. Na przykład zbieranie poświadczeń użytkownika do logowania, renderowania strony tworzenia konta lub resetowania hasła.
- **WsFed** -Federacji za pomocą dowolnego dostawcy tożsamości protokołu WsFed. 
- [Zarządzanie sesjami](active-directory-b2c-reference-sso-custom.md) -obsługi różnych typów sesji. 
- **Usługa Application insights**

## <a name="technical-profile-flow"></a>Profil techniczny przepływu

Wszystkie typy profile techniczne współużytkować tę samą koncepcję. Wysyłanie oświadczeń wejściowych, uruchom przekształcania oświadczeń i komunikować się ze stroną skonfigurowany, takich jak dostawcy tożsamości, interfejs API REST lub usług katalogu usługi Azure AD. Po zakończeniu procesu profilu technicznego zwraca oświadczeń danych wyjściowych i mogą być uruchamiane przekształcania oświadczeń danych wyjściowych. Na poniższym diagramie przedstawiono, jak są przetwarzane przekształceń i mapowania, do którego odwołuje się profil techniczny. Niezależnie od innych firm, gdy profil techniczny wchodzi w interakcję, po wszelkie oświadczenia, które jest wykonywane przekształcenia oświadczeń danych wyjściowych z profilu technicznego natychmiast są przechowywane w zbiorze oświadczeń. 

![Profil techniczny przepływu](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** -wejściowych roszczenia, każdy danych wejściowych [przekształcania oświadczeń](claimstransformations.md) są pobierane ze zbioru oświadczenia, wykonywania i po nim, oświadczenia są ponownie umieszczane w zbiorze oświadczeń danych wyjściowych. Przekształcenia oświadczeń wejściowych oświadczeń danych wyjściowych może być oświadczeń wejściowych transformacji kolejnych oświadczeń wejściowych.
2. **InputClaims** — oświadczeń są pobierane ze zbioru oświadczeń i służą do profilu technicznego. Na przykład [własnym potwierdzone profilu technicznego](self-asserted-technical-profile.md) używa danych wejściowych oświadczeń, aby wstępnie wypełnić oświadczeń danych wyjściowych, które użytkownik udostępnia. Profil techniczny interfejsu API REST używa oświadczeń wejściowych do wysyłania parametrów wejściowych do punktu końcowego interfejsu API REST. Usługa Azure Active Directory używa oświadczeń wejściowych jako unikatowy identyfikator do odczytywania, aktualizowania lub usuwania konta.
3. **Profil techniczny wykonywania** — profil techniczny wymienia oświadczenia, za pomocą skonfigurowanych firm. Na przykład:
    - Przekierowanie użytkownika do dostawcy tożsamości, aby zakończyć logowanie. Po pomyślnym zalogowaniu użytkownik zwraca się ponownie, a następnie kontynuuje wykonywanie profilu technicznego.
    - Podczas wysyłania parametry jako InputClaims i pobieranie informacji o zwrotnym w formie OutputClaims, należy wywołać interfejs API REST.
    - Utwórz lub zaktualizuj konto użytkownika.
    - Wysyła i weryfikuje wiadomości SMS usługi MFA.
4. **ValidationTechnicalProfiles** — [samodzielnie określonych profilu technicznego](self-asserted-technical-profile.md), można wywołać dane wejściowe [profilu technicznego weryfikacji](validation-technical-profile.md). Profil techniczny weryfikacji sprawdza poprawność danych profilowanych przez użytkownika i zwraca komunikat o błędzie, lub przycisk Ok, z lub bez oświadczeń danych wyjściowych. Na przykład zanim usługa Azure AD B2C utworzy nowe konto, sprawdza czy użytkownik istnieje już w usługach katalogowych. Możesz wywołać profilu technicznego interfejsu API REST, aby dodać własną logiką biznesową.<p>Zakres oświadczeń danych wyjściowych profilu technicznego sprawdzania poprawności jest ograniczone do profilu technicznego, wywołująca profilu technicznego sprawdzania poprawności i inne sprawdzania poprawności profile techniczne w ramach tego samego profilu technicznego. Korzystanie z oświadczeń danych wyjściowych w następnym kroku aranżacji, musisz dodać oświadczeń danych wyjściowych do profilu technicznego, wywołująca profilu technicznego sprawdzania poprawności.
5. **OutputClaims** -oświadczenia są przywracane do zbioru oświadczeń. Możesz użyć tych oświadczeń w następnym kroku aranżacji lub przekształcenia oświadczeń danych wyjściowych.
6. **OutputClaimsTransformations** -wejściowych roszczenia, każdy danych wyjściowych [przekształcania oświadczeń](claimstransformations.md) są pobierane ze zbioru oświadczeń. Oświadczeń danych wyjściowych profilu technicznego z poprzednich kroków może być oświadczeń wejściowych przekształcenia oświadczeń danych wyjściowych. Po wykonaniu oświadczeń danych wyjściowych są ponownie umieszczane w zbiorze oświadczeń. Oświadczeń danych wyjściowych przekształcania oświadczeń danych wyjściowych może być również oświadczeń wejściowych przekształcania oświadczeń kolejne dane wyjściowe.
7. **Single Zarządzanie sesjami logowania jednokrotnego (SSO)**  - [Zarządzanie sesjami logowania jednokrotnego](active-directory-b2c-reference-sso-custom.md) kontrolki interakcji z użytkownikiem, po użytkownik już uwierzytelniony. Na przykład administrator może kontrolować, czy ma być wyświetlana wyboru dostawcy tożsamości lub tego, czy szczegóły konta lokalnego muszą zostać wprowadzone ponownie.

Profil techniczny może dziedziczyć z innego profilu technicznego, aby zmienić ustawienia, lub Dodaj nowe funkcje.  **IncludeTechnicalProfile** element jest odwołanie do bazowej profilu technicznego, z którego pochodzi profilu technicznego.  

Na przykład **AAD-UserReadUsingAlternativeSecurityId — brak błędu** zawiera profilu technicznego **AAD UserReadUsingAlternativeSecurityId**. Ustawia ten profil techniczny **RaiseErrorIfClaimsPrincipalDoesNotExist** elementu metadanych do `true`i zgłasza błąd, jeśli w katalogu nie ma konta w sieci społecznościowej. **AAD — UserReadUsingAlternativeSecurityId — brak błędu** zastąpienia tego zachowania i wyłącza komunikat o błędzie, jeśli użytkownik nie istniało.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
``` 

**AAD — UserReadUsingAlternativeSecurityId** obejmuje `AAD-Common` profilu technicznego.

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

Zarówno **AAD-UserReadUsingAlternativeSecurityId — brak błędu** i **AAD UserReadUsingAlternativeSecurityId** określać wymagane **protokołu** element ponieważ jest ono określone w **typowe usługi AAD** profilu technicznego.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Profil techniczny może obejmować lub dziedziczą innego profilu technicznego, który może zawierać inny. Nie ma żadnego limitu liczby poziomów. W zależności od wymagań biznesowych swoją podróż po użytkownik może wywołać **AAD UserReadUsingAlternativeSecurityId** zgłasza błąd, jeśli nie ma konta użytkownika w sieci społecznościowej, lub  **AAD — UserReadUsingAlternativeSecurityId — brak błędu** której nie zgłosić błąd.











