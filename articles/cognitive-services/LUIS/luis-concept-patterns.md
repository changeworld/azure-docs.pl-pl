---
title: Dowiedz się, jak wzorce zwiększenia dokładności prognozy | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak zaprojektować wzorców w celu zwiększenia wyniki prognozowania konwersji i Znajdź jednostek.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr
ms.openlocfilehash: c3c0d12bbbeec85d2cbf0daead49ee16ca7728fb
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046262"
---
# <a name="patterns-improve-prediction-accuracy"></a>Wzorce poprawić precyzję przewidywania
Wzorce są przeznaczone do poprawy dokładności po kilku zniesławiających są bardzo podobne. Udostępniając wzorca utterance, LUIS może mieć wysokiego zaufania prognozowania. 

## <a name="patterns-solve-low-intent-confidence"></a>Wzorce rozwiązać niski zaufania konwersji
Należy wziąć pod uwagę aplikacji kadr, który raport dotyczący schemat organizacyjny w odniesieniu do pracownika. Podana nazwa i relacji pracownika, LUIS zwraca pracowników związane. Należy wziąć pod uwagę pracownik Tomasz, z menedżerem nazwę Alicja i zespołu podwładnych o nazwie: Michael Rebecca i Carl.

![Obraz schematu organizacyjnego](./media/luis-concept-patterns/org-chart.png)

|Wypowiedzi|Celem przewidzieć|Wynik konwersji|
|--|--|--|
|Kto jest podrzędny w Tomasz?|GetOrgChart|.30|
|Kto jest podwładnym niestandardowego?|GetOrgChart|.30|

Jeśli aplikacja ma od 10 do 20 zniesławiających z różne długości zdania, innej kolejności word i nawet innych wyrazów (synonimy "podrzędny", "manage", "raport"), LUIS może zwrócić wynik niski zaufania. Aby lepiej zrozumieć znaczenie kolejności word LUIS, należy utworzyć wzorca. 

Wzorce rozwiązania następujących sytuacjach: 

* Jeśli brakuje wynik konwersji
* Gdy poprawne celem nie jest najwyższym wynik, ale zbyt blisko górnej wynik. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Wzorce nie są gwarancji założeń
Wzorce używać różnych technologii prognozowania. Ustawienie we wzorcu opcje utterance szablon nie ma gwarancji konwersji prognozowania, ale jest silny sygnał. 

## <a name="patterns-do-not-improve-entity-detection"></a>Wzorce nie poprawić wykrywanie jednostki
Podczas gdy wzorce wymagają jednostek, wzorca nie wykrywania jednostki. Wzorzec jest przeznaczone tylko do pomocy prognozowania intencje i ról.  

## <a name="patterns-use-entity-roles"></a>Wzorce użycia ról jednostki
Jeśli co najmniej dwie jednostki w układzie kontekstowej są powiązane, wzorce użycia jednostek [ról](luis-concept-roles.md) aby wyodrębnić informacje kontekstowe dotyczące jednostek. To jest odpowiednikiem dzieci hierarchiczna jednostki, ale jest **tylko** dostępne we wzorcach. 

## <a name="prediction-scores-with-and-without-patterns"></a>Wyniki prognozowania z włączonymi i wyłączonymi wzorce
Mając wystarczająco dużo przykład zniesławiających, LUIS będą mogli zwiększyć zaufanie prognozowania bez wzorce. Wzorce zwiększyć wynik zaufania bez konieczności podawania zniesławiających dowolną liczbę.  

## <a name="pattern-matching"></a>Dopasowanie wzorca
Wzorzec jest takie samo oparte na wykrywanie jednostek wewnątrz wzorzec najpierw, a następnie sprawdzanie poprawności rest słów i kolejność słów wzorca. Jednostki są wymagane we wzorcu dla wzorzec do dopasowania. 

## <a name="pattern-syntax"></a>Składnia wzorca
Składnia wzorzec jest szablon utterance. Szablon powinien zawierać słów i jednostek, które chcesz dopasować, a także słów i znaków interpunkcyjnych, które chcesz zignorować. Jest **nie** wyrażenia regularnego. 

Jednostki w wzorce są ujęte w nawiasach klamrowych `{}`. Wzorce mogą obejmować jednostek i obiektów z rolami. Pattern.any to jednostka tylko używane we wzorcach. Składnia jest wyjaśnione w poniższych sekcjach.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Składni, aby dodać jednostkę do szablonu wzorca
Aby dodać jednostkę do szablonu wzorzec, otaczające nazwa jednostki w nawiasach klamrowych, takich jak `Who does {Employee} manage?`. 

