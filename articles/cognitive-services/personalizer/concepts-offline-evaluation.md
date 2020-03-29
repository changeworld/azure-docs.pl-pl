---
title: Użyj metody oceny trybu offline — Personalizator
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak używać oceny w trybie offline do pomiaru skuteczności aplikacji i analizowania pętli uczenia się.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: f8ceef5e80bf15f0ba52a9c289e617018febfb5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623590"
---
# <a name="offline-evaluation"></a>Ocena w trybie offline

Ocena w trybie offline to metoda, która umożliwia testowanie i ocenę skuteczności usługi Personalizer bez zmiany kodu lub wpływu na środowisko użytkownika. Ocena w trybie offline wykorzystuje wcześniejsze dane wysyłane z aplikacji do interfejsów API rangi i nagród, aby porównać wyniki różnych rang.

Ocena w trybie offline jest przeprowadzana w zakresie dat. Zakres może zakończyć się tak późno, jak bieżący czas. Początek zakresu nie może być większy niż liczba dni określona dla [przechowywania danych](how-to-settings.md).

Ocena offline może pomóc w udzieleniu odpowiedzi na następujące pytania:

* Jak skuteczne są rangi Personalizer dla pomyślnej personalizacji?
    * Jakie są średnie nagrody uzyskane przez personalizator online zasady uczenia maszynowego?
    * Jak Personalizer porównać do skuteczności tego, co aplikacja zrobiłaby domyślnie?
    * Jaka byłaby porównawcza skuteczność losowego wyboru personalizacji?
    * Jaka byłaby porównywalna skuteczność różnych polityk uczenia się określonych ręcznie?
* Które funkcje kontekstu przyczyniają się mniej więcej do pomyślnej personalizacji?
* Które funkcje akcji przyczyniają się w mniejszym lub większym stopniu do pomyślnej personalizacji?

Ponadto ocena w trybie offline może służyć do odkrywania bardziej zoptymalizowanych zasad uczenia się, które Personalizer może wykorzystać do poprawy wyników w przyszłości.

Oceny w trybie offline nie zawierają wskazówek co do procentu zdarzeń, które mają być używane do eksploracji.

## <a name="prerequisites-for-offline-evaluation"></a>Wymagania wstępne dotyczące oceny w trybie offline

Poniżej przedstawiono ważne zagadnienia dotyczące reprezentatywnej oceny w trybie offline:

* Posiadaj wystarczającą ilość danych. Zalecane minimum to co najmniej 50 000 zdarzeń.
* Zbieraj dane z okresów o reprezentatywnym zachowaniu użytkownika i ruchu.

## <a name="discovering-the-optimized-learning-policy"></a>Odkrywanie zoptymalizowanych zasad uczenia się

Personalizator może użyć procesu oceny w trybie offline, aby automatycznie odkryć bardziej optymalne zasady uczenia się.

Po wykonaniu oceny w trybie offline można zobaczyć porównawczą skuteczność Personalizer z tą nową polityką w porównaniu z bieżącą polityką online. Następnie można zastosować tę zasadę uczenia się, aby natychmiast wprowadzić je w życie w Personalizer, pobierając je i przesyłając w panelu Modele i zasady. Można również pobrać go do przyszłej analizy lub wykorzystania.

Obecne polityki uwzględnione w ocenie:

| Ustawienia uczenia się | Przeznaczenie|
|--|--|
|**Polityka online**| Aktualna polityka uczenia się stosowana w Personalizer |
|**Punkt odniesienia**|Domyślna aplikacja (określona przez pierwszą akcję wysłano w wywołaniach rangi)|
|**Polityka losowa**|Wyimaginowane zachowanie rangi, które zawsze zwraca losowy wybór akcji z podanych.|
|**Zasady niestandardowe**|Dodatkowe zasady uczenia się przekazywane podczas uruchamiania oceny.|
|**Zoptymalizowane zasady**|Jeśli ocena została rozpoczęta z opcją wykryć zoptymalizowaną politykę, zostanie ona również porównana i będzie można ją pobrać lub uczynić z niej politykę uczenia się online, zastępując obecną.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Zrozumienie trafności wyników oceny offline

Po uruchomieniu oceny w trybie offline, jest bardzo ważne, aby analizować _granice zaufania_ wyników. Jeśli są one szerokie, oznacza to, że aplikacja nie otrzymała wystarczającej ilości danych, aby szacunki nagrody były dokładne lub znaczące. Ponieważ system gromadzi więcej danych i uruchamiasz oceny w trybie offline przez dłuższy czas, przedziały ufności stają się węższe.

## <a name="how-offline-evaluations-are-done"></a>Jak wykonywane są oceny offline

Oceny w trybie offline są wykonywane przy użyciu metody o nazwie **Ocena scenariusza alternatywnego**.

Personalizer opiera się na założeniu, że zachowanie użytkowników (a tym samym nagrody) są niemożliwe do przewidzenia retrospektywnie (Personalizer nie może wiedzieć, co by się stało, gdyby użytkownik został pokazany coś innego niż to, co widzieli), a tylko uczyć się od zmierzonych nagród.

Jest to proces koncepcyjny używany do oceny:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Ocena w trybie offline używa tylko obserwowane zachowanie użytkownika. Ten proces odrzuca duże ilości danych, zwłaszcza jeśli aplikacja nie Rank wywołania z dużą liczbą akcji.


## <a name="evaluation-of-features"></a>Ocena funkcji

Oceny offline mogą dostarczyć informacji o tym, ile określonych funkcji dla działań lub kontekstu ważą dla wyższych nagród. Informacje są obliczane przy użyciu oceny względem danego okresu i danych i mogą się zmieniać w czasie.

Zalecamy przyjrzenie się ocenom funkcji i pytanie:

* Jakie inne, dodatkowe funkcje może zapewnić aplikacji lub systemu na wzór tych, które są bardziej skuteczne?
* Jakie funkcje można usunąć ze względu na niską skuteczność? Funkcje o niskiej skuteczności zwiększają _poziom hałasu_ w uczeniu maszynowym.
* Czy są jakieś funkcje, które są przypadkowo dołączone? Przykładami są: informacje umożliwiające identyfikację użytkownika, zduplikowane identyfikatory itp.
* Czy są jakieś niepożądane funkcje, które nie powinny być używane do personalizacji ze względu na kwestie regulacyjne lub odpowiedzialne użycie? Czy istnieją funkcje, które mogą proxy (czyli ściśle dublować lub korelować z) niepożądane funkcje?


## <a name="next-steps"></a>Następne kroki

[Konfigurowanie personalizatora](how-to-settings.md)
[Uruchom oceny w trybie offline](how-to-offline-evaluation.md) [zrozumieć, jak działa Personalizer](how-personalizer-works.md)
