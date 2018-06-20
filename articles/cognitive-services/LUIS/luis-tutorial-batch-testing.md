---
title: Za pomocą testowania partii zwiększyć prognoz LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Wsadowe test obciążenia, przejrzeć wyniki i poprawić prognoz LUIS ze zmianami.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266400"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Umożliwia testowanie partii znaleźć dokładności przewidywania

Ten samouczek pokazuje, jak na potrzeby testowania partii znaleźć utterance prognozowania problemy.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Utwórz plik wsadowy testu 
* Uruchom test partii
* Przejrzyj wyniki testu
* Usuń błędy dla lokalizacji docelowych
* Sprawdź jeszcze raz partii

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * W tym artykule, należy również [LUIS][LUIS] konta, aby można było tworzyć LUIS aplikacji.

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować dla [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Utwórz nową aplikację
W tym artykule używa wbudowane domeny HomeAutomation. Wbudowane domeny ma lokalizacji docelowych, jednostki i zniesławiających kontroli HomeAutomation urządzeń, takich jak świateł. Tworzenie aplikacji, Dodaj domenę, szkolenie i publikowania.

1. W [LUIS] witryny sieci Web, Utwórz nową aplikację, wybierając **Utwórz nową aplikację** na **MyApps** strony. 

    ![Utwórz nową aplikację](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Wprowadź nazwę `Batchtest-HomeAutomation` w oknie dialogowym.

    ![Wprowadź nazwę aplikacji](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Wybierz **wbudowane domen** w lewym dolnym rogu. 

    ![Wybierz domenę wbudowane](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Wybierz **Dodawanie domeny** dla HomeAutomation.

    ![Dodawanie domeny HomeAutomation](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Wybierz **pociągu** w górnym prawym pasku nawigacyjnym.

    ![Wybierz przycisk pociągu](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Kryteria testu partii
Testowanie partii można testować maksymalnie 1000 zniesławiających naraz. Partia nie powinna mieć duplikatów. [Eksportuj](create-new-app.md#export-app) aplikacji, aby zapoznać się z listą zniesławiających bieżącej.  

Strategia testu LUIS używa trzech oddzielnych zestawów danych: model zniesławiających, zniesławiających testu partii i zniesławiających punktu końcowego. W tym samouczku upewnij się, że nie używasz zniesławiających z obu zniesławiających modelu (dodawane do celem) lub zniesławiających punktu końcowego. 

Nie używaj żadnego zniesławiających już w aplikacji dla testu partii:

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

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Tworzenie partii, aby przetestować dokładność prognozowania konwersji
1. Utwórz `homeauto-batch-1.json` w edytorze tekstu, takich jak [VSCode](https://code.visualstudio.com/). 

2. Dodaj zniesławiających z **zamiar** ma przewidywane w teście. W tym samouczku, aby był prosty, wykonaj zniesławiających w `HomeAutomation.TurnOn` i `HomeAutomation.TurnOff` i przełączanie `on` i `off` w zniesławiających tekstu. Dla `None` przeznaczeniu dodać kilka zniesławiających, które nie są częścią [domeny](luis-glossary.md#domain) obszaru (temat). 

    Aby zrozumieć, jak wyniki testu partii skorelowany partii JSON, Dodaj tylko sześć lokalizacji docelowych.

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

2. Wybierz **partii testowania panelu** w panelu po prawej stronie. 

    ![Wybierz test partii](./media/luis-tutorial-batch-testing/test-2.png)

3. Wybierz **dataset importu**.

    ![Wybierz zestaw danych importu](./media/luis-tutorial-batch-testing/test-3.png)

4. Wybierz lokalizację systemu plików `homeauto-batch-1.json` pliku.

5. Nazwa zestawu danych `set 1`.

    ![Wybierz plik](./media/luis-tutorial-batch-testing/test-4.png)

6. Wybierz przycisk **Uruchom**. Zaczekaj, aż zakończeniu testu.

    ![Wybierz polecenie Uruchom](./media/luis-tutorial-batch-testing/test-5.png)

7. Wybierz **zobaczyć wyniki**.

    ![Zobacz wyniki](./media/luis-tutorial-batch-testing/test-6.png)

8. Przejrzyj wyniki w wykresu i legenda.

    ![Wyniki partii](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Przejrzyj wyniki partii
Wyniki partii znajdują się w dwóch częściach. Pierwsza sekcja zawiera wykresu i legenda. Na dole Wyświetla zniesławiających, po wybraniu nazwa obszaru wykresu.

Błędy są oznaczone kolorem czerwonym. Wykres jest w cztery sekcje przy użyciu dwóch sekcjach czerwony. **Są to sekcje skoncentrować się na**. 

Górnym rogu sekcji niepoprawnie wskazuje testu przewidzieć istnienie zamiar lub jednostki. Na dole po lewej stronie wskazuje, że test niepoprawnie przewidzieć braku zamiar lub jednostki.

### <a name="homeautomationturnoff-test-results"></a>Wyniki testu HomeAutomation.TurnOff
W legendzie, wybierz `HomeAutomation.TurnOff` celem. Ma ona ikoną zielony Powodzenie się po lewej stronie nazwy w legendzie. Nie ma żadnych błędów dla tego celem. 

![Wyniki partii](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn i Brak lokalizacji docelowych zawiera błędy.
Innej metody pojawiły się błędy, co oznacza, że prognoz testu nie odpowiadają oczekiwaniom pliku wsadowego. Wybierz `None` konwersji w legendzie do przejrzenia pierwszego błędu. 

![Brak konwersji](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Błędy są wyświetlane na wykresie w sekcjach red: **fałszywych** i **False ujemna**. Wybierz **False ujemna** nazwę sekcji na wykresie, aby wyświetlić nie powiodło się zniesławiających pod wykresem. 

![Błędy ujemna wartość false](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

Utterance niepowodzenie `help` oczekiwano jako `None` zamiar, ale test przewidzieć `HomeAutomation.TurnOn` celem.  

Istnieją awarii dwóch węzłów: jeden w HomeAutomation.TurnOn i jeden w None. Zarówno zostały spowodowane utterance `help` ponieważ go nie może spełnić oczekiwania żadna i był nieoczekiwany dopasowania dla zamiar HomeAutomation.TurnOn. 

Aby określić, dlaczego `None` zniesławiających kończą się niepowodzeniem, zapoznaj się obecnie w zniesławiających `None`. 

## <a name="review-none-intents-utterances"></a>Przejrzyj brak konwersji na zniesławiających

1. Zamknij **testu** panelu, wybierając **testu** przycisk na górnym pasku nawigacyjnym. 

2. Wybierz **kompilacji** w górnym menu nawigacyjnym panelu. 

3. Wybierz **Brak** konwersji z listy lokalizacji docelowych.

4. Wybierz kontroli + E, aby wyświetlić widok tokenu zniesławiających 
    
    |Brak konwersji elementu zniesławiających|Wynik prognozowania|
    |--|--|
    |"zmniejszyć temperatury dla mnie należy"|0.44|
    |"dim świateł kuchni 25".|0.43|
    |"niższego woluminu"|0.46|
    |"Włącz w Internecie w mojej Sprawdź sypialnię"|0.28|

## <a name="fix-none-intents-utterances"></a>Usuń brak konwersji na zniesławiających
    
Wszelkie zniesławiających w `None` powinna być spoza domeny aplikacji. Te zniesławiających są względem HomeAutomation, dlatego są one nieprawidłowe opcje. 

LUIS daje również zniesławiających, mniej niż 50% (<.50) wynik prognozowania. W zniesławiających w innej metody, można zobaczyć wyniki prognozowania znacznie wyższa. Kiedy LUIS ma niski wyniki dla przykład zniesławiających, które jest wskazuje, zniesławiających są trudne do LUIS między bieżącym celem i innych opcji. 

Ustalenie aplikacji, w obecnie zniesławiających `None` zamiar należy przenieść do poprawne opcje i `None` zamiar potrzebuje nowych, odpowiednie opcje. 

Trzy zniesławiających w `None` celem są przeznaczone do obniżyć ustawienia urządzenia automatyzacji. Takie jak używają słowa `dim`, `lower`, lub `decrease`. Czwarty utterance zapyta włączyć w Internecie. Ponieważ wszystkie cztery zniesławiających o włączeniu lub zmianie stopień zasilania do urządzenia, powinna zostać przeniesiona do `HomeAutomation.TurnOn` celem. 

To jest tylko jedno rozwiązanie. Można również utworzyć nowy celem `ChangeSetting` i Przenieś zniesławiających przy użyciu instrukcji dim, zmniejszyć oraz zmniejszeniu do tego nowe opcje. 

## <a name="fix-the-app-based-on-batch-results"></a>Usuń aplikację na podstawie wyników wsadowego
Przenieś cztery zniesławiających do `HomeAutomation.TurnOn` celem. 

1. Zaznacz pole wyboru powyżej listy utterance, więc są wybrane wszystkie zniesławiających. 

2. W **ponownie przypisać zamiar** listy rozwijanej, wybierz pozycję `HomeAutomation.TurnOn`. 

    ![Przenieś zniesławiających](./media/luis-tutorial-batch-testing/move-utterances.png)

    Po ponownym przypisaniem cztery zniesławiających, utterance lista dla `None` celem jest pusta.

3. Dodaj cztery nowe opcje dla żadnego konwersji:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Te zniesławiających ostatecznie są spoza domeny HomeAutomation. Podczas wpisywania każdego utterance Obejrzyj wynik dla niego. Wynik może być niski lub nawet bardzo niskim (z czerwonym prostokątem wokół niego). Po uczenia aplikacji, w kroku 8, wynik będzie znacznie wyższa. 

7. Usuń wszystkich etykiet, wybierając niebieski etykiety w utterance i wybierz **Usuń etykietę**.

8. Wybierz **pociągu** w górnym prawym pasku nawigacyjnym. Wynik każdego utterance jest znacznie wyższa. Wszystkie wyniki dla `None` celem powinno być teraz powyżej.80. 

## <a name="verify-the-fix-worked"></a>Sprawdź poprawkę pracy
Aby sprawdzić, czy zniesławiających w teście partii są poprawnie przewidzieć dla **Brak** zamiar, ponownie uruchom test partii.

1. Wybierz **testu** w górnym pasku nawigacyjnym. 

2. Wybierz **partii testowania panelu** w panelu po prawej stronie. 

3. Wybierz wielokropek (...) z prawej strony nazwy partii i wybierz **Uruchom zestaw danych**. Zaczekaj, aż zakończeniu testu partii.

    ![Uruchom zestaw danych](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Wybierz **zobaczyć wyniki**. Intencje powinien mieć zielony ikony na lewo od nazwy metody konwersji. Z właściwego filtru ustawioną `HomeAutomation.Turnoff` zamiar, wybierz zielonego dot w górnym prawym panelu najbardziej zbliżony do środka wykresu. Nazwa utterance pojawia się w tabeli poniżej wykresu. Wynik z `breezeway off please` jest bardzo mała. Opcjonalne działanie to można dodać więcej zniesławiających do zamiar, aby zwiększyć ten wynik. 

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

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zniesławiających przykład](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions