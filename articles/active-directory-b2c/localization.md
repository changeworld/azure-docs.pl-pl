---
title: Lokalizacja — usługa Azure Active Directory B2C
description: Określ element lokalizacji zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126759"
---
# <a name="localization"></a>Lokalizacja

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** Element umożliwia obsługę wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Obsługa lokalizacji w zasadach umożliwia:

- Skonfiguruj jawną listę obsługiwanych języków w zasadach i wybierz język domyślny.
- Podaj ciągi i kolekcje specyficzne dla języka.

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

Element **Lokalizacja** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Enabled (Włączony) | Nie | Możliwe `true` wartości: `false`lub . |

Element **Lokalizacja** zawiera następujące elementy XML

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Obsługiwanelanguages | 1:n | Lista obsługiwanych języków. |
| Zlokalizowane Źródła zasobów | 0:n | Lista zlokalizowanych zasobów. |

## <a name="supportedlanguages"></a>Obsługiwanelanguages

Element **SupportedLanguages** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| DefaultLanguage | Tak | Język, który ma być używany jako domyślny dla zlokalizowanych zasobów. |
| MergeBehavior (Zachowanie scalania) | Nie | Wartości wyliczenia wartości, które są scalane wraz z dowolnego ClaimType obecnych w zasadach nadrzędnych o tym samym identyfikatorze. Użyj tego atrybutu podczas zastępowanie oświadczenia określonego w zasadach podstawowych. Możliwe `Append`wartości: `Prepend`, `ReplaceAll`, lub . Wartość `Append` określa, że kolekcja obecnych danych powinny być dołączane na końcu kolekcji określonych w zasadach nadrzędnych. Wartość `Prepend` określa, że kolekcja obecnych danych powinny zostać dodane przed kolekcji określonych w zasadach nadrzędnych. Wartość `ReplaceAll` określa, że zbieranie danych zdefiniowanych w zasadach nadrzędnych powinny być ignorowane, zamiast tego dane zdefiniowane w bieżącej zasad. |

### <a name="supportedlanguages"></a>Obsługiwanelanguages

Element **SupportedLanguages** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ObsługiwaneLanguage | 1:n | Wyświetla zawartość, która jest zgodna z tagiem języka na RFC 5646 — tagi dla języków identyfikujących. |

## <a name="localizedresources"></a>Zlokalizowane Źródła zasobów

Element **LocalizedResources** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator, który jest używany do jednoznacznej identyfikacji zlokalizowanych zasobów. |

Element **LocalizedResources** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Zlokalizowaneukłaki | 0:n | Definiuje całe kolekcje w różnych kulturach. Kolekcja może mieć różną liczbę elementów i różne ciągi dla różnych kultur. Przykłady kolekcji obejmują wyliczenia, które pojawiają się w typach oświadczeń. Na przykład lista krajów/regionów jest wyświetlana użytkownikowi na liście rozwijanej. |
| Zlokalizowane Sznurki | 0:n | Definiuje wszystkie ciągi, z wyjątkiem tych ciągów, które pojawiają się w kolekcjach, w różnych kulturach. |

### <a name="localizedcollections"></a>Zlokalizowaneukłaki

Element **LocalizedCollections** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Zlokalizowanacollection | 1:n | Lista obsługiwanych języków. |

#### <a name="localizedcollection"></a>Zlokalizowanacollection

Element **LocalizedCollection** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Elementtype | Tak | Odwołuje się do elementu ClaimType lub elementu interfejsu użytkownika w pliku zasad. |
| ElementId (ład elementu) | Tak | Ciąg, który zawiera odwołanie do typu oświadczenia już zdefiniowane w ClaimsSchema sekcji, która jest używana, jeśli **ElementType** jest ustawiona na ClaimType. |
| CelCollection (CelCollection) | Tak | Kolekcja docelowa. |

Element **LocalizedCollection** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Element | 0:n | Definiuje dostępną opcję dla użytkownika, aby wybrać dla oświadczenia w interfejsie użytkownika, takich jak wartość w rozwijanej. |

**Element element** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Tekst | Tak | Przyjazny dla użytkownika ciąg wyświetlania, który powinien być wyświetlany użytkownikowi w interfejsie użytkownika dla tej opcji. |
| Wartość | Tak | Wartość oświadczenia ciągu skojarzona z wybraniem tej opcji. |
| Wybierz opcjęObdefault | Nie | Wskazuje, czy ta opcja powinna być zaznaczona domyślnie w interfejsie użytkownika. Możliwe wartości: Prawda lub Fałsz. |

