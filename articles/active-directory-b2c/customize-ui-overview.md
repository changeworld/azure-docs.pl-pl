---
title: Dostosowywanie interfejsu użytkownika w usłudze Azure Active Directory B2C — informacje | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o dostosowywaniu interfejsu użytkownika dla aplikacji korzystających z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 26dea1b636fce9e7f5defddd5cf4bc4c7d78d5da
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682017"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika w usłudze Azure Active Directory B2C — informacje

Można oznaczyć i dostosowywanie interfejsu użytkownika (UI) Azure Active Directory (Azure AD) B2C służy do aplikacji jest ważne w przypadku zapewniając bezproblemowe środowisko do klienta. Te doświadczenia obejmują profilu rejestracji, logowania, edytowania i resetowania hasła. Ten artykuł zawiera informacje ułatwiające dostosowywanie interfejsu użytkownika aplikacji.

Jeśli chodzi o tych środowisk, w zależności od potrzeb, możesz dostosować interfejsu użytkownika aplikacji na różne sposoby. Na przykład:

- Jeśli używasz [przepływy użytkownika](active-directory-b2c-reference-policies.md) Podaj hasło rejestracji lub logowania, zresetuj lub edytowania profilu środowiska w aplikacji, należy użyć [witryny Azure portal, aby dostosować interfejs użytkownika](tutorial-customize-ui.md).
- Jeśli używasz wersji 2 przepływ użytkownika, możesz użyć [szablon układu strony](#page-layout-templates) można zmienić wygląd stron przepływ użytkownika bez dalszego dostosowania. Na przykład można zastosować motyw niebieski oceanem lub tablecie szary do wszystkich stron w przepływie użytkownika.
- Jeśli udostępniasz logowania tylko, strony resetowania jego hasła towarzyszący i weryfikacji wiadomości e-mail, użyj te same kroki dostosowywania, które są używane do [strony logowania usługi Azure AD](../active-directory/fundamentals/customize-branding.md).
- Jeśli klienci spróbujesz edytować swój profil, aby się zalogować, zostanie przekierowany do strony można dostosować za pomocą te same kroki, które są używane do dostosowania strony logowania usługi Azure AD.
- Jeśli używasz [zasady niestandardowe](active-directory-b2c-overview-custom.md) podać hasło rejestracji lub logowania, należy zresetować lub profilu edycji w aplikacji, możesz użyć [pliki zasad, aby dostosować interfejs użytkownika](active-directory-b2c-ui-customization-custom.md).
- Jeśli konieczne jest zapewnienie dynamicznej zawartości, w oparciu o decyzji klienta, możesz użyć [zawartości strony zasad niestandardowych, które można zmienić](active-directory-b2c-ui-customization-custom-dynamic.md) w zależności od parametru, który będzie wysyłany w ciągu zapytania. Na przykład obraz tła na stronie tworzenia konta lub logowania usługi Azure AD B2C zmiany, na podstawie parametru, które przechodzą z sieci web lub aplikacji mobilnej.
- Można włączyć kodu po stronie klienta JavaScript w usługi Azure AD B2C [przepływy użytkownika](user-flow-javascript-overview.md) lub [zasady niestandardowe](page-contract.md).

Usługa Azure AD B2C kodu w przeglądarce klienta, korzysta z nowoczesnego podejścia o nazwie [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/). W czasie wykonywania zawartość jest ładowany z adresu URL, który określisz w przepływ użytkownika lub zasad. Możesz określić inny adres URL dla różnych stronach. Po załadowaniu zawartości z adresu URL zostało scalone z fragment kodu HTML wstawione z usługi Azure AD B2C, a następnie wyświetlane do klienta.

Dostosowywanie interfejsu użytkownika za pomocą własnych plików HTML i CSS, przed rozpoczęciem należy przejrzeć poniższe wskazówki:

- Usługa Azure AD B2C Scala zawartość HTML strony. Nie Kopiuj i spróbuj zmienić domyślnej zawartości, który zapewnia usługi Azure AD B2C. Najlepiej do tworzenia zawartości HTML od nowa i użyj domyślnej zawartości jako odwołanie.
- JavaScript mogą być teraz dołączane w zawartości niestandardowej.
- Dostępne są następujące wersje obsługiwanych przeglądarek: 
    - Internet Explorer 11, 10 i Microsoft Edge
    - Ograniczona obsługa programu Internet Explorer 9 i 8
    - Google Chrome 42.0 i nowsze wersje
    - Mozilla Firefox 38.0 i nowsze wersje
- Upewnij się, że nie uwzględniono tagów w kodzie HTML ponieważ zakłócać operacji POST, generowanych przez wprowadzonego kodu HTML z usługi Azure AD B2C.

## <a name="page-layout-templates"></a>Szablony układu strony

Przepływy użytkownika w wersji 2 można wstępnie zdefiniowanych szablon, który zapewnia lepszą wygląd domyślnych stron i służy jako podstawa dobre dla własne dostosowania.

W menu po lewej stronie w obszarze **Dostosuj**, wybierz opcję **strony układów**. Następnie wybierz pozycję **szablonu (wersja zapoznawcza)**.

![Wybierz szablon układu strony](media/customize-ui-overview/template.png)

Wybierz szablon z listy. Na przykład **Ocean niebieski** szablonu dotyczy następujących układu strony przepływ użytkownika:

![Szablon Ocean niebieski](media/customize-ui-overview/ocean-blue.png)

Wybierz szablon, wybrany układ jest stosowany do wszystkich stron w Twój przepływ użytkownika i identyfikator URI dla każdej strony jest widoczna w **identyfikator URI strony niestandardowe** pola.

## <a name="where-do-i-store-ui-content"></a>Gdzie przechowywać zawartość interfejsu użytkownika?

Dostosowywanie interfejsu użytkownika za pomocą własnych plików HTML i CSS, może obsługiwać interfejs użytkownika zawartości w dowolnym miejscu, takich jak na [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md), serwery usługi CDN, AWS S3 w sieci web lub systemów do udostępniania plików. Istotną kwestią jest to użytkownik obsługujących zawartość publicznie dostępnego punktu końcowego HTTPS z włączonym mechanizmem CORS. Po określeniu we własnych Treściach, należy użyć bezwzględnego adresu URL.

## <a name="how-do-i-get-started"></a>Jak rozpocząć?

Należy wykonać następujące polecenie, aby dostosować interfejsu użytkownika:

- Tworzenie zawartości z pustą sformułowany HTML `<div id="api"></div>` element znajduje się gdzieś w `<body>`. Ten element znaczniki, polegający na wstawieniu zawartości usługi Azure AD B2C. Minimalna strona można znaleźć w poniższym przykładzie:

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

- Hostowanie zawartości w punkcie końcowym protokołu HTTPS (przy użyciu mechanizmu CORS dozwolone). Pobierz zarówno i opcje żądania metody musi być włączona, podczas konfigurowania mechanizmu CORS.
- Wykorzystanie arkuszy CSS do określania stylu elementy interfejsu użytkownika usługi Azure AD B2C wstawia na Twoją stronę. Poniższy przykład pokazuje prosty plik CSS, który również zawiera ustawienia dla rejestracji wprowadzonego elementów kodu HTML:

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

- Tworzenie lub edytowanie zasad, aby korzystać z zawartości, który został utworzony.

W poniższej tabeli przedstawiono fragmenty kodu HTML, które usługi Azure AD B2C, scala `<div id="api"></div>` element znajduje się w zawartości.

| Wstawiono strony | Opis elementu HTML |
| ------------- | ------------------- |
| Wybieranie dostawcy tożsamości | Zawiera listę przycisków dla dostawców tożsamości, które odbiorcy mogą wybierać podczas rejestracji lub logowania. Przyciski te obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Google lub kont lokalnych (oparte na nazwę użytkownika lub adres e-mail). |
| Konto lokalne, zarejestruj się | Zawiera formularz dla lokalnego konta, zarejestruj się na podstawie adresu e-mail lub nazwę użytkownika. Formularz może zawierać innej kontrolki wejściowe, takie jak pola wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, wybieranych list rozwijanych i pól wyboru wielokrotnego wyboru. |
| Zarejestruj się konta społecznościowego | Może pojawić się podczas logowania przy użyciu istniejącego konta z dostawcy tożsamości społecznościowych, takich jak Facebook lub Google. Jest używany podczas dodatkowe informacje muszą zostać pobrane od klientów, za pomocą formularza rejestracji. |
| Ujednolicone rejestracji lub logowania | Obsługuje zarówno rejestracji i logowania klientów, którzy mogą korzystać z dostawców tożsamości społecznościowych, takich jak Facebook, Google lub kont lokalnych. |
| Uwierzytelnianie wieloskładnikowe | Klientów można sprawdzić swoje numery telefonów (przy użyciu tekstowych lub głosowych), podczas tworzenia konta lub logowania. |
| Błąd | Zawiera informacje o błędzie do klienta. |


## <a name="how-do-i-localize-content"></a>Jak zlokalizować zawartość?

Lokalizowanie zawartość HTML, należy włączyć [Dostosowywanie języka](active-directory-b2c-reference-language-customization.md) w dzierżawie usługi Azure AD B2C. Włączenie tej funkcji umożliwia usłudze Azure AD B2C do przekazywania parametru Open ID Connect `ui-locales` do punktu końcowego usługi. Serwer zawartości można użyć tego parametru zapewnienie specyficzny dla języka HTML strony.

Zawartość może zostać pobrane z różnych miejsc, w oparciu o ustawienia regionalne, który jest używany. Punkt końcowy z obsługą mechanizmu CORS służy do konfigurowania strukturę folderów do hostowania zawartości dla określonych języków. Jeśli używasz wartość symbolu wieloznacznego {kultury: RFC5646} wywołasz właściwy. Na przykład swoją niestandardową stronę identyfikatora URI może wyglądać `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Możesz załadować strony w języku francuskim, ściągając zawartość z `https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Przykłady

Przykłady dostosowywania, Pobierz i przejrzyj te [przykładowe pliki szablonów](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Kolejne kroki

- Jeśli używasz przepływy użytkownika można zacząć Dostosowywanie interfejsu użytkownika za pomocą tego samouczka: [Dostosowywanie interfejsu użytkownika aplikacji w usłudze Azure Active Directory B2C](tutorial-customize-ui.md).
- Jeśli używasz zasad niestandardowych, można zacząć Dostosowywanie interfejsu użytkownika z artykułem: [Dostosowywanie interfejsu użytkownika aplikacji za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

