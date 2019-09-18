---
title: Konfigurowanie złożoności haseł przy użyciu zasad niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Jak skonfigurować wymagania dotyczące złożoności hasła przy użyciu zasad niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6454d380b0f34e940951e3de44d1dee0ff6b597f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065533"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie złożoności haseł przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W Azure Active Directory B2C (Azure AD B2C) można skonfigurować wymagania dotyczące złożoności haseł dostarczonych przez użytkownika podczas tworzenia konta. Domyślnie Azure AD B2C używa silnych haseł. W tym artykule opisano sposób konfigurowania złożoności haseł w [zasadach niestandardowych](active-directory-b2c-overview-custom.md). Istnieje również możliwość skonfigurowania złożoności haseł w [przepływach użytkowników](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Dodaj elementy

1. Skopiuj pobrany plik *SignUpOrSignIn. XML* z pakietem Starter i nadaj mu nazwę *SingUpOrSignInPasswordComplexity. XML*.
2. Otwórz plik *SingUpOrSignInPasswordComplexity. XML* i zmień wartość **PolicyId** i **PublicPolicyUri** na nową nazwę zasad. Na przykład *B2C_1A_signup_signin_password_complexity*.
3. Dodaj następujące elementy **ClaimType** z identyfikatorami `newPassword` i `reenterPassword`:

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

4. [Predykaty](predicates.md) mają typy `IsLengthRange` metod lub `MatchesRegex`. `MatchesRegex` Typ jest używany do dopasowania wyrażenia regularnego. `IsLengthRange` Typ przyjmuje minimalną i maksymalną długość ciągu. Dodaj element **predykats** do elementu **BuildingBlocks** , jeśli nie istnieje z następującymi elementami predykatu :

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

5. Każdy element **InputValidation** jest konstruowany przy użyciu zdefiniowanych elementów **predykatu** . Ten element umożliwia wykonywanie agregacji logicznych, które są podobne do `and` i. `or` Dodaj element **InputValidations** do elementu **BuildingBlocks** , jeśli nie istnieje z następującym elementem **InputValidation** :

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

6. Upewnij się, że profil techniczny **PolicyProfile** zawiera następujące elementy:

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

Podczas testowania aplikacji w Azure AD B2C może być przydatne, aby token `https://jwt.ms` Azure AD B2C był w stanie przejrzeć oświadczenia w nim.

### <a name="upload-the-files"></a>Przekaż pliki

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **platforma obsługi tożsamości**.
5. Na stronie zasady niestandardowe kliknij pozycję **Przekaż zasady**.
6. Wybierz opcję **Zastąp zasady, jeśli istnieje**, a następnie wyszukaj i wybierz plik *SingUpOrSignInPasswordComplexity. XML* .
7. Kliknij pozycję **Przekaż**.

### <a name="run-the-policy"></a>Uruchom zasady

1. Otwórz zasady, które zostały zmienione. Na przykład *B2C_1A_signup_signin_password_complexity*.
2. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token, należy `https://jwt.ms`wyświetlić **adres URL odpowiedzi** .
3. Kliknij pozycję **Uruchom teraz**.
4. Wybierz pozycję **zarejestruj się teraz**, wprowadź adres e-mail i wprowadź nowe hasło. Wskazówki przedstawiono w temacie ograniczenia dotyczące haseł. Zakończ wprowadzanie informacji o użytkowniku, a następnie kliknij przycisk **Utwórz**. Powinna zostać wyświetlona zawartość zwróconego tokenu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować zmianę hasła przy użyciu zasad niestandardowych w Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


