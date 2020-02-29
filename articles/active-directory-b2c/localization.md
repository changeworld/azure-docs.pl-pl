---
title: Lokalizacja — Azure Active Directory B2C
description: Określ element lokalizacji niestandardowych zasad w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a9fb1d64fb27507e4ee42c39be9ea80fbe5f168
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184455"
---
# <a name="localization"></a>Lokalizacja

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Lokalizacja** umożliwia obsługę wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Obsługa lokalizacji w ramach zasad pozwala:

- Skonfiguruj jawną listę obsługiwanych języków w zasadach i wybierz język domyślny.
- Podaj odpowiednie dla języka ciągi i kolekcje.

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

Element **lokalizacji** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Enabled (Włączony) | Nie | Możliwe wartości: `true` lub `false`. |

Element **lokalizacji** zawiera następujące elementy XML

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Lista obsługiwanych języków. |
| LocalizedResources | 0: n | Lista zlokalizowanych zasobów. |

## <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| DefaultLanguage | Yes | Język, który ma być używany jako domyślny dla zlokalizowanych zasobów. |
| MergeBehavior | Nie | Wartości wyliczenia wartości, które są scalone ze wszystkimi oświadczeniami obecnymi w zasadach nadrzędnych o tym samym identyfikatorze. Użyj tego atrybutu podczas zastępowania żądania określonego w zasadach podstawowych. Możliwe wartości: `Append`, `Prepend`lub `ReplaceAll`. Wartość `Append` określa, że kolekcja danych powinna być dołączana na końcu kolekcji określonej w zasadach nadrzędnych. Wartość `Prepend` określa, że kolekcja danych powinna zostać dodana przed kolekcją określoną w zasadach nadrzędnych. Wartość `ReplaceAll` określa, że kolekcja danych zdefiniowana w zasadach nadrzędnych powinna być ignorowana, przy użyciu zamiast danych zdefiniowanych w bieżących zasadach. |

### <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Wyświetla zawartość, która jest zgodna ze znacznikiem języka na RFC 5646-Tagi dla identyfikacji języków. |

## <a name="localizedresources"></a>LocalizedResources

Element **LocalizedResources** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator, który jest używany do unikatowego identyfikowania zlokalizowanych zasobów. |

Element **LocalizedResources** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definiuje całe kolekcje w różnych kulturach. Kolekcja może mieć różną liczbę elementów i różne ciągi dla różnych kultur. Przykłady kolekcji obejmują wyliczenia, które pojawiają się w typach zgłoszeń. Na przykład lista krajów/regionów jest pokazywana użytkownikowi na liście rozwijanej. |
| LocalizedStrings | 0: n | Definiuje wszystkie ciągi, z wyjątkiem tych, które znajdują się w kolekcjach w różnych kulturach. |

### <a name="localizedcollections"></a>LocalizedCollections

Element **LocalizedCollections** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Zlokalizowany | 1: n | Lista obsługiwanych języków. |

#### <a name="localizedcollection"></a>Zlokalizowany

**Zlokalizowany** ElementCollection zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| ElementType | Yes | Odwołuje się do elementu ClaimType lub elementu interfejsu użytkownika w pliku zasad. |
| ElementId | Yes | Ciąg, który zawiera odwołanie do typu oświadczenia zdefiniowanego już w sekcji ClaimsSchema, która jest używana, jeśli **ElementType** jest ustawiony jako typ oświadczenia. |
| Targetcollection | Yes | Kolekcja docelowa. |

Element **zlokalizowany** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Element | 0: n | Definiuje dostępną opcję dla użytkownika do wyboru w interfejsie użytkownika, na przykład wartość z listy rozwijanej. |

Element **Item** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Tekst | Yes | Przyjazny dla użytkownika ciąg wyświetlania, który powinien być widoczny dla użytkownika w interfejsie użytkownika dla tej opcji. |
| Wartość | Yes | Wartość żądania ciągu skojarzona z wybraniem tej opcji. |
| SelectByDefault | Nie | Wskazuje, czy ta opcja powinna być wybrana domyślnie w interfejsie użytkownika. Możliwe wartości: true lub false. |

W poniższym przykładzie pokazano użycie elementu **LocalizedCollections** . Zawiera dwa **zlokalizowane** elementycollection, jeden dla języka angielskiego i drugi dla języka hiszpańskiego. Oba te ustawienia są ustawiane jako kolekcja **ograniczeń** `Gender` z listą elementów w języku angielskim i hiszpańskim.

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

Element **LocalizedStrings** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Zlokalizowany ciąg. |

