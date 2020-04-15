---
title: Odwołanie do składni wzorca — usługa LUIS
description: Tworzenie jednostek w celu wyodrębnienia kluczowych danych z wypowiedzi użytkowników w aplikacjach do zrozumienia języka (LUIS). Wyodrębnione dane są używane przez aplikację kliencką.
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: cc24667f43dfedc032f52c40fc5f8fe5c80bad70
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382146"
---
# <a name="pattern-syntax"></a>Składnia wzorca

Składnia wzorca jest szablonem wypowiedź. Szablon powinien zawierać słowa i encje, które chcesz dopasować, a także słowa i [znaki interpunkcyjne,](luis-reference-application-settings.md#punctuation-normalization) które chcesz zignorować. **Nie** jest to wyrażenie regularne.

> [!CAUTION]
> Wzorce obejmują tylko jednostki uzownie maszynowe, a nie podskładne.

Elementy we wzorach są otoczone nawiasami `{}`klamrowymi, . Wzorce mogą obejmować jednostki i jednostki z rolami. [Pattern.any](luis-concept-entity-types.md#patternany-entity) jest jednostką używaną tylko w wzorcach.

Składnia wzorca obsługuje następującą składnię:

|Funkcja|Składnia|Poziom zagnieżdżenia|Przykład|
|--|--|--|--|
|jednostka| {}- wsporniki kręcone|2|Gdzie jest formularz {nazwa encji}?|
|optional|[] - nawiasy kwadratowe<BR><BR>Istnieje limit 3 na poziomach zagnieżdżania dowolnej kombinacji fakultatywnych i grupujących |2|Znak zapytania jest opcjonalny [?]|
|grouping|() - nawiasy|2|jest (a \| b)|
|lub| \|- pręt pionowy (rura)<br><br>Istnieje limit 2 na pionowych prętach (Lub) w jednej grupie |-|Gdzie jest formularz ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|początek i/lub koniec wypowiedź|^ - cieszka|-|^rozpocznij wypowiedź<br>wypowiedź jest wykonywana^<br>^ścisłe dopasowanie dosłowne całej wypowiedź z jednostką {number}^|

## <a name="nesting-syntax-in-patterns"></a>Składnia zagnieżdżania w szykach

**Opcjonalna** składnia z nawiasami kwadratowymi może być zagnieżdżona na dwóch poziomach. Na przykład: `[[this]is] a new form`. W tym przykładzie zezwala na następujące wypowiedzi:

|Zagnieżdżony przykład wypowiedź opcjonalna|Wyjaśnienie|
|--|--|
|jest to nowa forma|dopasowuje wszystkie wyrazy we wzorcu|
|to nowa forma|dopasowuje zewnętrzne słowo opcjonalne i wyrazy nieopcjonalne we wzorcu|
|nowy formularz|pasuje tylko do wymaganych słów|

Składnia **grupowania** z nawiasami może być zagnieżdżona na dwóch poziomach. Na przykład: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Ta funkcja umożliwia dopasowywać dowolne z trzech elementów.

Jeśli Entity1 jest lokalizacją z rolami, takimi jak pochodzenie (Seattle) i miejsce docelowe (Kair) i Jednostka 2 jest znaną nazwą budynku z jednostki listy (RedWest-C), następujące wypowiedzi będą mapowane do tego wzorca:

|Przykład wypowiedź grupowania zagnieżdżonego|Wyjaśnienie|
|--|--|
|RedWest-C (Polski)|dopasowuje jednostkę grupowania zewnętrznego|
|Seattle|dopasowuje jeden z encji grupowania wewnętrznego|
|Cairo|dopasowuje jeden z encji grupowania wewnętrznego|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Zagnieżdżanie limitów dla grup ze składnią opcjonalną

Kombinacja **grupowania** z **opcjonalną** składnią ma limit 3 poziomów zagnieżdżenia.

|Dozwolone|Przykład|
|--|--|
|Tak|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Nie|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Zagnieżdżanie limitów dla grup ze składnią lub składnią

Kombinacja **grupowania** ze składnią **lub składnia** ma limit 2 pionowych prętów.

|Dozwolone|Przykład|
|--|--|
|Tak|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4) ) )|
|Nie|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Składnia dodawania encji do szablonu wzorca
Aby dodać element do szablonu wzorca, otoczyć nazwę encji nawiasami klamrowymi, takimi jak `Who does {Employee} manage?`.

|Wzorzec z encjami|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Składnia dodawania encji i roli do szablonu wzorca
Rola jednostki jest oznaczona tak, jak `{entity:role}` w przypadku nazwy jednostki, po której następuje dwukropek, a następnie nazwa roli. Aby dodać encję z rolą do szablonu wzorca, otoczyć nazwę `Book a ticket from {Location:Origin} to {Location:Destination}`encji i nazwę roli nawiasami klamrowymi, takimi jak .

|Wzorzec z rolami encji|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Składnia, aby dodać pattern.any do szablonu wzorca
Pattern.any jednostki umożliwia dodanie jednostki o różnej długości do szyku. Tak długo, jak szablon wzorca jest przestrzegane, pattern.any może być dowolna długość.

Aby dodać element **Pattern.any** do szablonu wzorca, otoczyć element Pattern.any `How much does {Booktitle} cost and what format is it available in?`za pomocą nawiasów klamrowych, takich jak .

|Wzorzec z elementem Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Tytuły książek we wzorze|
|--|
|Ile kosztuje **kradzież tej książki** i w jakim formacie jest ona dostępna?|
|Ile kosztuje **pytanie** i w jakim formacie jest dostępny?|
|Ile kosztuje **Ciekawy incydent z psem w nocy** i w jakim formacie jest dostępny?|

Słowa tytułu książki nie są mylące dla usługi LUIS, ponieważ usługa LUIS wie, gdzie kończy się tytuł książki, na podstawie pattern.any jednostki.

## <a name="explicit-lists"></a>Jawne listy

utworzyć [listę jawną](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) za pośrednictwem interfejsu API tworzenia, aby zezwolić na wyjątek, gdy:

* Wzorzec zawiera [pattern.any](luis-concept-entity-types.md#patternany-entity)
* I tej składni wzorca pozwala na możliwość wyodrębniania niepoprawne jednostki na podstawie wypowiedź.

Załóżmy na przykład, że istnieje wzorzec `[]`zawierający zarówno składnię opcjonalną, jak i składnię encji, `{}`połączoną w sposób nieprawidłowego wyodrębniania danych.

Rozważmy wzorzec "[znajdź] wiadomość e-mail dotyczącą {subject} [od {person}]".

W następujących wypowiedziach **podmiot** i **osoba** jednostki są wyodrębniane poprawnie i niepoprawnie:

|Wypowiedź|Jednostka|Prawidłowa ekstrakcja|
|--|--|:--:|
|e-mail o psach od Chrisa|temat=psy<br>osoba=Chris|✔|
|e-mail o człowieku z La Mancha|subject=człowiek<br>osoba=La Mancha|X|

W powyższej tabeli temat `the man from La Mancha` powinien być (tytuł książki), ale ponieważ `from`temat zawiera słowo opcjonalne, tytuł jest błędnie przewidywany.

Aby naprawić ten wyjątek `the man from la mancha` do wzorca, dodaj jako jawne dopasowanie listy dla jednostki {subject} przy użyciu [interfejsu API tworzenia dla listy jawnej](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Składnia oznaczania tekstu opcjonalnego w wypowiedź szablonu
Oznacz opcjonalny tekst w wypowiedź przy użyciu `[]`składni nawiasu kwadratowego wyrażenia regularnego, . Opcjonalny tekst może zagnieżdżać nawiasy kwadratowe tylko do dwóch nawiasów.

|Wzór z opcjonalnym tekstem|Znaczenie|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`i `from {person}` są opcjonalne|
|'Czy możesz mi pomóc[?]|Znak interpunkcyjny jest opcjonalny|

Znaki interpunkcyjne`?` `!`( `.`, , , ) powinny być ignorowane i należy je zignorować przy użyciu składni nawiasu kwadratowego w wzorach.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wzorcach:

* [Jak dodać wzory](luis-how-to-model-intent-pattern.md)
* [Jak dodać pattern.any encji](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Koncepcje wzorców](luis-concept-patterns.md)

Zrozumieć, jak [sentyment](luis-reference-prebuilt-sentiment.md) jest zwracany w odpowiedzi .json.