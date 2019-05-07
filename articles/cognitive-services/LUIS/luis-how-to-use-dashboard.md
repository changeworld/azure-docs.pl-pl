---
title: Pulpit nawigacyjny — interpretacji języka
titleSuffix: Azure Cognitive Services
description: Napraw intencji za pomocą analizy pulpit nawigacyjny podsumowania, wizualizowany narzędziu do raportowania.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a518a697369ff74689a0c4ac05af96453b6a5ca4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072459"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Jak korzystać z pulpitu nawigacyjnego na ulepszenie aplikacji

Znajdowanie i rozwiązywanie problemów z opcjami aplikacji przeszkolonych, korzystając z wypowiedzi przykład. Pulpit nawigacyjny podsumowania przedstawia ogólne informacje o aplikacji, przy użyciu światła intencji, które powinny zostać usunięte. 

Przejrzyj pulpit nawigacyjny analizy jest procesem iteracyjnym, powtarzany zmiany i poprawy modelu.

Ta strona nie będzie analizy odpowiednie dla aplikacji, które nie mają wypowiedzi przykład intencji, znane jako _tylko do wzorca_ aplikacji. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Problemy, jakie można naprawić z poziomu pulpitu nawigacyjnego?

Dostępne są następujące trzy problemy, które zostały rozwiązane w pulpicie nawigacyjnym:

