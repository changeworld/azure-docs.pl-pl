---
title: Predykaty i PredicateValidations — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Konta społecznościowego oświadczeń przykłady przekształcania tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0dfe6553778092c33f9e1bd55ac7a7ae65137a6e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881005"
---
# <a name="predicates-and-predicatevalidations"></a>Predykaty i PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predykaty** i **PredicateValidations** elementy umożliwiają wykonywanie procesu weryfikacji, aby upewnić się, tylko poprawnie sformułowany danych wprowadzonych do dzierżawy usługi Azure Active Directory (Azure AD) B2C .  

Na poniższym diagramie przedstawiono relację między elementami:  

![Predykaty](./media/predicates/predicates.png)

## <a name="predicates"></a>Predykaty  

**Predykatu** element określa podstawowe sprawdzanie poprawności, aby sprawdzić wartość typu oświadczenia i zwraca `true` lub `false`. Sprawdzanie poprawności jest wykonywane przy użyciu określonego **metoda** elementu oraz zestaw **parametru** elementy odpowiednie do metody. Na przykład predykat można sprawdzić, czy długość wartości oświadczeń ciąg jest w zakresie minimalnej i maksymalnej parametry określone lub tego, czy wartość oświadczenia ciąg zawiera zestaw znaków. **UserHelpText** element zawiera komunikat o błędzie dla użytkowników, jeśli sprawdzenie zakończy się niepowodzeniem. Wartość **UserHelpText** element może być lokalizowane za pomocą [Dostosowywanie języka](localization.md).

**Predykaty** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Predykat | 1: n | Lista predykatów. | 

**Predykatu** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator, który jest używany dla predykatu. Inne elementy, można użyć tego identyfikatora w zasadach. |
| Metoda | Yes | Typ metody do użycia w celu weryfikacji. Możliwe wartości: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**, lub **IsDateRange**. **IsLengthRange** wartość sprawdza, czy długość wartości oświadczeń ciągu w zakresie minimalnej i maksymalnej parametry określone. **MatchesRegex** wartość sprawdza, czy wartość oświadczenia ciąg pasuje do wyrażenia regularnego. **IncludesCharacters** wartość sprawdza, czy wartość oświadczenia ciąg zawiera zestaw znaków. **IsDateRange** wartość sprawdza, czy wartość oświadczenia daty z zakresu określono parametry minimalną i maksymalną. |

**Predykatu** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Komunikat o błędzie dla użytkowników, jeśli sprawdzenie zakończy się niepowodzeniem. Ten ciąg może być lokalizowane za pomocą [Dostosowywanie języka](localization.md) |
| Parametry | 1:1 | Parametry dla typu metody sprawdzania poprawności ciągu. | 

**Parametry** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Parametr | 1: n | Parametry dla typu metody sprawdzania poprawności ciągu. | 

**Parametru** element zawiera następujące atrybuty:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Identyfikator | 1:1 | Identyfikator parametru. |

W poniższym przykładzie przedstawiono `IsLengthRange` metody z parametrami `Minimum` i `Maximum` określające zakres długości ciągu:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

W poniższym przykładzie przedstawiono `MatchesRegex` metody z parametrem `RegularExpression` określająca wyrażenie regularne:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

W poniższym przykładzie przedstawiono `IncludesCharacters` metody z parametrem `CharacterSet` , który określa zestaw znaków:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

