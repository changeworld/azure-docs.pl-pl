---
title: Dostosowywanie języka w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o dostosowywaniu środowiska języka.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 3c319349d721a390562faac0fc6f90a7b471db0f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703417"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Dostosowywanie języka w usłudze Azure Active Directory B2C

Dostosowywanie języka w usłudze Azure Active Directory B2C (Azure AD B2C) umożliwia przepływ użytkownika do obsługi różnych języków, w zależności od potrzeb klienta.  Firma Microsoft udostępnia tłumaczenia na języki [36 języków](#supported-languages), ale możesz też podać własne tłumaczenia w dowolnym języku. Nawet wtedy, gdy środowisko jest dostępne w jednym języku, możesz dostosować tekst na stronach.  

## <a name="how-language-customization-works"></a>Jak działa Dostosowywanie języka
Dostosowywanie języka umożliwia wybierz języki, które przepływ użytkownika jest dostępna w. Po włączeniu tej funkcji możesz podać parametr ciągu zapytania, `ui_locales`, z poziomu aplikacji. Po wywołaniu do usługi Azure AD B2C, strona jest tłumaczony ustawień regionalnych, które mają określony przez użytkownika. Ten typ konfiguracji zapewnia pełną kontrolę nad językach przepływ użytkownika i ignoruje ustawień języka w przeglądarce klienta. 

Ten poziom kontroli nad jakich języków klienta widzi nie może być konieczne. Jeśli nie podasz `ui_locales` parametru, środowisko klienta zależy od ustawień ich przeglądarki.  Można wybrać, które języki są przekształcane przepływ użytkownika przez dodawanie ich jako obsługiwanego języka. Jeśli przeglądarka klienta jest ustawiony na wyświetlanie języka, które nie mają być obsługiwane, języka, który jest wybrany jako domyślny w obsługiwanych kultur jest wyświetlany zamiast tego.

- **Ustawienia regionalne interfejsu użytkownika określony język**: Dostosowywanie języka jest włączona, przepływ użytkownika jest przetłumaczony na język, który jest określony w tym miejscu.
- **Zażądano przeglądarki języka**: Jeśli nie `ui_locales` parametr został określony, przepływ użytkownika jest tłumaczona na język żądane przeglądarki *Jeśli jest obsługiwany przez język*.
- **Język domyślny zasad**: Jeśli przeglądarka nie określa język, lub określa, który nie jest obsługiwany, przepływ użytkownika jest tłumaczona na domyślny język przepływu użytkownika.

>[!NOTE]
>Jeśli używasz atrybutów niestandardowych użytkowników należy udostępnić własne tłumaczenia. Aby uzyskać więcej informacji, zobacz [dostosować Twoimi ciągami](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Obsługa języków Żądana wartość ui_locales 
Zasady, które zostały utworzone przed ogólną dostępność Dostosowywanie języka, należy najpierw włączyć tę funkcję. Zasady i przepływy użytkownika, które zostały utworzone po mają Dostosowywanie języka domyślnie włączone. 

Po włączeniu Dostosowywanie języka na przepływ użytkownika, możesz kontrolować języka przepływ użytkownika, dodając `ui_locales` parametru.
1. W ramach dzierżawy usługi Azure AD B2C wybierz **przepływy użytkownika**.
2. Kliknij przycisk przepływu użytkownika, który chcesz włączyć tłumaczenia.
3. Wybierz **języków**.  
4. Wybierz **użytkowania Włącz dostosowywanie języka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Wybierz języki przepływu użytkownika mają być włączone 
Włączanie zestawu języków przepływu użytkownika mają być tłumaczone na żądanie w przeglądarce, bez `ui_locales` parametru.
1. Upewnij się, że przepływ użytkownika ma Dostosowywanie języka włączyć za pomocą poprzednich instrukcji.
2. Na **języków** stronie przepływ użytkownika, wybierz język, który ma być obsługiwana.
3. W okienku właściwości zmienić **włączone** do **tak**.  
4. Wybierz **Zapisz** w górnej części okienka właściwości.

>[!NOTE]
>Jeśli `ui_locales` parametr nie zostanie podany, strona jest tłumaczona na język przeglądarki klienta, tylko wtedy, gdy jest włączone.
>

## <a name="customize-your-strings"></a>Dostosowywanie Twoimi ciągami
Dostosowywanie języka pozwala dostosowywania dowolny ciąg przepływu użytkownika.
1. Upewnij się, że przepływ użytkownika ma Dostosowywanie języka włączyć za pomocą poprzednich instrukcji.
2. Na **języków** stronie przepływ użytkownika, wybierz język, który chcesz dostosować.
3. W obszarze **pliki zasoby w przypadku poziomu strony**, wybierz stronę, którą chcesz edytować.
4. Wybierz **pobieranie domyślnych** (lub **pobieranie zastąpień** Jeśli edytowano wcześniej ten język).

Poniższe kroki umożliwiają pliku JSON, który można użyć w celu rozpoczęcia edycji Twoimi ciągami.

### <a name="change-any-string-on-the-page"></a>Zmień dowolny ciąg, na stronie
1. Otwórz plik JSON, pobrany z poprzednich instrukcji w edytorze kodu JSON.
2. Znajdź element, który chcesz zmienić.  Możesz znaleźć `StringId` ciągu, czego szukasz, lub poszukać `Value` atrybut, który chcesz zmienić.
3. Aktualizacja `Value` atrybut o jakie, które mają być wyświetlane.
4. Każdy ciąg, który chcesz zmienić, można zmienić `Override` do `true`.
5. Zapisz plik i Przekaż zmiany. (W tym samym miejscu co którego został pobrany plik JSON można znaleźć formantu przekazywania). 

>[!IMPORTANT]
>Jeśli potrzebujesz zastąpić ciąg, upewnij się ustawić `Override` wartość `true`.  Jeśli wartość nie jest zmieniona, jest ignorowany wpis. 
>

### <a name="change-extension-attributes"></a>Zmienianie atrybutów rozszerzenia
Jeśli chcesz zmienić parametry dla atrybutu użytkownika niestandardowego, a chcesz dodać do kodu JSON jest w następującym formacie:
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

Zastąp `<ExtensionAttribute>` o nazwie atrybut użytkownika niestandardowego.  

Zastąp `<ExtensionAttributeValue>` za pomocą nowego ciągu znaków, które mają być wyświetlane.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Podaj listę wartości przy użyciu LocalizedCollections
Jeśli chcesz udostępnić listę wartości dla odpowiedzi, należy utworzyć `LocalizedCollections` atrybutu.  `LocalizedCollections` jest tablicą `Name` i `Value` pary. Kolejność elementów będzie kolejności, w której są wyświetlane.  Aby dodać `LocalizedCollections`, użyj następującego formatu:

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

* `ElementId` jest atrybut użytkownika, że `LocalizedCollections` atrybut jest odpowiedź.
* `Name` to wartość, która jest wyświetlana użytkownikowi.
* `Value` to, co jest zwracany w oświadczeniu, jeśli ta opcja jest zaznaczona.

### <a name="upload-your-changes"></a>Przekazywanie zmian
1. Po zakończeniu zmiany do pliku JSON, wróć do dzierżawy usługi B2C.
2. Wybierz **przepływy użytkownika** i kliknij przycisk przepływu użytkownika, który chcesz włączyć tłumaczenia.
3. Wybierz **języków**.
4. Wybierz język, którego chcesz przetłumaczyć na.
5. Wybierz stronę, której chcesz podać tłumaczenia.
6. Wybierz ikonę folderu, a następnie wybierz plik JSON do przekazania.
 
Zmiany są automatycznie zapisywane do przepływu użytkownika.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Dostosowywanie strony interfejsu użytkownika przy użyciu Dostosowywanie języka

Istnieją dwa sposoby, aby zlokalizować zawartość HTML. Jednym ze sposobów jest włączenie [Dostosowywanie języka](active-directory-b2c-reference-language-customization.md). Włączenie tej funkcji umożliwia usłudze Azure AD B2C do przekazywania parametru Open ID Connect `ui-locales`, do punktu końcowego usługi.  Serwer zawartości ten parametr służy do zapewnienia dostosowanych stron HTML określonego języka.

Alternatywnie możesz ściągnąć zawartość z różnych miejsc, w oparciu o ustawienia regionalne, który jest używany. Punkt końcowy z obsługą mechanizmu CORS można skonfigurować strukturę folderów do hostowania zawartości dla określonych języków. Będzie wywołać właściwy, jeśli używasz wartości symboli wieloznacznych `{Culture:RFC5646}`.  Na przykład załóżmy, że to niestandardowy identyfikator URI strony:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Możesz załadować strony w `fr`. Po stronie ściąga zawartość HTML i CSS, ciągnie się od:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Dodaj niestandardowe języki

Można również dodać języki, które firma Microsoft obecnie udostępnia tłumaczenia na języki. Musisz podać tłumaczenia dla wszystkich ciągów w przepływu użytkownika.  Kody język i ustawienia regionalne są ograniczone do tych w normie ISO 639-1. 

1. W ramach dzierżawy usługi Azure AD B2C wybierz **przepływy użytkownika**.
2. Kliknij przycisk przepływu użytkownika, które chcesz dodać niestandardowe języki, a następnie kliknij przycisk **języków**.
3. Wybierz **Dodaj język niestandardowy** w górnej części strony.
4. W okienku kontekstu identyfikowanie języka, w którym udostępniasz tłumaczenia na języki, wprowadzając kod ustawień regionalnych prawidłowe.
5. Dla każdej strony można pobrać zestawu przesłonięcia dla języka angielskiego i pracować nad tłumaczenia.
6. Po zakończeniu korzystania z plików JSON, możesz je przekazać dla każdej strony.
7. Wybierz **Włącz**, a przepływ użytkownika można teraz wyświetlić ten język dla użytkowników.
8. Zapisz język.

>[!IMPORTANT]
>Należy włączyć obsługę języków niestandardowych lub przekazywanie zastąpień dla niego, zanim będzie można zapisać.
>

## <a name="additional-information"></a>Dodatkowe informacje

### <a name="page-ui-customization-labels-as-overrides"></a>Dostosowywanie interfejsu użytkownika strony etykiet zastąpień
Po włączeniu Dostosowywanie języka zmian poprzednim etykiet przy użyciu dostosowywania interfejsu użytkownika strony są utrwalane w pliku JSON dla języka angielskiego (en). Można kontynuować zmienić etykiety i inne parametry, przekazując zasobów językowych w Dostosowywanie języka.
### <a name="up-to-date-translations"></a>Aktualne tłumaczenia
Firma Microsoft jest zobowiązana do zapewnienia najbardziej aktualne tłumaczeń do użycia. Firma Microsoft stale zwiększa tłumaczenia i pozostaną w zakresie zgodności dla Ciebie. Firma Microsoft identyfikowanie błędów i zmiany w terminologii globalnych i wprowadzić aktualizacje, które będzie działać bezproblemowo przepływu użytkownika.
### <a name="support-for-right-to-left-languages"></a>Obsługa języków od prawej do lewej
Firma Microsoft obecnie nie zapewnia obsługi języków od prawej do lewej. Można to zrobić przy użyciu niestandardowych ustawień regionalnych i przy użyciu CSS, aby zmienić sposób, w jaki ciągi są wyświetlane.  Jeśli potrzebujesz tej funkcji, głosować na na [opinii Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Tłumaczenia dostawcy tożsamości dla sieci społecznościowej
Firma Microsoft udostępnia `ui_locales` OIDC parametr społecznościowych nazw logowania. Ale niektórzy dostawcy tożsamości społecznościowych, w tym usługi Facebook i Google, nie uznaje ich. 
### <a name="browser-behavior"></a>Zachowanie przeglądarki
Chrome i Firefox zarówno zażądać dla swojego języka zestawu. Jeśli jest obsługiwany język, jest poprzedzana domyślnie. Microsoft Edge aktualnie nie żąda języka i przechodzi bezpośrednio do domyślnego języka.

### <a name="supported-languages"></a>Obsługiwane języki

| Język              | Kod języka |
|-----------------------|---------------|
| Bengalski                | bn            |
| Czeski                 | cs            |
| Duński                | da            |
| Niemiecki                | de            |
| Grecki                 | el            |
| Polski               | pl            |
| Hiszpański               | es            |
| Fiński               | fi            |
| Francuski                | fr            |
| Gudżarati              | gu            |
| Hindi                 | hi            |
| Chorwacki              | godz.            |
| Węgierski             | hu            |
| Włoski               | it            |
| Japoński              | ja            |
| Kannada               | kn            |
| Koreański                | ko            |
| Malajalam             | ml            |
| Marathi               | mr            |
| Malajski                 | ms            |
| Norweski (Nynorsk)      | nb            |
| Holenderski                 | nl            |
| Pendżabski               | pa            |
| Polski                | pl            |
| Portugalski (Brazylia)   | pt-br         |
| Portugalski — Portugalia | pt-pt         |
| Rumuński              | ro            |
| Rosyjski               | ru            |
| Słowacki                | SK            |
| Szwedzki               | sv            |
| Tamilski                 | ta            |
| Telugu                | Usuń            |
| Tajlandzki                  | .            |
| Turecki               | tr            |
| Chiński (uproszczony)  | nazwy zh-hans       |
| Chiński — tradycyjny | nazwy zh-hant       |
