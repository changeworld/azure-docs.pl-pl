---
title: Przykłady skryptów w języku JavaScript
titleSuffix: Azure AD B2C
description: Informacje o używaniu języka JavaScript w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1381ddb16697b1e892794604bbfafda815bd6182
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149081"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Przykładowe skrypty JavaScript do użycia w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Możesz dodać własny kod po stronie klienta JavaScript do aplikacji Azure Active Directory B2C (Azure AD B2C).

Aby włączyć język JavaScript dla aplikacji:

* Dodawanie elementu do [zasad niestandardowych](custom-policy-overview.md)
* Wybierz [Układ strony](page-layout.md)
* Używanie [b2clogin.com](b2clogin.md) w żądaniach

W tym artykule opisano sposób zmiany zasad niestandardowych w celu włączenia wykonywania skryptu.

> [!NOTE]
> Jeśli chcesz włączyć język JavaScript dla przepływów użytkownika, zobacz [wersje JavaScript i układ strony w Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="select-a-page-layout"></a>Wybierz układ strony

* Wybierz [Układ strony](contentdefinitions.md#select-a-page-layout) dla elementów interfejsu użytkownika aplikacji.

    Jeśli zamierzasz używać języka JavaScript, musisz [zdefiniować wersję układu strony](contentdefinitions.md#migrating-to-page-layout) z wersją `contract` strony dla *wszystkich* definicji zawartości w niestandardowych zasadach.

## <a name="add-the-scriptexecution-element"></a>Dodaj ScriptExecution element

Wykonanie skryptu jest włączane przez dodanie elementu **ScriptExecution** do elementu [RelyingParty](relyingparty.md) .

1. Otwórz plik zasad niestandardowych. Na przykład *SignUpOrSignin. XML*.
2. Dodaj element **ScriptExecution** do elementu **UserJourneyBehaviors** **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Zapisz i przekazać plik.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>Przykłady skryptów w języku JavaScript

### <a name="show-or-hide-a-password"></a>Pokaż lub Ukryj hasło

Typowym sposobem pomóc klientom w ich Powodzenie rejestracji jest aby umożliwić im zobaczyć, jakie wprowadzono jako jego hasło. Ta opcja pomaga użytkownikom zarejestrować się, co pozwala na łatwe Zobacz oraz wprowadzić korekty do swojego hasła, jeśli to konieczne. Każde pole typu hasło ma pole wyboru z etykietą **Pokaż hasło** .  Dzięki temu użytkownika, aby wyświetlić hasło w postaci zwykłego tekstu. Uwzględnij ten fragment kodu w szablonie rejestracji lub logowania dla strony samodzielnie:

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

Dołącz następujący kod do strony, na której chcesz dołączyć pole wyboru **warunki użytkowania** . To pole wyboru jest zazwyczaj potrzebne na swoich stronach rejestracji konta rejestracji i społecznościowych konta lokalnego.

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

W kodzie Zastąp `termsOfUseUrl` linkiem do warunków użytkowania. Dla katalogu Utwórz nowy atrybut użytkownika o nazwie **termsOfUse** , a następnie Dołącz **termsOfUse** jako atrybut użytkownika.

## <a name="next-steps"></a>Następne kroki

Więcej informacji o sposobach dostosowywania interfejsu użytkownika aplikacji można znaleźć w temacie [Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C](custom-policy-ui-customization.md).
