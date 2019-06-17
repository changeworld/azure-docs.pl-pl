---
title: Wzorce pomocy prognoz
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wzorzec pozwala uzyskać większą precyzję dla intencji bez podawania wielu wypowiedzi więcej.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 2a160ab7447304dc6eb14f76a723df4e8a4d9f46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813571"
---
# <a name="patterns-improve-prediction-accuracy"></a>Wzorce zwiększenia dokładności prognozy
Wzorce są przeznaczone do zwiększenia dokładności, gdy kilka wypowiedzi są bardzo podobne.  Wzorzec pozwala uzyskać większą precyzję dla intencji bez podawania wielu wypowiedzi więcej. 

## <a name="patterns-solve-low-intent-confidence"></a>Wzorce rozwiązać niski ufności intencji
Należy wziąć pod uwagę aplikacja zarządzania zasobami ludzkimi, zawierający raporty dotyczące schematu organizacyjnego w odniesieniu do pracownika. Podana nazwa pracownika i relacji, LUIS zwraca pracowników zaangażowane. Należy wziąć pod uwagę pracownika, Tom, za pomocą Menedżera nazwę Alicja i zespół o nazwie elementów podrzędnych: Michael Rebecca i Carl.

![Obraz schematu organizacyjnego](./media/luis-concept-patterns/org-chart.png)

|Wypowiedzi|Przewidywany intencji|Wynik konwersji|
|--|--|--|
|Kto jest podwładnym Tom firmy?|GetOrgChart|.30|
|Kto jest podwładnym Tom?|GetOrgChart|.30|

Jeśli aplikacja ma od 10 do 20 wypowiedzi o różnej długości zdania, inną kolejność słów i nawet innych wyrazów (synonimy "podrzędne", "manage", "raport"), LUIS, mogą zwracać współczynnik ufności niski. Tworzenie wzorca pomagające zrozumieć znaczenie kolejność słów usługi LUIS. 

Wzorce rozwiązać w następujących sytuacjach: 

* Wynik konwersji jest niska
* Prawidłowe opcje nie jest najważniejsze wynik, ale zbyt blisko oceny najważniejszych. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Wzorce nie są gwarancji intencji
Wzorce używać różnych technologii prognozy. Ustawienie przeznaczenie wypowiedź szablonu we wzorcu nie jest gwarancja intencji prognozowania, ale jest silny sygnał. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Wzorce poprawienia wykrywania maszyny do opanowania jednostki

Wzorzec jest przeznaczony głównie ułatwiające prognozowanie intencje i ról. Jednostka pattern.any jest używana do wyodrębnienia z dowolnych obiektów. Gdy wzorce użycia jednostek, wzorzec nie wykrywania jednostka maszyny do opanowania.  

Nie spodziewa się zobaczyć prognozowania ulepszone jednostki, czy zwijane wypowiedzi wiele do jednego wzorca. W przypadku prostych jednostek na uruchomienie należy dodawanie wypowiedzi lub korzystanie z listy jednostek else deseń nie zostanie wyzwolony.

## <a name="patterns-use-entity-roles"></a>Wzorce użycia ról jednostki
Jeśli dwa lub więcej jednostek w wzorzec kontekstowe są powiązane, wzorców użycia jednostki [role](luis-concept-roles.md) można wyodrębnić informacje kontekstowe dotyczące jednostek.  

## <a name="prediction-scores-with-and-without-patterns"></a>Wyniki prognozowania z i bez wzorców
Mając wystarczająco dużo wypowiedzi przykładu, LUIS będą mogli zwiększyć ufności prognoz bez wzorców. Wzorce zwiększyć współczynnik ufności bez konieczności podawania tyle wypowiedzi.  

## <a name="pattern-matching"></a>Dopasowanie wzorca
Wzorzec jest takie samo zależnie od wykrywania jednostek w wzorzec najpierw, a następnie weryfikowania reszty słów i kolejność słów w wzorca. Jednostki są wymagane w wzorzec wzorzec do dopasowania. Wzorzec jest stosowany na poziomie token, a nie na poziomie znaków. 

## <a name="pattern-syntax"></a>Składnia wzorca
Składnia wzorca jest szablonem wypowiedź. Szablon powinien zawierać słów i jednostkami, z którymi chcesz dopasować, a także słów i znaków interpunkcyjnych, który chcesz zignorować. Jest **nie** wyrażenia regularnego. 

