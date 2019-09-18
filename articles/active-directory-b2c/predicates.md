---
title: Predykaty i PredicateValidations Azure Active Directory B2C | Microsoft Docs
description: Przykłady transformacji oświadczeń konta społecznościowego dla schematu struktury środowiska tożsamości Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ecec18945b53711094307162c4aeab2e0580bd5e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063851"
---
# <a name="predicates-and-predicatevalidations"></a>Predykaty i PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predykaty** i elementy **PredicateValidations** umożliwiają wykonanie procesu walidacji w celu upewnienia się, że w dzierżawie Azure Active Directory B2C (Azure AD B2C) są wprowadzane tylko prawidłowo uformowane dane.

Na poniższym diagramie przedstawiono relację między elementami:

![Diagram przedstawiający predykaty i relacje walidacji predykatu](./media/predicates/predicates.png)

## <a name="predicates"></a>Predykaty

Element **predykatu** definiuje podstawową walidację, aby sprawdzić wartość typu i zwraca `true` lub. `false` Walidacja jest wykonywana przy użyciu określonego elementu **metody** i zestawu elementów **parametrów** odpowiednich dla metody. Na przykład predykat może sprawdzić, czy długość wartości żądania ciągu mieści się w zakresie minimalnym i maksymalnym określonym parametrem lub czy wartość żądania ciągu zawiera zestaw znaków. Element **UserHelpText** zawiera komunikat o błędzie dla użytkowników, jeśli sprawdzenie zakończy się niepowodzeniem. Wartość elementu **UserHelpText** można lokalizować przy użyciu [dostosowywania języka](localization.md).

Element **predykatów** zawiera następujący element:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Predykat | 1: n | Lista predykatów. |

Element **predykatu** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany dla predykatu. Inne elementy mogą używać tego identyfikatora w zasadach. |
| Metoda | Tak | Typ metody do użycia na potrzeby walidacji. Możliwe wartości: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**lub **IsDateRange**. Wartość **IsLengthRange** sprawdza, czy długość wartości żądania ciągu mieści się w zakresie określonych parametrów minimum i maksimum. Wartość **MatchesRegex** sprawdza, czy wartość żądania ciągu pasuje do wyrażenia regularnego. Wartość **IncludesCharacters** sprawdza, czy wartość żądania ciągu zawiera zestaw znaków. Wartość **IsDateRange** sprawdza, czy wartość żądania daty należy do zakresu od określonych parametrów minimum i maksimum. |

Element **predykatu** zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Komunikat o błędzie dla użytkowników, jeśli sprawdzenie zakończy się niepowodzeniem. Ten ciąg może być lokalizowany przy użyciu [dostosowania języka](localization.md) |
| Parametry | 1:1 | Parametry typu metody weryfikacji ciągu. |

Element **Parameters** zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Parametr | 1: n | Parametry typu metody weryfikacji ciągu. |

Element **Parameter** zawiera następujące atrybuty:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Id | 1:1 | Identyfikator parametru. |

Poniższy przykład przedstawia `IsLengthRange` metodę z `Maximum` parametrami `Minimum` , które określają zakres długości ciągu:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

Poniższy przykład przedstawia `MatchesRegex` metodę z parametrem `RegularExpression` , który określa wyrażenie regularne:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

Poniższy przykład przedstawia `IncludesCharacters` metodę z parametrem `CharacterSet` , który określa zestaw znaków:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

