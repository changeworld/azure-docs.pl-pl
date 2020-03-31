---
title: Konfigurowanie złożoności haseł przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Jak skonfigurować wymagania dotyczące złożoności haseł przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138438"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie złożoności haseł przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W usłudze Azure Active Directory B2C (Azure AD B2C) można skonfigurować wymagania dotyczące złożoności haseł, które są dostarczane przez użytkownika podczas tworzenia konta. Domyślnie usługa Azure AD B2C używa **silnych** haseł. W tym artykule pokazano, jak skonfigurować złożoność haseł w [zasadach niestandardowych](custom-policy-overview.md). Możliwe jest również skonfigurowanie złożoności hasła w [przepływach użytkowników.](user-flow-password-complexity.md)

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md). Aby zarejestrować się i zalogować za pomocą kont lokalnych, powinny być dostępne robocze zasady niestandardowe.


## <a name="add-the-elements"></a>Dodawanie elementów

Aby skonfigurować złożoność hasła, należy `newPassword` `reenterPassword` zastąpić [typy](claimsschema.md) i oświadczenia odwołaniem do [sprawdzania poprawności predykatu](predicates.md#predicatevalidations). PredicateValidations element grupuje zestaw predykatów do utworzenia sprawdzania poprawności danych wejściowych użytkownika, które mogą być stosowane do typu oświadczenia. Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.

1. Wyszukaj [element BuildingBlocks.](buildingblocks.md) Jeśli element nie istnieje, dodaj go.
1. Znajdź [ClaimsSchema](claimsschema.md) element. Jeśli element nie istnieje, dodaj go.
1. Dodaj `newPassword` i `reenterPassword` oświadczenia do **ClaimsSchema** elementu.

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predykaty](predicates.md) definiuje podstawowe sprawdzanie poprawności, aby sprawdzić wartość typu oświadczenia i zwraca wartość true lub false. Sprawdzanie poprawności odbywa się przy użyciu określonego elementu metody i zestaw parametrów istotnych dla metody. Dodaj następujące predykaty do **BuildingBlocks** elementu, natychmiast `</ClaimsSchema>` po zamknięciu elementu:

    ```XML
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Dodaj następujące sprawdzanie poprawności predykatu do **BuildingBlocks** elementu, `</Predicates>` natychmiast po zamknięciu elementu:

    ```XML
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. Następujące profile techniczne to [profile techniczne usługi Active Directory,](active-directory-technical-profile.md)które odczytują i zapisują dane w usłudze Azure Active Directory. Zastąp te profile techniczne w pliku rozszerzenia. Służy `PersistedClaims` do wyłączania zasad silnego hasła. Znajdź **ClaimsProviders** element.  Dodaj następujących dostawców oświadczeń w następujący sposób:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zapisz plik zasad.

## <a name="test-your-policy"></a>Testowanie zasad

### <a name="upload-the-files"></a>Przekazywanie plików

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **platformę środowiska tożsamości**.
5. Na stronie Zasady niestandardowe kliknij pozycję **Przekaż zasady**.
6. Wybierz **opcję Zastąp zasadę, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions.xml.*
7. Kliknij pozycję **Przekaż**.

### <a name="run-the-policy"></a>Uruchamianie zasad

1. Otwórz zasady rejestracji lub logowania. Na przykład *B2C_1A_signup_signin*.
2. W **przypadku aplikacji**wybierz aplikację, która została wcześniej zarejestrowana. Aby wyświetlić token, powinien `https://jwt.ms`być wyświetlony **adres URL odpowiedzi** .
3. Kliknij pozycję **Uruchom teraz**.
4. Wybierz **pozycję Zarejestruj się teraz**, wprowadź adres e-mail i wprowadź nowe hasło. Wskazówki są prezentowane w zakresie ograniczeń haseł. Zakończ wprowadzanie informacji o użytkowniku, a następnie kliknij przycisk **Utwórz**. Powinna zostać wyświetlna zawartość tokenu, który został zwrócony.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować zmianę hasła przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-password-change.md).
- Dowiedz się więcej o [predykatów](predicates.md) i [PredicateValidations](predicates.md#predicatevalidations) elementów w odwołaniu IEF.
