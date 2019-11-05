---
title: Wzorce ułatwiają prognozowanie — LUIS
titleSuffix: Azure Cognitive Services
description: Wzorzec pozwala uzyskać większą dokładność dla intencji bez zapewniania wielu dodatkowych wypowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 4ca3a27a63f84eccb66b24d5046e2ae7d751387d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487544"
---
# <a name="patterns-improve-prediction-accuracy"></a>Wzorce poprawiają dokładność przewidywania
Wzorce zostały zaprojektowane w celu poprawy dokładności, gdy kilka wyrażenia długości jest bardzo podobne.  Wzorzec pozwala uzyskać większą dokładność dla intencji bez zapewniania wielu dodatkowych wypowiedzi. 

## <a name="patterns-solve-low-intent-confidence"></a>Wzorce rozwiązują niski stopień zaufania
Rozważmy aplikację kadr, która raportuje na schemacie organizacyjnym w odniesieniu do pracownika. Mając daną nazwę i relację pracownika, LUIS zwraca pracowników, których to dotyczy. Weź pod uwagę pracownika, Tomasz, z nazwą Menedżera Alicja i zespół podwładnych o nazwie: Michael, Rebecca i Carl.

![Obraz przedstawiający schemat organizacyjny](./media/luis-concept-patterns/org-chart.png)

|Wypowiedzi|Przewidywany cel|Ocena intencji|
|--|--|--|
|Kto jest podwładnym?|GetOrgChart|.30|
|Kto jest podwładnym elementu "Tomasz"?|GetOrgChart|.30|

Jeśli aplikacja ma od 10 do 20 wyrażenia długości z różnymi długościami zdania, inną kolejnością wyrazów, a nawet różnymi wyrazami (synonimami "podwładnych", "manage", "Report"), LUIS może zwrócić wynik o niskim poziomie pewności. Utwórz wzorzec, aby pomóc LUIS zrozumieć znaczenie kolejności wyrazów. 

Wzorce rozwiązują następujące sytuacje: 

* Wynik przeznaczenie jest niski
* Poprawna wartość nie jest najważniejszym wynikiem, ale zbyt blisko najwyższego wyniku. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Wzorce nie są gwarancją zamiaru
Wzorce korzystają z różnych technologii przewidywania. Ustawienie zamiaru dla szablonu wypowiedź w wzorcu nie jest gwarancją przewidywania intencji, ale jest to silny sygnał. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Wzorce nie poprawiają wykrywania jednostek wykrywanych przez maszynę

Wzorzec jest głównie przeznaczony do przewidywania intencji i ról. Wzorzec. Każda jednostka służy do wyodrębniania jednostek o dowolnej postaci. Podczas gdy wzorce używają jednostek, wzorzec nie pomaga wykrywać jednostki obsługiwanej przez maszynę.  

Nie oczekuje się, że w przypadku zwinięcia wielu wyrażenia długości do jednego wzorca nie widzisz ulepszonej przewidywania jednostek. Aby proste jednostki były wyzwalane, należy dodać wyrażenia długości lub użyć jednostek listy, ponieważ wzorzec nie zostanie uruchomiony.

## <a name="patterns-use-entity-roles"></a>Wzorce używają ról jednostek
Jeśli co najmniej dwie jednostki we wzorcu są powiązane z kontekstem, wzorce używają [ról](luis-concept-roles.md) jednostki do wyodrębniania informacji kontekstowych dotyczących jednostek.  

## <a name="prediction-scores-with-and-without-patterns"></a>Wyniki przewidywania z wzorcem i bez
Mając wystarczająco dużo przykład wyrażenia długości, LUIS może zwiększyć wiarygodność przewidywania bez wzorców. Wzorce zwiększają wynik pewności bez konieczności podawania tylu wyrażenia długości.  

## <a name="pattern-matching"></a>Dopasowanie wzorca
Wzorzec jest dopasowywany w oparciu o najpierw wykrywanie jednostek wewnątrz wzorca, a następnie sprawdzanie pozostałej części wyrazów i kolejności wyrazów wzorca. Jednostki są wymagane we wzorcu do dopasowania do wzorca. Wzorzec jest stosowany na poziomie tokenu, a nie na poziomie znaku. 

## <a name="pattern-syntax"></a>Składnia wzorca
Składnia wzorca jest szablonem dla elementu wypowiedź. Szablon powinien zawierać słowa i jednostki, które mają być dopasowane, a także słowa i znaki interpunkcyjne, które mają być ignorowane. To **nie** jest wyrażenie regularne. 

