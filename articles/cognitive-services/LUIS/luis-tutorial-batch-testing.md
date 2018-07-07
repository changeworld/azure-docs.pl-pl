---
title: Umożliwia testowanie partii poprawić prognoz usługi LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Batch test obciążeniowy, przejrzyj wyniki i poprawy usługi LUIS prognozy ze zmianami.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 27d6bbc628ac3183032a90d8f3ad98998c76a957
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888834"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Umożliwia testowanie partii znajdowanie problemów, dokładności prognozy

Ten samouczek pokazuje, jak na potrzeby testowania partii Znajdź wypowiedź prognozowania problemy.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Utwórz plik wsadowy testu 
* Uruchom test usługi batch
* Przejrzyj wyniki testu
* Napraw błędy do intencji
* Przetestowanie usługi batch

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * W tym artykule, należy również [LUIS](luis-reference-regions.md) konta, aby utworzyć aplikację usługi LUIS.

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Tworzenie nowej aplikacji
W tym artykule korzysta ze wstępnie utworzonych domen HomeAutomation. Wstępnie utworzone domeny ma intencji, jednostek i wypowiedzi do kontrolowania urządzeń HomeAutomation, takich jak światła. Tworzenie aplikacji, dodać domenę, uczenie i publikowanie.

1. W [LUIS](luis-reference-regions.md) witryny sieci Web, Utwórz nową aplikację, wybierając **Utwórz nową aplikację** na **MyApps** strony. 

    ![Tworzenie nowej aplikacji](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Wprowadź nazwę `Batchtest-HomeAutomation` w oknie dialogowym.

    ![Wprowadź nazwę aplikacji](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Wybierz **ze wstępnie utworzonych domen** w lewym dolnym rogu. 

    ![Wybierz domenę wbudowanych](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Wybierz **Dodawanie domeny** dla HomeAutomation.

    ![Dodawanie domeny HomeAutomation](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Wybierz **Train** w górnym prawym pasku nawigacyjnym.

    ![Wybierz przycisk szkolenie](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Kryterium testowania usługi Batch
Testowanie usługi Batch można testować maksymalnie 1000 wypowiedzi w danym momencie. Partia nie powinna mieć duplikatów. [Eksportuj](create-new-app.md#export-app) aplikacji, aby zobaczyć listę wypowiedzi bieżącego.  

Strategii testowania, która LUIS używa trzech oddzielnych zestawów danych: model wypowiedzi wypowiedzi testu usługi batch oraz wypowiedzi punktu końcowego. Na potrzeby tego samouczka upewnij się, że nie używasz wypowiedzi z obu wypowiedzi modelu (dodawane do intencji) lub wypowiedzi punktu końcowego. 

Nie używaj żadnego z wypowiedzi już w aplikacji do testowania usługi batch:

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Tworzenie partii, aby przetestować dokładność prognozowania intencji
1. Tworzenie `homeauto-batch-1.json` w edytorze tekstu, takie jak [VSCode](https://code.visualstudio.com/). 

2. Dodawanie wypowiedzi z **intencji** mają dostęp do przewidywanych w teście. Na potrzeby tego samouczka, aby stał się proste, wykonać wypowiedzi `HomeAutomation.TurnOn` i `HomeAutomation.TurnOff` i przełączać `on` i `off` tekstu w wypowiedzi. Dla `None` przeznaczenie, dodaj kilka wypowiedzi, które nie są częścią [domeny](luis-glossary.md#domain) obszaru (podmiot). 

    Aby dowiedzieć się, jak wyniki testu partii odnoszą się do usługi batch w formacie JSON, Dodaj jedynie sześć intencji.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Uruchom partię
1. Wybierz **testu** w górnym pasku nawigacyjnym. 

    ![Wybierz Test na pasku nawigacyjnym](./media/luis-tutorial-batch-testing/test-1.png)

2. Wybierz **Batch testowania panelu** w panelu po prawej stronie. 

    ![Wybierz test usługi Batch](./media/luis-tutorial-batch-testing/test-2.png)

3. Wybierz **Importowanie zestawu danych**.

    ![Wybierz zestaw importu danych](./media/luis-tutorial-batch-testing/test-3.png)

4. Wybierz lokalizację systemu plików `homeauto-batch-1.json` pliku.

5. Nazwij zestaw danych `set 1`.

    ![Wybierz plik](./media/luis-tutorial-batch-testing/test-4.png)

6. Wybierz przycisk **Uruchom**. Zaczekaj, aż testu jest wykonywane.

    ![Wybierz przebieg](./media/luis-tutorial-batch-testing/test-5.png)

7. Wybierz **wyniki**.

    ![Wyświetlanie wyników](./media/luis-tutorial-batch-testing/test-6.png)

8. Przejrzyj wyniki w wykresu i legenda.

    ![Wyniki usługi Batch](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Przejrzyj wyniki usługi batch
Wyniki przetwarzania wsadowego są wyświetlane w dwie sekcje. Górna sekcja zawiera wykresu i legenda. Dolną sekcję Wyświetla wypowiedzi po wybraniu nazwy obszaru wykresu.

Błędy są oznaczone kolorem czerwonym. Wykres ma cztery sekcje przy użyciu dwóch sekcjach wyświetlane na czerwono. **Są to sekcje, aby skoncentrować się na**. 

Prawym górnym rogu sekcji niepoprawnie wskazuje testu przewidzieć istnienie przeznaczenie lub jednostki. Na dole po lewej stronie wskazuje, że test niepoprawnie przewidzieć braku przeznaczenie lub jednostki.

### <a name="homeautomationturnoff-test-results"></a>Wyniki testu HomeAutomation.TurnOff
W legendzie, wybierz `HomeAutomation.TurnOff` intencji. Posiada ikoną zielony sukces na lewo od nazwy w legendzie. Nie ma żadnych błędów, dla tego intencji. 

![Wyniki usługi Batch](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn i brak intencji z błędami.
Dwie opcje mają błędy, co oznacza, że prognozy testu nie odpowiadają oczekiwaniom pliku wsadowego. Wybierz `None` intencji w legendzie, aby zapoznać się z pierwszego błędu. 

![Brak elementu intent](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Błędy są wyświetlane na wykresie w sekcjach red: **fałszywie dodatnie** i **fałszywie ujemny**. Wybierz **fałszywie ujemny** nazwa sekcji na wykresie, aby wyświetlić zakończone niepowodzeniem wypowiedzi pod wykresem. 

![Fałszywe błędy ujemna](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

Wypowiedź niepowodzenie, `help` Oczekiwano `None` przeznaczenie, ale test przewidzieć `HomeAutomation.TurnOn` intencji.  

Istnieją awarii dwóch węzłów: jeden w HomeAutomation.TurnOn i jeden w None. Oba zostały spowodowane przez wypowiedź `help` , ponieważ nie można spełnić oczekiwania żadna i był nieoczekiwany dopasowanie intencji HomeAutomation.TurnOn. 

Aby ustalić, dlaczego `None` wypowiedzi kończą się niepowodzeniem, przejrzyj wypowiedzi obecnie `None`. 

## <a name="review-none-intents-utterances"></a>Przegląd Brak intencji użytkownika wypowiedzi

1. Zamknij **testu** panel, wybierając **testu** przycisk na górnym pasku nawigacyjnym. 

2. Wybierz **kompilacji** z panelu w górnym menu nawigacyjnym. 

3. Wybierz **Brak** intencji z listy opcji.

4. Wybierz formant + E, aby wyświetlić widok tokenu wypowiedzi 
    
    |Brak intencji użytkownika wypowiedzi|Współczynnik przewidywania|
    |--|--|
    |"Zmniejsz temperatury dla mnie."|0.44|
    |"wymiar kuchenne światła 25".|0.43|
    |"obniżyć woluminu"|0.46|
    |"turn w Internecie w mojej Sprawdź sypialni"|0,28|

## <a name="fix-none-intents-utterances"></a>Napraw Brak intencji użytkownika wypowiedzi
    
Wypowiedzi w `None` powinna być spoza domeny aplikacji. Te wypowiedzi są względem HomeAutomation, dzięki czemu są one nieprawidłowe opcje. 

Usługa LUIS udostępnia też wypowiedzi, mniej niż 50% (<.50) wynik prognozy. Jeśli przyjrzymy się wypowiedzi w innej metody, zostanie wyświetlony znacznie wyższa wyniki prognozy. Gdy usługa LUIS ma niski wyniki dla przykładu wypowiedzi, jest dobrym wskaźnikiem wypowiedzi są mylące dla usługi LUIS między bieżącym celem i innymi opcjami. 

Ustalenie jej wypowiedzi obecnie w `None` przeznaczenie, należy je przenieść do intencji poprawne i `None` intencji wymaga nowego, odpowiednie intencji. 

Trzy wypowiedzi w `None` celem są przeznaczone do obniżenia automatyzacji ustawień urządzenia. Takie jak używają słów `dim`, `lower`, lub `decrease`. Czwarty wypowiedź pyta, czy włączyć w Internecie. Ponieważ wszystkie cztery wypowiedzi o włączeniu lub zmiana stopień zasilania na urządzeniu, powinny zostać przeniesione do `HomeAutomation.TurnOn` intencji. 

Jest to tylko jedno rozwiązanie. Można również utworzyć nowy cel `ChangeSetting` Przenieś wypowiedzi przy użyciu instrukcji dim, obniżyć i zmniejszyć w tym nowe przeznaczeniem. 

## <a name="fix-the-app-based-on-batch-results"></a>Usuń aplikację na podstawie wyników usługi batch
Przenieś cztery wypowiedzi do `HomeAutomation.TurnOn` intencji. 

1. Zaznacz pole wyboru powyżej listy wypowiedź, więc są zaznaczone wszystkie wypowiedzi. 

2. W **ponownie przypisać intencji** listę rozwijaną, wybierz opcję `HomeAutomation.TurnOn`. 

    ![Przenieś wypowiedzi](./media/luis-tutorial-batch-testing/move-utterances.png)

    Po ponownym przypisaniem cztery wypowiedzi, wypowiedź lista dla `None` celem jest pusty.

3. Dodaj cztery nowe opcje dla elementu intent żadna:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Te wypowiedzi są zdecydowanie spoza domeny HomeAutomation. Podczas wprowadzania każdego wypowiedź Obejrzyj wynik dla niego. Wynik może być niski lub nawet bardzo małe (z czerwoną otoczkę wokół niej). Po uczenie aplikacji, w kroku 8, wynik będzie znacznie wyższa. 

7. Usuń wszelkie etykiety, wybierając niebieska etykieta wypowiedź, a następnie wybierz pozycję **Usuń etykietę**.

8. Wybierz **Train** w górnym prawym pasku nawigacyjnym. Wynik każdej wypowiedź jest znacznie wyższa. Wszystkie wyniki dla `None` celem powinno być nad.80 teraz. 

## <a name="verify-the-fix-worked"></a>Sprawdzić zadziałała poprawki
Aby sprawdzić, czy wypowiedzi w teście usługi batch są poprawnie przewidywany **Brak** przeznaczenie, ponownie uruchom test usługi batch.

1. Wybierz **testu** w górnym pasku nawigacyjnym. 

2. Wybierz **Batch testowania panelu** w panelu po prawej stronie. 

3. Wybierz przycisk wielokropka (***...*** ) znajdujący się na prawo od nazwy usługi batch i wybierz **Uruchom zestaw danych**. Zaczekaj, aż odbywa się badanie usługi batch.

    ![Uruchom zestaw danych](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Wybierz **wyniki**. Intencji powinien mieć zielony ikon po lewej stronie nazwy metody konwersji. Przy użyciu właściwego filtru równa `HomeAutomation.Turnoff` przeznaczenie, wybierz zielony dot w górnym prawym panelu, najbardziej zbliżony do środka wykresu. Nazwa wypowiedź pojawia się w tabeli pod wykresem. Wynik `breezeway off please` jest bardzo niskie. Opcjonalne działanie to można dodać więcej wypowiedzi na intencje, aby zwiększyć ten wynik. 

    ![Uruchom zestaw danych](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the ellipsis (***...***) button at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykład wypowiedzi](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions