---
title: Dostosowywanie interfejsu użytkownika
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować interfejs użytkownika dla aplikacji korzystających z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ddf57057b736cd76a74276e5593a865e7df8cc
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666865"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika w usłudze Azure Active Directory B2C

Znakowanie i dostosowywanie interfejsu użytkownika, który usługa Azure Active Directory B2C (Azure AD B2C) wyświetla klientom, pomaga zapewnić bezproblemowe środowisko użytkownika w aplikacji. Te środowiska obejmują rejestrację, logowanie się, edycję profilu i resetowanie hasła. W tym artykule przedstawiono metody dostosowywania interfejsu użytkownika dla przepływów użytkownika i zasad niestandardowych.

## <a name="ui-customization-in-different-scenarios"></a>Dostosowywanie interfejsu użytkownika w różnych scenariuszach

Istnieje kilka sposobów, aby dostosować interfejs użytkownika środowiska aplikacji, każdy odpowiedni dla różnych scenariuszy.

### <a name="user-flows"></a>Przepływy użytkowników

W przypadku korzystania z [przepływów użytkownika](user-flow-overview.md)można zmienić wygląd stron przepływu użytkownika za pomocą *wbudowanych szablonów układu stron*lub przy użyciu własnych formatów HTML i CSS. Obie metody są omówione w dalszej części tego artykułu.

Za pomocą [witryny Azure Portal](tutorial-customize-ui.md) można skonfigurować dostosowanie interfejsu użytkownika dla przepływów użytkowników.

