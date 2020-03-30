---
title: Dostosowywanie języka w usłudze Azure Active Directory B2C
description: Dowiedz się więcej o dostosowywaniu środowiska językowego w przepływach użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b3af812b2b78c276b5345b9b19226e6e1dba80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185764"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Dostosowywanie języka w usłudze Azure Active Directory B2C

Dostosowywanie języka w usłudze Azure Active Directory B2C (Azure AD B2C) umożliwia przepływ użytkownika w celu dostosowania do różnych języków dostosowanych do potrzeb klienta. Firma Microsoft udostępnia tłumaczenia na [36 języków,](#supported-languages)ale można również dostarczyć własne tłumaczenia dla dowolnego języka. Nawet jeśli środowisko jest dostępne tylko dla jednego języka, możesz dostosować dowolny tekst na stronach.

## <a name="how-language-customization-works"></a>Jak działa dostosowywanie języka

Dostosowywanie języka służy do wybierania języków, w których przepływ użytkownika jest dostępny. Po włączeniu funkcji można podać parametr ciągu `ui_locales`zapytania, z aplikacji. Po wywołaniu usługi Azure AD B2C, strona jest tłumaczona na ustawienia regionalne, które zostały wskazane. Ten typ konfiguracji daje pełną kontrolę nad językami w przepływie użytkownika i ignoruje ustawienia języka przeglądarki klienta.

Być może nie jest potrzebny ten poziom kontroli nad językami, które widzi klient. Jeśli nie podasz `ui_locales` parametru, doświadczenie klienta jest podyktowane ustawieniami przeglądarki. Nadal można kontrolować, które języki przepływ użytkownika jest tłumaczony, dodając go jako obsługiwany język. Jeśli przeglądarka klienta jest ustawiona na wyświetlanie języka, którego nie chcesz obsługiwać, zamiast tego zostanie wyświetlony język wybrany jako domyślny w obsługiwanych kulturach.

* **ui-locales określony język:** Po włączeniu dostosowywania języka przepływ użytkownika jest tłumaczony na język określony w tym miejscu.
* **Język żądany przez przeglądarkę:** Jeśli nie określono żadnego `ui_locales` parametru, przepływ użytkownika jest tłumaczony na język żądany przez *przeglądarkę, jeśli język jest obsługiwany*.
* **Domyślny język zasad:** Jeśli przeglądarka nie określa języka lub określa taki, który nie jest obsługiwany, przepływ użytkownika jest tłumaczony na domyślny język przepływu użytkownika.

> [!NOTE]
> Jeśli używasz niestandardowych atrybutów użytkownika, musisz podać własne tłumaczenia. Aby uzyskać więcej informacji, zobacz [Dostosowywanie ciągów](#customize-your-strings)znaków .

## <a name="support-requested-languages-for-ui_locales"></a>Obsługa żądanych języków dla ui_locales

Zasady utworzone przed ogólną dostępnością dostosowywania języka muszą najpierw włączyć tę funkcję. Zasady i przepływy użytkowników, które zostały utworzone po domyślnie włączone dostosowywanie języka.

Po włączeniu dostosowywania języka w przepływie użytkownika, można kontrolować język `ui_locales` przepływu użytkownika, dodając parametr.

1. W dzierżawie usługi Azure AD B2C wybierz **pozycję Przepływy użytkownika**.
1. Kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **pozycję Języki**.
1. Wybierz **pozycję Włącz dostosowywanie języka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Wybieranie języków w przepływie użytkownika jest włączone

Włącz zestaw języków dla przepływu użytkownika, które mają być przetłumaczone na `ui_locales` żądanie przeglądarki bez parametru.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowywanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który chcesz obsługiwać.
1. W okienku właściwości zmień **pozycję Włączone** na **Tak**.
1. Wybierz **pozycję Zapisz** u góry okienka właściwości.

>[!NOTE]
>Jeśli `ui_locales` parametr nie zostanie podany, strona jest tłumaczona na język przeglądarki klienta tylko wtedy, gdy jest włączona.
>

## <a name="customize-your-strings"></a>Dostosowywanie ciągów

Dostosowanie języka umożliwia dostosowanie dowolnego ciągu w przepływie użytkownika.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowywanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który chcesz dostosować.
1. W obszarze **Pliki zasobów na poziomie strony**wybierz stronę, którą chcesz edytować.
1. Wybierz **pozycję Pobierz domyślne** (lub **Pobierz zastąpienia,** jeśli wcześniej edytowano ten język).

Te kroki zapewniają plik JSON, którego można użyć do rozpoczęcia edycji ciągów.

### <a name="change-any-string-on-the-page"></a>Zmienianie dowolnego ciągu na stronie

1. Otwórz plik JSON pobrany z poprzednich instrukcji w edytorze JSON.
1. Znajdź element, który chcesz zmienić. Możesz znaleźć `StringId` ciąg, którego szukasz, lub poszukać `Value` atrybutu, który chcesz zmienić.
1. Zaktualizuj `Value` atrybut o to, co chcesz wyświetlić.
1. Dla każdego ciągu, który chcesz `Override` `true`zmienić, zmień na .
1. Zapisz plik i przekaż zmiany. (Formant przekazywania można znaleźć w tym samym miejscu, w którym pobrano plik JSON).

> [!IMPORTANT]
> Jeśli chcesz zastąpić ciąg, upewnij się, że `Override` ustawisz wartość na `true`. Jeśli wartość nie zostanie zmieniona, wpis jest ignorowany.

### <a name="change-extension-attributes"></a>Zmienianie atrybutów rozszerzenia

Jeśli chcesz zmienić ciąg dla niestandardowego atrybutu użytkownika lub chcesz dodać go do JSON, jest on w następującym formacie:

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

Zamień `<ExtensionAttribute>` na nazwę atrybutu użytkownika niestandardowego.

Zamień `<ExtensionAttributeValue>` na nowy ciąg, który ma być wyświetlany.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Podaj listę wartości przy użyciu localizedcollections

Jeśli chcesz podać ustawioną listę wartości odpowiedzi, musisz utworzyć `LocalizedCollections` atrybut. `LocalizedCollections`jest tablicą `Name` `Value` i parami. Zamówienie na towary będzie kolejnością, w jakiej są wyświetlane. Aby `LocalizedCollections`dodać , użyj następującego formatu:

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

* `ElementId`jest atrybutem użytkownika, `LocalizedCollections` na który ten atrybut jest odpowiedzią.
* `Name`to wartość wyświetlana użytkownikowi.
* `Value`jest to, co jest zwracane w reklamacji, gdy ta opcja jest zaznaczona.

### <a name="upload-your-changes"></a>Przesyłanie zmian

1. Po zakończeniu zmian w pliku JSON, wróć do dzierżawy B2C.
1. Wybierz **przepływy użytkownika** i kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **pozycję Języki**.
1. Wybierz język, na który chcesz tłumaczyć.
1. Wybierz stronę, na której chcesz dostarczyć tłumaczenia.
1. Wybierz ikonę folderu i wybierz plik JSON do przekazania.

Zmiany są zapisywane w przepływie użytkownika automatycznie.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Dostosowywanie interfejsu użytkownika strony przy użyciu dostosowywania języka

Istnieją dwa sposoby lokalizowania zawartości HTML. Jednym ze sposobów jest włączenie [dostosowywania języka.](user-flow-language-customization.md) Włączenie tej funkcji umożliwia usługi Azure AD B2C `ui-locales`do przekazywania openid connect parametru, do punktu końcowego. Serwer zawartości może używać tego parametru do dostarczania niestandardowych stron HTML, które są specyficzne dla języka.

Alternatywnie można pobierać zawartość z różnych miejsc na podstawie ustawień regionalnych, które są używane. W punkcie końcowym obsługującym mechanizm CORS można skonfigurować strukturę folderów do obsługi zawartości dla określonych języków. Jeśli użyjesz wartości `{Culture:RFC5646}`symboli wieloznacznych, zadzwonisz do właściwego. Załóżmy na przykład, że jest to niestandardowy identyfikator URI strony niestandardowej:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Możesz załadować stronę `fr`w pliku . Gdy strona pobiera zawartość HTML i CSS, pobiera z:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Dodawanie języków niestandardowych

Można również dodać języki, dla których firma Microsoft obecnie nie zapewnia tłumaczeń. Musisz podać tłumaczenia dla wszystkich ciągów w przepływie użytkownika. Kody językowe i regionalne są ograniczone do tych w normie ISO 639-1.

1. W dzierżawie usługi Azure AD B2C wybierz **pozycję Przepływy użytkownika**.
2. Kliknij przepływ użytkownika w miejscu, w którym chcesz dodać języki niestandardowe, a następnie kliknij pozycję **Języki**.
3. Wybierz **pozycję Dodaj język niestandardowy** u góry strony.
4. W okienku kontekstu, które zostanie otwarte, określ język, dla którego dostarczasz tłumaczenia, wprowadzając prawidłowy kod ustawień regionalnych.
5. Dla każdej strony można pobrać zestaw nadpisań dla języka angielskiego i pracować nad tłumaczeniami.
6. Po zakończeniu pracy z plikami JSON możesz przesłać je dla każdej strony.
7. Wybierz **włącz**, a przepływ użytkownika może teraz wyświetlać ten język dla użytkowników.
8. Zapisz język.

>[!IMPORTANT]
>Przed zapisaniem należy włączyć języki niestandardowe lub przekazać dla niego zastąpienia.

## <a name="additional-information"></a>Dodatkowe informacje

### <a name="page-ui-customization-labels-as-overrides"></a>Etykiety dostosowywania interfejsu użytkownika strony jako zastąpienia

Po włączeniu dostosowywania języka poprzednie zmiany etykiet przy użyciu dostosowywania interfejsu użytkownika strony są utrwalane w pliku JSON dla języka angielskiego (en). Możesz nadal zmieniać etykiety i inne ciągi, przekazując zasoby językowe w dostosowywaniu języka.

### <a name="up-to-date-translations"></a>Aktualne tłumaczenia

Firma Microsoft dokłada wszelkich starań, aby dostarczać najbardziej aktualne tłumaczenia do użytku użytkownika. Firma Microsoft stale udoskonala tłumaczenia i zapewnia ich zgodność z przepisami. Firma Microsoft zidentyfikuje błędy i zmiany w terminologii globalnej i dokona aktualizacji, które będą bezproblemowo działać w przepływie użytkownika.

### <a name="support-for-right-to-left-languages"></a>Obsługa języków od prawej do lewej

Firma Microsoft obecnie nie zapewnia obsługi języków od prawej do lewej. Można to osiągnąć za pomocą niestandardowych ustawień regionalnych i za pomocą CSS, aby zmienić sposób, w jaki ciągi są wyświetlane. Jeśli potrzebujesz tej funkcji, zagłosuj na nią na [platformie Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Tłumaczenia dostawców tożsamości społecznościowych

Firma Microsoft `ui_locales` udostępnia parametr OIDC do logowania społecznościowego. Ale niektórzy dostawcy tożsamości społecznościowych, w tym Facebook i Google, nie szanują ich.

### <a name="browser-behavior"></a>Zachowanie przeglądarki

Chrome i Firefox żądają swojego języka. Jeśli jest obsługiwanym językiem, jest wyświetlany przed ustawieniem domyślnym. Microsoft Edge obecnie nie żąda języka i przechodzi bezpośrednio do języka domyślnego.

## <a name="supported-languages"></a>Obsługiwane języki

Usługa Azure AD B2C zawiera obsługę następujących języków. Języki przepływu użytkownika są dostarczane przez usługę Azure AD B2C. Języki powiadomień o uwierzytelnianiu wieloskładnikowym (MFA) są dostarczane przez [usługę Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Język              | Kod języka | Przepływy użytkowników         | Powiadomienia o pomocy makrofinansowej  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabski                | Ar            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Bułgarski             | Bg            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Bangla                | Bn            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Kataloński               | Ca            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Czeski                 | Cs            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| duński                | da            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Niemiecki                | de            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| grecki                 | El            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Polski               | pl            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Hiszpański               | Tak            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Estoński              | Et            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Baskijski                | Ue            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| fiński               | fi            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Francuski                | fr            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Galicyjski              | Gl            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Gudżarati              | Gu            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Hebrajski                | On            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Cześć            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Chorwacki              | Kadry            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| węgierski             | Hu            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Indonezyjski            | id            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Włoski               | it            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Japoński              | ja            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Kazachski                | Kk            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Kannada               | Kn            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Koreański                | Ko            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Litewski            | lt            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Łotewski               | Lv            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Marathi               | Pan            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Malajski                 | Pani            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Bokmal norweski      | Nb            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Niderlandzki                 | nl            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Norweski             | nie            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Pendżabski               | Pa            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Polski                | Pl            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Portugalski (Brazylia)   | pt-br         | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Portugalski (Portugalia) | pt-pt         | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Rumuński              | Ro            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Rosyjski               | ru            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Słowacki                | Sk            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Słoweński             | Sl            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Serbski - cyrylica    | sr-cryl-cs    | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Serbski - Łaciński       | sr-latn-cs    | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| szwedzki               | sv            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Tamilski                 | ta            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![tak](./media/user-flow-language-customization/yes.png) | ![nie](./media/user-flow-language-customization/no.png) |
| Tajski                  | Th            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Turecki               | Tr            | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Ukraiński             | Uk            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Wietnamski            | Vi            | ![nie](./media/user-flow-language-customization/no.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Chiński - Uproszczony  | zh-hans       | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
| Chiński - Tradycyjny | zh-hant       | ![tak](./media/user-flow-language-customization/yes.png) | ![tak](./media/user-flow-language-customization/yes.png) |
