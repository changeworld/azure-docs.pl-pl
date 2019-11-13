---
title: Odwołanie do składni wzorca — LUIS
titleSuffix: Azure Cognitive Services
description: Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS). Wyodrębnione dane są używane przez aplikację kliencką.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: c1353ae530493c34413399a7fbbfe56fb74d452f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010766"
---
# <a name="pattern-syntax"></a>Składnia wzorca

Składnia wzorca jest szablonem wypowiedź. Szablon powinien zawierać słów i jednostkami, z którymi chcesz dopasować, a także słów i znaków interpunkcyjnych, który chcesz zignorować. Jest **nie** wyrażenia regularnego. 

Jednostki we wzorcach są ujęte w nawiasach klamrowych `{}`. Wzorce mogą obejmować jednostek i jednostkami z użyciem rolach. [Wzorzec. any](luis-concept-entity-types.md#patternany-entity) jest jednostką używaną tylko w wzorcach. 

Składnia wzorca obsługuje następującą składnię:

|Funkcja|Składnia|Poziom zagnieżdżenia|Przykład|
|--|--|--|--|
|jednostka| {} — nawiasy klamrowe|2|Gdzie jest formularz {Entity-Name}?|
|obowiązkowe|[] — nawiasy kwadratowe<BR><BR>Istnieje limit 3 na poziomach zagnieżdżenia dowolnej kombinacji opcjonalne i grupowania |2|Znak zapytania jest opcjonalny [?]|
|Grupie|() — nawiasy|2|jest (a \| b)|
|lub| \| — pionowy pasek (potok)<br><br>Istnieje limit 2 na pionowych paskach (lub) w jednej grupie |-|Gdzie jest formularz ({form-Name-Short} &#x7c; {form-Name-Long} &#x7c; {form-Number})| 
|Początek i/lub koniec elementu wypowiedź|^ — karetka|-|^ Rozpocznij wypowiedź<br>wypowiedź wykonuje ^<br>{ścisłe dopasowanie literału dla całej wypowiedź z {Number} jednostką ^|

## <a name="nesting-syntax-in-patterns"></a>Zagnieżdżanie składni w wzorcach

**Opcjonalna** składnia, z nawiasami kwadratowymi, może być zagnieżdżona na dwa poziomy. Na przykład: `[[this]is] a new form`. Ten przykład umożliwia korzystanie z następujących wyrażenia długości: 

|Zagnieżdżony opcjonalny przykład wypowiedź|Wyjaśnienie|
|--|--|
|to jest nowy formularz|dopasowuje wszystkie wyrazy we wzorcu|
|jest nowym formularzem|dopasowuje zewnętrzne słowo opcjonalne i słowa inne niż opcjonalne we wzorcu|
|nowy formularz|Dopasowuje tylko wymagane wyrazy|

Składnia **grupowania** z nawiasami może być zagnieżdżona na dwa poziomy. Na przykład: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Ta funkcja umożliwia dopasowanie dowolnej z trzech jednostek. 

Jeśli jednostki Entity1 jest lokalizacją z rolami, takimi jak Origin (Seattle) i Destination (Kair), a Entity 2 to znana nazwa budynku z jednostki listy (RedWest-C), następująca wyrażenia długości zostałaby zamapowana na ten wzorzec:

|Przykład zagnieżdżonej wypowiedź grupowania|Wyjaśnienie|
|--|--|
|RedWest-C|dopasowuje zewnętrzną jednostkę grupowania|
|Seattle|dopasowuje jedną z wewnętrznych jednostek grupowania|
|Kair|dopasowuje jedną z wewnętrznych jednostek grupowania|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limity zagnieżdżania dla grup z opcjonalną składnią

Kombinacja **grupowania** z **opcjonalną** składnią ma limit 3 poziomów zagnieżdżenia.

|Występować|Przykład|
|--|--|
|Tak|([(TEST1 &#x7c; TEST2)] &#x7c; test3)|
|Nie|([([TEST1] &#x7c; TEST2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limity zagnieżdżania dla grup ze składnią lub-w

Kombinacja **grupowania** z składnią **lub-** w ma limit 2 pionowych słupków.

|Występować|Przykład|
|--|--|
|Tak|(TEST1 &#x7c; TEST2 &#x7c; (test3 &#x7c; test4))|
|Nie|(TEST1 &#x7c; TEST2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Składni, aby dodać obiekt do szablonu wzorca
Dodawanie jednostki do szablonu wzorca, należy ująć nazwę jednostki w nawiasach klamrowych, takich jak `Who does {Employee} manage?`. 

|Wzorzec z jednostką|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Składnia służąca do dodawania jednostki i roli do szablonu wzorca
Rola jednostki jest oznaczona jako `{entity:role}` nazwą jednostki, następuje dwukropek, a następnie nazwę roli. Aby dodać obiekt za pomocą roli do szablonu wzorca, należy ująć nazwę podmiotu i nazwy roli za pomocą nawiasów klamrowych, takich jak `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Wzorzec z rolami jednostki|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Składnia służąca do dodawania pattern.any do szablonu wzorca
Jednostka Pattern.any umożliwia dodawanie jednostki o różnej długości do wzorca. Tak długo, jak następnie szablonu wzorca pattern.any może być dowolnej długości. 

Aby dodać **Pattern.any** jednostki do szablonu wzorca Otocz jednostki Pattern.any za pomocą nawiasów klamrowych, takich jak `How much does {Booktitle} cost and what format is it available in?`.  

|Wzorzec z jednostką Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Tytułów książek we wzorcu|
|--|
|Ile kosztuje **wykradać tę książkę** kosztów i format jest dostępna w?|
|Ile kosztuje **poproś** kosztów i format jest dostępna w?|
|Ile kosztuje **zdarzenia wiedzieć o pies w nocy** kosztów i format jest dostępna w?| 

Słowa tytułu książki nie są mylące dla LUIS, ponieważ LUIS wie, gdzie się znajduje tytuł książki, na podstawie wzorca. dowolna jednostka.

## <a name="explicit-lists"></a>Jawne list

Utwórz [jawną listę](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) za pomocą interfejsu API tworzenia, aby zezwolić na wyjątek, gdy:

* Wzorzec zawiera [wzorzec. any](luis-concept-entity-types.md#patternany-entity)
* Składnia wzorca pozwala na możliwość nieprawidłowej ekstrakcji jednostek na podstawie wypowiedź. 

Załóżmy, że masz wzorzec, zawierający zarówno opcjonalnych składni `[]`i składnię jednostki `{}`, są one połączone w sposób, aby wyodrębnić dane niepoprawnie.

Należy wziąć pod uwagę wzorzec "[Znajdź] poziomu poczty e-mail, o {subject} [{osoba}]".

W poniższym wypowiedzi **podmiotu** i **osoby** jednostki są wyodrębniane poprawne i niepoprawne:

|Wypowiedź|Jednostka|Poprawne wyodrębniania|
|--|--|:--:|
|wiadomości e-mail dotyczących psy z Chris|podmiotu = psy<br>osoba = Chris|✔|
|wiadomości e-mail dotyczących ataków typu man z La Mancha|podmiotu = mężczyzna<br>osoba = La Mancha|X|

W powyższej tabeli podmiot powinien być `the man from La Mancha` (tytuł książki), ale ponieważ temat zawiera opcjonalny wyraz `from`, tytuł jest nieprawidłowo przewidziany. 

Aby rozwiązać ten wyjątek z wzorcem, dodać `the man from la mancha` jako dopasowanie jawną listę przy użyciu jednostki {subject} [tworzenia interfejsu API dla listy jawne](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Składnia służąca do oznaczania opcjonalny tekst w polu wypowiedź szablonu
Oznacz opcjonalny tekst w wypowiedź przy użyciu składni wyrażeń regularnych nawias kwadratowy, `[]`. Opcjonalny tekst można zagnieżdżać maksymalnie tylko dwa nawiasy kwadratowe nawiasy kwadratowe.

|Wzorzec z opcjonalnym tekstem|Znaczenie|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` i `from {person}` są opcjonalne|
|"Czy jesteś w stanie pomóc mi [?]|Znak interpunkcyjny jest opcjonalny|

Znaki interpunkcyjne (`?`, `!`, `.`) powinny być ignorowane i należy je zignorować przy użyciu składni nawiasów kwadratowych w wzorcach. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wzorcach:

* [Jak dodać wzorce](luis-how-to-model-intent-pattern.md)
* [Jak dodać wzorzec. dowolna jednostka](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Koncepcje wzorców](luis-concept-patterns.md)

Dowiedz się, w jaki sposób [tonacji](luis-reference-prebuilt-sentiment.md) jest zwracany w odpowiedzi. JSON.