---
title: 'Samouczek: Testowanie wsadowe w celu znalezienia problemów - USŁUGA LUIS'
description: W tym samouczku pokazano, jak używać testowania wsadowego do sprawdzania poprawności jakości aplikacji do rozumienia języka (LUIS).
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78250570"
---
# <a name="tutorial-batch-test-data-sets"></a>Samouczek: Zestawy danych testu wsadowego

W tym samouczku pokazano, jak używać testowania wsadowego do sprawdzania poprawności jakości aplikacji do rozumienia języka (LUIS).

Testowanie wsadowe umożliwia sprawdzenie poprawności stanu aktywnego, uczonego modelu za pomocą znanego zestawu wypowiedzi i jednostek oznaczonych etykietą. W pliku wsadowym w formacie JSON dodaj wypowiedzi i ustaw etykiety jednostek, których potrzebujesz, przewidywane wewnątrz wypowiedź.

Wymagania dotyczące badania partii:

* Maksymalnie 1000 wypowiedzi na test.
* Brak duplikatów.
* Dozwolone typy jednostek: tylko jednostki o obrobione.

Podczas korzystania z aplikacji innej niż ten samouczek, *nie* należy używać wypowiedzi przykład już dodane do aplikacji.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Tworzenie pliku testu wsadowego
> * Uruchamianie testu wsadowego
> * Przeglądanie wyników testów

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

