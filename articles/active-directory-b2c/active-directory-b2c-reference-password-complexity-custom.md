---
title: Złożoność hasła jako w zasadach niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak skonfigurować zasady niestandardowe wymagania co do złożoności haseł.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c6b8312a08d1d92bccf70e7d3dda5f01811b4f87
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848531"
---
# <a name="configure-password-complexity-in-custom-policies"></a>Skonfigurować złożoność hasła jako zasady niestandardowe

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule jest zaawansowane opis sposobu działania złożoność hasła i jest włączony, za pomocą zasad niestandardowych usługi Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Usługa Azure AD B2C: Konfigurowanie wymagań dotyczących złożoności haseł

Usługa Azure Active Directory B2C (Azure AD B2C) obsługuje zmieniające się wymagania dotyczące złożoności haseł podane przez użytkownika końcowego podczas tworzenia konta.  Domyślnie program Azure AD B2C używa **silne** hasła.  Usługa Azure AD B2C obsługuje również opcji konfiguracji służących do kontrolowania złożoności haseł, których klienci mogą używać.  W tym artykule opowiada, jak skonfigurować złożoność hasła jako zasady niestandardowe.  Istnieje również możliwość użycia [skonfigurować złożoność hasła jako wbudowane zasady](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Wymagania wstępne

Dzierżawy usługi Azure AD B2C skonfigurowany tak, aby ukończyć konta lokalnego konta-dokonywania/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Jak skonfigurować złożoność hasła jako w zasadach niestandardowych

Aby skonfigurować złożoność hasła jako zasady niestandardowe, mogą zawierać ogólną strukturę zasad niestandardowych `ClaimsSchema`, `Predicates`, i `InputValidations` element wewnątrz `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Przeznaczenie tych elementów jest następująca:

- Każdy `Predicate` element definiuje ciąg podstawowe sprawdzenie poprawności, która zwraca wartość PRAWDA lub FAŁSZ.
- `InputValidations` Element ma co najmniej jeden `InputValidation` elementów.  Każdy `InputValidation` jest konstruowany przy użyciu szeregu `Predicate` elementów. Ten element umożliwia wykonywanie logiczna agregacji (podobnie jak `and` i `or`).
- `ClaimsSchema` Definiuje, które oświadczenia jest weryfikowany.  Następnie definiuje, które `InputValidation` reguła jest używana do zweryfikowania tego oświadczenia.

### <a name="defining-a-predicate-element"></a>Definiowanie predykatu elementu

Predykaty ma dwa typy metoda: IsLengthRange lub MatchesRegex. Omówmy teraz każdy przykład.  Najpierw udostępniamy przykład MatchesRegex, który jest używany do dopasowywania wyrażenia regularnego.  W tym przykładzie dopasowuje ciąg, który zawiera cyfry.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Następny Omówmy przykładem IsLengthRange.  Ta metoda przyjmuje długość ciągu minimalną i maksymalną.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Użyj `HelpText` atrybutu, aby podać komunikat o błędzie dla użytkowników końcowych, jeśli sprawdzenie zakończy się niepowodzeniem.  Ten ciąg może być lokalizowane za pomocą [funkcji dostosowywania języka](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definiowanie elementu InputValidation

`InputValidation` To Agregacja `PredicateReferences`. Każdy `PredicateReferences` musi mieć wartość true, aby `InputValidation` zakończyło się sukcesem.  Jednak wewnątrz `PredicateReferences` użycia elementu o nazwie atrybutu `MatchAtLeast` Aby określić, ile `PredicateReference` kontroli musi zwracać wartość true.  Opcjonalnie można zdefiniować `HelpText` atrybutu, aby zastąpić komunikat o błędzie zdefiniowane w `Predicate` elementów, których się odwołuje.

```XML
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
```

### <a name="defining-a-claimsschema-element"></a>Definiowanie elementów ClaimsSchema

Typy oświadczeń `newPassword` i `reenterPassword` są traktowane jako specjalne, więc nie należy zmieniać nazwy.  Interfejs użytkownika weryfikuje użytkownika poprawnie ponownie wprowadzić swoje hasło podczas tworzenia konta, w oparciu o te `ClaimType` elementów.  Aby znaleźć takie same `ClaimType` elementów, Szukaj w TrustFrameworkBase.xml w dany pakiet startowy.  Co nowego w tym przykładzie jest, że firma Microsoft zastępowanie te elementy, aby zdefiniować `InputValidationReference`. `ID` Atrybut ten nowy element wskazuje `InputValidation` element, który zdefiniowaliśmy.

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

### <a name="putting-it-all-together"></a>Zebranie wszystkich elementów

Ten przykład przedstawia sposób dopasowania wszystkich elementów w celu utworzenia zasady pracy.  Aby użyć tego przykładu:

1. Postępuj zgodnie z instrukcjami wymaganie wstępne [wprowadzenie](active-directory-b2c-get-started-custom.md) do pobrania, należy skonfigurować i przekazać TrustFrameworkBase.xml i TrustFrameworkExtensions.xml
1. Utwórz plik SignUporSignIn.xml przy użyciu przykłady w tej sekcji.
1. Aktualizowanie, zastępowanie SignUporSignIn.xml `yourtenant` nazwą dzierżawy usługi Azure AD B2C.
1. Przekaż plik zasad SignUporSignIn.xml ostatnio.

Ten przykład zawiera weryfikacji haseł numeru pin, a drugi dla silne hasła:

- Wyszukaj `PINpassword`. To `InputValidation` element sprawdza poprawność kodu pin o dowolnej długości.  Nie są używane w tej chwili, ponieważ nie odwołuje się do niego `InputValidationReference` element wewnątrz `ClaimType`. 
- Wyszukaj `PasswordValidation`. To `InputValidation` element sprawdza poprawność hasła to 8 do 16 znaków i zawiera 3 z 4 cyfry, wielkie litery, małe litery lub symboli.  Odwołuje się do niego `ClaimType`.  W związku z tym ta zasada jest wymuszana w tych zasadach.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">[a-z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">[A-Z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">[0-9]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">[!@#$%^*()]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
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
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