Jednostki w wzorcach są ujęte w nawiasy klamrowe, `{}`. Wzorce mogą obejmować jednostki i jednostki z rolami. [Wzorzec. any](luis-concept-entity-types.md#patternany-entity) jest jednostką używaną tylko w wzorcach. 

Składnia wzorca obsługuje następującą składnię:

|Funkcja|Składnia|Poziom zagnieżdżenia|Przykład|
|--|--|--|--|
|Podmiotów| {} — nawiasy klamrowe|2|Gdzie jest formularz {Entity-Name}?|
|Obowiązkowe|[] — nawiasy kwadratowe<BR><BR>Istnieje limit 3 na poziomach zagnieżdżenia dowolnej kombinacji opcjonalne i grupowania |2|Znak zapytania jest opcjonalny [?]|
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

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Składnia służąca do dodawania jednostki do szablonu wzorca
Aby dodać jednostkę do szablonu wzorca, należy ująć nazwę jednostki za pomocą nawiasów klamrowych, takich jak `Who does {Employee} manage?`. 

|Wzorzec z jednostką|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Składnia służąca do dodawania jednostki i roli do szablonu wzorca
Rola jednostki jest oznaczona jako `{entity:role}` z nazwą jednostki, po której następuje dwukropek, a następnie nazwa roli. Aby dodać jednostkę z rolą do szablonu wzorca, należy ująć nazwę jednostki i nazwę roli z nawiasami klamrowymi, takimi jak `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Wzorzec z rolami jednostek|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Składnia służąca do dodawania wzorca. dowolny do szablonu wzorca
Wzorzec. Każda jednostka umożliwia dodanie jednostki o różnej długości do wzorca. Tak długo, jak następuje szablon wzorca, wzorzec. any może być dowolną długością. 

Aby dodać **wzorzec. dowolna** jednostka do szablonu wzorca, otaczająca wzorzec. Każda jednostka z nawiasami klamrowymi, taka jak `How much does {Booktitle} cost and what format is it available in?`.  

|Wzorzec ze wzorcem. dowolna jednostka|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Tytuły książek we wzorcu|
|--|
|Ile kosztuje **ten koszt książki** i w jakim formacie jest dostępny?|
|Ile jest **poproszonych** kosztów i w jakim formacie jest dostępny?|
|Jak dużo trwa **chcesz wiedzieć zdarzenia w czasie nocnym i w** jakim formacie jest dostępny?| 

Słowa tytułu książki nie są mylące dla LUIS, ponieważ LUIS wie, gdzie się znajduje tytuł książki, na podstawie wzorca. dowolna jednostka.

## <a name="explicit-lists"></a>Listy jawne

Utwórz [jawną listę](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) za pomocą interfejsu API tworzenia, aby zezwolić na wyjątek, gdy:

* Wzorzec zawiera [wzorzec. any](luis-concept-entity-types.md#patternany-entity)
* Składnia wzorca pozwala na możliwość nieprawidłowej ekstrakcji jednostek na podstawie wypowiedź. 

Załóżmy na przykład, że masz wzorzec zawierający zarówno składnię opcjonalną, `[]`, jak i składnię jednostki, `{}`, połączone w sposób, aby wyodrębnić dane nieprawidłowo.

Rozważmy wiadomość e-mail z wzorcem "[Find]" {subject} [od {Person}] ".

W poniższym wyrażenia długości jednostka **podmiotu** i **osoba osoby** są wyodrębniane poprawnie i niepoprawnie:

|Wypowiedź|Jednostka|Poprawna Ekstrakcja|
|--|--|:--:|
|Wyślij wiadomość e-mail na temat psów z Krzysztof|subject = psy<br>osoba = Krzysztof|✔|
|Wyślij wiadomość e-mail na temat Man z La Mancha|podmiot = człowiek<br>osoba = La Mancha|X|

W powyższej tabeli podmiot powinien być `the man from La Mancha` (tytuł książki), ale ponieważ temat zawiera opcjonalny wyraz `from`, tytuł jest nieprawidłowo przewidziany. 

Aby naprawić ten wyjątek we wzorcu, Dodaj `the man from la mancha` jako jawne dopasowanie listy dla jednostki {subject} za pomocą [interfejsu API tworzenia dla jawnej listy](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Składnia służąca do oznaczania tekstu opcjonalnego w szablonie wypowiedź
Oznacz opcjonalny tekst w wypowiedź przy użyciu składni nawiasu kwadratowego wyrażenia regularnego `[]`. Tekst opcjonalny może zagnieżdżać nawiasy kwadratowe tylko do dwóch nawiasów.

|Wzorzec z opcjonalnym tekstem|Znaczenie|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` i `from {person}` są opcjonalne|
|"Czy jesteś w stanie pomóc mi [?]|Znak interpunkcyjny jest opcjonalny|

Znaki interpunkcyjne (`?`, `!`, `.`) powinny być ignorowane i należy je zignorować przy użyciu składni nawiasów kwadratowych w wzorcach. 

## <a name="pattern-only-apps"></a>Aplikacje tylko dla wzorców
Możesz utworzyć aplikację z intencjami, które nie mają przykładowej wyrażenia długości, o ile istnieje wzorzec dla każdego zamiaru. W przypadku aplikacji tylko dla wzorców wzorzec nie powinien zawierać jednostek maszynowych, ponieważ wymagają one przykładowej wyrażenia długości. 

## <a name="best-practices"></a>Najlepsze praktyki
Poznaj [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować wzorce w tym samouczku](luis-tutorial-pattern.md)
