---
title: Lokalizacja — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Określ element Localization zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a02983c5019870e8b17db48184b2f238a82f8a40
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510581"
---
# <a name="localization"></a>Lokalizacja

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokalizacji** elementu pozwala na potrzeby obsługi wielu ustawień regionalnych lub języków w ramach zasad dla podróży użytkownika. Obsługa lokalizacji, w ramach zasad umożliwia:

- Konfigurowanie jawną listę obsługiwanych języków w zasadach, a następnie wybierz język domyślny.
- Podaj parametry specyficzne dla języka i kolekcje.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Lokalizacji** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Enabled (Włączony) | Nie | Możliwe wartości: `true` lub `false`. |

**Lokalizacji** element zawiera następujące elementy XML

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Listę obsługiwanych języków. | 
| LocalizedResources | 0: n | Lista zlokalizowanych zasobów. |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| DefaultLanguage | Tak | Język, który ma być używany jako domyślny dla zlokalizowanych zasobów. |
| MergeBehavior | Nie | Wartości wyliczenia wartości, które zostaną scalone razem z dowolnego typu oświadczenia obecne w zasadach nadrzędnego zawierających ten sam identyfikator. Podczas zastępowania oświadczenia określone w zasadach podstawowych, należy użyć tego atrybutu. Możliwe wartości: `Append`, `Prepend`, lub `ReplaceAll`. `Append` Wartość określa, że zbieranie danych jest obecny powinna zostać dołączona do końca kolekcji określonej w zasadach nadrzędnej. `Prepend` Wartość określa, że zbieranie danych jest obecny należy dodać przed określonej w zasadach nadrzędny kolekcji. `ReplaceAll` Wartość określa, że zbieranie danych, zdefiniowane w zasadach nadrzędnej mają być ignorowane, zamiast dane zdefiniowane w bieżących zasadach. |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Wyświetla zawartość, który jest zgodny z tagu języka na RFC 5646 - znaczniki identyfikacji języków. | 

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator, który jest używany do jednoznacznego identyfikowania zlokalizowanych zasobów. |

**LocalizedResources** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definiuje całej kolekcji w różnych kulturach. Kolekcja może mieć różne liczby elementów oraz różne parametry dla różnych kultur. Kolekcje przykładami wyliczenia, które pojawiają się w typów oświadczeń. Na przykład listę krajów/regionów jest wyświetlany na liście rozwijanej. |
| LocalizedStrings | 0: n | Określa wszystkie parametry, z wyjątkiem tych ciągów, które pojawiają się w kolekcji, w różnych kulturach. |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | Listę obsługiwanych języków. |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ElementType | Tak | Odwołuje się do elementu typu oświadczenia lub element interfejsu użytkownika w pliku zasad. |
| ElementId | Tak | Ciąg, który zawiera odwołanie do typu oświadczenia już zdefiniowane w sekcji ClaimsSchema, która jest używana, gdy **ElementType** jest ustawiona na typ oświadczenia. |
| TargetCollection | Yes | Kolekcji docelowej. |

**LocalizedCollection** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Element | 0: n | Definiuje opcję dostępną dla użytkownika wybrać oświadczenia w interfejsie użytkownika, takie jak wartości na liście rozwijanej. |

**Elementu** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Text | Tak | Ciąg wyświetlania przyjazny dla użytkownika, który ma być wyświetlany użytkownikowi w interfejsie użytkownika dla tej opcji. |
| Wartość | Tak | Ciąg oświadczenie wartość skojarzoną z wybraniu tej opcji. |

Poniższy przykład pokazuje użycie **LocalizedCollections** elementu. Zawiera dwa **LocalizedCollection** elementy, jeden dla języka angielskiego i inny dla języka hiszpańskiego. Jednocześnie ustawionych **ograniczeń** kolekcji oświadczenia `Gender` z listy elementów w języku angielskim i hiszpańskim.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

```

### <a name="localizedstrings"></a>LocalizedStrings

**LocalizedStrings** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Zlokalizowany ciąg. |

**LocalizedString** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ElementType | Tak | Odwołanie do elementu typu oświadczenia lub element interfejsu użytkownika w ramach zasad. Możliwe wartości: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`, lub. `ClaimType` Wartość jest używana do lokalizowania jeden z atrybutów oświadczenia, jak to określono w identyfikator ciągu. `UxElement` Wartość jest używana do lokalizowania jeden z elementów interfejsu użytkownika, jak to określono w identyfikator ciągu. `ErrorMessage` Wartość jest używana do lokalizowania komunikat o błędzie systemu, jak to określono w identyfikator ciągu. `Predicate` Wartość jest używana do lokalizowania jednego z [predykatu](predicates.md) komunikaty o błędach, jak to określono w identyfikator ciągu. `InputValidation` Wartość jest używana do lokalizowania jednego z [PredicateValidation](predicates.md) grupie komunikaty o błędach, jak to określono w identyfikator ciągu. |
| ElementId | Tak | Jeśli **ElementType** ustawiono `ClaimType`, `Predicate`, lub `InputValidation`, ten element zawiera odwołanie do typu oświadczenia już zdefiniowane w sekcji ClaimsSchema. | 
| StringId | Yes | Jeśli **ElementType** ustawiono `ClaimType`, ten element zawiera odwołanie do atrybutu typu oświadczenia. Możliwe wartości: `DisplayName`, `AdminHelpText`, lub `PatternHelpText`. `DisplayName` Wartość jest używana do ustawiania oświadczenia nazwy wyświetlanej. `AdminHelpText` Wartość jest używana do ustawiania Nazwa tekstu pomocy oświadczenia użytkownika. `PatternHelpText` Wartość jest używana do ustawiania tekst pomocy wzorzec oświadczenia. Jeśli **ElementType** ustawiono `UxElement`, ten element zawiera odwołanie do atrybutu elementu interfejsu użytkownika. Jeśli **ElementType** ustawiono `ErrorMessage`, ten element Określa identyfikator komunikatu o błędzie. Zobacz [ciągu lokalizacji identyfikatorów](localization-string-ids.md) szczegółowy wykaz `UxElement` identyfikatorów.|