Poniższy przykład `IsDateRange` przedstawia metodę z `Maximum` parametrami `Minimum` , które `yyyy-MM-dd` określają zakres dat z formatem i `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Podczas gdy predykaty definiują walidację w celu sprawdzenia typu, Grupa **PredicateValidations** zestawu predykatów do tworzenia walidacji danych wejściowych użytkownika, które można zastosować do typu zgłoszenia. Każdy element **PredicateValidation** zawiera zestaw elementów **predykatu** , które zawierają zestaw elementów **PredicateReference** , które wskazują **predykat**. Aby przekazać weryfikację, wartość żądania powinna przekazać wszystkie testy dowolnego predykatu w ramach całej grupy **predykatów** z ich zestawem elementów **PredicateReference** .

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

Element **PredicateValidations** zawiera następujący element:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Lista walidacji predykatu. |

Element **PredicateValidation** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany na potrzeby walidacji predykatu. Element **ClaimType** może używać tego identyfikatora w zasadach. |

Element **PredicateValidation** zawiera następujący element:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Lista grup predykatów. |

Element **PredicateGroups** zawiera następujący element:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Predykat | 1: n | Lista predykatów. |

Element grupy predykatów zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany dla grupy predykatu.  |

Element grupy predykatów zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Opis predykatu, który może być przydatny dla użytkowników, aby wiedzieć, jaka wartość powinna być typu. |
| PredicateReferences | 1: n | Lista odwołań predykatu. |

Element **PredicateReferences** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| MatchAtLeast | Nie | Określa, że wartość musi być zgodna co najmniej z wieloma definicjami predykatów dla danych wejściowych, które mają zostać zaakceptowane. |

Element **PredicateReferences** zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Odwołanie do predykatu. |

Element **PredicateReference** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany na potrzeby walidacji predykatu.  |


## <a name="configure-password-complexity"></a>Konfigurowanie złożoności hasła

Przy użyciu predykatów i **PredicateValidationsInput** można kontrolować wymagania dotyczące złożoności dla haseł dostarczonych przez użytkownika podczas tworzenia konta. Domyślnie Azure AD B2C używa silnych haseł. Azure AD B2C obsługuje również opcje konfiguracji w celu kontrolowania złożoności haseł, które mogą być używane przez klientów. Złożoność hasła można zdefiniować przy użyciu następujących elementów predykatu:

- **IsLengthBetween8And64** przy użyciu `IsLengthRange` metody, sprawdza, czy hasło musi zawierać od 8 do 64 znaków.
- **Małe** litery przy `IncludesCharacters` użyciu metody, sprawdza, czy hasło zawiera małą literę.
- **Wielkie litery** przy `IncludesCharacters` użyciu metody, sprawdza, czy hasło zawiera wielką literę.
- **Liczba** przy użyciu `IncludesCharacters` metody, sprawdza, czy hasło zawiera cyfrę.
- **Symbol** przy użyciu `IncludesCharacters` metody, sprawdza, czy hasło zawiera jeden z następujących symboli`@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **Przypnij** przy użyciu `MatchesRegex` metody, sprawdza, czy hasło zawiera tylko cyfry.
- **AllowedAADCharacters** przy użyciu `MatchesRegex` metody, sprawdza, czy podano tylko nieprawidłowy znak.
- **DisallowedWhitespace** przy użyciu `MatchesRegex` metody, sprawdza, czy hasło nie zaczyna się ani nie kończy znakiem odstępu.

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

Po zdefiniowaniu podstawowych walidacji można połączyć je ze sobą i utworzyć zestaw zasad haseł, których można użyć w ramach zasad:

- **SimplePassword** sprawdza poprawność DisallowedWhitespace, AllowedAADCharacters i IsLengthBetween8And64
- **Strongpassword** sprawdza poprawność DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Ostatnia Grupa `CharacterClasses` uruchamia dodatkowy zestaw predykatów z `MatchAtLeast` ustawioną na 3. Hasło użytkownika musi mieć długość od 8 do 16 znaków i zawierać trzy z następujących znaków: Małe litery, wielkie litery, cyfry lub symbole.
- **CustomPassword** weryfikuje tylko DisallowedWhitespace, AllowedAADCharacters. W takim przypadku użytkownik może podać dowolne hasło o dowolnej długości, o ile znaki są prawidłowe.

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

W polu Typ zgłoszenia Dodaj element **PredicateValidationReference** i określ identyfikator jako jedną z walidacji predykatów, takich jak SimplePassword, strongpassword lub CustomPassword.

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

Poniżej pokazano, jak elementy są zorganizowane, gdy Azure AD B2C wyświetla komunikat o błędzie:

![Diagram przykładu złożoności hasła predykatu i predykatu](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurowanie zakresu dat

Za pomocą **predykatów** i elementów **PredicateValidations** można kontrolować minimalną i maksymalną wartość daty `DateTimeDropdown` **UserInputType** przy użyciu. W tym celu należy utworzyć **predykat** z `IsDateRange` metodą i podać parametry minimalne i maksymalne.

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

Dodaj element **PredicateValidation** z odwołaniem do `DateRange` predykatu.

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

W polu Typ zgłoszenia Dodaj element **PredicateValidationReference** i określ identyfikator jako `CustomDateRange`.

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
