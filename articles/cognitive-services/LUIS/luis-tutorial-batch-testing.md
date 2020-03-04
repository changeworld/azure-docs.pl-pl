---
title: 'Samouczek: testowanie wsadowe w celu znalezienia problemów — LUIS'
description: W tym samouczku pokazano, jak za pomocą testów wsadowych sprawdzić poprawność jakości aplikacji Language Understanding (LUIS).
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250570"
---
# <a name="tutorial-batch-test-data-sets"></a>Samouczek: zestawy danych testów wsadowych

W tym samouczku pokazano, jak za pomocą testów wsadowych sprawdzić poprawność jakości aplikacji Language Understanding (LUIS).

Testowanie usługi Batch umożliwia zweryfikowanie aktywny, skonfigurowanych pod kątem stanu modelu przy użyciu znanego zestawu etykietami wypowiedzi i jednostek. W pliku wsadowym w formacie JSON Dodawanie wypowiedzi i ustawić etykiety jednostki, które należy przewidzieć wewnątrz wypowiedź.

Wymagania dotyczące testowania usługi batch:

* Maksymalnie 1000 wypowiedzi na test.
* Bez duplikatów.
* Dozwolone typy jednostek: tylko jednostki rozpoznajce maszynowo.

W przypadku korzystania z aplikacji innej niż w tym samouczku *nie należy używać* przykładowej wyrażenia długości już dodanej do aplikacji.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Utwórz plik wsadowy testu
> * Uruchom test usługi batch
> * Przejrzyj wyniki testu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

Zaimportuj aplikację, która przyjmuje zamówienie Pizza, takie jak `1 pepperoni pizza on thin crust`.

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Użyj [portalu Luis w wersji zapoznawczej](https://preview.luis.ai/), zaimportuj kod JSON do nowej aplikacji, nazwij aplikację `Pizza app`.

1. Wybierz pozycję **szkolenie** w prawym górnym rogu nawigacji, aby szkolić aplikację.

## <a name="what-should-the-batch-file-utterances-include"></a>Co powinien zawierać plik wsadowy wyrażenia długości

Plik wsadowy powinien zawierać wyrażenia długości z przydziałem maszyn najwyższego poziomu z etykietą z uwzględnieniem pozycji początkowych i końcowych. Wyrażenia długości nie powinna być częścią przykładów już w aplikacji. Powinny one być wyrażenia długości, aby uzyskać pozytywne przewidywania dotyczące zamiar i jednostek.

Możesz oddzielić testy na podstawie zamiaru i/lub jednostki lub uzyskać wszystkie testy (do 1000 wyrażenia długości) w tym samym pliku.

## <a name="batch-file"></a>Plik wsadowy

Przykładowy kod JSON zawiera jeden wypowiedź z etykietą Entity, aby zilustrować, jak wygląda plik testowy. We własnych testach powinna istnieć wiele wyrażenia długości z poprawnym zamiarem i podaną przez maszyną jednostką.

1. Utwórz `pizza-with-machine-learned-entity-test.json` w edytorze tekstu lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) go.

2. W pliku wsadowym w formacie JSON Dodaj wypowiedź z **zamiarem** , który ma być przewidywany w teście.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Uruchom partię

1. Na górnym pasku nawigacyjnym wybierz pozycję **test** .

2. Wybierz pozycję **panel testowania partii** w panelu po prawej stronie.

