---
title: Dostosowanie języka w Azure Active Directory B2C
description: Dowiedz się więcej o dostosowywaniu środowiska w przepływach użytkownika.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bced7a4b994172a1a2076149d6f25adb39c99b54
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015563"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Dostosowanie języka w Azure Active Directory B2C

Dostosowanie języka w programie Azure Active Directory B2C (Azure AD B2C) umożliwia przepływom użytkownika dostosowywać się do potrzeb klientów. Firma Microsoft udostępnia tłumaczenia dla [języków 36](#supported-languages), ale można również udostępniać własne tłumaczenia w dowolnym języku. Nawet jeśli środowisko użytkownika jest dostarczane tylko dla jednego języka, można dostosować dowolny tekst na stronach.

## <a name="how-language-customization-works"></a>Jak działa dostosowanie języka

Za pomocą dostosowywania języka można wybrać Języki, w których przepływ użytkownika jest dostępny. Po włączeniu funkcji można podać parametr ciągu zapytania, `ui_locales`z poziomu aplikacji. Gdy wywołasz się do Azure AD B2C, Strona zostanie przetłumaczona na wskazane ustawienia regionalne. Ten typ konfiguracji zapewnia pełną kontrolę nad językami w przepływie użytkownika i ignoruje ustawienia języka w przeglądarce klienta.

Użytkownik może nie potrzebować tego poziomu kontroli nad językami, które widzi klient. Jeśli nie podano `ui_locales` parametru, środowisko klienta jest podyktowane ustawieniami przeglądarki. Można nadal kontrolować Języki, do których przepływ użytkownika jest tłumaczony przez dodanie go jako obsługiwanego języka. Jeśli w przeglądarce klienta ustawiono język, który nie ma być obsługiwany, w zamian zostanie wyświetlony język wybrany jako domyślny w obsługiwanych kulturach.

* **określony język interfejsu użytkownika**: Po włączeniu dostosowywania języka przepływ użytkownika zostanie przetłumaczony na język, który został określony w tym miejscu.
* **Język żądany w przeglądarce**: Jeśli żaden `ui_locales` parametr nie został określony, przepływ użytkownika jest tłumaczony na język żądany w przeglądarce, *Jeśli język jest obsługiwany*.
* **Język domyślny zasad**: Jeśli w przeglądarce nie określono języka lub określono taki, który nie jest obsługiwany, przepływ użytkownika jest tłumaczony na język domyślny przepływu użytkownika.

> [!NOTE]
> Jeśli używasz niestandardowych atrybutów użytkownika, musisz podać własne tłumaczenia. Aby uzyskać więcej informacji, zobacz [Dostosowywanie ciągów](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Obsługa żądanych języków dla ui_locales

Zasady, które zostały utworzone przed ogólnym udostępnieniem dostosowania języka, muszą najpierw włączyć tę funkcję. Zasady i przepływy użytkowników utworzone po włączeniu dostosowywania języka są domyślnie włączone.

Po włączeniu dostosowywania języka w przepływie użytkownika można kontrolować język przepływu użytkownika, dodając `ui_locales` parametr.

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **Języki**.
1. Wybierz pozycję **Włącz Dostosowywanie języka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Wybierz Języki, w których przepływ użytkownika jest włączony

Zezwól na tłumaczenie zestawu języków dla przepływu użytkownika na żądanie w przeglądarce bez `ui_locales` parametru.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który ma być obsługiwany.
1. W okienku właściwości Zmień wartość **włączone** na **tak**.
1. Wybierz pozycję **Zapisz** w górnej części okienka właściwości.

>[!NOTE]
>`ui_locales` Jeśli parametr nie zostanie podany, Strona zostanie przetłumaczona na język przeglądarki klienta tylko wtedy, gdy jest włączona.
>

## <a name="customize-your-strings"></a>Dostosowywanie ciągów

Dostosowanie języka umożliwia dostosowanie dowolnego ciągu w przepływie użytkownika.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który chcesz dostosować.
1. W obszarze **pliki zasobów na poziomie strony**wybierz stronę, którą chcesz edytować.
1. Wybierz pozycję **Pobierz domyślne** (lub **Pobierz zastąpienia** , jeśli wcześniej edytowano ten język).

Te kroki zapewniają plik JSON, którego można użyć do rozpoczęcia edytowania ciągów.

### <a name="change-any-string-on-the-page"></a>Zmień dowolny ciąg na stronie

1. Otwórz plik JSON pobrany z poprzednich instrukcji w edytorze JSON.
1. Znajdź element, który chcesz zmienić. Możesz znaleźć `StringId` ciąg, którego szukasz, lub poszukać `Value` atrybutu, który ma zostać zmieniony.
1. `Value` Zaktualizuj atrybut o dane, które mają być wyświetlane.
1. Dla każdego ciągu, który chcesz zmienić, przejdź `Override` do. `true`
1. Zapisz plik i przekaż zmiany. (Formant przekazywania można znaleźć w tym samym miejscu, w którym został pobrany plik JSON).

> [!IMPORTANT]
> Jeśli musisz przesłonić ciąg, upewnij się, że wartość jest `Override` ustawiona na `true`. Jeśli wartość nie zostanie zmieniona, wpis zostanie zignorowany.

### <a name="change-extension-attributes"></a>Zmień atrybuty rozszerzenia

Jeśli chcesz zmienić ciąg dla niestandardowego atrybutu użytkownika lub chcesz dodać go do pliku JSON, jest w następującym formacie:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Zamień `<ExtensionAttribute>` na nazwę niestandardowego atrybutu użytkownika.

Zamień `<ExtensionAttributeValue>` na nowy ciąg, który ma być wyświetlany.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Podaj listę wartości za pomocą LocalizedCollections

Jeśli chcesz podać listę wartości odpowiedzi, musisz utworzyć `LocalizedCollections` atrybut. `LocalizedCollections`jest tablicą `Name` par i `Value` . Kolejność dla elementów będzie kolejność, w jakiej są wyświetlane. Aby dodać `LocalizedCollections`, użyj następującego formatu:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId`jest atrybutem użytkownika, do `LocalizedCollections` którego ten atrybut jest odpowiedzią.
* `Name`jest wartością pokazywaną użytkownikowi.
* `Value`to co jest zwracane w ramach żądania, gdy ta opcja jest zaznaczona.

### <a name="upload-your-changes"></a>Przekaż zmiany

1. Po wprowadzeniu zmian w pliku JSON Wróć do swojej dzierżawy B2C.
1. Wybierz pozycję **przepływy użytkownika** i kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **Języki**.
1. Wybierz język, w którym chcesz przeprowadzić tłumaczenie.
1. Wybierz stronę, na której chcesz przekazać tłumaczenia.
1. Wybierz ikonę folderu i wybierz plik JSON do przekazania.

Zmiany są zapisywane automatycznie w przepływie użytkownika.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Dostosowywanie interfejsu użytkownika strony przy użyciu dostosowywania języka

Istnieją dwa sposoby lokalizowania zawartości HTML. Jednym ze sposobów jest włączenie [dostosowywania języka](active-directory-b2c-reference-language-customization.md). Włączenie tej funkcji pozwala Azure AD B2C do przesyłania dalej parametru `ui-locales`Connect OpenID Connect do punktu końcowego. Na serwerze zawartości można użyć tego parametru, aby zapewnić dostosowane strony HTML, które są specyficzne dla języka.

Alternatywnie można ściągnąć zawartość z różnych miejsc na podstawie ustawień regionalnych, które są używane. W punkcie końcowym z obsługą mechanizmu CORS można skonfigurować strukturę folderów, aby hostować zawartość dla określonych języków. Jeśli używasz wartości `{Culture:RFC5646}`wieloznacznej, nastąpi wywołanie odpowiedniej metody. Załóżmy na przykład, że jest to identyfikator URI strony niestandardowej:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Możesz załadować stronę w `fr`. Gdy Strona pobiera zawartość HTML i CSS, pobiera z:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Dodawanie języków niestandardowych

Możesz również dodać Języki, dla których firma Microsoft obecnie nie udostępnia tłumaczeń. Musisz podać tłumaczenia dla wszystkich ciągów w przepływie użytkownika. Kody języka i ustawień regionalnych są ograniczone do tych w standardzie ISO 639-1.

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
2. Kliknij przepływ użytkownika, w którym chcesz dodać Języki niestandardowe, a następnie kliknij pozycję **Języki**.
3. Wybierz pozycję **Dodaj język niestandardowy** w górnej części strony.
4. W otwartym okienku kontekstu Zidentyfikuj język, w którym są udostępniane tłumaczenia, wprowadzając prawidłowy kod ustawień regionalnych.
5. Dla każdej strony można pobrać zestaw zastąpień dla języka angielskiego i rozpocząć korzystanie z tłumaczeń.
6. Po zakończeniu pracy z plikami JSON można przekazać je dla każdej strony.
7. Wybierz pozycję **Włącz**, a przepływ użytkownika może teraz pokazać ten język dla użytkowników.
8. Zapisz język.

>[!IMPORTANT]
>Przed zapisaniem należy włączyć Języki niestandardowe lub przekazywać do nich zastąpienia.

## <a name="additional-information"></a>Dodatkowe informacje

### <a name="page-ui-customization-labels-as-overrides"></a>Etykiety dostosowywania interfejsu użytkownika strony jako zastąpienia

Po włączeniu dostosowywania języka poprzednie zmiany w etykietach korzystających z dostosowywania interfejsu użytkownika są utrwalane w pliku JSON dla języka angielskiego (pl). Można nadal zmieniać etykiety i inne ciągi przez przekazywanie zasobów języka w celu dostosowania języka.

### <a name="up-to-date-translations"></a>Aktualne tłumaczenia

Firma Microsoft dokłada starań, aby zapewnić najbardziej aktualne tłumaczenia do użycia. Firma Microsoft stale ulepsza tłumaczenia i zapewnia ich zgodność. Firma Microsoft zidentyfikuje usterki i zmiany w globalnej terminologii oraz wprowadzi aktualizacje, które bezproblemowo pracują w przepływie użytkownika.

### <a name="support-for-right-to-left-languages"></a>Obsługa języków pisanych od prawej do lewej

Firma Microsoft obecnie nie obsługuje języków pisanych od prawej do lewej. Można to zrobić za pomocą niestandardowych ustawień regionalnych i CSS, aby zmienić sposób wyświetlania ciągów. Jeśli potrzebujesz tej funkcji, zagłosuj na opinię na temat [platformy Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Tłumaczenia dostawcy tożsamości społecznościowej

Firma Microsoft udostępnia `ui_locales` parametr OIDC na potrzeby logowania do sieci społecznościowych. Jednak niektórzy dostawcy tożsamości społecznościowych, w tym Facebook i Google, nie honorli.

### <a name="browser-behavior"></a>Zachowanie przeglądarki

Programy Chrome i Firefox żądają swojego języka zestawu. Jeśli jest to obsługiwany język, jest on wyświetlany przed ustawieniem domyślnym. Przeglądarka Microsoft Edge obecnie nie żąda języka i przechodzi bezpośrednio do języka domyślnego.

## <a name="supported-languages"></a>Obsługiwane języki

Azure AD B2C obejmuje obsługę następujących języków. Języki przepływu użytkownika są udostępniane przez Azure AD B2C. Języki powiadomień usługi uwierzytelniania wieloskładnikowego (MFA) są udostępniane przez [usługę Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Język              | Kod języka | Przepływy użytkowników         | Powiadomienia usługi MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabski                | ty            | y                | :heavy_check_mark: |
| Bułgarski             | BG            | y                | :heavy_check_mark: |
| Bengalski                | bn            | :heavy_check_mark: | y                |
| Kataloński               | urzędu certyfikacji            | y                | :heavy_check_mark: |
| Czeski                 | cs            | :heavy_check_mark: | :heavy_check_mark: |
| Duński                | da            | :heavy_check_mark: | :heavy_check_mark: |
| niemiecki                | de            | :heavy_check_mark: | :heavy_check_mark: |
| Grecki                 | el            | :heavy_check_mark: | :heavy_check_mark: |
| Angielski               | pl-PL            | :heavy_check_mark: | :heavy_check_mark: |
| Hiszpański               | es            | :heavy_check_mark: | :heavy_check_mark: |
| Estoński              | staw            | y                | :heavy_check_mark: |
| Baskijski                | Europejska            | y                | :heavy_check_mark: |
| Fiński               | fi            | :heavy_check_mark: | :heavy_check_mark: |
| Francuski                | fr            | :heavy_check_mark: | :heavy_check_mark: |
| Galicyjski              | GL            | y                | :heavy_check_mark: |
| Gudżarati              | gu            | :heavy_check_mark: | y                |
| Hebrajski                | Przewodniczący            | y                | :heavy_check_mark: |
| Hindi                 | hi            | :heavy_check_mark: | :heavy_check_mark: |
| Chorwacki              | godz.            | :heavy_check_mark: | :heavy_check_mark: |
| Węgierski             | hu            | :heavy_check_mark: | :heavy_check_mark: |
| Indonezyjski            | id            | y                | :heavy_check_mark: |
| Włoski               | it            | :heavy_check_mark: | :heavy_check_mark: |
| Japoński              | ja            | :heavy_check_mark: | :heavy_check_mark: |
| Kazachski                | kk            | y                | :heavy_check_mark: |
| Kannada               | kn            | :heavy_check_mark: | y                |
| Koreański                | ko            | :heavy_check_mark: | :heavy_check_mark: |
| Litewski            | przelew            | y                | :heavy_check_mark: |
| Łotewski               | lv            | y                | :heavy_check_mark: |
| Malajalam             | ml            | :heavy_check_mark: | y                |
| Marathi               | mr            | :heavy_check_mark: | y                |
| Malajski                 | ms            | :heavy_check_mark: | :heavy_check_mark: |
| Norweski (Bokmal)      | nb            | :heavy_check_mark: | y                |
| Holenderski                 | nl            | :heavy_check_mark: | :heavy_check_mark: |
| Norweski             | nie            | y                | :heavy_check_mark: |
| Pendżabski               | pa            | :heavy_check_mark: | y                |
| Polski                | pl            | :heavy_check_mark: | :heavy_check_mark: |
| Portugalski (Brazylia)   | pt-br         | :heavy_check_mark: | :heavy_check_mark: |
| Portugalski (Portugalia) | pt-pt         | :heavy_check_mark: | :heavy_check_mark: |
| Rumuński              | ro            | :heavy_check_mark: | :heavy_check_mark: |
| Rosyjski               | ru            | :heavy_check_mark: | :heavy_check_mark: |
| Słowacki                | SK            | :heavy_check_mark: | :heavy_check_mark: |
| Słoweński             | sl            | y                | :heavy_check_mark: |
| Serbski — Cyrylica    | Wirtualizacja SR-cryl-CS    | y                | :heavy_check_mark: |
| Serbski (łaciński)       | Wirtualizacja sr-latn-cs    | y                | :heavy_check_mark: |
| Szwedzki               | sv            | :heavy_check_mark: | :heavy_check_mark: |
| Tamilski                 | ta            | :heavy_check_mark: | y                |
| Telugu                | Usuń            | :heavy_check_mark: | y                |
| Tajlandzki                  | .            | :heavy_check_mark: | :heavy_check_mark: |
| Turecki               | zdawczy            | :heavy_check_mark: | :heavy_check_mark: |
| Ukraiński             | Zjednoczone Królestwo            | y                | :heavy_check_mark: |
| Wietnamski            | vi            | y                | :heavy_check_mark: |
| Chiński (uproszczony)  | zh-Hans       | :heavy_check_mark: | :heavy_check_mark: |
| Chiński — tradycyjny | zh-Hant       | :heavy_check_mark: | :heavy_check_mark: |
