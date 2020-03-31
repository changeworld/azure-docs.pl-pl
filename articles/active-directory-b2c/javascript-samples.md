---
title: Przykłady skryptów w języku JavaScript
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o używaniu języka JavaScript w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a26f6c5e69ca083335580a0368459e062de3941e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187665"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Przykłady języka JavaScript do użycia w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Do aplikacji usługi Azure Active Directory B2C (Azure AD B2C) można dodać własny kod po stronie klienta JavaScript.

Aby włączyć obsługę języka JavaScript dla aplikacji:

* Dodawanie elementu do [zasad niestandardowych](custom-policy-overview.md)
* Wybieranie [układu strony](page-layout.md)
* Użyj [b2clogin.com](b2clogin.md) w swoich żądaniach

W tym artykule opisano, jak można zmienić zasady niestandardowe, aby włączyć wykonywanie skryptów.

> [!NOTE]
> Aby włączyć obsługę języka JavaScript dla przepływów użytkowników, zobacz [JavaScript i wersje układu strony w usłudze Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="select-a-page-layout"></a>Wybieranie układu strony

* Wybierz [układ strony](contentdefinitions.md#select-a-page-layout) dla elementów interfejsu użytkownika aplikacji.

    Jeśli zamierzasz używać języka JavaScript, musisz zdefiniować `contract` wersję układu strony z [wersją](contentdefinitions.md#migrating-to-page-layout) strony dla *wszystkich* definicji zawartości w zasadach niestandardowych.

## <a name="add-the-scriptexecution-element"></a>Dodawanie elementu ScriptExecution

Można włączyć wykonywanie skryptu przez dodanie **ScriptExecution** element do [RelyingParty](relyingparty.md) element.

1. Otwórz niestandardowy plik zasad. Na przykład *signuporsignin.xml*.
2. Dodaj **ScriptExecution** element do **UserJourneyBehaviors** element **RelyingParty:**

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Zapisz i przekaż plik.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>Przykłady skryptów w języku JavaScript

### <a name="show-or-hide-a-password"></a>Pokazywale lub ukrywanie hasła

Typowym sposobem, aby pomóc klientom w ich sukces rejestracji jest umożliwienie im zobaczyć, co wprowadzili jako hasło. Ta opcja pomaga użytkownikom zarejestrować się, umożliwiając im łatwe wyświetlanie i wprowadzanie poprawek do hasła w razie potrzeby. Każde pole hasła typu ma pole wyboru z etykietą **Pokaż hasło.**  Dzięki temu użytkownik może zobaczyć hasło w postaci zwykłego tekstu. Dołącz ten fragment kodu do szablonu rejestracji lub logowania dla strony, która została potwierdzona samodzielnie:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Dodawanie warunków użytkowania

Dołącz do strony następujący kod, w którym chcesz dołączyć pole wyboru **Warunki użytkowania.** To pole wyboru jest zwykle potrzebne na stronach rejestracji konta lokalnego i konta społecznościowego.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

W kodzie `termsOfUseUrl` zastąp linkiem do umowy dotyczącej warunków użytkowania. Dla katalogu utwórz nowy atrybut użytkownika o nazwie **termsOfUse,** a następnie dołącz **termsOfUse** jako atrybut użytkownika.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat dostosowywania interfejsu użytkownika aplikacji można znaleźć w [obszarze Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-ui-customization.md).