> [!TIP]
> Jeśli chcesz zmodyfikować tylko logo baneru, obraz tła i kolor tła stron przepływu użytkownika, możesz wypróbować funkcję [znakowania firmy (podgląd)](#company-branding-preview) opisaną w dalszej części tego artykułu.

### <a name="custom-policies"></a>Zasady niestandardowe

Jeśli używasz [zasad niestandardowych](custom-policy-overview.md) do rejestrowania się lub logowania, resetowania hasła lub edycji profilu w aplikacji, użyj [plików zasad, aby dostosować interfejs użytkownika](custom-policy-ui-customization.md).

Jeśli musisz udostępnić zawartość dynamiczną na podstawie decyzji klienta, użyj zasad niestandardowych, które mogą [dynamicznie zmieniać zawartość strony](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) w zależności od parametru wysyłanego w ciągu zapytania. Na przykład można zmienić obraz tła na stronie rejestracji usługi Azure AD B2C lub logowania na podstawie parametru, który przechodzisz z aplikacji sieci web lub aplikacji mobilnej.

### <a name="javascript"></a>JavaScript

Kod JavaScript po stronie klienta można włączyć zarówno w [przepływach użytkownika,](user-flow-javascript-overview.md) jak i [w zasadach niestandardowych.](page-layout.md)

### <a name="sign-in-only-ui-customization"></a>Dostosowywanie tylko do logowania się do interfejsu użytkownika

Jeśli udostępniasz tylko logowanie, wraz z towarzyszącą mu stroną resetowania hasła i weryfikacyjnymi wiadomościami e-mail, użyj tych samych kroków dostosowywania, które są używane dla [strony logowania usługi Azure AD.](../active-directory/fundamentals/customize-branding.md)

Jeśli klienci próbują edytować swój profil przed zalogowaniem się, są przekierowywani do strony, którą dostosowujesz, wykonując te same kroki, które są używane do dostosowywania strony logowania usługi Azure AD.

## <a name="page-layout-templates"></a>Szablony układu strony

Przepływy użytkowników zapewniają kilka wbudowanych szablonów, z których można wybierać, aby nadać stronom doświadczenia użytkownika profesjonalny wygląd. Te szablony układu mogą również służyć i służyć jako punkt wyjścia dla własnego dostosowania.

W obszarze **Dostosuj** w menu po lewej stronie wybierz pozycję **Układy stron,** a następnie wybierz pozycję **Szablon**.

![Z listy rozwijanej wyboru szablonu na stronie przepływu użytkownika witryny Azure portal](media/customize-ui-overview/template-selection.png)

Następnie wybierz szablon z listy. Oto przykłady stron logowania dla każdego szablonu:

| Błękitny Ocean | Szary łupek | Wdrożenie klasyczne |
|:-:|:-:|:-:|
|![Przykład szablonu Ocean Blue renderowanego na stronie logowania do rejestracji](media/customize-ui-overview/template-ocean-blue.png)|![Przykład szablonu Szary łupek renderowany na stronie logowania do rejestracji](media/customize-ui-overview/template-slate-gray.png)|![Przykład szablonu klasycznego renderowanego na stronie logowania do rejestracji](media/customize-ui-overview/template-classic.png)|

Po wybraniu szablonu wybrany układ jest stosowany do wszystkich stron w przepływie użytkownika, a identyfikator URI dla każdej strony jest widoczny w polu **URI strony niestandardowej.**

## <a name="custom-html-and-css"></a>Niestandardowy kod HTML i CSS

Jeśli chcesz zaprojektować własny układ zasad za pomocą dostosowanego kodu HTML i CSS, możesz to zrobić, przełączając przełącznik "Użyj niestandardowej zawartości strony" dla każdej nazwy układu obecnej w zasadach. Postępuj zgodnie z poniższymi instrukcjami dotyczącymi konfiguracji układu niestandardowego:

Usługa Azure AD B2C uruchamia kod w przeglądarce klienta przy użyciu podejścia o nazwie [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/).

W czasie wykonywania zawartość jest ładowana z adresu URL określonego w przepływie użytkownika lub zasadach niestandardowych. Każda strona w środowiska użytkownika ładuje swoją zawartość z adresu URL, który określisz dla tej strony. Po załadowaniu zawartości z adresu URL jest scalana z fragmentem HTML wstawionym przez usługę Azure AD B2C, a następnie strona jest wyświetlana klientowi.

Przed użyciem własnych plików HTML i CSS do dostosowania interfejsu użytkownika należy zapoznać się z następującymi wskazówkami:

- Usługa Azure AD B2C **scala** zawartość HTML ze stronami. Nie kopiuj i nie próbuj zmieniać domyślnej zawartości dostarczanej przez usługę Azure AD B2C. Najlepiej jest tworzyć zawartość HTML od podstaw i używać domyślnej zawartości jako odniesienia.
- **JavaScript** może być zawarty w zawartości niestandardowej zarówno dla [przepływów użytkownika,](user-flow-javascript-overview.md) jak i [zasad niestandardowych.](javascript-samples.md)
- Obsługiwane **wersje przeglądarki** to:
  - Internet Explorer 11, 10 i Microsoft Edge
  - Ograniczona obsługa programu Internet Explorer 9 i 8
  - Google Chrome 42.0 lub nowsze
  - Mozilla Firefox 38.0 i powyżej
  - Safari dla iOS i macOS w wersji 12 i wyższej
- Nie dołączaj **tagów formularzy** do kodu HTML. Znaczniki formularzy zakłócają operacje POST generowane przez kod HTML wstrzyknięty przez usługę Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Gdzie przechowywać zawartość interfejsu użytkownika?

Korzystając z własnych plików HTML i CSS, aby dostosować interfejs użytkownika, można hostować zawartość interfejsu użytkownika w dowolnym publicznie dostępnym punkcie końcowym HTTPS obsługującym cors. Na przykład [magazyn obiektów Blob platformy Azure](../storage/blobs/storage-blobs-introduction.md), serwery sieci web, sieci CDN, AWS S3 lub systemy udostępniania plików.

Ważne jest to, że zawartość jest hostowana w publicznie dostępnym punkcie końcowym HTTPS z [włączoną funkcją CORS](https://enable-cors.org/server.html). Podczas określania go w treści należy użyć bezwzględnego adresu URL.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat tworzenia zawartości HTML, przekazywania zawartości do magazynu obiektów Blob platformy Azure i konfigurowania usługi CORS, zobacz [sekcję instruktażową zawartości strony niestandardowej](custom-policy-ui-customization.md#custom-page-content-walkthrough) w artykule dostosowywania interfejsu użytkownika.

## <a name="get-started-with-custom-html-and-css"></a>Wprowadzenie do niestandardowego kodu HTML i CSS

Zacznij używać własnych html i CSS na stronach środowiska użytkownika, postępować zgodnie z tymi wskazówkami.

- Tworzenie dobrze ukształtowanej zawartości HTML z pustym `<div id="api"></div>` elementem znajdującym się gdzieś w pliku `<body>`. Ten element oznacza, gdzie jest wstawiana zawartość usługi Azure AD B2C. W poniższym przykładzie przedstawiono minimalną stronę:

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

- Użyj CSS do stylu elementów interfejsu użytkownika, które usługa Azure AD B2C wstawia do strony. W poniższym przykładzie pokazano prosty plik CSS, który zawiera również ustawienia elementów HTML wstrzykniętych w trybie rejestracji:

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

- Hostuj zawartość w punkcie końcowym HTTPS (z dozwolonym mechanizmem CORS). Metody żądania GET i OPTIONS muszą być włączone podczas konfigurowania mechanizmu CORS.
- Utwórz lub edytuj przepływ użytkownika lub zasady niestandardowe, aby użyć utworzonej zawartości.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmenty HTML z usługi Azure AD B2C

W poniższej tabeli wymieniono fragmenty HTML, które `<div id="api"></div>` usługa Azure AD B2C scala z elementem znajdującym się w zawartości.

| Wstawiona strona | Opis kodu HTML |
| ------------- | ------------------- |
| Wybór dostawcy tożsamości | Zawiera listę przycisków dla dostawców tożsamości, które klient może wybrać podczas rejestracji lub logowania. Przyciski te obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Google lub konta lokalne (na podstawie adresu e-mail lub nazwy użytkownika). |
| Rejestracja konta lokalnego | Zawiera formularz do tworzenia konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki wprowadzania, takie jak pole wprowadzania tekstu, pole wprowadzania hasła, przycisk opcji, pola rozwijane z pojedynczym zaznaczeniem i pola wyboru z wieloma zaznaczeniami. |
| Rejestracja konta społecznościowego | Może pojawić się podczas rejestracji przy użyciu istniejącego konta od dostawcy tożsamości społecznościowych, takiego jak Facebook lub Google. Jest on używany, gdy dodatkowe informacje muszą być zbierane od klienta za pomocą formularza rejestracji. |
| Ujednolicona rejestracja lub logowanie | Obsługuje zarówno rejestrację, jak i logowanie klientów, którzy mogą korzystać z dostawców tożsamości społecznościowych, takich jak Facebook, Google lub konta lokalne. |
| Uwierzytelnianie wieloskładnikowe | Podczas rejestracji lub logowania klienci mogą weryfikować swoje numery telefonów (za pomocą tekstu lub głosu). |
| Błąd | Zawiera informacje o błędzie dla klienta. |

## <a name="company-branding-preview"></a>Branding firmowy (wersja zapoznawcza)

Strony przepływu użytkownika można dostosować za pomocą logo banera, obrazu tła i koloru tła za pomocą [znakowania firmy](../active-directory/fundamentals/customize-branding.md)Usługi Azure Active Directory Company .

Aby dostosować strony przepływu użytkownika, należy najpierw skonfigurować znakowanie firmy w usłudze Azure Active Directory, a następnie włączyć go w układach stron przepływów użytkownika w usłudze Azure AD B2C.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Konfigurowanie oznaczenia marką firmy

Zacznij od ustawienia logo banera, obrazu tła i koloru tła w **obszarze znakowanie firmy**.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W witrynie Azure portal wyszukaj i wybierz **pozycję Azure AD B2C**.
1. W obszarze **Zarządzaj**wybierz pozycję **Znakowanie firmy**.
1. Wykonaj kroki opisane w obszarze [Dodawanie znakowania do strony logowania usługi Azure Active Directory w organizacji](../active-directory/fundamentals/customize-branding.md).

Należy pamiętać o tych rzeczach podczas konfigurowania znakowania firmy w usłudze Azure AD B2C:

* Znakowanie firmy w usłudze Azure AD B2C jest obecnie ograniczone do **obrazu tła,** **logo banera**i dostosowywania **kolorów tła.** Inne właściwości w okienku znakowania firmy, na przykład te w **ustawieniach zaawansowanych,** nie są *obsługiwane*.
* Na stronach przepływu użytkownika kolor tła jest wyświetlany przed załadowaniem obrazu tła. Zalecamy wybranie koloru tła, który będzie ściśle pasować do kolorów obrazu tła, aby uzyskać płynniejsze ładowanie.
* Logo baneru pojawia się w wiadomościach e-mail weryfikacyjnych wysyłanych do użytkowników po zainicjowaniu przepływu użytkownika rejestracji.

### <a name="enable-branding-in-user-flow-pages"></a>Włączanie znakowania na stronach przepływu użytkownika

Po skonfigurowaniu marki firmowych włącz ją w przepływach użytkowników.

1. W menu po lewej stronie witryny Azure portal wybierz pozycję **Azure AD B2C**.
1. W obszarze **Zasady**wybierz **pozycję Przepływy użytkownika (zasady)**.
1. Wybierz przepływ użytkownika, dla którego chcesz włączyć znakowanie firmy. Znakowanie firmy nie jest **obsługiwane** dla typów przepływu użytkowników logowania w *wersji 1* i profilu *edycji w wersji 1.*
1. W obszarze **Dostosuj**wybierz pozycję **Układy stron**, a następnie wybierz układ, który chcesz oznaczyć. Na przykład wybierz pozycję **Unified zarejestruj się lub zaloguj się na stronie**.
1. W przypadku **wersji układu strony (wersja zapoznawcza)** wybierz wersję **1.2.0** lub więcej.
1. Wybierz **pozycję Zapisz**.

Jeśli chcesz oznaczyć wszystkie strony w przepływie użytkownika, ustaw wersję układu strony dla każdego układu strony w przepływie użytkownika.

![Wybór układu strony w usłudze Azure AD B2C w witrynie Azure portal](media/customize-ui-overview/portal-02-page-layout-select.png)

W tym przykładzie z adnotacjami pokazano niestandardowe logo baneru i obraz tła na stronie *Zarejestruj się i zaloguj* się na stronie przepływu użytkownika, która używa szablonu Ocean Blue:

![Strona rejestracji/logowania pod znakiem markasów obsługiwana przez usługę Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Używanie zasobów marki firmowych w niestandardowym formacie HTML

Aby użyć zasobów marki firmowej w niestandardowym kodzie `<div id="api">` HTML, dodaj następujące tagi poza tagiem:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Źródło obrazu zostanie zastąpione obrazem tła i logo banera. Jak opisano w sekcji [Wprowadzenie do niestandardowego kodu HTML i CSS,](#get-started-with-custom-html-and-css) użyj klas CSS, aby stylizować i pozycjonować zasoby na stronie .

## <a name="localize-content"></a>Lokalizowanie zawartości

Lokalizuj zawartość HTML, włączając [dostosowywanie języka](user-flow-language-customization.md) w dzierżawie usługi Azure AD B2C. Włączenie tej funkcji umożliwia usłudze Azure AD B2C przekazywanie parametru `ui-locales` OpenID Connect do punktu końcowego. Serwer zawartości może używać tego parametru do dostarczania stron HTML specyficznych dla języka.

Zawartość można pobierać z różnych miejsc na podstawie ustawień regionalnych, które są używane. W punkcie końcowym obsługującym mechanizm CORS skonfigurowano strukturę folderów do hosta zawartości dla określonych języków. Jeśli użyjesz wartości `{Culture:RFC5646}`symboli wieloznacznych, zadzwonisz do właściwego.

Na przykład identyfikator URI strony niestandardowej może wyglądać następująco:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Możesz załadować stronę w języku francuskim, pobierając zawartość z:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Przykłady

Kilka przykładowych plików szablonów można znaleźć w repozytorium [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) w usłudze GitHub.

Przykładowe pliki HTML i CSS w szablonach znajdują się w katalogu [/sample_templates.](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)

## <a name="next-steps"></a>Następne kroki

- Jeśli używasz **przepływów użytkownika,** możesz rozpocząć dostosowywanie interfejsu użytkownika za pomocą samouczka:

    [Dostosuj interfejs użytkownika aplikacji w usłudze Azure Active Directory B2C](tutorial-customize-ui.md).
- Jeśli używasz **zasad niestandardowych,** możesz rozpocząć dostosowywanie interfejsu użytkownika za pomocą tego artykułu:

    [Dostosuj interfejs użytkownika aplikacji przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-ui-customization.md).