Poniższy przykład pokazuje użycie **LocalizedCollections** elementu. Zawiera dwa **elementy LocalizedCollection,** jeden dla języka angielskiego, a drugi dla języka hiszpańskiego. Oba ustawić **ograniczenie** kolekcji `Gender` roszczenia z listy elementów w języku angielskim i hiszpańskim.

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

### <a name="localizedstrings"></a>Zlokalizowane Sznurki

Element **LocalizedStrings** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Zlokalizowany ciąg. |

Element **LocalizedString** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Elementtype | Tak | Odwołanie do elementu typu oświadczenia lub elementu interfejsu użytkownika w zasadach. `ClaimType`Możliwe wartości: `UxElement` `ErrorMessage`, `Predicate`, `GetLocalizedStringsTransformationClaimType`, , lub . Wartość `ClaimType` jest używana do lokalizowania jednego z atrybutów oświadczeń, jak określono w StringId. Wartość `UxElement` jest używana do lokalizowania jednego z elementów interfejsu użytkownika, jak określono w StringId. Wartość `ErrorMessage` jest używana do lokalizowania jednego z komunikatów o błędach systemu, jak określono w StringId. Wartość `Predicate` jest używana do lokalizowania jednego z [predykatu](predicates.md) komunikatów o błędach, jak określono w StringId. Wartość `InputValidation` jest używana do lokalizowania jednego z [predykatuWątów](predicates.md) wiadomości o błędach grupy, jak określono w StringId. Wartość `GetLocalizedStringsTransformationClaimType` jest używana do kopiowania zlokalizowanych ciągów do oświadczeń. Aby uzyskać więcej informacji, zobacz [Transformacja oświadczeń GetLocalizedStringsTransformation](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId (ład elementu) | Tak | Jeśli **ElementType** jest `ClaimType` `Predicate`ustawiona `InputValidation`na , lub , ten element zawiera odwołanie do typu oświadczenia już zdefiniowane w ClaimsSchema sekcji. |
| StringId (ida ciągiem) | Tak | Jeśli **ElementType** jest `ClaimType`ustawiona na , ten element zawiera odwołanie do atrybutu typu oświadczenia. Możliwe `DisplayName`wartości: `AdminHelpText`, `PatternHelpText`, lub . Wartość `DisplayName` jest używana do ustawiania nazwy wyświetlanej oświadczenia. Wartość `AdminHelpText` służy do ustawiania nazwy tekstowej pomocy użytkownika oświadczenia. Wartość `PatternHelpText` służy do ustawiania tekstu pomocy wzorca oświadczeń. Jeśli **ElementType** jest `UxElement`ustawiona na , ten element zawiera odwołanie do atrybutu elementu interfejsu użytkownika. Jeśli **elementType** jest `ErrorMessage`ustawiona na , ten element określa identyfikator komunikatu o błędzie. Zobacz [Identyfikatory ciągów lokalizacji,](localization-string-ids.md) aby `UxElement` uzyskać pełną listę identyfikatorów.|


W poniższym przykładzie przedstawiono zlokalizowaną stronę rejestracji. Pierwsze trzy **localizedString** wartości ustawić atrybut oświadczenia. Trzeci zmienia wartość przycisku continue. Ostatni zmienia komunikat o błędzie.

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

W poniższym przykładzie przedstawiono zlokalizowany **Tekst pomocy użytkownika** **predykatu** o identyfikatorze `IsLengthBetween8And64`. I zlokalizowane **UserHelpText** **predicategroup** z `CharacterClasses` identyfikatorem **PredicateValidation** z Id `StrongPassword`.

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

W tym artykule pokazano, jak obsługiwać wiele ustawień regionalnych lub języków w zasadach dotyczących podróży użytkowników. Lokalizacja wymaga trzech kroków: skonfigurować jawną listę obsługiwanych języków, podać ciągi i kolekcje specyficzne dla języka i edytować ContentDefinition dla strony.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Konfigurowanie jawnej listy obsługiwanych języków

W obszarze **BuildingBlocks** element, dodaj **Localization** element z listy obsługiwanych języków. W poniższym przykładzie pokazano, jak zdefiniować obsługę lokalizacji zarówno dla języka angielskiego (domyślnego), jak i hiszpańskiego:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły przykładów lokalizacji:

- [Dostosowywanie języka za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-localization.md)
- [Dostosowywanie języka z przepływami użytkowników w usłudze Azure Active Directory B2C](user-flow-language-customization.md)
