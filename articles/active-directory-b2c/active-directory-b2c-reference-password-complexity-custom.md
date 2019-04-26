---
title: Konfigurowanie złożoność hasła, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak skonfigurować wymagania dotyczące złożoności hasła za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e8e3157bc9dfc97d364effee2ea90cfad85d18ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317143"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie złożoność hasła, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W usłudze Azure Active Directory (Azure AD) B2C można skonfigurować wymagania dotyczące złożoności haseł, które są dostarczane przez użytkownika podczas tworzenia konta. Domyślnie program Azure AD B2C używa **silne** hasła. W tym artykule pokazano, jak w celu skonfigurowania złożoności hasła w [zasady niestandardowe](active-directory-b2c-overview-custom.md). Istnieje również możliwość skonfigurowania złożoności hasła w [przepływy użytkownika](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych w Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Dodawanie elementów

1. Kopiuj *SignUpOrSignIn.xml* pliku pobrany z pakietu startowego, a następnie nadaj mu nazwę *SingUpOrSignInPasswordComplexity.xml*.
2. Otwórz *SingUpOrSignInPasswordComplexity.xml* pliku, a następnie zmień **PolicyId** i **PublicPolicyUri** na nową nazwę zasad. Na przykład *B2C_1A_signup_signin_password_complexity*.
3. Dodaj następujący kod **oświadczenia** elementów przy użyciu identyfikatorów `newPassword` i `reenterPassword`:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Predykaty](predicates.md) typów metoda `IsLengthRange` lub `MatchesRegex`. `MatchesRegex` Typ jest używany do dopasowywania wyrażenia regularnego. `IsLengthRange` Typu ma długość ciągu minimalną i maksymalną. Dodaj **predykaty** elementu **BuildingBlocks** elementu, jeśli nie istnieje następującym **predykatu** elementy:

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Każdy **InputValidation** element jest konstruowany przy użyciu zdefiniowanego **predykatu** elementów. Ten element umożliwia wykonywanie logiczna agregacji, które są podobne do `and` i `or`. Dodaj **InputValidations** elementu **BuildingBlocks** elementu, jeśli nie istnieje następującym **InputValidation** elementu:

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Upewnij się, że **PolicyProfile** profil techniczny zawiera następujące elementy:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Zapisz plik zasad.

## <a name="test-your-policy"></a>Testowanie zasad

Podczas testowania aplikacji w usłudze Azure AD B2C, może być przydatne do ma tokenu usługi Azure AD B2C, powrót do `https://jwt.ms` aby mieć możliwość przejrzenia oświadczeń w nim.

### <a name="upload-the-files"></a>Przekaż pliki

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **struktura środowiska tożsamości**.
5. Na stronie zasad niestandardowych kliknij **zasady przekazywania**.
6. Wybierz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *SingUpOrSignInPasswordComplexity.xml* pliku.
7. Kliknij pozycję **Przekaż**.

### <a name="run-the-policy"></a>Uruchom zasady

1. Otwórz zasady, który został zmodyfikowany. Na przykład *B2C_1A_signup_signin_password_complexity*.
2. Aby uzyskać **aplikacji**, wybierz swoją aplikację, która została wcześniej zarejestrowana. Aby wyświetlić token, **adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
3. Kliknij pozycję **Uruchom teraz**.
4. Wybierz **Zarejestruj się teraz**, wprowadź adres e-mail i wprowadź nowe hasło. Wskazówki dotyczące jest przedstawiona na ograniczenia haseł. Zakończ wprowadzanie informacji użytkownika, a następnie kliknij przycisk **Utwórz**. Powinien zostać wyświetlony zawartości tokenu, który został zwrócony.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [skonfigurować zmiany hasła, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