W poniższym przykładzie przedstawiono `IsDateRange` metody z parametrami `Minimum` i `Maximum` określające zakres dat przy użyciu formatu `yyyy-MM-dd` i `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations 

Gdy predykaty zdefiniować weryfikacji sprawdzania typu oświadczenia **PredicateValidations** grupowania zestawu predykaty w celu utworzenia walidacji danych wejściowych użytkownika, który można zastosować do typu oświadczenia. Każdy **PredicateValidation** element zawiera zbiór **PredicateGroup** elementy, które zawiera zbiór **PredicateReference** elementy, które wskazuje **Predykatu**. Aby przekazać sprawdzania poprawności, wartość oświadczenia należy przekazywać wszystkich testów żadnych predykat we wszystkich **PredicateGroup** z ich zestaw **PredicateReference** elementów.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

**PredicateValidations** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Lista sprawdzania poprawności predykatu. | 

**PredicateValidation** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator, który służy do sprawdzania poprawności predykatu. **Oświadczenia** elementu można użyć tego identyfikatora w zasadach. |

**PredicateValidation** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Lista grup predykatu. | 

**PredicateGroups** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| PredicateGroup | 1: n | Lista predykatów. | 

**PredicateGroup** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator, który jest używany dla predykatu grupy.  |

**PredicateGroup** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Opis predykatu, które mogą być przydatne dla użytkowników wiedzieć, jakie mogą wpisywać. | 
| PredicateReferences | 1: n | Lista odwołań predykatu. | 

**PredicateReferences** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| MatchAtLeast | Nie | Określa, że musi ona odpowiadać co najmniej, predykat wiele definicji dla danych wejściowych do zaakceptowania. |

**PredicateReferences** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Odwołanie do predykatu. | 

**PredicateReference** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator, który służy do sprawdzania poprawności predykatu.  |


## <a name="configure-password-complexity"></a>Konfigurowanie złożoność hasła

Za pomocą **predykaty** i **PredicateValidationsInput** można kontrolować wymagań dotyczących złożoności haseł podane przez użytkownika, podczas tworzenia konta. Domyślnie program Azure AD B2C używa silne hasła. Usługa Azure AD B2C obsługuje również opcji konfiguracji służących do kontrolowania złożoności haseł, których klienci mogą używać. Złożoność hasła można zdefiniować, korzystając z tych elementów predykatu: 

- **IsLengthBetween8And64** przy użyciu `IsLengthRange` metoda, sprawdza, czy hasło musi mieć długość od 8 do 64 znaków.
- **Małe** przy użyciu `IncludesCharacters` metody sprawdza, czy hasło zawiera małe litery.
- **Wielkie litery** przy użyciu `IncludesCharacters` metody sprawdza, czy hasło zawiera wielką literę.
- **Liczba** przy użyciu `IncludesCharacters` metody sprawdza, czy hasło zawiera cyfrę.
- **Symbol** przy użyciu `IncludesCharacters` metody sprawdza, czy hasło zawiera jedną z następujących symboli `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **Numer PIN** przy użyciu `MatchesRegex` metody sprawdza, czy hasło zawiera tylko cyfry.
- **AllowedAADCharacters** przy użyciu `MatchesRegex` metody, weryfikuje, że podano hasło tylko nieprawidłowy znak.
- **DisallowedWhitespace** przy użyciu `MatchesRegex` metoda, sprawdza, czy hasło nie rozpoczyna się lub kończy się znakiem odstępu.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
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
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Po zdefiniowaniu podstawowe operacje sprawdzania poprawności, można je połączyć ze sobą i utworzymy zestaw zasad haseł, które można użyć w zasadach:

- **SimplePassword** weryfikuje DisallowedWhitespace, AllowedAADCharacters i IsLengthBetween8And64
- **StrongPassword** weryfikuje IsLengthBetween8And64 DisallowedWhitespace, AllowedAADCharacters,. Ostatnią grupę `CharacterClasses` uruchamia dodatkowy zestaw predykaty z `MatchAtLeast` ustawiony na wartość 3. Hasło użytkownika musi zawierać od 8 do 16 znaków i trzy z następujących znaków: Małe litery, wielkie litery, liczby lub symbolu.
- **CustomPassword** sprawdza tylko DisallowedWhitespace AllowedAADCharacters. Tak użytkownik może podać dowolnym hasłem z dowolnej długości, tak długo, jak znaki są prawidłowe.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

W przypadku danego typu oświadczeń, dodać **PredicateValidationReference** elementu i podaj identyfikator jako jeden z sprawdzania poprawności predykatu, takie jak SimplePassword, StrongPassword lub CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Poniżej przedstawiono, jak elementy są zorganizowane w przypadku usługi Azure AD B2C wyświetla komunikat o błędzie:

![Proces predykatu](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurowanie zakresu dat

Za pomocą **predykaty** i **PredicateValidations** elementy można kontrolować wartości minimalną i maksymalną data **UserInputType** przy użyciu `DateTimeDropdown`. Aby to zrobić, należy utworzyć **predykatu** z `IsDateRange` metody i Podaj parametry minimalną i maksymalną.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Dodaj **PredicateValidation** z odwołaniem do `DateRange` predykat.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

W przypadku danego typu oświadczeń, dodać **PredicateValidationReference** elementu i podaj identyfikator jak `CustomDateRange`. 
    
```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