|Problem|Kolor wykresu|Wyjaśnienie|
|--|--|--|
|Brak równowagi danych|-|Dzieje się tak, gdy ilość wypowiedzi przykład różni się znacznie. Wszystkie opcje, które muszą mieć _około_ taką samą liczbę wypowiedzi przykład — z wyjątkiem sytuacji żadne intencji. 10-15% całkowitej ilości wypowiedzi powinien mieć tylko w aplikacji.<br><br> Jeśli dane są imbalanced, ale intencji dokładność przekracza określony próg, to nierównowagi nie został zgłoszony jako problem.<br><br>**Rozpocznij z tym problemem — może być przyczyny inne problemy.**|
|Prognozy niejasny|Orange|Ten błąd występuje podczas najważniejsze intencji i dalej intencji wyniki są na tyle bliskie, że może Przerzuć na szkolenie dalej w ze względu na [ujemna próbkowania](luis-how-to-train.md#train-with-all-data) lub więcej wypowiedzi przykładzie dodano na intencje. |
|Niepoprawne prognozy|Czerwony|Dzieje się tak, gdy nie przewiduje się wypowiedź przykład dla oznaczonych intencji (przeznaczenie, w którym się).|

Poprawne prognozy są reprezentowane przez kolor niebieski.

Pulpit nawigacyjny podsumowania przedstawia te problemy i informujący o tym, intencji, które ma wpływ i sugeruje, co należy zrobić, aby udoskonalać aplikację. 

## <a name="before-app-is-trained"></a>Zanim skonfigurowanych pod kątem aplikacji 

Przed uczyć się aplikacja, pulpit nawigacyjny podsumowania nie zawiera żadnych sugestie dotyczące poprawki. Szkolenie aplikację, aby wyświetlić te sugestie.  

## <a name="check-your-publishing-status"></a>Sprawdź stan publikowania

**Stan publikowania** karta zawiera informacje dotyczące aktywnej wersji ostatniego publikowania. 

Sprawdź, czy wersja aktywna jest wersję, którą chcesz naprawić. 

![Pulpit nawigacyjny podsumowania przedstawia aplikacji usług zewnętrznych, opublikowane regionów i zagregowane trafienia punktu końcowego.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

To również pokazuje wszelkie usług zewnętrznych, opublikowanych regionów i zagregowane trafienia punktu końcowego. 

## <a name="review-training-evaluation"></a>Przejrzyj oceny szkolenia

**Oceny szkolenia** karta zawiera zagregowane podsumowanie dokładność ogólnej aplikacji według obszaru. Wynik wskazuje intencji jakości. 

![Karta oceny szkolenia zawiera pierwszy obszar informacji na temat dokładność ogólnej Twojej aplikacji.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Wykres wskazuje poprawnie przewidywane intencje i obszarów problemów w różnych kolorach. Jak poprawić się aplikacji z sugestiami, ta zwiększa się wynik. 

Sugerowanymi poprawkami rozdziela typ problemu i są najbardziej istotne dla twojej aplikacji. Jeśli chcesz użyć przejrzeć i rozwiązywanie problemów na intencje, użyj **[intencji z błędami](#intents-with-errors)** karty w dolnej części strony. 

Każdy obszar problemu ma intencji, które trzeba naprawić. Po wybraniu opcji Nazwa **intencji** z filtrem wypowiedzi zostanie otwarta strona. Ten filtr pozwala skoncentrować się na wypowiedzi, które są przyczyną problemu.

### <a name="compare-changes-across-versions"></a>Porównaj zmiany w różnych wersjach

Utwórz nową wersję przed wprowadzeniem zmian w aplikacji. W nowej wersji dokonać sugerowane zmiany wypowiedzi przykład celem, a następnie szkolenie ponownie. Na stronie pulpitu nawigacyjnego **oceny szkolenia** karty, należy użyć **Pokaż zmiany z wersji uczonego** Aby porównać zmiany. 

![Porównaj zmiany w różnych wersjach](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Napraw wersją przez dodawanie lub edytowanie przykład wypowiedzi i ponownego trenowania

Podstawową metodą ustalania aplikacji będzie Dodaj lub Edytuj przykład wypowiedzi i ponowne szkolenie. Nowe lub zostały zmienione wypowiedzi konieczne postępuj zgodnie z wytycznymi dla [zróżnicowane wypowiedzi](luis-concept-utterance.md).

Dodawanie wypowiedzi przykład powinno się odbywać przez osobę, która:

* ma wysoki stopień zrozumieć, jakie wypowiedzi znajdują się w różnych intencji
* wie, jak wypowiedzi w jednym profilu konwersji nieco mylące może być z inną intencji
* możliwość określenia, czy dwa intencji, które są często mylone ze sobą, powinien zostać zwinięty do pojedynczego intencji i inne dane pobierane przy użyciu jednostek

### <a name="patterns-and-phrase-lists"></a>Wzorce i frazy list

Strona analiza nie wskazuje, kiedy należy używać [wzorców](luis-concept-patterns.md) lub [frazę list](luis-concept-feature.md). Jeśli dodasz je, może pomóc w prognozy niepoprawne lub niejasnego, ale nie będzie pomóc nierównowagi danych. 

### <a name="review-data-imbalance"></a>Przegląd danych nierównowagi

Rozpocznij z tym problemem — może być przyczyny inne problemy.

**Nierównowagi danych** intencji lista intencji, które muszą wypowiedzi więcej, aby można było rozwiązać nierównowagi danych. 

**Aby rozwiązać ten problem**:

* Dodawanie wypowiedzi więcej do intencji, a następnie szkolenie ponownie. 

Nie należy dodawać wypowiedzi intencji None, chyba że, jest zalecane na pulpicie nawigacyjnym podsumowania.

> [!Tip]
> Użyj trzecia sekcja na stronie **wypowiedzi na intencje** z **wypowiedzi (numer)** ustawienie jako szybki przewodnik wizualny po których intencji potrzebujesz więcej wypowiedzi.  
    ![Użyj "Wypowiedzi (numer)", aby znaleźć intencji z nierównowagi danych.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Przejrzyj niepoprawne prognozy

**Niepoprawne prognozowania** intencji lista intencji, które mają wypowiedzi, które służą jako przykłady dla określonego przeznaczenie, ale oczekuje na inną intencji. 

**Aby rozwiązać ten problem**:

* Edytuj wypowiedzi na dokładniejszą intencji i szkolenie ponownie.
* Połącz intencji, jeśli wypowiedzi zbyt ściśle są wyrównane i uczenie się ponownie.

### <a name="review-unclear-predictions"></a>Przejrzyj niejasne prognozy

**Niejasne prognozowania** intencji lista intencji z wypowiedzi o wyniki prognozowania, które nie są wystarczający sposób z dokładnością rywal, że najważniejsze przeznaczenie wypowiedź może ulec zmianie po następnym szkolenia, ze względu na [ ujemna próbkowania](luis-how-to-train.md#train-with-all-data).

**Aby rozwiązać ten problem**;

* Edytuj wypowiedzi na dokładniejszą intencji i szkolenie ponownie.
* Połącz intencji, jeśli wypowiedzi zbyt ściśle są wyrównane i uczenie się ponownie.

## <a name="utterances-per-intent"></a>Wypowiedzi na intencji

Ta karta przedstawia ogólną kondycję aplikacji między intencji. Po poprawieniu intencje i ponownego próbkowania w dalszym ciągu Przegląd na tej karcie problemów.

Na poniższym wykresie przedstawiono zrównoważonego aplikacji prawie bez problemów, aby rozwiązać problem.

![Na poniższym wykresie przedstawiono zrównoważonego aplikacji prawie bez problemów, aby rozwiązać problem.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Na poniższym wykresie przedstawiono nieprawidłowo zrównoważone aplikacji zawierające wiele problemów, aby rozwiązać problem.

![Na poniższym wykresie przedstawiono zrównoważonego aplikacji prawie bez problemów, aby rozwiązać problem.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Umieść kursor nad paskiem każdego przeznaczenie, aby uzyskać informacje o zamiar. 

![Na poniższym wykresie przedstawiono zrównoważonego aplikacji prawie bez problemów, aby rozwiązać problem.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Użyj **sortować według** funkcję, aby rozmieścić intencji przez typ problemu, dzięki czemu możesz skupić się na najbardziej problemowymi intencji z tym problemem. 

## <a name="intents-with-errors"></a>Intencji z błędami

Ta karta umożliwia przeglądanie problemów dotyczących określonych intencji. Domyślny widok ta karta jest najbardziej problemowymi intencji, aby wiedzieć, gdzie można skoncentrować się na.

![Intencji kartą błędy umożliwia przeglądanie problemów dotyczących określonych intencji. Karta jest filtrowana w celu najbardziej problemowymi intencji domyślnie, aby wiedzieć, gdzie można skoncentrować się na.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Wykres pierścieniowy najważniejsze przedstawiono problemy związane z zamiarem różnych typów trzy problem. Jeśli występują problemy w typach trzy problemu, każdy typ ma swój własny wykres poniżej, wraz z dowolnym konkurencyjnych intencji. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtruj intencji problemu i wartość procentowa

Tej sekcji karty umożliwia znajdowanie wypowiedzi przykład wchodzące w próg błędu. Optymalnym rozwiązaniem jest poprawne prognozy znacząca. Ta wartość procentowa jest biznesowych i przez klientów. 

Określ wartości procentowych wartości progowej, które potrafisz dla Twojej firmy. 

Filtr umożliwia wyszukiwanie intencji konkretnego problemu:

|Filtr|Sugerowane wartości procentowej|Przeznaczenie|
|--|--|--|
|Najbardziej problemowymi intencji|-|**Zacznij tutaj** — naprawianie wypowiedzi w tym intencji poprawi aplikacji więcej niż inne poprawki.|
|Poprawne prognoz poniżej|60%|Jest to wartość procentowa wypowiedzi w metodę konwersji, które są poprawne, ale mają współczynnik ufności, poniżej wartości progowej. |
|Prognozy niejasne powyżej|15%|Jest to wartość procentowa wypowiedzi w metodę konwersji, które są mylić z dokładnością intencji konkurencyjnych.|
|Niepoprawne prognozy powyżej|15%|Jest to wartość procentowa wypowiedzi w metodę konwersji, które są niepoprawnie przewidzieć. |

### <a name="correct-prediction-threshold"></a>Próg poprawne prognoz

Co to jest współczynnik ufności prognozy pewność, do Ciebie? Na początku tworzenia aplikacji, 60% może być urządzenie docelowe. Użyj **Popraw prognoz poniżej** procentowej 60%, aby znaleźć wypowiedzi w wybranych intencji, które trzeba naprawić.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Prognozy niejasna lub niepoprawne wartości progowej

Te dwa filtry umożliwiają znajdowanie wypowiedzi w metodę konwersji przekracza próg. Te dwie wartości procentowe można traktować jako wartość procentowa błędów. Jeśli masz doświadczenia z 10 – 15% współczynnik błędów dla prognoz, należy ustawić dla progu filtr 15%, aby znaleźć wszystkie wypowiedzi powyżej tej wartości. 

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzaj zasobami platformy Azure](luis-how-to-azure-subscription.md)
