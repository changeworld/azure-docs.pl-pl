---
title: Dodaj wbudowane intencje i jednostek, aby wyodrębnić wspólne dane w uzgodnieniu języka - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać wbudowane intencje i jednostek w celu wyodrębnienia różnych typów danych jednostki.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266383"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>Użyj wbudowane intencje i jednostek do obsługi wspólnej lokalizacji docelowych i danych
Do aplikacji Szybki Start kadr, aby szybko uzyskać konwersji wyodrębniania danych i prognozowania, należy dodać wbudowane intencje i jednostek. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Dodaj intencje wbudowane 
* Dodaj datetimeV2 wbudowane jednostek i numer
* Szkolenie i publikowanie
* Zapytania LUIS i odbierają prognozowania odpowiedzi

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji kadr z [domeny niestandardowej](luis-quickstart-intents-only.md) Szybki Start, [zaimportować](create-new-app.md#import-new-app) JSON do nowej aplikacji w [LUIS] [ LUIS] witryny sieci Web , z [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) repozytorium Github.

Jeśli chcesz zachować oryginalne aplikacji kadr, klonowanie wersji na [ustawienia](luis-how-to-manage-versions.md#clone-a-version) strony i nadaj mu nazwę `prebuilts`. Jest to dobry sposób na odtwarzania z różnymi funkcjami LUIS bez wpływu na oryginalną wersją klonowanie. 

## <a name="add-prebuilt-intents"></a>Dodaj intencje wbudowane
LUIS zapewnia kilka opcji wbudowane ułatwiające pracę z typowych zamiarach użytkownika.  

1. Upewnij się, że aplikacja jest w **kompilacji** sekcji LUIS. W tej sekcji można zmienić, wybierając **kompilacji** u góry, kliknij prawym przyciskiem myszy pasek menu. 

    [ ![Zrzut ekranu LUIS aplikacji przy użyciu siatki kompilacji na pasku nawigacyjnym góry, prawej](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Wybierz **Dodawanie domeny wbudowane zamiar**. 

    [ ![Zrzut ekranu opcji Strona z przycisku konwersji wbudowane domeny Dodaj wyróżnione](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Wyszukaj `Utilities`. 

    [ ![Zrzut ekranu okna dialogowego Opcje wbudowane narzędzia w polu wyszukiwania](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Wybierz następujące opcje, a następnie wybierz **gotowe**: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>Dodaj jednostki wbudowane
LUIS udostępnia kilka podmiotów wbudowane dla wspólnych wyodrębniania danych. 

1. Wybierz **jednostek** w menu nawigacji po lewej stronie.

    [ ![Zrzut ekranu opcji listy z jednostkami wyróżnione nawigacji po lewej stronie](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Wybierz **Zarządzanie wbudowane jednostek** przycisku.

    [ ![Zrzut ekranu jednostek listy z Zarządzanie wyróżnionym wbudowane jednostek](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Wybierz **numer** i **datetimeV2** wybierz z listy jednostek wbudowane następnie **gotowe**.

    ![Zrzut ekranu przedstawiający numerów wybierz w oknie dialogowym wbudowane jednostek](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Szkolenie i publikowanie aplikacji
1. W górnym rogu LUIS witryny sieci Web, wybierz **pociągu** przycisku. 

    ![Przycisk pociągu](./media/luis-quickstart-intents-only/train-button.png)

    Szkolenie zostało ukończone, gdy zostanie wyświetlony pasek stanu zielonego w górnej części witryny sieci Web potwierdzeniem powodzenia.

    ![Pasek stanu uczonego](./media/luis-quickstart-intents-only/trained.png)

2. W górnej, prawej strony LUIS witryny sieci Web, wybierz **publikowania** przycisk, aby otworzyć stronę publikowania. Domyślnie jest wybrany miejsca produkcji. Wybierz **publikowania** przycisk poprzez wybór miejsca produkcji. Publikowanie została ukończona, gdy zostanie wyświetlony pasek stanu zielonego w górnej części witryny sieci Web potwierdzeniem powodzenia.

    Nie trzeba utworzyć klucz LUIS w portalu Azure, przed opublikowaniem lub przed przetestowaniem adres URL punktu końcowego. Każda aplikacja LUIS ma klucz wolnego początkowy do tworzenia. Umożliwia tworzenie nieograniczone i [kilka trafień punktu końcowego](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Punkt końcowy zapytania o utterance
Na **publikowania** wybierz pozycję **punktu końcowego** łącze umieszczone u dołu strony. Akcja ta Otwiera inne okno przeglądarki z adresem URL punktu końcowego na pasku adresu. Przejdź do końca w adresie URL, a następnie wprowadź `I want to cancel on March 3`. Ostatni parametr ciągu zapytania jest `q`, utterance **zapytania**. 

Wynik przewidzieć zamiar Utilities.Cancel i wyodrębnione daty 3 marca i liczbę 3. 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

Dodając łatwo i szybko wbudowane intencje i jednostek, aplikacja kliencka można dodawać konwersacji zarządzania i Wyodrębnij wspólnych typów danych. 

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat jednostek](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
