---
title: Predykaty i predicateValidations
titleSuffix: Azure AD B2C
description: Zapobiegaj dodaniu nieprawidłowo sformułowanych danych do dzierżawy usługi Azure AD B2C przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 887c9432f04cce775e045bb6da83f0af4a4a4bce
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396894"
---
# <a name="predicates-and-predicatevalidations"></a>Predykaty i predicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predykates** i **PredicateValidations** elementów umożliwiają wykonywanie procesu sprawdzania poprawności, aby upewnić się, że tylko poprawnie utworzone dane są wprowadzane do dzierżawy usługi Azure Active Directory B2C (Azure AD B2C).

Na poniższym diagramie przedstawiono relację między elementami:

![Diagram przedstawiający relację Predykaty i Predykat sprawdzania poprawności](./media/predicates/predicates.png)

## <a name="predicates"></a>Predykaty

**Predykat** element definiuje podstawowe sprawdzanie poprawności, aby sprawdzić wartość `true` `false`typu oświadczenia i zwraca lub . Sprawdzanie poprawności odbywa się przy użyciu określonego **elementu Metody** i zestaw **parameter** elementów istotnych dla metody. Na przykład predykat może sprawdzić, czy długość wartości oświadczenia ciągu mieści się w zakresie określonych parametrów minimalnych i maksymalnych, czy też wartość oświadczenia ciągu zawiera zestaw znaków. **UserHelpText** element zawiera komunikat o błędzie dla użytkowników, jeśli sprawdzanie nie powiedzie się. Wartość **elementu UserHelpText** można zlokalizować przy użyciu [dostosowywania języka](localization.md).

**Predykates** element musi pojawić się bezpośrednio po **ClaimsSchema** elementu w [BuildingBlocks](buildingblocks.md) elementu.

**Predykates** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Predykat | 1:n | Lista predykatów. |

