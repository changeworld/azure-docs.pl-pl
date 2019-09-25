---
title: Dostosowywanie interfejsu użytkownika w Azure Active Directory B2C
description: Dowiedz się, jak dostosować interfejs użytkownika dla aplikacji korzystających z Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6ebaeedf88bc02aa16e8be07fcb734e44ffa5bb6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258165"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika w Azure Active Directory B2C

Oznakowanie i dostosowanie interfejsu użytkownika, który jest wyświetlany Azure Active Directory B2C (Azure AD B2C) dla klientów, zapewnia bezproblemowe środowisko użytkownika w aplikacji. Do tych środowisk należą: rejestrowanie, logowanie, edytowanie profilów i resetowanie hasła. W tym artykule przedstawiono metody dostosowywania interfejsu użytkownika dla przepływów użytkowników i zasad niestandardowych.

## <a name="ui-customization-in-different-scenarios"></a>Dostosowanie interfejsu użytkownika w różnych scenariuszach

Istnieje kilka sposobów na dostosowanie interfejsu użytkownika aplikacji, z których każdy jest odpowiedni dla różnych scenariuszy.

### <a name="user-flows"></a>Przepływy użytkownika

Jeśli używasz [przepływów użytkownika](active-directory-b2c-reference-policies.md), możesz zmienić wygląd stron przepływu użytkownika przy użyciu wbudowanych *szablonów układów stron*lub przy użyciu własnego kodu HTML i CSS. Obie metody zostały omówione w dalszej części tego artykułu.

Użyj [Azure Portal](tutorial-customize-ui.md) , aby skonfigurować Dostosowywanie interfejsu użytkownika dla przepływów użytkowników.

### <a name="custom-policies"></a>Zasady niestandardowe

Jeśli używasz [zasad niestandardowych](active-directory-b2c-overview-custom.md) w celu zapewnienia rejestracji lub logowania, resetowania haseł lub edytowania profilu w aplikacji, użyj [plików zasad, aby dostosować interfejs użytkownika](active-directory-b2c-ui-customization-custom.md).

Jeśli musisz dostarczyć zawartość dynamiczną na podstawie decyzji klienta, użyj zasad niestandardowych, które mogą [dynamicznie zmieniać zawartość strony](active-directory-b2c-ui-customization-custom-dynamic.md) w zależności od parametru, który jest wysyłany w ciągu zapytania. Na przykład można zmienić obraz tła na stronie rejestracji lub logowania na Azure AD B2C na podstawie parametru przekazanego z aplikacji sieci Web lub mobilnej.

### <a name="javascript"></a>JavaScript