Zaimportuj aplikację, która `1 pepperoni pizza on thin crust`przyjmuje zamówienie pizzy, takie jak .

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Użyj [portalu usługi LUIS w wersji zapoznawczej](https://preview.luis.ai/), `Pizza app`zaimportuj JSON do nowej aplikacji, nazwij aplikację .

1. Wybierz **pociąg** w prawym górnym rogu nawigacji, aby trenować aplikację.

## <a name="what-should-the-batch-file-utterances-include"></a>Co powinny zawierać wypowiedzi plików wsadowych

Plik wsadowy powinien zawierać wypowiedzi z jednostek najwyższego poziomu maszyny dowiedział się oznaczone, w tym pozycji początkowej i końcowej. Wypowiedzi nie powinny być częścią przykładów już w aplikacji. Powinny one być wypowiedzi, które chcesz pozytywnie przewidzieć dla intencji i jednostek.

Można oddzielić testy przez intencji i/lub jednostki lub wszystkie testy (do 1000 wypowiedzi) w tym samym pliku.

## <a name="batch-file"></a>Plik wsadowy

Przykład JSON zawiera jeden wypowiedź z jednostki etykietą, aby zilustrować, jak wygląda plik testowy. W własnych testach powinien mieć wiele wypowiedzi z poprawnym intencji i jednostki uczenia maszynowego oznaczone.

1. Utwórz `pizza-with-machine-learned-entity-test.json` go w edytorze tekstu lub [pobierz.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)

2. W pliku wsadowym w formacie JSON dodaj wypowiedź z **intencją,** którą chcesz przewidzieć w teście.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Uruchamianie partii

1. Wybierz **pozycję Testuj** na górnym pasku nawigacyjnym.

2. Wybierz **panel testowania wsadowego** w panelu po prawej stronie.

3. Wybierz **pozycję Importuj zestaw danych**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający aplikację usługi LUIS z wyróżnionym zestawem danych Importuj](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Wybierz lokalizację `pizza-with-machine-learned-entity-test.json` pliku.

5. Nazwij `pizza test` zestaw danych i wybierz pozycję **Gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Wybierz plik](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Wybierz przycisk **Uruchom**.

7. Wybierz **pozycję Zobacz wyniki**.

8. Przejrzyj wyniki na wykresie i legendzie.

## <a name="review-batch-results-for-intents"></a>Przeglądanie wyników partii dla intencji

Wyniki testu pokazują graficznie, jak wypowiedzi testu były przewidywane względem aktywnej wersji.

Na wykresie wsadowym są wyświetlane cztery kwadranty wyników. Po prawej stronie wykresu znajduje się filtr. Filtr zawiera intencje i encje. Po [wybraniu sekcji wykresu](luis-concept-batch-test.md#batch-test-results) lub punktu w obrębie wykresu poniżej wykresu zostanie wyświetlona skojarzona wypowiedź.

Podczas najeżdżania kursorem na wykres kółko myszy może powiększyć lub zmniejszyć wyświetlanie na wykresie. Jest to przydatne, gdy istnieje wiele punktów na wykresie ściśle ze sobą połączone.

Wykres jest w czterech kwadrantach, z dwiema sekcjami wyświetlanymi na czerwono.

1. Wybierz opcję **ModifyOrder** na liście filtrów.

    > [!div class="mx-imgBorder"]
    > ![Wybierz opcję ModifyOrder intent z listy filtrów](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Wypowiedź jest przewidywana jako **True Positive** co oznacza, że wypowiedź pomyślnie dopasowane jego przewidywania dodatnie wymienione w pliku wsadowym.

    > [!div class="mx-imgBorder"]
    > ![Wypowiedź pomyślnie dopasowała jego pozytywną prognozę](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Zielone znaczniki wyboru na liście filtrów wskazują również powodzenie testu dla każdego zamiaru. Wszystkie inne intencje są wymienione z wynikiem dodatnim 1/1, ponieważ wypowiedź została przetestowana dla każdego zamiaru, jako negatywny test dla wszelkich intencji niewymienionych w teście wsadowym.

1. Wybierz intencję **potwierdzenia.** Ta intencja nie jest wymieniona w teście wsadowym, więc jest to negatywny test wypowiedź, który jest wymieniony w teście wsadowym.

    > [!div class="mx-imgBorder"]
    > ![Wypowiedź pomyślnie przewidziała wartość ujemną dla nienotowanego zamiaru w pliku wsadowym](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Negatywny test zakończył się pomyślnie, jak wspomniano w przypadku zielonego tekstu w filtrze i siatki.

## <a name="review-batch-test-results-for-entities"></a>Przeglądanie wyników testów wsadowych dla jednostek

ModifyOrder jednostki, jako element maszyny z podentami, wyświetla, jeśli jednostki najwyższego poziomu dopasowane i wyświetlić, jak subentities są przewidywane.

1. Wybierz element **ModifyOrder** na liście filtrów, a następnie wybierz okrąg w siatce.

1. Przewidywanie jednostki jest wyświetlane poniżej wykresu. Wyświetlanie zawiera linie stałe dla prognoz, które odpowiadają oczekiwaniom i kropkowane linie dla prognoz, które nie pasują do oczekiwań.

    > [!div class="mx-imgBorder"]
    > ![Jednostka nadrzędna jednostki pomyślnie przewidywana w pliku wsadowym](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Znajdowanie błędów w teście wsadowym

W tym samouczku pokazano, jak uruchomić test i zinterpretować wyniki. Nie obejmowała filozofii testów ani sposobu reagowania na nieudane testy.

* Upewnij się, że obejmuje zarówno wypowiedzi pozytywne, jak i negatywne w teście, w tym wypowiedzi, które mogą być przewidywane dla innego, ale powiązanych intencji.
* W przypadku nie po awarii wypowiedzi, wykonaj następujące zadania, a następnie uruchom testy ponownie:
    * Przejrzyj bieżące przykłady intencji i jednostek, sprawdź poprawność wypowiedzi przykład aktywnej wersji są poprawne zarówno dla intencji i etykietowania jednostek.
    * Dodawanie funkcji ułatwiające aplikacji przewidywanie intencji i encji
    * Dodawanie bardziej pozytywnych wypowiedzi przykładowych
    * Przejrzyj równowagę wypowiedzi przykładowych w intencjach

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Następny krok

Samouczek używany test wsadowy, aby sprawdzić poprawność bieżącego modelu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wzorcach](luis-tutorial-pattern.md)

