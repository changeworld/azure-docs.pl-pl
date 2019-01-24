---
title: Dostosowywanie interfejsu użytkownika w usłudze Azure Active Directory B2C — informacje | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o dostosowywaniu interfejsu użytkownika dla aplikacji korzystających z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 44d1e5996b1f9b8ae65df9ad16c4bb543fdab1a0
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844776"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika w usłudze Azure Active Directory B2C — informacje

Można oznaczyć i dostosowywanie interfejsu użytkownika (UI) Azure Active Directory (Azure AD) B2C służy do aplikacji jest ważne w przypadku zapewniając bezproblemowe środowisko do klienta. Te doświadczenia obejmują profilu rejestracji, logowania, edytowania i resetowania hasła. Ten artykuł zawiera informacje ułatwiające dostosowywanie interfejsu użytkownika aplikacji.

Jeśli chodzi o tych środowisk, w zależności od potrzeb, możesz dostosować interfejsu użytkownika aplikacji na różne sposoby. Na przykład:

- Jeśli używasz [przepływy użytkownika](active-directory-b2c-reference-policies.md) Podaj hasło rejestracji lub logowania, zresetuj lub edytowania profilu środowiska w aplikacji, należy użyć [witryny Azure portal, aby dostosować interfejs użytkownika](tutorial-customize-ui.md).
- Jeśli udostępniasz logowania tylko, strony resetowania jego hasła towarzyszący i weryfikacji wiadomości e-mail, użyj te same kroki dostosowywania, które są używane do [strony logowania usługi Azure AD](../active-directory/fundamentals/customize-branding.md).
- Jeśli klienci spróbujesz edytować swój profil, aby się zalogować, zostanie przekierowany do strony można dostosować za pomocą te same kroki, które są używane do dostosowania strony logowania usługi Azure AD.
- Jeśli używasz [zasady niestandardowe](active-directory-b2c-overview-custom.md) podać hasło rejestracji lub logowania, należy zresetować lub profilu edycji w aplikacji, możesz użyć [pliki zasad, aby dostosować interfejs użytkownika](active-directory-b2c-ui-customization-custom.md).
- Jeśli konieczne jest zapewnienie dynamicznej zawartości, w oparciu o decyzji klienta, możesz użyć [zawartości strony zasad niestandardowych, które można zmienić](active-directory-b2c-ui-customization-custom-dynamic.md) w zależności od parametru, który będzie wysyłany w ciągu zapytania. Na przykład obraz tła na stronie tworzenia konta lub logowania usługi Azure AD B2C zmiany, na podstawie parametru, które przechodzą z sieci web lub aplikacji mobilnej.

Usługa Azure AD B2C kodu w przeglądarce klienta, korzysta z nowoczesnego podejścia o nazwie [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/). W czasie wykonywania zawartość jest ładowany z adresu URL, który określisz w przepływ użytkownika lub zasad. Możesz określić inny adres URL dla różnych stronach. Po załadowaniu zawartości z adresu URL zostało scalone z fragment kodu HTML wstawione z usługi Azure AD B2C, a następnie wyświetlane do klienta.

Przed rozpoczęciem należy przejrzeć następujące wskazówki:

- Usługa Azure AD B2C Scala zawartość HTML strony. Nie Kopiuj i spróbuj zmienić domyślnej zawartości, który zapewnia usługi Azure AD B2C. Najlepiej do tworzenia zawartości HTML od nowa i użyj domyślnej zawartości jako odwołanie.
- Ze względów bezpieczeństwa nie jest dozwolone do uwzględnienia JavaScript w zawartości.
- Dostępne są następujące wersje obsługiwanych przeglądarek: 
    - Internet Explorer 11, 10 i Microsoft Edge
    - Ograniczona obsługa programu Internet Explorer 9 i 8
    - Google Chrome 42.0 i nowsze wersje
    - Mozilla Firefox 38.0 i nowsze wersje
- Upewnij się, że nie uwzględniono tagów w kodzie HTML ponieważ zakłócać operacji POST, generowanych przez wprowadzonego kodu HTML z usługi Azure AD B2C.

## <a name="where-do-i-store-ui-content"></a>Gdzie przechowywać zawartość interfejsu użytkownika?

Możesz hostować zawartość w dowolnym miejscu, takich jak na interfejs użytkownika [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md), serwery usługi CDN, AWS S3 w sieci web lub systemów do udostępniania plików. Istotną kwestią jest to użytkownik obsługujących zawartość publicznie dostępnego punktu końcowego HTTPS z włączonym mechanizmem CORS. Po określeniu we własnych Treściach, należy użyć bezwzględnego adresu URL.

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