Kod JavaScript po stronie klienta można włączyć zarówno w przypadku [przepływów użytkownika](user-flow-javascript-overview.md) , jak i [zasad niestandardowych](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Dostosowanie interfejsu użytkownika tylko do logowania

Jeśli udostępniasz tylko logowanie, wraz z towarzyszącą stroną resetowania hasła i weryfikacyjnymi wiadomościami e-mail, Użyj tych samych kroków dostosowania, które są używane dla [strony logowania usługi Azure AD](../active-directory/fundamentals/customize-branding.md).

Jeśli klienci próbują edytować swój profil przed zalogowaniem się, nastąpi przekierowanie do strony, która została dostosowana, przy użyciu tych samych kroków, które są używane do dostosowywania strony logowania usługi Azure AD.

## <a name="page-layout-templates"></a>Szablony układów stron

Przepływy użytkownika udostępniają kilka wbudowanych szablonów, z których można skorzystać, aby umożliwić użytkownikom korzystanie ze stron o profesjonalnym wyglądzie. Te szablony układów mogą również działać jako punkt wyjścia dla własnych dostosowań.

W obszarze **Dostosuj** w menu po lewej stronie wybierz pozycję **układy stron** , a następnie wybierz pozycję **szablon**.

![Lista rozwijana wybierania szablonu na stronie przepływu użytkownika Azure Portal](media/customize-ui-overview/template-selection.png)

Następnie wybierz szablon z listy. Poniżej przedstawiono przykłady stron logowania dla każdego szablonu:

| Oceaniczny niebieski | Szaroniebieski | Klasyczny |
|:-:|:-:|:-:|
|![Przykład niebieskiego szablonu oceanu renderowany podczas rejestracji na stronie logowania](media/customize-ui-overview/template-ocean-blue.png)|![Przykład szarego szablonu renderowanego na stronie logowania](media/customize-ui-overview/template-slate-gray.png)|![Przykładowy szablon klasyczny renderowany podczas rejestracji na stronie logowania](media/customize-ui-overview/template-classic.png)|

Po wybraniu szablonu wybrany układ zostanie zastosowany do wszystkich stron w przepływie użytkownika, a identyfikator URI dla każdej strony jest widoczny w polu **niestandardowy identyfikator URI strony** .

## <a name="custom-html-and-css"></a>Niestandardowe HTML i CSS

Azure AD B2C uruchamia kod w przeglądarce klienta przy użyciu metody zwanej [współużytkowaniem zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).

W czasie wykonywania zawartość jest ładowana z adresu URL określonego w przepływie użytkownika lub w zasadach niestandardowych. Każda Strona w środowisku użytkownika ładuje swoją zawartość z adresu URL określonego dla tej strony. Po załadowaniu zawartości z adresu URL zostanie on scalony z fragmentem kodu HTML wstawionym przez Azure AD B2C, a następnie zostanie wyświetlona strona klienta.

Zapoznaj się z poniższymi wskazówkami przed użyciem własnych plików HTML i CSS, aby dostosować interfejs użytkownika:

- Azure AD B2C **Scala** zawartość HTML na stronach. Nie Kopiuj ani nie próbuj zmienić domyślnej zawartości dostarczanej przez Azure AD B2C. Najlepszym rozwiązaniem jest skompilowanie zawartości HTML od podstaw i użycie domyślnej zawartości jako odwołania.
- **Kod JavaScript** można uwzględnić w zawartości niestandardowej zarówno dla [przepływów użytkownika](user-flow-javascript-overview.md) , jak i [zasad niestandardowych](javascript-samples.md).
- Obsługiwane **wersje przeglądarek** :
  - Internet Explorer 11, 10 i Microsoft Edge
  - Ograniczona obsługa programu Internet Explorer 9 i 8
  - Google Chrome 42,0 i nowsze
  - Mozilla Firefox 38,0 i nowsze
- Nie dołączaj **tagów formularza** w kodzie HTML. Tagi formularzy zakłócają operacje POST generowane przez kod HTML wprowadzony przez Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Gdzie mogę przechowywać zawartość interfejsu użytkownika?

Korzystając z własnych plików HTML i CSS, aby dostosować interfejs użytkownika, można hostować zawartość interfejsu użytkownika w dowolnym publicznie dostępnym punkcie końcowym HTTPS, który obsługuje mechanizm CORS. Na przykład [usługa Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), serwery sieci Web, sieci CDN, AWS S3 lub systemy udostępniania plików.

Ważnym punktem jest hostowanie zawartości w publicznie dostępnym punkcie końcowym HTTPS z włączonym mechanizmem CORS. Musisz użyć bezwzględnego adresu URL, gdy określisz go w zawartości.

## <a name="get-started-with-custom-html-and-css"></a>Wprowadzenie do niestandardowego kodu HTML i CSS

Zacznij korzystać z własnego kodu HTML i CSS na stronach środowiska użytkownika, postępując zgodnie z tymi wskazówkami.

- Utwórz poprawnie sformułowaną zawartość HTML z pustym `<div id="api"></div>` elementem znajdującym się `<body>`w miejscu. Ten element oznacza miejsce wstawienia zawartości Azure AD B2C. W poniższym przykładzie przedstawiono minimalną stronę:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Użyj CSS do stylu elementów interfejsu użytkownika, które Azure AD B2C wstawia do strony. Poniższy przykład pokazuje prosty plik CSS, który zawiera również ustawienia dla elementów z wprowadzonym kodem HTML:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Hostowanie zawartości w punkcie końcowym HTTPS (z dozwolonym mechanizmem CORS). Podczas konfigurowania mechanizmu CORS należy włączyć obie metody żądania GET i OPTIONS.
- Utwórz lub Edytuj przepływ użytkownika lub zasady niestandardowe, aby użyć utworzonej zawartości.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmenty kodu HTML z Azure AD B2C

W poniższej tabeli wymieniono fragmenty kodu HTML, które Azure AD B2C scalane `<div id="api"></div>` do elementu znajdującego się w zawartości.

| Wstawiona Strona | Opis HTML |
| ------------- | ------------------- |
| Wybór dostawcy tożsamości | Zawiera listę przycisków dostawców tożsamości, z których może skorzystać klient podczas rejestracji lub logowania. Te przyciski obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Google lub konta lokalne (na podstawie adresu e-mail lub nazwy użytkownika). |
| Rejestracja konta lokalnego | Zawiera formularz rejestracji konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki wejściowe, takie jak pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z pojedynczym wybieraniem i pola wyboru z zaznaczeniem wielu. |
| Konto społecznościowe — Rejestracja | Może pojawić się podczas rejestracji przy użyciu istniejącego konta od dostawcy tożsamości społecznościowej, takiego jak Facebook lub Google. Jest on używany, gdy dodatkowe informacje muszą być zbierane z klienta przy użyciu formularza tworzenia konta. |
| Ujednolicone rejestrowanie lub logowanie | Obsługuje zarówno rejestrowanie, jak i logowanie klientów, którzy mogą korzystać z dostawców tożsamości społecznościowych, takich jak Facebook, Google lub konta lokalnego. |
| Uwierzytelnianie wieloskładnikowe | Klienci mogą weryfikować numery telefonów (przy użyciu tekstu lub głosu) podczas rejestracji lub logowania. |
| Błąd | Zawiera informacje o błędach dla klienta. |

## <a name="localize-content"></a>Lokalizowanie zawartości

Zawartość HTML można zlokalizować, włączając [Dostosowywanie języka](active-directory-b2c-reference-language-customization.md) w dzierżawie Azure AD B2C. Włączenie tej funkcji umożliwia Azure AD B2C przekazanie parametru `ui-locales` OpenID Connect Connect do punktu końcowego. Twój serwer zawartości może użyć tego parametru, aby udostępnić strony HTML specyficzne dla języka.

Zawartość można ściągnąć z różnych miejsc w oparciu o używane ustawienia regionalne. W punkcie końcowym z obsługą mechanizmu CORS można skonfigurować strukturę folderów, aby hostować zawartość dla określonych języków. Jeśli używasz wartości `{Culture:RFC5646}`wieloznacznej, nastąpi wywołanie odpowiedniej metody.

Na przykład identyfikator URI strony niestandardowej może wyglądać następująco:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Możesz załadować stronę w języku francuskim, pobierając zawartość z:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Przykłady

Kilka przykładowych plików szablonów można znaleźć w repozytorium [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) w witrynie GitHub.

Przykładowe pliki HTML i CSS w szablonach znajdują się w katalogu [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Następne kroki

- Jeśli używasz **przepływów użytkowników**, możesz rozpocząć Dostosowywanie interfejsu użytkownika przy użyciu samouczka:

    [Dostosuj interfejs użytkownika aplikacji w Azure Active Directory B2C](tutorial-customize-ui.md).
- Jeśli używasz **zasad niestandardowych**, możesz rozpocząć Dostosowywanie interfejsu użytkownika przy użyciu artykułu:

    [Dostosuj interfejs użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