3. Wybierz pozycję **Importuj zestaw danych**.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu aplikacji LUIS z wyróżnionym zestawem danych](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Wybierz lokalizację pliku `pizza-with-machine-learned-entity-test.json`.

5. Nazwij zestaw danych `pizza test` a następnie wybierz pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![wybierz plik](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Wybierz przycisk **Uruchom**.

7. Wybierz pozycję **Zobacz wyniki**.

8. Przejrzyj wyniki w wykresu i legenda.

## <a name="review-batch-results-for-intents"></a>Przejrzyj wyniki partii dla intencji

Wyniki testu pokazują graficznie, w jaki sposób przewidywalno wyrażenia długości testów względem aktywnej wersji.

Wykres batch przedstawia cztery quadrants firmy Gartner wyników. Z prawej strony wykresu jest filtrem. Filtr zawiera intencje i jednostki. Po wybraniu [sekcji wykresu](luis-concept-batch-test.md#batch-test-results) lub punktu na wykresie, skojarzone wypowiedź są wyświetlane poniżej wykresu.

Podczas przesuwania wskaźnika w obrębie wykresu, kółka myszy można zwiększyć lub zmniejszyć wyświetlane na wykresie. Jest to przydatne, gdy istnieje wiele punktów na wykresie klastrowane ściśle ze sobą.

Wykres jest w czterech quadrants firmy Gartner, przy użyciu dwóch sekcjach wyświetlane na czerwono.

1. Wybierz zamiar **ModifyOrder** na liście filtrów.

    > [!div class="mx-imgBorder"]
    > ![wybrać ModifyOrder zamierzenia z listy filtrów](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Wypowiedź jest przewidywane jako **prawdziwe pozytywne** znaczenie, że wypowiedź pomyślnie pasowała do swojej pozytywnej przewidywania wymienionej w pliku wsadowym.

    > [!div class="mx-imgBorder"]
    > ![wypowiedź pomyślnie pasowała do swojej pozytywnej przewidywania](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Zielony znacznik wyboru na liście filtry wskazuje również sukces testu dla każdego zamiaru. Wszystkie inne intencje są wymienione z 1/1 pozytywnego wyniku, ponieważ wypowiedź został przetestowany z każdym zamiarem, jako negatywny test dla wszelkich intencji niewymienionych w teście wsadowym.

1. Wybierz zamiar **potwierdzenia** . Ten cel nie jest wymieniony w teście wsadowym, więc jest to negatywny test wypowiedź, który jest wymieniony w teście wsadowym.

    > [!div class="mx-imgBorder"]
    > ![wypowiedź pomyślnie przewidywalna wartość ujemna dla zamiaru nieznajdującego się na liście w pliku wsadowym](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Test negatywny zakończył się pomyślnie, jak zanotowano zielony tekst w filtrze i siatka.

## <a name="review-batch-test-results-for-entities"></a>Przegląd wyników testów partii dla jednostek

Jednostka ModifyOrder jako jednostka maszyny z podjednostkami wyświetla, czy jednostka najwyższego poziomu jest dopasowana i wyświetla sposób przewidywania podjednostek.

1. Wybierz jednostkę **ModifyOrder** na liście filtrów, a następnie wybierz okrąg w siatce.

1. Prognoza jednostki jest wyświetlana poniżej wykresu. Wyświetlacz zawiera pełne linie dla prognoz, które pasują do oczekiwanych i kropkowanych wierszy w przypadku prognoz, które nie pasują do oczekiwanej wartości.

    > [!div class="mx-imgBorder"]
    > element nadrzędny jednostki ![został pomyślnie przewidywalny w pliku wsadowym](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Znajdowanie błędów z testem wsadowym

W tym samouczku pokazano, jak uruchomić test i interpretować wyniki. Nie obejmuje to zasady testowania ani reagowania na testy zakończone niepowodzeniem.

* Upewnij się, że w teście są pokryte pozytywne i ujemne wyrażenia długości, w tym wyrażenia długości, które mogą być przewidywane dla różnych, ale powiązanych zamierzeń.
* W przypadku niepowodzenia wyrażenia długości wykonaj następujące zadania, a następnie ponownie uruchom testy:
    * Zapoznaj się z bieżącymi przykładami założeń i jednostek, sprawdzając, czy przykład wyrażenia długości aktywnej wersji jest poprawny zarówno w przypadku zamiaru, jak i etykiety jednostek.
    * Dodawanie funkcji, które ułatwiają prognozowanie intencji i jednostek
    * Dodaj bardziej pozytywne przykładowe wyrażenia długości
    * Przeglądanie bilansu przykładowej wyrażenia długości w ramach intencji

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Następny krok

Samouczek użył testu wsadowego do walidacji bieżącego modelu.

> [!div class="nextstepaction"]
> [Informacje o wzorcach](luis-tutorial-pattern.md)

