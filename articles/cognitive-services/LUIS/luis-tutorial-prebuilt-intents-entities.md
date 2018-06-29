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
ms.openlocfilehash: 37d67bef7712012a95543041744706b240b16e2d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085501"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Samouczek: 2. Dodawanie wstępnie utworzonych intencji i jednostek
Do aplikacji Szybki Start kadr, aby szybko uzyskać konwersji wyodrębniania danych i prognozowania, należy dodać wbudowane intencje i jednostek. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Dodaj intencje wbudowane 
* Dodaj datetimeV2 wbudowane jednostek i numer
* Szkolenie i publikowanie
* Zapytania LUIS i odbierają prognozowania odpowiedzi

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz [kadr](luis-quickstart-intents-only.md) aplikacji z poprzednich samouczka [zaimportować](create-new-app.md#import-new-app) JSON do nowej aplikacji w [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web, z [LUIS próbek ](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) Repozytorium Github.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `prebuilts`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="add-prebuilt-intents"></a>Dodaj intencje wbudowane
LUIS zapewnia kilka opcji wbudowane ułatwiające pracę z typowych zamiarach użytkownika.  

1. Upewnij się, że aplikacja jest w **kompilacji** sekcji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

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
1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz). 

    ![Przycisk pociągu](./media/luis-quickstart-intents-only/train-button.png)

    Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Pasek stanu uczonego](./media/luis-quickstart-intents-only/trained.png)

2. W górnej, prawej strony LUIS witryny sieci Web, wybierz **publikowania** przycisk, aby otworzyć stronę publikowania. Domyślnie jest wybrany miejsca produkcji. Wybierz **publikowania** przycisk poprzez wybór miejsca produkcji. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    Nie trzeba utworzyć klucz LUIS w portalu Azure, przed opublikowaniem lub przed przetestowaniem adres URL punktu końcowego. Każda aplikacja LUIS ma klucz wolnego początkowy do tworzenia. Umożliwia tworzenie nieograniczone i [kilka trafień punktu końcowego](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Punkt końcowy zapytania o utterance
Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. Przejdź na koniec tego adresu URL i wprowadź ciąg `I want to cancel on March 3`. Ostatni parametr ciągu zapytania jest `q`, utterance **zapytania**. 

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodaj jednostki wyrażenia regularnego do aplikacji](luis-quickstart-intents-regex-entity.md)

