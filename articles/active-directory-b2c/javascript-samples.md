---
title: Przykładowe skrypty JavaScript — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Informacje o używaniu języka JavaScript w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9e19df7c50ca9d2c57ab385a567f4911b200c5e2
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510891"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Przykładowe skrypty JavaScript do użycia w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Do aplikacji w usłudze Azure Active Directory (Azure AD) B2C można dodać własny kod po stronie klienta języka JavaScript. Aby włączyć język JavaScript dla aplikacji, należy dodać element do Twojej [zasad niestandardowych](active-directory-b2c-overview-custom.md), wybierz opcję [strony umowy](page-contract.md)i użyj [z usługi b2clogin.com](b2clogin.md) w żądaniach. W tym artykule opisano, jak zmienić zasady niestandardowe umożliwiające wykonywanie skryptu.

> [!NOTE]
> Jeśli chcesz włączyć język JavaScript, w przypadku przepływów użytkownika, zobacz [języków JavaScript i strony umowy wersji w usłudze Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Zaznacz kontrakt strony dla elementów interfejsu użytkownika aplikacji. Jeśli zamierzasz użyć języka JavaScript, musisz zdefiniować wersja kontraktu strony dla wszystkich definicji zawartości w zasadach niestandardowych.

## <a name="add-the-scriptexecution-element"></a>Dodaj ScriptExecution element

Włącz wykonywanie skryptu, dodając **ScriptExecution** elementu [RelyingParty](relyingparty.md) elementu.

1. Otwórz plik zasad niestandardowych. Na przykład *SignUpOrSignin.xml*.
2. Dodaj **ScriptExecution** elementu **UserJourneyBehaviors** elementu **RelyingParty**:

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

## <a name="guidelines-for-using-javascript"></a>Wytyczne dotyczące przy użyciu języka JavaScript

Podczas dostosowywania interfejsu aplikacji przy użyciu języka JavaScript, należy przestrzegać następujących wytycznych:

- Nie wiążą Zdarzenie kliknięcia w `<a>` elementów HTML.
- Nie wykona zależności kodu usługi Azure AD B2C lub komentarzy.
- Zmienianie kolejności lub hierarchia elementów HTML w usłudze Azure AD B2C. Można określić kolejność elementów interfejsu użytkownika, należy użyć zasad usługi Azure AD B2C.
- Można wywołać dowolną usługę RESTful za pomocą tych zagadnień:
    - Może być konieczne ustawienie usługi RESTful CORS, aby umożliwić połączeń HTTP klienta.
    - Upewnij się, że usługi RESTful są bezpieczne i korzystają z protokołu HTTPS.
    - Nie bezpośrednio za pomocą języka JavaScript wywoływanie punktów końcowych usługi Azure AD B2C.
- Możesz osadzić Twój kod JavaScript, lub można połączyć z zewnętrznych plików JavaScript. Korzystając z zewnętrznego pliku JavaScript, upewnij się użyć bezwzględnego adresu URL i nie względnym adresem URL.
- Struktury języka JavaScript:
    - Usługa Azure AD B2C używa określonej wersji jQuery. Nie dołączaj inna wersja jQuery. Na tej samej stronie przy użyciu więcej niż jedna wersja powoduje problemy.
    - Za pomocą RequireJS nie jest obsługiwane.
    - Większość platform JavaScript nie są obsługiwane przez usługę Azure AD B2C.
- Ustawienia usługi Azure AD B2C, może zostać odczytany przez wywołanie `window.SETTINGS`, `window.CONTENT` obiektów, takich jak bieżący język interfejsu użytkownika. Nie zmieniaj wartości tych obiektów.
- Aby dostosować komunikat o błędzie usługi Azure AD B2C, należy użyć lokalizacji w zasadach.
- Jeśli wszystko można osiągnąć za pomocą zasad, zazwyczaj jest to zalecany sposób.

## <a name="javascript-samples"></a>Przykładowe skrypty JavaScript

### <a name="show-or-hide-a-password"></a>Pokaż lub Ukryj hasło

Typowym sposobem pomóc klientom w ich Powodzenie rejestracji jest aby umożliwić im zobaczyć, jakie wprowadzono jako jego hasło. Ta opcja pomaga użytkownikom zarejestrować się, co pozwala na łatwe Zobacz oraz wprowadzić korekty do swojego hasła, jeśli to konieczne. Dowolne pole o typie hasło ma pole wyboru przy użyciu **hasło Show** etykiety.  Dzięki temu użytkownika, aby wyświetlić hasło w postaci zwykłego tekstu. Uwzględnij ten fragment kodu w szablonie rejestracji lub logowania dla strony samodzielnie:

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

Zawrzyj następujący kod na stronę, której chcesz dołączyć **warunki użytkowania** pola wyboru. To pole wyboru jest zazwyczaj potrzebne na swoich stronach rejestracji konta rejestracji i społecznościowych konta lokalnego.

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

W kodzie, Zastąp `termsOfUseUrl` z linkiem do Twoich warunków użytkowania. Dla katalogu, należy utworzyć nowy atrybut użytkownika o nazwie **termsOfUse** , a następnie dołącz **termsOfUse** jako atrybut użytkownika.

## <a name="next-steps"></a>Kolejne kroki

Znajdź więcej informacji na temat w jaki sposób dostosować interfejs użytkownika aplikacji w [Dostosowywanie interfejsu użytkownika aplikacji za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
