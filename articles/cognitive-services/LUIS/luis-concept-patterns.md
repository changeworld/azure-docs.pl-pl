---
title: Wzorce pomagają w przewidywaniu — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Wzorzec pozwala uzyskać większą dokładność intencji bez dostarczania wielu wypowiedzi więcej.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890285"
---
# <a name="patterns-improve-prediction-accuracy"></a>Wzorce zwiększają dokładność przewidywania
Wzorce są przeznaczone do poprawy dokładności, gdy kilka wypowiedzi są bardzo podobne.  Wzorzec pozwala uzyskać większą dokładność intencji bez dostarczania wielu wypowiedzi więcej. 

## <a name="patterns-solve-low-intent-confidence"></a>Wzorce rozwiązują niskie zaufanie intencji
Należy wziąć pod uwagę aplikację Zasobów Ludzkich, która raportuje na wykresie organizacyjnym w odniesieniu do pracownika. Biorąc pod uwagę nazwę i relację pracownika, usługa LUIS zwraca zaangażowanych pracowników. Rozważmy pracownika, Toma, z nazwą menedżera Alice i zespół podwładnych o nazwie: Michael, Rebecca i Carl.

![Obraz schematu organizacyjnego](./media/luis-concept-patterns/org-chart.png)

|Wypowiedzi|Przewidywane intencje|Wynik intencji|
|--|--|--|
|Kto jest podwładnym Toma?|Schemat GetOrgChart|.30|
|Kto jest podwładnym Toma?|Schemat GetOrgChart|.30|

Jeśli aplikacja ma od 10 do 20 wypowiedzi o różnych długościach zdania, innej kolejności wyrazów, a nawet różnych wyrazach (synonimy "podrzędny", "zarządzaj", "raport"), usługa LUIS może zwrócić niski wynik zaufania. Utwórz wzorzec, aby pomóc usługi LUIS zrozumieć znaczenie kolejności wyrazów. 

Wzorce rozwiązują następujące sytuacje: 

* Wynik intencji jest niski
* Prawidłowa intencja nie jest najlepszy wynik, ale zbyt blisko najwyższego wyniku. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Wzorce nie są gwarancją intencji
Wzorce używają kombinacji technologii przewidywania. Ustawienie intencji dla wypowiedź szablonu we wzorcu nie jest gwarancją przewidywania intencji, ale jest silnym sygnałem. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Wzorce nie poprawiają wykrywania jednostek nauczanych maszynowo

Wzorzec ma przede wszystkim pomóc w przewidywaniu intencji i ról. _Pattern.any_ jednostki jest używany do wyodrębniania jednostek dowolnych. Podczas gdy wzorce używają jednostek, wzorzec nie pomaga wykryć jednostki nauczanych maszynowo.  

Nie oczekuj, aby zobaczyć lepsze przewidywanie jednostki, jeśli zwiń wiele wypowiedzi w jeden wzorzec. Dla prostych jednostek do ognia, należy dodać wypowiedzi lub użyć jednostek listy inaczej wzorzec nie będzie uruchamiany.

## <a name="patterns-use-entity-roles"></a>Wzorce używają ról encji
Jeśli dwie lub więcej jednostek we wzorcu są powiązane kontekstowo, wzorce używają [ról jednostek](luis-concept-roles.md) do wyodrębniania informacji kontekstowych o jednostkach.  

## <a name="prediction-scores-with-and-without-patterns"></a>Wyniki prognozowania z i bez wzorców
Biorąc pod uwagę wiele wypowiedzi przykład, usługa LUIS będzie w stanie zwiększyć zaufanie przewidywania bez wzorców. Wzorce zwiększyć wynik zaufania bez konieczności zapewnienia jak najwięcej wypowiedzi.  

## <a name="pattern-matching"></a>Dopasowanie do wzorca
Wzorzec jest dopasowywał się na podstawie wykrywania jednostek wewnątrz wzorca, a następnie sprawdzania poprawności pozostałych wyrazów i kolejności wyrazów wzorca. Jednostki są wymagane we wzorcu dla wzorca, aby dopasować. Wzorzec jest stosowany na poziomie tokenu, a nie na poziomie znaku. 

## <a name="pattern-only-apps"></a>Aplikacje tylko do wzorka
Można utworzyć aplikację z intencji, które nie mają wypowiedzi przykład, tak długo, jak istnieje wzorzec dla każdego zamiaru. Dla aplikacji tylko do wzorca wzorzec nie powinien zawierać jednostek nauczanych maszynowo, ponieważ wymagają one wypowiedzi przykładowych. 

## <a name="best-practices"></a>Najlepsze rozwiązania
Poznaj [najlepsze wskazówki](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Składnia wzorca

Naucz się składni wzorca na podstawie [odwołania do składni wzorca](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wzorcach:

* [Jak dodać wzory](luis-how-to-model-intent-pattern.md)
* [Jak dodać pattern.any encji](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Składnia wzorców](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować wzorce w tym samouczku](luis-tutorial-pattern.md)
