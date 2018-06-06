---
title: Złożoność hasła w niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak skonfigurować wymagania dotyczące złożoności haseł w zasadach niestandardowych.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6ad205167477715713b58fe06a771c3e683f5c04
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712168"
---
# <a name="configure-password-complexity-in-custom-policies"></a>Skonfigurowania złożoności hasła w ramach zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule jest zaawansowane opis działania złożoności hasła i jest włączony, za pomocą niestandardowych zasad usługi Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Usługa Azure AD B2C: Konfigurowanie wymagań dotyczących złożoności haseł

Usługa Azure Active Directory B2C (Azure AD B2C) obsługuje zmienianie wymagania dotyczące złożoności haseł dostarczone przez użytkownika końcowego podczas tworzenia konta.  Domyślnie używa usługi Azure AD B2C **silne** hasła.  Usługa Azure AD B2C obsługuje również opcje kontroli złożoności haseł, których klienci mogą używać konfiguracji.  Ten artykuł zawiera informacje o sposobu skonfigurowania złożoności hasła w zasadach niestandardowych.  Istnieje również możliwość użycia [skonfigurowania złożoności hasła w wbudowane zasady](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Wymagania wstępne

Dzierżawy usługi Azure AD B2C, skonfigurowany tak, aby ukończyć konta lokalnego konta-konta/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Jak skonfigurować złożoności hasła w zasadach niestandardowych

W celu skonfigurowania złożoności hasła w zasadach niestandardowych, musi zawierać ogólną strukturę zasad niestandardowych `ClaimsSchema`, `Predicates`, i `InputValidations` element wewnątrz `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Przeznaczenie tych elementów jest następujący:

- Każdy `Predicate` element definiuje ciąg podstawowe sprawdzanie poprawności, która zwraca wartość PRAWDA lub FAŁSZ.
- `InputValidations` Element ma co najmniej jeden `InputValidation` elementów.  Każdy `InputValidation` jest tworzony przy użyciu serii `Predicate` elementów. Ten element umożliwia wykonywanie logiczna agregacji (podobnie jak `and` i `or`).
- `ClaimsSchema` Definiuje, które oświadczenia jest sprawdzana.  Które następnie definiuje `InputValidation` reguła jest używana do sprawdzania poprawności tego oświadczenia.

### <a name="defining-a-predicate-element"></a>Definiowanie predykatu elementu

Predykaty ma dwa typy metody: IsLengthRange lub MatchesRegex. Załóżmy Przejrzyj przykład każdego z nich.  Najpierw mamy przykładem MatchesRegex, który służy do dopasowania do wyrażenia regularnego.  W tym przykładzie odpowiada ciąg, który zawiera numery.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Następny umożliwia zapoznaj się z przykładem IsLengthRange.  Ta metoda przyjmuje długość ciągu minimalną i maksymalną.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Użyj `HelpText` atrybutu Podaj komunikat o błędzie dla użytkowników końcowych, jeśli sprawdzenie zakończy się niepowodzeniem.  Ten ciąg może być lokalizowany przy użyciu [funkcji dostosowanie języka](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definiowanie elementu InputValidation

`InputValidation` Jest agregacji `PredicateReferences`. Każdy `PredicateReferences` musi mieć wartość true, aby `InputValidation` powiodło się.  Jednak wewnątrz `PredicateReferences` Użyj elementu o nazwie atrybutu `MatchAtLeast` Aby określić, ile `PredicateReference` kontroli musi zwrócić wartość true.  Opcjonalnie można zdefiniować `HelpText` atrybut do przesłonięcia komunikat o błędzie zdefiniowany w `Predicate` elementów, których się odwołuje.

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

### <a name="defining-a-claimsschema-element"></a>Definiowanie elementu ClaimsSchema

Typy oświadczeń `newPassword` i `reenterPassword` są traktowane jako specjalne, dlatego nie należy zmieniać nazwy.  Interfejs użytkownika weryfikuje użytkownika poprawnie ponownie wprowadzić swoje hasło podczas tworzenia konta w oparciu o te `ClaimType` elementów.  Aby znaleźć takie same `ClaimType` elementów, Szukaj w TrustFrameworkBase.xml w pakiecie programu początkowego.  Nowości w tym przykładzie jest, że firma Microsoft są zastępowanie tych elementów, aby zdefiniować `InputValidationReference`. `ID` Atrybut ten nowy element wskazuje `InputValidation` zdefiniowanego elementu.

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

### <a name="putting-it-all-together"></a>Składanie wszystkiego razem

W tym przykładzie pokazano sposób wszystkich części dopasowania do utworzenia zasad pracy.  Aby użyć w tym przykładzie:

1. Postępuj zgodnie z instrukcjami wstępnymi [wprowadzenie](active-directory-b2c-get-started-custom.md) Aby pobrać, skonfigurować i przekazać TrustFrameworkBase.xml i TrustFrameworkExtensions.xml
1. Utwórz plik SignUporSignIn.xml przy użyciu zawartości przykładzie w tej sekcji.
1. Aktualizowanie, zastępowanie SignUporSignIn.xml `yourtenant` nazwą dzierżawy usługi Azure AD B2C.
1. Przekaż plik zasad SignUporSignIn.xml ostatnio.

W tym przykładzie zawiera weryfikacji haseł numeru pin i jeden dla silne hasła:

- Wyszukaj `PINpassword`. To `InputValidation` element sprawdza poprawność kodu pin o dowolnej długości.  Nie są używane w tej chwili, ponieważ nie odwołuje się do niego `InputValidationReference` element wewnątrz `ClaimType`. 
- Wyszukaj `PasswordValidation`. To `InputValidation` element weryfikuje hasło jest 8 do 16 znaków i zawiera 3 spośród 4 wielkich i małych liter, cyfr lub symboli.  Odwołuje się do niego `ClaimType`.  W związku z tym ta reguła jest wymuszany w tej zasadzie.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
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