**Predykat** element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator, który jest używany dla predykatu. Inne elementy można użyć tego identyfikatora w zasadach. |
| Metoda | Tak | Typ metody do użycia do sprawdzania poprawności. Możliwe wartości: [IsLengthRange](#islengthrange), [MatchesRegex](#matchesregex), [IncludesCharacters](#includescharacters)lub [IsDateRange](#isdaterange).  |
| Helptext | Nie | Komunikat o błędzie dla użytkowników, jeśli sprawdzanie nie powiedzie się. Ten ciąg można zlokalizować przy użyciu [dostosowania języka](localization.md) |

**Predykat** element zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| UserHelpText (Tekst pomocy użytkownika) | 0:1 | (Przestarzałe) Komunikat o błędzie dla użytkowników, jeśli sprawdzanie nie powiedzie się. |
| Parametry | 1:1 | Parametry dla typu metody sprawdzania poprawności ciągu. |

Element **Parametry** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Parametr | 1:n | Parametry dla typu metody sprawdzania poprawności ciągu. |

Element **Parametr** zawiera następujące atrybuty:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Identyfikator | 1:1 | Identyfikator parametru. |

### <a name="predicate-methods"></a>Metody predykatu

#### <a name="islengthrange"></a>IsLengthRange (IsLengthRange)

IsLengthRange Metoda sprawdza, czy długość wartości oświadczenia ciąg ów mieści się w zakresie minimalnych i maksymalnych parametrów określonych. Element predykatu obsługuje następujące parametry:

| Parametr | Wymagany | Opis |
| ------- | ----------- | ----------- |
| Maksimum | Tak | Maksymalna liczba znaków, które można wprowadzić. |
| Minimalne | Tak | Minimalna liczba znaków, które należy wprowadzić. |


Poniższy przykład pokazuje IsLengthRange metody `Minimum` `Maximum` z parametrami i które określają zakres długości ciągu:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>Termin dopasowaniaRegex

MatchRegex Metoda sprawdza, czy wartość oświadczenia ciągu pasuje do wyrażenia regularnego. Element predykatu obsługuje następujące parametry:

| Parametr | Wymagany | Opis |
| ------- | ----------- | ----------- |
| Regularnewygasijenie | Tak | Wzorzec wyrażenia regularnego do dopasowania. |

Poniższy przykład `MatchesRegex` przedstawia metodę `RegularExpression` z parametrem określającym wyrażenie regularne:

```XML
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>ZawieraWykładywacze

Metoda IncludesCharacters sprawdza, czy wartość oświadczenia ciągu zawiera zestaw znaków. Element predykatu obsługuje następujące parametry:

| Parametr | Wymagany | Opis |
| ------- | ----------- | ----------- |
| Characterset | Tak | Zestaw znaków, które można wprowadzić. Na przykład `a-z`małe litery, wielkie `A-Z`litery, `0-9`cyfry lub lista symboli, takich jak `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!`. |

Poniższy przykład `IncludesCharacters` przedstawia metodę `CharacterSet` z parametrem określającym zestaw znaków:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange (IsDateRange)

Metoda IsDateRange sprawdza, czy wartość oświadczenia daty znajduje się między określonym zakresem parametrów minimalnych i maksymalnych. Element predykatu obsługuje następujące parametry:

| Parametr | Wymagany | Opis |
| ------- | ----------- | ----------- |
| Maksimum | Tak | Największa możliwa data, którą można wprowadzić. Format daty następuje `yyyy-mm-dd` konwencji `Today`lub . |
| Minimalne | Tak | Najmniejsza możliwa data, którą można wprowadzić. Format daty następuje `yyyy-mm-dd` konwencji `Today`lub .|

Poniższy przykład `IsDateRange` przedstawia metodę `Minimum` z `Maximum` parametrami i które określają `yyyy-mm-dd` `Today`zakres dat w formacie i .

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Podczas predykatów zdefiniować sprawdzania poprawności względem typu oświadczenia, **PredicateValidations** grupy zestaw predykatów do utworzenia sprawdzania poprawności danych wejściowych użytkownika, które mogą być stosowane do typu oświadczenia. Każdy element **PredicateValidation** zawiera zestaw elementów **PredicateGroup,** które zawierają zestaw elementów **PredicateReference,** który wskazuje **predykat**. Aby przekazać sprawdzanie poprawności, wartość oświadczenia należy przejść wszystkie testy dowolnego predykatu w ramach wszystkich **PredicateGroup** z ich zestaw **PredicateReference** elementów.

**PredicateValidations** element musi pojawić się bezpośrednio po **Predicates** elementu w [BuildingBlocks](buildingblocks.md) elementu.

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

**PredicateValidations** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PredicateValidation (PredicateValidation) | 1:n | Lista sprawdzania poprawności predykatu. |

**Element PredicateValidation** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator, który jest używany do sprawdzania poprawności predykatu. **Element ClaimType** można użyć tego identyfikatora w zasadach. |

**PredicateValidation** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | Lista grup predykatów. |

**PredicateGroups** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Grupa predykatów | 1:n | Lista predykatów. |

**PredicateGroup** element zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator, który jest używany dla grupy predykatu.  |

**PredicateGroup** element zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| UserHelpText (Tekst pomocy użytkownika) | 0:1 |  Opis predykatu, który może być pomocny dla użytkowników, aby wiedzieć, jaką wartość powinni wpisać. |
| PredicateReferences | 1:n | Lista odwołań predykatu. |

**PredicateReferences** element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| MatchAtNajlesz | Nie | Określa, że wartość musi być zgodna co najmniej tyle definicji predykatu dla danych wejściowych, które mają być akceptowane. Jeśli nie zostanie określona, wartość musi być zgodna ze wszystkimi definicjami predykatu. |

**PredicateReferences** element zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PredicateReference (Wniosek predykatu) | 1:n | Odwołanie do predykatu. |

**PredicateReference** element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator, który jest używany do sprawdzania poprawności predykatu.  |


## <a name="configure-password-complexity"></a>Konfigurowanie złożoności hasła

Z **predicates** i **PredicateValidationsInput** można kontrolować wymagania dotyczące złożoności haseł dostarczonych przez użytkownika podczas tworzenia konta. Domyślnie usługa Azure AD B2C używa silnych haseł. Usługa Azure AD B2C obsługuje również opcje konfiguracji, aby kontrolować złożoność haseł, których klienci mogą używać. Złożoność hasła można zdefiniować przy użyciu następujących elementów predykatu:

- **IsLengthBetween8And64** przy `IsLengthRange` użyciu metody, sprawdza, czy hasło musi być między 8 i 64 znaków.
- **Małe litery** `IncludesCharacters` przy użyciu metody, sprawdza, czy hasło zawiera małe litery.
- **Wielkie litery** `IncludesCharacters` przy użyciu metody, sprawdza, czy hasło zawiera wielką literę.
- **Numer** przy `IncludesCharacters` użyciu metody, sprawdza, czy hasło zawiera cyfrę.
- **Symbol** przy `IncludesCharacters` użyciu metody, sprawdza, czy hasło zawiera jeden z kilku znaków symbolu.
- **Przy** użyciu `MatchesRegex` tej metody sprawdza, czy hasło zawiera tylko numery.
- **AllowedAADCharacters** przy `MatchesRegex` użyciu metody, sprawdza, czy hasło tylko nieprawidłowy znak został dostarczony.
- **NiedozwoloneWhitespace** przy użyciu `MatchesRegex` metody, sprawdza, czy hasło nie rozpoczyna się lub kończy znak odstępu.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Po zdefiniowaniu podstawowych weryfikacji można je połączyć i utworzyć zestaw zasad haseł, których można używać w zasadach:

- **SimplePassword** sprawdza poprawność niedozwolonychwhitespace, AllowedAADCharacters i IsLengthBetween8And64
- **StrongPassword** sprawdza poprawność DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Ostatnia `CharacterClasses` grupa uruchamia dodatkowy zestaw predykatów z `MatchAtLeast` zestawem 3. Hasło użytkownika musi zawierać od 8 do 16 znaków i trzy z następujących znaków: Małe litery, Wielkie litery, Liczba lub Symbol.
- **CustomPassword** sprawdza poprawność tylko NiedozwoloneWhitespace, AllowedAADCharacters. Tak, użytkownik może podać dowolne hasło o dowolnej długości, tak długo, jak znaki są prawidłowe.

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

W typie oświadczenia dodaj **predykatValidationReference** element i określ identyfikator jako jeden z sprawdzania predykatu, takich jak SimplePassword, StrongPassword lub CustomPassword.

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

Poniżej przedstawiono sposób organizowania elementów, gdy usługa Azure AD B2C wyświetla komunikat o błędzie:

![Przykład złożoności hasła predykatu i grupy predykatów](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurowanie zakresu dat

Za pomocą **predykatów** i **predykatuZawieranie** można kontrolować minimalne i maksymalne wartości `DateTimeDropdown`daty **UserInputType** za pomocą . Aby to zrobić, należy utworzyć `IsDateRange` **predykat** z metodą i podać minimalne i maksymalne parametry.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Dodaj **PredicateValidation** z odwołaniem `DateRange` do predykatu.

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

W typie oświadczenia dodaj element **PredicateValidationReference** i `CustomDateRange`określ identyfikator jako .

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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować złożoność haseł przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-password-complexity.md) przy użyciu sprawdzania predykatu.