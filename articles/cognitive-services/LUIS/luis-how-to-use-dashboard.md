---
title: Pulpit nawigacyjny — rozumienie języka — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Napraw intencje i encje za pomocą pulpitu nawigacyjnego wyszkolonej aplikacji. Pulpit nawigacyjny wyświetla ogólne informacje o aplikacji z najważniejszymi intencjami, które powinny zostać naprawione.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888212"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Jak ulepszyć aplikację za pomocą pulpitu nawigacyjnego

Znajdź i rozwiąż problemy z intencjami przeszkolonej aplikacji, gdy używasz wypowiedzi przykładowych. Pulpit nawigacyjny wyświetla ogólne informacje o aplikacji z najważniejszymi intencjami, które powinny zostać naprawione. 

Przejrzyj analizę pulpitu nawigacyjnego jest procesem iteracyjnym, powtarzanym podczas zmiany i ulepszania modelu.

Ta strona nie będzie mieć odpowiedniej analizy dla aplikacji, które nie mają żadnych wypowiedzi przykład w intencji, znany jako aplikacje _tylko do wzorca._ 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Jakie problemy można rozwiązać z pulpitu nawigacyjnego?

Trzy problemy rozwiązane na pulpicie nawigacyjnym to:

|Problem|Kolor wykresu|Wyjaśnienie|
|--|--|--|
|Brak równowagi w danych|-|Dzieje się tak, gdy ilość wypowiedzi przykład różni się znacznie. Wszystkie intencje muszą mieć _mniej więcej_ taką samą liczbę wypowiedzi przykład — z wyjątkiem None intencji. Powinien mieć tylko 10%-15% całkowitej ilości wypowiedzi w aplikacji.<br><br> Jeśli dane są niezrównoważone, ale dokładność intencji jest powyżej pewnego progu, ta nierównowaga nie jest zgłaszana jako problem.<br><br>**Zacznij od tego problemu - może to być główną przyczyną innych problemów.**|
|Niejasne prognozy|Orange|Dzieje się tak, gdy najlepsze intencje i następnego intencji wyniki są na tyle blisko, że mogą one przerzucać na następnego szkolenia, ze względu na [negatywne próbkowanie](luis-how-to-train.md#train-with-all-data) lub więcej wypowiedzi przykład dodane do intencji. |
|Nieprawidłowe prognozy|Red|Dzieje się tak, gdy wypowiedź przykład nie jest przewidywane dla intencji oznaczone (intencji, w którym jest w).|

Poprawne prognozy są reprezentowane z kolorem niebieskim.

Pulpit nawigacyjny pokazuje te problemy i informuje, których intencji dotyczy i sugeruje, co należy zrobić, aby ulepszyć aplikację. 

## <a name="before-app-is-trained"></a>Zanim aplikacja zostanie przeszkolona 

Przed rozpoczęciem szkolenia aplikacji pulpit nawigacyjny nie zawiera żadnych sugestii dotyczących poprawek. Trenuj aplikację, aby zobaczyć te sugestie.  

## <a name="check-your-publishing-status"></a>Sprawdzanie stanu publikowania

Karta **stanu publikowania** zawiera informacje o ostatnim opublikowaniu w aktywnej wersji. 

Sprawdź, czy aktywna wersja jest wersją, którą chcesz naprawić. 

![Pulpit nawigacyjny pokazuje usługi zewnętrzne aplikacji, opublikowane regiony i zagregowane trafienia z punktu końcowego.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Pokazuje również wszystkie usługi zewnętrzne, opublikowane regiony i zagregowane trafienia z punktu końcowego. 

## <a name="review-training-evaluation"></a>Przegląd oceny szkoleń

Karta **oceny szkolenia** zawiera zagregowane podsumowanie ogólnej dokładności aplikacji według obszaru. Wynik wskazuje jakość intencji. 

![Karta oceny szkolenia zawiera pierwszy obszar informacji o ogólnej dokładności aplikacji.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Wykres wskazuje poprawnie przewidywane intencje i obszary problemowe o różnych kolorach. Jak poprawić aplikację z sugestiami, ten wynik wzrasta. 

Sugerowane poprawki są oddzielone według typu problemu i są najbardziej istotne dla aplikacji. Jeśli wolisz przejrzeć i rozwiązać problemy według intencji, użyj karty **[Intencje z błędami](#intents-with-errors)** u dołu strony. 

Każdy obszar problemu ma intencje, które należy naprawić. Po wybraniu nazwy intencji strona **intencji** otwiera się z filtrem zastosowane do wypowiedzi. Ten filtr pozwala skupić się na wypowiedzi, które są przyczyną problemu.

### <a name="compare-changes-across-versions"></a>Porównywanie zmian między wersjami

Utwórz nową wersję przed wprowadzeniem zmian w aplikacji. W nowej wersji, aby sugerowane zmiany do wypowiedzi przykład intencji, a następnie trenować ponownie. Na karcie oceny **szkolenia** na stronie pulpitu nawigacyjnego użyj **pokaż zmiany z wersji szkoleniowej,** aby porównać zmiany. 

![Porównywanie zmian między wersjami](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Napraw wersję, dodając lub edytując przykładowe wypowiedzi i przekwalifikowanie

Podstawową metodą naprawy aplikacji będzie dodawanie lub edytowanie wypowiedzi przykład i przekwalifikowania. Nowe lub zmienione wypowiedzi muszą być zgodne ze wskazówkami dotyczącymi [wyrozumiałych wypowiedzi.](luis-concept-utterance.md)

Dodawanie wypowiedzi przykład powinny być wykonywane przez kogoś, kto:

* ma wysoki stopień zrozumienia, jakie wypowiedzi są w różnych intencji.
* wie, jak wypowiedzi w jednym zamiarze mogą być mylone z innym zamiarem.
* jest w stanie zdecydować, czy dwa zamiary, które są często mylone ze sobą, powinny być zwinięte w jeden zamiar. W takim przypadku różne dane muszą zostać wyciągnięte z jednostkami.

### <a name="patterns-and-phrase-lists"></a>Wzory i listy fraz

Strona analizy nie wskazuje, kiedy używać [wzorców](luis-concept-patterns.md) lub [list fraz](luis-concept-feature.md). Jeśli je dodać, może pomóc w niepoprawne lub niejasne prognoz, ale nie pomoże w braku równowagi danych. 

### <a name="review-data-imbalance"></a>Przegląd braku równowagi danych

Zacznij od tego problemu - może to być główną przyczyną innych problemów.

Lista intencji **braku równowagi danych** zawiera intencje, które wymagają więcej wypowiedzi w celu skorygowania braku równowagi danych. 

**Aby rozwiązać ten problem:**

* Dodaj więcej wypowiedzi do intencji, a następnie ponownie trenować. 

Nie należy dodawać wypowiedzi do None intencji, chyba że jest to sugerowane na pulpicie nawigacyjnym.

> [!Tip]
> Użyj trzeciej sekcji na stronie **Wypowiedzi na intencji** z **wypowiedzi (liczba)** ustawienie, jako szybki wizualny przewodnik, który intencje potrzebują więcej wypowiedzi.  
    ![Użyj "Wypowiedzi (liczba)", aby znaleźć intencje z nierównowagi danych.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Przejrzyj nieprawidłowe prognozy

Lista intencji **niepoprawne przewidywanie** pokazuje intencje, które mają wypowiedzi, które są używane jako przykłady dla określonego zamiaru, ale są przewidywane dla różnych intencji. 

**Aby rozwiązać ten problem:**

* Edytuj wypowiedzi, aby być bardziej szczegółowe dla intencji i trenować ponownie.
* Połącz intencje, jeśli wypowiedzi są zbyt ściśle wyrównane i ponownie trenować.

### <a name="review-unclear-predictions"></a>Przejrzyj niejasne prognozy

**Niejasnych przewidywania** listy intencji pokazuje intencje z wypowiedzi z wyników przewidywania, które nie są wystarczająco daleko od najbliższego rywala, że górna intencja dla wypowiedź może ulec zmianie na następnym treningu, z powodu [negatywnego próbkowania](luis-how-to-train.md#train-with-all-data).

**Aby rozwiązać ten problem;**

* Edytuj wypowiedzi, aby być bardziej szczegółowe dla intencji i trenować ponownie.
* Połącz intencje, jeśli wypowiedzi są zbyt ściśle wyrównane i ponownie trenować.

## <a name="utterances-per-intent"></a>Wypowiedzi na intencję

Ta karta pokazuje ogólny stan aplikacji w intencji. Jak naprawić intencje i przekwalifikować, nadal patrzeć na tę kartę dla problemów.

Na poniższym wykresie przedstawiono dobrze wyważoną aplikację, która prawie nie ma problemów do naprawienia.

![Na poniższym wykresie przedstawiono dobrze wyważoną aplikację, która prawie nie ma problemów do naprawienia.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Na poniższym wykresie przedstawiono źle zbilansowaną aplikację z wieloma problemami do naprawienia.

![Na poniższym wykresie przedstawiono dobrze wyważoną aplikację, która prawie nie ma problemów do naprawienia.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Umieść wskaźnik myszy na pasku każdej intencji, aby uzyskać informacje o zamiarze. 

![Na poniższym wykresie przedstawiono dobrze wyważoną aplikację, która prawie nie ma problemów do naprawienia.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Funkcja **Sortuj według** służy do rozmieszczenia intencji według typu problemu, aby skupić się na najbardziej problematycznych zamiarach związanych z tym problemem. 

## <a name="intents-with-errors"></a>Intencje z błędami

Ta karta umożliwia przeglądanie problemów pod kątem określonego zamiaru. Domyślny widok tej karty jest najbardziej problematycznym zamiarem, dzięki czemu wiesz, gdzie skupić swoje wysiłki.

![Karta Intencje z błędami umożliwia przeglądanie problemów pod kątem określonego zamiaru. Karta jest filtrowana do najbardziej problematycznych intencji, domyślnie, więc wiesz, gdzie skupić swoje wysiłki.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Wykres górnego pączka pokazuje problemy z intencją w trzech typach problemów. Jeśli występują problemy w trzech typach problemów, każdy typ ma swój własny wykres poniżej, wraz z wszelkimi intencjami rywala. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrowanie intencji według problemu i wartości procentowej

Ta sekcja karty umożliwia znajdowanie wypowiedzi przykład, które są chodzące poza próg błędu. Najlepiej, jeśli chcesz, aby poprawne prognozy były znaczące. Ten odsetek jest oparty na biznesie i kliencie. 

Określ wartości progowe, które są wygodne dla swojej firmy. 

Filtr umożliwia znalezienie intencji z określonym problemem:

|Filtr|Sugerowana wartość procentowa|Przeznaczenie|
|--|--|--|
|Najbardziej problematyczne intencje|-|**Rozpocznij tutaj** — naprawienie wypowiedzi w tym zamiarze poprawi aplikację bardziej niż inne poprawki.|
|Popraw prognozy poniżej|60%|Jest to procent wypowiedzi w wybranej intencji, które są poprawne, ale mają wynik zaufania poniżej progu. |
|Niejasne prognozy powyżej|15%|Jest to procent wypowiedzi w wybranej intencji, które są mylone z najbliższego zamiaru rywala.|
|Nieprawidłowe prognozy powyżej|15%|Jest to procent wypowiedzi w wybranej intencji, które są niepoprawnie przewidywane. |

### <a name="correct-prediction-threshold"></a>Poprawny próg przewidywania

Co to jest pewny wynik przewidywania zaufania dla Ciebie? Na początku tworzenia aplikacji 60% może być twoim celem. Użyj **correct prognoz poniżej** z procentem 60%, aby znaleźć wszelkie wypowiedzi w wybranej intencji, które muszą zostać naprawione.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Niejasny lub nieprawidłowy próg przewidywania

Te dwa filtry umożliwiają znajdowanie wypowiedzi w wybranej intencji poza progiem. Te dwie wartości procentowe można potraktować jako wartości procentowe błędów. Jeśli masz komfort z 10-15% poziom błędu dla prognoz, ustaw próg filtru do 15%, aby znaleźć wszystkie wypowiedzi powyżej tej wartości. 

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami platformy Azure](luis-how-to-azure-subscription.md)
