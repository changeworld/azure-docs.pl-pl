---
title: Język dostosowania w zasadach niestandardowych usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zlokalizowania zawartości w zasady niestandardowe dla wielu języków.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 11/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c8deabd4d0a4126365b014875624525d5b1f3063
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711760"
---
# <a name="language-customization-in-custom-policies"></a>Język dostosowania w zasadach niestandardowych

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> 

W zasadach niestandardowych dostosowania języka działa tak samo, jak wbudowane zasady.  Zobacz wbudowane [dokumentacji](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) opisujący zachowanie, w jaki język jest wybierany na podstawie parametrów i ustawienia przeglądarki.

## <a name="enable-supported-languages"></a>Włącz obsługiwane języki
Jeśli nie określono ustawień regionalnych interfejsu użytkownika przeglądarki użytkownika wprowadza się jeden z tych języków, obsługiwane języki są wyświetlane dla użytkownika.  

Obsługiwane języki są definiowane w `<BuildingBlocks>` w następującym formacie:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

Język domyślny i obsługiwanych języków zachowują się w taki sam sposób jak w przypadku wbudowanych zasad.

## <a name="enable-custom-language-strings"></a>Włącz ciągi języka niestandardowych

Tworzenie niestandardowych języka ciągów wymaga wykonania dwóch kroków:
1. Edytuj `<ContentDefinition>` dla strony, aby określić identyfikator zasobu żądane języki
2. Utwórz `<LocalizedResources>` z odpowiednich identyfikatorów w sieci `<BuildingBlocks>`

Należy pamiętać, że można umieścić `<ContentDefinition>` i `<BuildingBlock>` w pliku rozszerzenie lub w pliku zasad jednostki uzależnionej, w zależności od tego, czy chcesz zachować zmiany we wszystkich zasadach dziedziczących lub nie.

### <a name="edit-the-contentdefinition-for-the-page"></a>Edytuj ContentDefinition strony

Dla każdej strony, aby zlokalizować, można określić w `<ContentDefinition>` język zasobów do wyszukiwania dla każdego języka kodu.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

W tym przykładzie francuski (fr) i niestandardowe ciągi angielski (en) są dodawane do strony tworzenia konta lub logowanie Unified.  `LocalizedResourcesReferenceId` Dla każdego `LocalizedResourcesReference` jest taka sama jak ich ustawień regionalnych, ale można użyć dowolnego ciągu jako identyfikator.  Każda kombinacja języka i strony należy utworzyć odpowiedni `<LocalizedResources>` pokazano poniżej.


### <a name="create-the-localizedresources"></a>Utwórz LocalizedResources

Wprowadzone zastąpienia są zawarte w Twojej `<BuildingBlocks>` i ma `<LocalizedResources>` dla każdej strony i języka określonego w `<ContentDefinition>` dla każdej strony.  Każdy zastąpienia jest określony jako `<LocalizedString>` takich jak w poniższym przykładzie:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Istnieją cztery typy elementów ciąg na stronie:

**ClaimsProvider** -etykiety dla Twojego dostawcy tożsamości (Facebook, Google, usługi Azure AD itp.) **Typ oświadczenia** -etykiety z atrybutów i odpowiednie tekst pomocy lub pola błędy sprawdzania poprawności **UxElement** — innych elementów na stronie, które są domyślnie, takie jak przyciski, linki lub tekst ciągu**ErrorMessage** -tworzą komunikatów o błędach

Upewnij się, że `StringId`s odpowiada strony używanym w przeciwnym razie zastąpienia jest zablokowany przez sprawdzanie zasad podczas przekazywania.  