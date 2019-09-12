---
title: Informacje o dostosowywaniu interfejsu użytkownika w programie Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej na temat dostosowywania interfejsu użytkownika dla aplikacji korzystających z Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ae12742c2ad50d5bf1caaf14ae2f6d34bd6d3a2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880784"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Informacje o dostosowywaniu interfejsu użytkownika w programie Azure Active Directory B2C

Możliwość oznaczania i dostosowywania interfejsu użytkownika (UI), który Azure Active Directory B2C (Azure AD B2C) służy do aplikacji, jest ważna dla zapewnienia bezproblemowego środowiska dla klienta. Te doświadczenia obejmują rejestrowanie, logowanie, edytowanie profilów i resetowanie hasła. Ten artykuł zawiera informacje ułatwiające dostosowanie interfejsu użytkownika aplikacji.

W zależności od potrzeb, gdy chodzi o te środowiska, można dostosować interfejs użytkownika aplikacji na różne sposoby. Przykład:

- Jeśli używasz przepływów [użytkowników](active-directory-b2c-reference-policies.md) , aby zapewnić obsługę tworzenia konta lub logowania, resetowania haseł lub edytowania profilu w aplikacji, użyj [Azure Portal, aby dostosować interfejs użytkownika](tutorial-customize-ui.md).
- Jeśli używasz przepływu użytkownika w wersji 2, możesz użyć [szablonu układu strony](#page-layout-templates) , aby zmienić wygląd stron przepływu użytkownika bez dalszych dostosowań. Na przykład można zastosować motyw Ocean niebieski lub ciemnoszary do wszystkich stron w przepływie użytkownika.
- Jeśli udostępniasz tylko logowanie, na stronie resetowania hasła i wiadomości e-mail weryfikacyjnych należy użyć tych samych kroków dostosowania, które są używane dla [strony logowania usługi Azure AD](../active-directory/fundamentals/customize-branding.md).
- Jeśli klienci próbują edytować swój profil przed zalogowaniem się, zostaną przekierowani do strony, którą można dostosować, przy użyciu tych samych kroków, które są używane do dostosowywania strony logowania usługi Azure AD.
- Jeśli używasz [zasad niestandardowych](active-directory-b2c-overview-custom.md) w celu zapewnienia rejestracji lub logowania, resetowania haseł lub edytowania profilu w aplikacji, użyj [plików zasad, aby dostosować interfejs użytkownika](active-directory-b2c-ui-customization-custom.md).
- Jeśli musisz dostarczyć zawartość dynamiczną na podstawie decyzji klienta, użyj [zasad niestandardowych, które mogą zmieniać zawartość strony](active-directory-b2c-ui-customization-custom-dynamic.md) w zależności od parametru, który jest wysyłany w ciągu zapytania. Na przykład obraz tła na stronie rejestracji i logowania Azure AD B2C zmienia się w oparciu o parametr przekazywany z aplikacji sieci Web lub mobilnej.
- Kod po stronie klienta w języku JavaScript można włączyć w Azure AD B2C [przepływów użytkownika](user-flow-javascript-overview.md) lub [zasad niestandardowych](page-layout.md).

Azure AD B2C uruchamia kod w przeglądarce klienta i korzysta z nowoczesnego podejścia zwanego [współużytkowaniem zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/). W czasie wykonywania zawartość jest ładowana z adresu URL określonego w przepływie lub zasadach użytkownika. Należy określić różne adresy URL dla różnych stron. Po załadowaniu zawartości z adresu URL jest on scalany ze fragmentem kodu HTML wstawionym z Azure AD B2C, a następnie wyświetlany dla klienta.

Korzystając z własnych plików HTML i CSS, aby dostosować interfejs użytkownika, przed rozpoczęciem należy zapoznać się z poniższymi wskazówkami:

- Azure AD B2C Scala zawartość HTML na stronach. Nie Kopiuj ani nie próbuj zmienić domyślnej zawartości dostarczanej przez Azure AD B2C. Najlepszym rozwiązaniem jest skompilowanie zawartości HTML od podstaw i użycie domyślnej zawartości jako odwołania.
- Kod JavaScript można teraz dołączać do zawartości niestandardowej.
- Obsługiwane wersje przeglądarek:
    - Internet Explorer 11, 10 i Microsoft Edge
    - Ograniczona obsługa programu Internet Explorer 9 i 8
    - Google Chrome 42,0 i nowsze
    - Mozilla Firefox 38,0 i nowsze
- Upewnij się, że nie dołączysz tagów formularza w kodzie HTML, ponieważ zakłóca operacje POST wygenerowane przez wprowadzony kod HTML z Azure AD B2C.

## <a name="page-layout-templates"></a>Szablony układów stron

W przypadku przepływów użytkownika w wersji 2 można wybrać wstępnie zaprojektowany szablon, który udostępnia stronom domyślnym lepszy wygląd i służy jako dobry sposób na własne dostosowanie.

W menu po lewej stronie w obszarze **Dostosuj**wybierz pozycję **układy strony**. Następnie wybierz pozycję **szablon (wersja zapoznawcza)** .

![Lista rozwijana wybierania szablonu na stronie przepływu użytkownika Azure Portal](media/customize-ui-overview/template.png)

Wybierz szablon z listy. Na przykład niebieski szablon **oceanu** stosuje następujący układ do stron przepływu użytkownika:

![Przykład niebieskiego szablonu oceanu renderowany podczas rejestracji na stronie logowania](media/customize-ui-overview/ocean-blue.png)

Po wybraniu szablonu wybrany układ zostanie zastosowany do wszystkich stron w przepływie użytkownika, a identyfikator URI dla każdej strony jest widoczny w polu **niestandardowy identyfikator URI strony** .

## <a name="where-do-i-store-ui-content"></a>Gdzie mogę przechowywać zawartość interfejsu użytkownika?

W przypadku używania własnych plików HTML i CSS do dostosowywania interfejsu użytkownika można hostować zawartość interfejsu użytkownika w dowolnym miejscu, na przykład na [platformie Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), serwerach sieci Web, sieci CDN, AWS S3 lub systemach udostępniania plików. Ważnym punktem jest hostowanie zawartości w publicznie dostępnym punkcie końcowym HTTPS z włączonym mechanizmem CORS. Musisz użyć bezwzględnego adresu URL, gdy określisz go w zawartości.

## <a name="how-do-i-get-started"></a>Jak rozpocząć?

Aby dostosować interfejs użytkownika, wykonaj następujące czynności:

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

- Hostowanie zawartości w punkcie końcowym HTTPS (z dozwolonym mechanizmem CORS). Podczas konfigurowania mechanizmu CORS należy włączyć obie metody żądania GET i OPTIONS.
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

- Utwórz lub Edytuj zasady, aby użyć utworzonej zawartości.

W poniższej tabeli wymieniono fragmenty kodu HTML, które Azure AD B2C scalane `<div id="api"></div>` do elementu znajdującego się w zawartości.

| Wstawiona Strona | Opis HTML |
| ------------- | ------------------- |
| Wybór dostawcy tożsamości | Zawiera listę przycisków dostawców tożsamości, z których może skorzystać klient podczas rejestracji lub logowania. Te przyciski obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Google lub konta lokalne (na podstawie adresu e-mail lub nazwy użytkownika). |
| Rejestracja konta lokalnego | Zawiera formularz rejestracji konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki wejściowe, takie jak pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z pojedynczym wybieraniem i pola wyboru z zaznaczeniem wielu. |
| Konto społecznościowe — Rejestracja | Może pojawić się podczas rejestracji przy użyciu istniejącego konta od dostawcy tożsamości społecznościowej, takiego jak Facebook lub Google. Jest on używany, gdy dodatkowe informacje muszą być zbierane z klienta przy użyciu formularza tworzenia konta. |
| Ujednolicone rejestrowanie lub logowanie | Obsługuje zarówno rejestrowanie, jak i logowanie klientów, którzy mogą korzystać z dostawców tożsamości społecznościowych, takich jak Facebook, Google lub konta lokalnego. |
| Uwierzytelnianie wieloskładnikowe | Klienci mogą weryfikować numery telefonów (przy użyciu tekstu lub głosu) podczas rejestracji lub logowania. |
| Błąd | Zawiera informacje o błędach dla klienta. |


## <a name="how-do-i-localize-content"></a>Jak mogę lokalizować zawartości?

Zawartość HTML można zlokalizować, włączając [Dostosowywanie języka](active-directory-b2c-reference-language-customization.md) w dzierżawie Azure AD B2C. Włączenie tej funkcji umożliwia Azure AD B2C przekazanie parametru `ui-locales` OpenID Connect Connect do punktu końcowego. Twój serwer zawartości może użyć tego parametru, aby udostępnić strony HTML specyficzne dla języka.

Zawartość można ściągnąć z różnych miejsc w oparciu o używane ustawienia regionalne. W punkcie końcowym z obsługą mechanizmu CORS można skonfigurować strukturę folderów, aby hostować zawartość dla określonych języków. Jeśli używasz wartości wieloznacznej {Culture: RFC5646}, nastąpi wywołanie tego samego. Na przykład identyfikator URI strony niestandardowej może wyglądać następująco `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Możesz załadować stronę w języku francuskim, pobierając zawartość z`https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Przykłady

Przykłady dostosowywania można pobrać i przejrzeć te [przykładowe pliki szablonów](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Następne kroki

- Jeśli używasz **przepływów użytkowników**, możesz rozpocząć Dostosowywanie interfejsu użytkownika przy użyciu samouczka:

    [Dostosuj interfejs użytkownika aplikacji w Azure Active Directory B2C](tutorial-customize-ui.md).
- Jeśli używasz **zasad niestandardowych**, możesz rozpocząć Dostosowywanie interfejsu użytkownika przy użyciu artykułu:

    [Dostosuj interfejs użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
