---
title: Dostosowywanie języka w zasadach niestandardowych usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zlokalizowania zawartości w niestandardowe zasady dla wielu języków.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6269ac65e5db20521346d5312bcbadd0905c36e2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440568"
---
# <a name="language-customization-in-custom-policies"></a>Dostosowywanie języka w zasadach niestandardowych

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> 

W zasadach niestandardowych Dostosowywanie języka działa tak samo, jak wbudowane zasady.  Zobacz wbudowane [dokumentacji](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) opisujący zachowanie w jak język jest wybierany w oparciu o parametry i ustawienia przeglądarki.

## <a name="enable-supported-languages"></a>Włącz obsługiwane języki
Jeśli nie określono ustawienia regionalne interfejsu użytkownika przeglądarki użytkownika prosi o jeden z tych języków, obsługiwane języki są wyświetlane użytkownikowi.  

Obsługiwane języki są zdefiniowane w `<BuildingBlocks>` w następującym formacie:

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

Domyślny język i obsługiwane języki, zachowują się w taki sam sposób jak w przypadku wbudowanych zasad.

## <a name="enable-custom-language-strings"></a>Włącz ciągów języka niestandardowego

Tworzenie ciągów języka niestandardowego wymaga dwóch kroków:
1. Edytuj `<ContentDefinition>` dla strony Aby określić identyfikator zasobu żądane języki
2. Utwórz `<LocalizedResources>` z odpowiadające im identyfikatory w sieci `<BuildingBlocks>`

Należy pamiętać, że możesz umieścić `<ContentDefinition>` i `<BuildingBlock>` w pliku rozszerzenie lub w pliku zasad jednostki uzależnionej, w zależności od tego, czy chcesz zachować zmiany we wszystkich zasadach dziedziczącej lub nie.

### <a name="edit-the-contentdefinition-for-the-page"></a>Edytuj ContentDefinition strony

Dla każdej strony, aby zlokalizować, można określić w `<ContentDefinition>` jakie zasoby języka, aby wyszukać każdy kod języka.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

W tym przykładzie francuski (fr) i angielski (en) — niestandardowe ciągi są dodawane do ujednoliconego strony rejestracji lub logowania.  `LocalizedResourcesReferenceId` Dla każdego `LocalizedResourcesReference` jest taka sama jak ich ustawień regionalnych, ale można użyć dowolnego ciągu jako identyfikator.  Dla każdej kombinacji języka i strony, należy utworzyć odpowiednią `<LocalizedResources>` pokazano poniżej.


### <a name="create-the-localizedresources"></a>Utwórz LocalizedResources

Przesłonięć są zawarte w Twojej `<BuildingBlocks>` i ma `<LocalizedResources>` dla każdej strony i języka określonego w `<ContentDefinition>` dla każdej strony.  Zastępują jest określony jako `<LocalizedString>` takich jak w poniższym przykładzie:

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

Istnieją cztery rodzaje elementów ciąg na stronie:

**ClaimsProvider** -etykiety dostawców tożsamości (Facebook, Google, usługa Azure AD itp.) **Element ClaimType** — etykiety dla atrybutów i odpowiadające im tekst pomocy lub pola błędy sprawdzania poprawności **UxElement** — inne ciągu elementów na stronie, które są domyślnie, takie jak przyciski, linki lub tekst **Komunikat o błędzie** — komunikaty o błędach weryfikacji formularza

Upewnij się, że `StringId`s odpowiada stronie korzystają te zastąpienia w przeciwnym razie jest zablokowany przez sprawdzanie poprawności zasad podczas przesyłania.  