Poniższy przykład pokazuje zlokalizowane stronę rejestracji. Pierwsze trzy **LocalizedString** atrybut roszczenia ustawić wartości. Trzeci zmienia wartość przycisk Kontynuuj. Ostatnie zmiany komunikat o błędzie.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

W poniższym przykładzie pokazano zlokalizowany **UserHelpText** z **predykatu** o identyfikatorze `IsLengthBetween8And64`. I zlokalizowanych **UserHelpText** z **PredicateGroup** o identyfikatorze `CharacterClasses` z **PredicateValidation** o identyfikatorze `StrongPassword`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>              
```

## <a name="set-up-localization"></a>Ustawianie lokalizacji

W tym artykule przedstawiono sposób obsługi wielu ustawień regionalnych lub języków w ramach zasad dla podróży użytkownika. Lokalizacja wymaga wykonania trzech kroków: konfiguracji jawną listę języków obsługiwanych ciągów specyficznych dla języka i kolekcji, a następnie edytuj ContentDefinition dla strony.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Konfigurowanie jawną listę obsługiwanych języków

W obszarze **BuildingBlocks** elementu Dodawanie **lokalizacji** element z listy obsługiwanych języków. Poniższy przykład pokazuje jak zdefiniować obsługi lokalizacji dla języka angielskiego (ustawienie domyślne) i hiszpański:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Podaj parametry specyficzne dla języka i kolekcji 

Dodaj **LocalizedResources** elementy wewnątrz **lokalizacji** elementu po zamknięciu **SupportedLanguages** elementu. Możesz dodać **LocalizedResources** elementy dla każdej strony (definicja zawartości) i dowolnego języka, które mają być obsługiwane. Aby dostosować ujednolicona strona rejestracji lub logowania, strony rejestracji oraz uwierzytelnianie wieloskładnikowe (MFA), angielski, hiszpański i (Francja), należy dodać następujące **LocalizedResources** elementów.  
- Ujednolicona strona tworzenia konta lub logowania, angielski `<LocalizedResources Id="api.signuporsignin.en">`
- Ujednolicona strona tworzenia konta lub logowania, hiszpański `<LocalizedResources Id="api.signuporsignin.es">`
- Ujednolicona strona tworzenia konta lub logowania, (Francja) `<LocalizedResources Id="api.signuporsignin.fr">` 
- Zarejestruj się, angielski `<LocalizedResources Id="api.localaccountsignup.en">`
- Zarejestruj się, hiszpański `<LocalizedResources Id="api.localaccountsignup.es">`
- Zarejestruj się, Francja `<LocalizedResources Id="api.localaccountsignup.fr">`
- Uwierzytelnianie wieloskładnikowe, angielski `<LocalizedResources Id="api.phonefactor.en">`
- Uwierzytelnianie wieloskładnikowe, hiszpański `<LocalizedResources Id="api.phonefactor.es">`
- MFA, France `<LocalizedResources Id="api.phonefactor.fr">`

Każdy **LocalizedResources** element zawiera wszystkie wymagane **LocalizedStrings** elementy z wieloma **LocalizedString** elementy i  **LocalizedCollections** elementy z wieloma **LocalizedCollection** elementów.  Poniższy przykład dodaje lokalizacji angielskiej stronę rejestracji: 

Uwaga: W tym przykładzie sprawia, że odwołanie do `Gender` i `City` typów oświadczeń. Aby wykorzystać ten przykład, upewnij się, że należy zdefiniować te oświadczenia. Aby uzyskać więcej informacji, zobacz [ClaimsSchema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Lokalizacja strony rejestracji dla języka hiszpańskiego.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>Edytuj ContentDefinition strony 

Dla każdej strony, którą chcesz zlokalizować Określ kodów języków do wyszukania w **ContentDefinition**.

W poniższym przykładzie język angielski (en) i hiszpański (es) — niestandardowe ciągi są dodawane do strony rejestracji. **LocalizedResourcesReferenceId** dla każdego **LocalizedResourcesReference** jest taka sama jak ich ustawień regionalnych, ale można użyć dowolnego ciągu jako identyfikator. Każda kombinacja języka i strony wskaż odpowiednich **LocalizedResources** utworzonego wcześniej.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

Końcowego pliku XML można znaleźć w poniższym przykładzie:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```




