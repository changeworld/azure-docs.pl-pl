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
ms.date: 12/10/2018
ms.author: diberry
ms.openlocfilehash: ed1b7b1b0f466b7dd16e8eaa47ec44d132d0f5ff
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859253"
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

Jeśli aplikacja ma od 10 do 20 wypowiedzi o różnej długości zdania, inną kolejność słów i nawet innych wyrazów (synonimy "podrzędne", "manage", "raport"), LUIS, mogą zwracać współczynnik ufności niski. Aby ułatwić LUIS najistotniejszej kolejność słów, należy utworzyć wzorca. 

Wzorce rozwiązać w następujących sytuacjach: 

* Gdy wynik konwersji jest niski
* Gdy poprawne zamiar nie jest najważniejsze wynik zbyt zamykaj jednak najważniejsze wynik. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Wzorce nie są gwarancji intencji
Wzorce używać różnych technologii prognozy. Ustawienie przeznaczenie wypowiedź szablonu we wzorcu nie jest gwarancja intencji prognozowania, ale jest silny sygnał. 

## <a name="patterns-do-not-improve-entity-detection"></a>Wzorce poprawienia wykrywania jednostki
Podczas gdy wzorce wymagają jednostek, wzorzec nie wykrywania jednostki. Wzorzec jest przeznaczone wyłącznie do pomocy prognozowania intencje i ról.  

Nie spodziewa się zobaczyć prognozowania ulepszone jednostki, czy zwijane wypowiedzi wiele do jednego wzorca. W przypadku prostych jednostek na uruchomienie należy dodawanie wypowiedzi lub korzystanie z listy jednostek else deseń nie zostanie wyzwolony.

## <a name="patterns-use-entity-roles"></a>Wzorce użycia ról jednostki
Jeśli dwa lub więcej jednostek w wzorzec kontekstowe są powiązane, wzorców użycia jednostki [role](luis-concept-roles.md) można wyodrębnić informacje kontekstowe dotyczące jednostek. To jest odpowiednikiem hierarchiczne jednostki podrzędne, ale **tylko** dostępne we wzorcach. 

## <a name="prediction-scores-with-and-without-patterns"></a>Wyniki prognozowania z i bez wzorców
Mając wystarczająco dużo wypowiedzi przykładu, LUIS będą mogli zwiększyć ufności prognoz bez wzorców. Wzorce zwiększyć współczynnik ufności bez konieczności podawania tyle wypowiedzi.  

## <a name="pattern-matching"></a>Dopasowanie wzorca
Wzorzec jest takie samo zależnie od wykrywania jednostek w wzorzec najpierw, a następnie weryfikowania reszty słów i kolejność słów w wzorca. Jednostki są wymagane w wzorzec wzorzec do dopasowania. Wzorzec jest stosowany na poziomie token, a nie na poziomie znaków. 

## <a name="pattern-syntax"></a>Składnia wzorca
Składnia wzorca jest szablonem wypowiedź. Szablon powinien zawierać słów i jednostkami, z którymi chcesz dopasować, a także słów i znaków interpunkcyjnych, który chcesz zignorować. Jest **nie** wyrażenia regularnego. 

Jednostki we wzorcach są ujęte w nawiasach klamrowych `{}`. Wzorce mogą obejmować jednostek i jednostkami z użyciem rolach. Pattern.any jest używany tylko we wzorcach jednostki. Składnia jest szczegółowo opisane w poniższych sekcjach.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Składni, aby dodać obiekt do szablonu wzorca
Dodawanie jednostki do szablonu wzorca, należy ująć nazwę jednostki w nawiasach klamrowych, takich jak `Who does {Employee} manage?`. 

|Wzorzec z jednostką|
|--|
|`Who does {Employee} manage?`|

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Składnia służąca do dodawania jednostki i roli do szablonu wzorca
Rola jednostki jest oznaczona jako `{entity:role}` nazwą jednostki, następuje dwukropek, a następnie nazwę roli. Aby dodać obiekt za pomocą roli do szablonu wzorca, należy ująć nazwę podmiotu i nazwy roli za pomocą nawiasów klamrowych, takich jak `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Wzorzec z rolami jednostki|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Składnia służąca do dodawania pattern.any do szablonu wzorca
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

W tych przykładach tytuł książki kontekstowych słów tytuł książki nie są mylące dla usługi LUIS. Usługa LUIS wie, gdzie tytułu kończy się, ponieważ jest we wzorcu i oznaczone atrybutem jednostki Pattern.any.

### <a name="explicit-lists"></a>Jawne list
Jeśli deseń zawiera Pattern.any i składnia wzorca daje możliwość wyodrębniania niepoprawne jednostki oparte na wypowiedź, Utwórz [jawną listę](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) za pośrednictwem tworzenia interfejsu API, aby zezwolić na wyjątek. 

Załóżmy, że masz wzorzec, zawierający zarówno opcjonalnych składni `[]`i składnię jednostki `{}`, są one połączone w sposób, aby wyodrębnić dane niepoprawnie.

Należy wziąć pod uwagę wzorzec "[Znajdź] poziomu poczty e-mail, o {subject} [{osoba}]". W poniższym wypowiedzi **podmiotu** i **osoby** jednostki są wyodrębniane poprawne i niepoprawne:

|Wypowiedź|Jednostka|Poprawne wyodrębniania|
|--|--|:--:|
|wiadomości e-mail dotyczących psy z Chris|podmiotu = psy<br>osoba = Chris|✔|
|wiadomości e-mail dotyczących ataków typu man z La Mancha|podmiotu = mężczyzna<br>osoba = La Mancha|X|

W powyższej tabeli wypowiedź `email about the man from La Mancha`, powinien być przedmiotem `the man from La Mancha` (tytuł książki), ale ponieważ podmiotu zawiera opcjonalne słowo `from`, niepoprawnie przewiduje się tytuł. 

Aby rozwiązać ten wyjątek z wzorcem, dodać `the man from la mancha` jako dopasowanie jawną listę przy użyciu jednostki {subject} [tworzenia interfejsu API dla listy jawne](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Składnia służąca do oznaczania opcjonalny tekst w polu wypowiedź szablonu
Oznacz opcjonalny tekst w wypowiedź przy użyciu składni wyrażeń regularnych nawias kwadratowy, `[]`. Opcjonalny tekst można zagnieżdżać maksymalnie tylko dwa nawiasy kwadratowe nawiasy kwadratowe.

|Wzorzec z opcjonalnym tekstem|
|--|
|`[find] email about {subject} [from {person}]`|

Znaków interpunkcyjnych, takie jak `.`, `!`, i `?` można zignorować przy użyciu nawiasami kwadratowymi. Aby ignorować te znaczniki, każdego znaku musi być w oddzielnym wzorca. Opcjonalnych składni nie obsługuje obecnie zignorowano element na liście kilka elementów.

## <a name="patterns-only"></a>Tylko wzorców
Usługa LUIS umożliwia aplikacji bez wypowiedzi przykład intencji. To obciążenie jest dozwolone tylko wtedy, gdy są używane wzorce. Wzorce wymagają co najmniej jedną jednostkę w każdy wzorzec. Dla aplikacji tylko do wzorca wzorzec nie powinna zawierać maszyny do opanowania jednostki, ponieważ wymagają one wypowiedzi przykład. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się, [najlepsze praktyki](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak Implementowanie wzorców w ramach tego samouczka](luis-tutorial-pattern.md)
