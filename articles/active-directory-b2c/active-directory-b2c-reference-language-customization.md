---
title: Dostosowywanie języka w usłudze Azure AD B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o dostosowywaniu środowiska języka.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.openlocfilehash: 3d0f1f2ffd02873df2e2e7eab9894d9c3421b0f7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Dostosowywanie języka w usłudze Azure Active Directory B2C

>[!NOTE]
>Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
>

Dostosowywanie języka w usłudze Azure Active Directory B2C (Azure AD B2C) umożliwia zasady, aby zmieścił się w różnych językach, w zależności od potrzeb klientów.  Firma Microsoft udostępnia tłumaczenia dla [36 języków](#supported-languages), ale można też podać własne tłumaczenia dla żadnego języka. Nawet jeśli środowiska jest dostarczany tylko jednego języka, można dostosować tekst na stronach.  

## <a name="how-language-customization-works"></a>Jak działa dostosowania języka
Dostosowywanie języka umożliwia wybierz języki, które są dostępne w podróży użytkownika. Po włączeniu tej funkcji można podać parametru ciągu zapytania `ui_locales`, z poziomu aplikacji. Po wywołaniu do usługi Azure AD B2C strony jest translacja ustawień regionalnych, który ma. Ten typ konfiguracji zapewnia pełną kontrolę nad językach w podróży użytkownika i ignoruje ustawienia języka w przeglądarce klienta. 

Ten poziom kontroli nad jakich języków klienta widzi nie może być konieczne. Jeśli nie podasz `ui_locales` parametru klienta zależy od ustawień ich przeglądarki.  Można wybrać języki podróży użytkownika jest translacji na, dodając ją jako obsługiwanego języka. Jeśli przeglądarka klienta ustawiono Pokaż języka, które nie mają być obsługiwane, język, w którym wybrana jako domyślna w obsługiwanych kultur jest wyświetlany zamiast tego.

- **Ustawienia regionalne interfejsu użytkownika określony język**: po włączeniu dostosowania języka podróży użytkownika jest translacja język, który został określony w tym miejscu.
- **Żądanie przeglądarki języka**: Jeśli nie `ui_locales` określono parametr, podróży użytkownika jest przetłumaczony na język żądanie przeglądarki *Jeśli jest obsługiwany przez język*.
- **Język domyślny zasad**: Jeśli przeglądarka nie określa język, lub określa, który nie jest obsługiwany, podróży użytkownika jest translacji na język domyślny zasad.

>[!NOTE]
>Jeśli używasz atrybutów niestandardowych użytkownika, musisz podać własne tłumaczenia. Aby uzyskać więcej informacji, zobacz [dostosować z ciągów](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Obsługa języków żądanego ui_locales 
Zasady, które zostały utworzone przed ogólnej dostępności dostosowania języka, należy najpierw włączyć tę funkcję. Zasady, które zostały utworzone po ma dostosowania języka domyślnie włączone. 

Podczas dostosowywania języka zasad zostanie włączone, można kontrolować języka podróży użytkownika przez dodanie `ui_locales` parametru.
1. [Przejdź do strony funkcji B2C w portalu Azure](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Przejdź do zasad, które chcesz włączyć tłumaczenia.
3. Wybierz **dostosowywania języka**.  
4. Wybierz **włączenia dostosowywania języka**.
5. Przeczytaj informacje w oknie dialogowym, a następnie wybierz **tak**.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Wybierz języki w podróży użytkownika są włączone 
Włącz zestaw języków dla podróży użytkownika ma zostać poddany translacji o tym, kiedy `ui_locales` nie podano parametru.
1. Sprawdź, czy zasady ma włączyć za pomocą poprzedniej instrukcje dostosowywania języka.
2. Z **edytować zasady** wybierz pozycję **dostosowywania języka**.
3. Wybierz język, który ma być obsługiwana.
4. W okienku właściwości Zmień **włączone** do **tak**.  
5. Wybierz **zapisać** w górnej części okienka właściwości.

>[!NOTE]
>Jeśli `ui_locales` parametr nie zostanie podany, strony jest przetłumaczony na język przeglądarki klienta tylko wtedy, gdy jest włączona.
>

## <a name="customize-your-strings"></a>Dostosowywanie z ciągów
Dostosowywanie języka umożliwia dostosowanie dowolny ciąg w podróży użytkownika.
1. Sprawdź, czy zasady ma włączyć za pomocą poprzedniej instrukcje dostosowywania języka.
2. Z **edytować zasady** wybierz pozycję **dostosowywania języka**.
3. Wybierz język, który chcesz dostosować.
4. Wybierz stronę, który chcesz edytować.
5. Wybierz **pobrać ustawień domyślnych** (lub **Pobierz zastąpienia** po zakończeniu edycji wcześniej ten język). 

Następujące kroki umożliwiają pliku JSON, który służy do edytowana z ciągów.

### <a name="change-any-string-on-the-page"></a>Zmień dowolny ciąg na stronie
1. Otwórz plik JSON pobrane z czynności opisanych w części edytora JSON.
2. Znajdź element, który chcesz zmienić.  Można znaleźć `StringId` ciągu szukasz, lub Wyszukaj `Value` atrybut, który chcesz zmienić.
3. Aktualizacja `Value` atrybut o to, co ma być wyświetlany.
4. Każdy ciąg, który chcesz zmienić, można zmienić `Override` do `true`.
5. Zapisz plik i Przekaż zmiany. (W tym samym miejscu jako którego został pobrany plik JSON można znaleźć formantu przekazywania). 

>[!IMPORTANT]
>Jeśli potrzebujesz zastąpić ciąg, należy ustawić `Override` do wartości `true`.  Jeśli wartość nie ulega zmianie, wpis zostanie zignorowany. 
>

### <a name="change-extension-attributes"></a>Zmień atrybuty rozszerzenia
Jeśli chcesz zmienić ciąg atrybutu niestandardowego użytkownika lub aby dodać je do formatu JSON, należy w następującym formacie:
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

Zastąp `<ExtensionAttributeValue>` o nowe parametry, które mają być wyświetlane.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Podaj listę wartości przy użyciu LocalizedCollections
Jeśli chcesz udostępnić listę zestaw wartości dla odpowiedzi, należy utworzyć `LocalizedCollections` atrybutu.  `LocalizedCollections` jest tablicą `Name` i `Value` pary. Aby dodać `LocalizedCollections`, użyj następującego formatu:

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
* `Name` jest to wartość, która jest pokazywana użytkownikowi.
* `Value` to, co jest zwracany w oświadczenia, jeśli ta opcja jest zaznaczona.

### <a name="upload-your-changes"></a>Przekazywanie zmian
1. Po zakończeniu zmiany do pliku JSON, wróć do dzierżawy usługi B2C.
2. Z **edytować zasady** wybierz pozycję **dostosowywania języka**.
3. Wybierz język, w którym mają zostać przetłumaczone do.
4. Wybierz stronę, której chcesz zapewnić tłumaczenia.
5. Wybierz ikonę folderu, a następnie wybierz plik JSON do przekazania.
 
Zmiany są automatycznie zapisywane do zasad.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Dostosowywanie interfejsu użytkownika strony za pomocą języka dostosowania

Istnieją dwa sposoby do zlokalizowania zawartości HTML. Jednym ze sposobów jest włączenie [dostosowywania języka](active-directory-b2c-reference-language-customization.md). Włączenie tej funkcji umożliwia usłudze Azure AD B2C do przekazywania parametru Open ID Connect `ui-locales`, do punktu końcowego.  Serwer zawartości można Użyj tego parametru, aby zapewnić dostosowanych stron HTML określonego języka.

Alternatywnie można pobierać zawartość z różnych miejsc, oparte na ustawienia regionalne, który jest używany. Punkt końcowy z obsługą CORS można skonfigurować strukturę folderów do hosta zawartości dla określonych języków. Właściwy będzie wywołania w przypadku użyj wartości symbolu wieloznacznego `{Culture:RFC5646}`.  Załóżmy na przykład, że jest to niestandardowej strony identyfikatora URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Można załadować strony w `fr`. Po stronie ściąga zawartości HTML i CSS, jest ściąganie z:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-locales"></a>Dodaj niestandardowe ustawienia regionalne

Można również dodać języki, które aktualnie nie oferuje tłumaczenia. Należy podać tłumaczenia dla wszystkich ciągów w zasadach.

1. Z **edytować zasady** wybierz pozycję **dostosowywania języka**.
2. Wybierz **dodać niestandardowe język** w górnej części strony.
3. W okienku kontekstu zidentyfikować język, który jest zapewnienie tłumaczenia podając kod ustawień regionalnych prawidłowe.
4. Dla każdej strony można pobrać zestaw zastąpień dla języka angielskiego i pracować nad tłumaczenia.
5. Po zakończeniu korzystania z pliki w formacie JSON, możesz przekazać je dla poszczególnych stron.
6. Wybierz **włączyć**, i zgodnie z zasadami można teraz wyświetlić ten język dla użytkowników.
7. Zapisz ten język.

## <a name="additional-information"></a>Dodatkowe informacje

### <a name="page-ui-customization-labels-as-overrides"></a>Etykiety dostosowania interfejsu użytkownika strony jako zastąpień
Po włączeniu dostosowania języka wcześniejszych zmian dla etykiet przy użyciu dostosowywania interfejsu użytkownika strony są zachowywane w pliku JSON dla języka angielskiego (en). Możesz zmienić etykiety i innych ciągów przekazując zasobów językowych w języku dostosowania.
### <a name="up-to-date-translations"></a>Aktualne tłumaczenia
Firma Microsoft dokłada starań, aby udostępniać aktualne tłumaczeń do użycia. Firma Microsoft stale zwiększa tłumaczenia i przechowuje je w zgodności dla Ciebie. Microsoft będzie zidentyfikować usterek i zmiany w terminologii globalne i upewnij aktualizacje, które będą działać bezproblemowo w podróży użytkownika.
### <a name="support-for-right-to-left-languages"></a>Obsługa języków od prawej do lewej
Firma Microsoft obecnie nie zapewnia obsługi języków od prawej do lewej. Jeśli chcesz dodać tę funkcję, należy głosowania dla niego w [opinii Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Tłumaczenie dostawcy tożsamości społecznościowych
Firma Microsoft udostępnia `ui_locales` OIDC parametr społecznościowych logowania. Jednak niektóre dostawców tożsamości społecznościowych, w tym Facebook i Google, nie uwzględnić je. 
### <a name="browser-behavior"></a>Zachowanie przeglądarki
Chrome i Firefox zarówno zażądać ich języka zestawu. Jeśli jest obsługiwanych języków, jest on wyświetlany przed domyślny. Krawędź obecnie nie żąda języka i przechodzą wprost do język domyślny.

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
| Norwegian Bokmal      | nb            |
| Holenderski                 | nl            |
| Pendżabski               | pa            |
| Polski                | pl            |
| Portugalski (Brazylia)   | pt-br         |
| Portugalski (Portugalia) | pt-pt         |
| Rumuński              | ro            |
| Rosyjski               | ru            |
| Słowacki                | SK            |
| Szwedzki               | sv            |
| Tamilski                 | ta            |
| Telugu                | te            |
| Tajlandzki                  | TH            |
| Turecki               | TR            |
| Chiński (uproszczony)  | zh-hans       |
| Chiński — tradycyjny | zh-hant       |