```
Who does {Employee} manage?
```

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Składnia można dodać do szablonu wzorzec jednostki i roli
Rola jednostki jest oznaczona jako `{entity:role}` z nazwą obiektu następuje dwukropek, a następnie nazwę roli. Aby dodać jednostkę z rolą do szablonu wzorzec, należy ująć nazwę podmiotu i nazwę roli nawiasy klamrowe, takich jak `Book a ticket from {Location:Origin} to {Location:Destination}`. 

```
Book a ticket from {Location:Origin} to {Location:Destination}
```

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Składni, aby dodać pattern.any do wzorca szablonu
Jednostka Pattern.any umożliwia dodanie jednostki o różnych długości z wzorcem. Jak długo szablonu wzorzec jest dołączana, pattern.any może być dowolnej długości. 

Aby dodać **Pattern.any** jednostki do szablonu wzorzec przestrzenny Pattern.any jednostki w nawiasach klamrowych, takich jak `How much does {Booktitle} cost and what format is it available in?`.  

```
How much does {Booktitle} cost and what format is it available in?
```

|Tytułów książek we wzorcu|
|--|
|Ile kosztuje **wykradać ten podręcznik** kosztów i format jest dostępna w?|
|Ile kosztuje **poprosić** kosztów i format jest dostępna w?|
|Ile kosztuje **zastanawiasz się zdarzenia Dog w nocy** kosztów i format jest dostępna w?| 

W tych przykładach tytuł książki kontekstowe wyrazy tytułu nie są trudne do LUIS. LUIS wie, gdzie tytułu kończy się, ponieważ jest w układzie i oznaczone jednostki Pattern.any.

### <a name="explicit-lists"></a>Jawne list
Deseń zawiera Pattern.any i składni wzorca umożliwia możliwości wyodrębniania niepoprawne jednostki oparte na utterance, utworzyć [jawną listę](https://aka.ms/ExplicitList) za pośrednictwem tworzenia interfejsu API, aby zezwolić na wyjątek. 

Na przykład załóżmy, że masz wzorca zawierający zarówno opcjonalnych składni `[]`i składni jednostki `{}`, połączonych w sposób, aby wyodrębnić dane niepoprawnie.

Należy wziąć pod uwagę wzorzec "[Znajdź] poczty e-mail, {temat} [{osoby}]". W poniższych zniesławiających **podmiotu** i **osoby** jednostki są wyodrębniane poprawnie i niepoprawnie:

|Wypowiedź|Jednostka|Poprawne wyodrębniania|
|--|--|:--:|
|wiadomości e-mail o psów z Krzysztof|temat = psów<br>osoba = Krzysztof|✔|
|Wyślij wiadomość e-mail o man z La Mancha|temat = man<br>osoba = La Mancha|X|

W powyższej tabeli utterance `email about the man from La Mancha`, podmiot powinien być `the man from La Mancha` (tytuł książki), ale ponieważ podmiot obejmuje opcjonalne słowo `from`, tytuł jest niepoprawnie przewidzieć. 

Aby naprawić tego wyjątku z wzorcem, Dodaj `the man from la mancha` jako jawną listę dopasowania dla jednostki {podmiotu} za pomocą [tworzenia interfejsu API dla jawną listę](https://aka.ms/ExplicitList).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Składnia służąca do zaznaczenia tekstu opcjonalne w utterance szablonu
Tekst jest opcjonalne w utterance przy użyciu składni wyrażeń regularnych nawias kwadratowy, `[]`. Opcjonalny tekst można zagnieżdżać nawiasy kwadratowe maksymalnie tylko dwa nawiasy.

```
[find] email about {subject} [from {person}]
```

Znaki interpunkcyjne takie jak `.`, `!`, i `?` można zignorować przy użyciu nawiasy kwadratowe. Aby ignorować te znaczniki, każdego znaku musi być w oddzielnym wzorca. Opcjonalne składni nie obsługuje obecnie ignorowanie elementu na liście kilka elementów.

## <a name="patterns-only"></a>Wzorce tylko
LUIS umożliwia aplikacji bez żadnych zniesławiających przykład z celem. To użycie jest dozwolone tylko wtedy, gdy są używane wzorce. Wzorce wymagają co najmniej jedną jednostkę w każdym wzorca. Dla aplikacji tylko do wzorca wzorzec nie powinna zawierać rozpoznane maszyny jednostki, ponieważ wymagają one wykonywania zniesławiających przykład. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak Implementowanie wzorców w tym samouczku](luis-tutorial-pattern.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