Jednostki we wzorcach są ujęte w nawiasach klamrowych `{}`. Wzorce mogą obejmować jednostek i jednostkami z użyciem rolach. [Pattern.any](luis-concept-entity-types.md#patternany-entity) jest używany tylko we wzorcach jednostki. 

Składnia wzorca obsługuje następującej składni:

|Funkcja|Składnia|Poziom zagnieżdżenia|Przykład|
|--|--|--|--|
|jednostka| {} -nawiasów klamrowych|2|Gdzie znajduje się w formie {nazwa jednostki}?|
|Opcjonalne|[] — nawiasami kwadratowymi<BR><BR>Obowiązuje limit 3 dla poziomów zagnieżdżenia z dowolnej kombinacji opcjonalne i grupowania |2|Znak zapytania jest opcjonalne [?]|
|Grupowanie|() - nawiasów|2|jest ( \| b).|
|lub| \| -pionowy pasek (potok)<br><br>Jest ograniczona do 2 na pionowych słupków (lub) w jednej grupie |-|Gdzie jest formularz ({formularza name krótki} &#x7c; {formularza name długich} &#x7c; {numer formularza})| 
|początkowy i końcowy wypowiedź|^ — karetki|-|^ rozpocząć wypowiedź<br>odbywa się wypowiedź ^<br>^ strict literału dopasowanie całego wypowiedź z jednostką {number} ^|

## <a name="nesting-syntax-in-patterns"></a>Zagnieżdżanie składni we wzorcach

**Opcjonalne** składni z nawiasami kwadratowymi mogą być zagnieżdżone dwa poziomy. Na przykład: `[[this]is] a new form`. Ten przykład umożliwia wypowiedzi następujące: 

|Przykład zagnieżdżonych opcjonalnych wypowiedź|Wyjaśnienie|
|--|--|
|jest to nowy formularz|Dopasowuje wszystkie wyrazy we wzorcu|
|to nowy formularz|pasuje do zewnętrznego opcjonalne słowo i — opcjonalnie wyrazów we wzorcu|
|nowy formularz|tylko wyrazy dopasowania wymagane|

**Grupowanie** składni z nawiasami, mogą być zagnieżdżone dwa poziomy. Na przykład: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Ta funkcja umożliwia dowolnej z trzech jednostek do dopasowania. 

Jeśli jednostki Entity1 to lokalizacja, z ról, takich jak pochodzenia (Seattle) i docelowy (Kair) i 2 jednostki jest nazwą budynku znane z obiektami listy (RedWest-C), następujące wypowiedzi mapującej do tego wzorca:

|Przykład wypowiedź zagnieżdżonego grupowania|Wyjaśnienie|
|--|--|
|RedWest-C|pasuje do jednostki zewnętrznej grupowania|
|Seattle|zgodny z jednym z podmiotów wewnętrzny grupowania|
|Cairo|zgodny z jednym z podmiotów wewnętrzny grupowania|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limity zagnieżdżanie grup za pomocą opcjonalnych składni

Kombinacji **grupowanie** z **opcjonalne** składni ma limit 3 poziomów zagnieżdżenia.

|Dozwolone|Przykład|
|--|--|
|Tak|([(test1 &#x7c; test2)] &#x7c; test3)|
|Nie|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limity zagnieżdżanie grup przy użyciu składni lub ing

Kombinacji **grupowanie** z **lub ing** składni obowiązuje limit wynoszący 2 pionowych słupków.

|Dozwolone|Przykład|
|--|--|
|Yes|(test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|Nie|(test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

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

Wyrazy tytuł książki nie są mylące dla usługi LUIS ponieważ LUIS wie, gdzie kończy się tytuł książki na podstawie jednostki Pattern.any.

## <a name="explicit-lists"></a>Jawne list

Tworzenie [jawną listę](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) za pośrednictwem tworzenia interfejsu API, aby zezwolić na wyjątek po:

* Deseń zawiera [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* I Składnia tego wzorca daje możliwość działania funkcji wydobywania podmiotów niepoprawne oparte na wypowiedź. 

Załóżmy, że masz wzorzec, zawierający zarówno opcjonalnych składni `[]`i składnię jednostki `{}`, są one połączone w sposób, aby wyodrębnić dane niepoprawnie.

Należy wziąć pod uwagę wzorzec "[Znajdź] poziomu poczty e-mail, o {subject} [{osoba}]".

W poniższym wypowiedzi **podmiotu** i **osoby** jednostki są wyodrębniane poprawne i niepoprawne:

|Wypowiedź|Jednostka|Poprawne wyodrębniania|
|--|--|:--:|
|wiadomości e-mail dotyczących psy z Chris|podmiotu = psy<br>osoba = Chris|✔|
|wiadomości e-mail dotyczących ataków typu man z La Mancha|podmiotu = mężczyzna<br>osoba = La Mancha|X|

W powyższej tabeli powinien być przedmiotem `the man from La Mancha` (tytuł książki), ale ponieważ podmiotu zawiera opcjonalne słowo `from`, niepoprawnie przewiduje się tytuł. 

Aby rozwiązać ten wyjątek z wzorcem, dodać `the man from la mancha` jako dopasowanie jawną listę przy użyciu jednostki {subject} [tworzenia interfejsu API dla listy jawne](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Składnia służąca do oznaczania opcjonalny tekst w polu wypowiedź szablonu
Oznacz opcjonalny tekst w wypowiedź przy użyciu składni wyrażeń regularnych nawias kwadratowy, `[]`. Opcjonalny tekst można zagnieżdżać maksymalnie tylko dwa nawiasy kwadratowe nawiasy kwadratowe.

|Wzorzec z opcjonalnym tekstem|Znaczenie|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` i `from {person}` są opcjonalne|
|' Można prosić o [?]|Znak interpunkcyjny jest opcjonalna|

Znaki interpunkcyjne (`?`, `!`, `.`) mają być ignorowane i trzeba je przy użyciu składni nawias kwadratowy we wzorcach zignorować. 

## <a name="pattern-only-apps"></a>Wzorzec — tylko do aplikacji
Można utworzyć aplikację z opcjami, które mają nie wypowiedzi przykładu, tak długo, jak jest wzorzec dla każdego intencji. Dla aplikacji tylko do wzorca wzorzec zakres nie powinien zawierać maszyny do opanowania jednostki, ponieważ wymagają one wypowiedzi przykład. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się, [najlepsze praktyki](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak Implementowanie wzorców w ramach tego samouczka](luis-tutorial-pattern.md)