Element **LocalizedString** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| ElementType | Yes | Odwołanie do elementu typu "Claim" lub elementu interfejsu użytkownika w zasadach. Możliwe wartości: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`lub. Wartość `ClaimType` służy do lokalizowania jednego z atrybutów, jak określono w StringId. Wartość `UxElement` służy do lokalizowania jednego z elementów interfejsu użytkownika określonych w StringId. Wartość `ErrorMessage` służy do lokalizowania jednego z komunikatów o błędach systemu określonych w StringId. Wartość `Predicate` służy do lokalizowania jednego z komunikatów o błędach [predykatu](predicates.md) , jak określono w StringId. Wartość `InputValidation` służy do lokalizowania jednej z komunikatów o błędach grupy [PredicateValidation](predicates.md) określonych w StringId. |
| ElementId | Yes | Jeśli **ElementType** ma wartość `ClaimType`, `Predicate`lub `InputValidation`, ten element zawiera odwołanie do typu zgłoszenia już zdefiniowanego w sekcji ClaimsSchema. |
| StringId | Yes | Jeśli **ElementType** ma wartość `ClaimType`, ten element zawiera odwołanie do atrybutu typu "typ". Możliwe wartości: `DisplayName`, `AdminHelpText`lub `PatternHelpText`. Wartość `DisplayName` służy do ustawiania nazwy wyświetlanej w ramach żądania. Wartość `AdminHelpText` służy do ustawiania nazwy tekstu pomocy użytkownika. Wartość `PatternHelpText` służy do ustawiania tekstu pomocy dla wzorca żądania. Jeśli **ElementType** ma wartość `UxElement`, ten element zawiera odwołanie do atrybutu elementu interfejsu użytkownika. Jeśli **ElementType** ma wartość `ErrorMessage`, ten element określa identyfikator komunikatu o błędzie. Aby uzyskać pełną listę identyfikatorów `UxElement`, zobacz [identyfikatory ciągów lokalizacji](localization-string-ids.md) .|


Poniższy przykład pokazuje zlokalizowaną stronę rejestracji. Pierwsze trzy wartości **LocalizedString** ustawiają atrybut Claim. Trzecia zmiana wartości przycisku Kontynuuj. Ostatni z nich zmienia komunikat o błędzie.

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

W poniższym przykładzie pokazano zlokalizowany **UserHelpText** **predykatu** z identyfikatorem `IsLengthBetween8And64`. I zlokalizowany **UserHelpText** z identyfikatorem `CharacterClasses` **PredicateValidation** z identyfikatorem `StrongPassword`.

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

## <a name="set-up-localization"></a>Konfigurowanie lokalizacji

W tym artykule przedstawiono sposób obsługi wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Lokalizacja wymaga trzech kroków: Skonfiguruj jawną listę obsługiwanych języków, podaj odpowiednie dla języka ciągi i kolekcje, a następnie Edytuj ContentDefinition dla strony.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Skonfiguruj jawną listę obsługiwanych języków

W elemencie **BuildingBlocks** Dodaj element **lokalizacji** z listą obsługiwanych języków. Poniższy przykład pokazuje, jak zdefiniować obsługę lokalizacji dla języka angielskiego (domyślnego) i hiszpańskiego:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Podawanie ciągów i kolekcji specyficznych dla języka

Dodaj elementy **LocalizedResources** wewnątrz elementu **lokalizacyjnego** po zamknięciu elementu **SupportedLanguages** . Należy dodać elementy **LocalizedResources** na każdej stronie (definicji zawartości) i dowolnym języku, który ma być obsługiwany. Aby dostosować ujednoliconą stronę rejestracji lub logowania, strony rejestracji i uwierzytelniania wieloskładnikowego (MFA) dla języka angielskiego, hiszpańskiego i Francji, należy dodać następujące elementy **LocalizedResources** .

- Ujednolicona Strona rejestracji lub logowania, `<LocalizedResources Id="api.signuporsignin.en">` w języku angielskim
- Ujednolicona Strona rejestracji lub logowania, `<LocalizedResources Id="api.signuporsignin.es">` Hiszpańska
- Ujednolicona Strona rejestracji lub logowania, Francja `<LocalizedResources Id="api.signuporsignin.fr">`
- Rejestracja, `<LocalizedResources Id="api.localaccountsignup.en">` w języku angielskim
- Rejestracja, `<LocalizedResources Id="api.localaccountsignup.es">` Hiszpańska
- Rejestracja, Francja `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, `<LocalizedResources Id="api.phonefactor.en">` w języku angielskim
- MFA, `<LocalizedResources Id="api.phonefactor.es">` hiszpański
- MFA, Francja `<LocalizedResources Id="api.phonefactor.fr">`

Każdy element **LocalizedResources** zawiera wszystkie wymagane elementy **LocalizedStrings** z wieloma elementami **LocalizedString** i **LocalizedCollections** zawierającymi wiele elementów **zlokalizowanych** .  Poniższy przykład dodaje lokalizację w języku angielskim strony rejestracji:

Uwaga: w tym przykładzie jest to odwołanie do `Gender` i `City` typów roszczeń. Aby użyć tego przykładu, należy się upewnić, że zostały zdefiniowane te oświadczenia. Aby uzyskać więcej informacji, zobacz [ClaimsSchema](claimsschema.md).

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

Dla każdej strony, którą chcesz zlokalizować, określ kody języka, które mają być wyszukiwane w **ContentDefinition**.

W poniższym przykładzie niestandardowe ciągi (EN) i hiszpański (ES) są dodawane do strony rejestracji. **LocalizedResourcesReferenceId** dla każdego **LocalizedResourcesReferenceu** jest taka sama jak ich ustawienia regionalne, ale można użyć dowolnego ciągu jako identyfikatora. Dla każdej kombinacji języka i strony należy wskazać odpowiednie **LocalizedResources** wcześniej utworzone.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

Poniższy przykład pokazuje końcowy kod XML:

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
