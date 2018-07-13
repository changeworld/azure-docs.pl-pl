---
title: Dodawanie wstępnie utworzonych intencji i jednostek w celu wyodrębniania wspólnych danych w usłudze Language Understanding — Azure | Microsoft Docs
description: Dowiedz się, jak używać wstępnie utworzonych intencji i jednostek do wyodrębniania różnych typów danych jednostki.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: 075cb270641ca995eec95aa6aa8986c90077148a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112199"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Samouczek: 2. Dodawanie wstępnie utworzonych intencji i jednostek
Dodaj wstępnie utworzone intencje i jednostki do aplikacji samouczka Human Resources, aby szybko zyskać funkcje przewidywania intencji i wyodrębniania danych. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Dodawanie wstępnie utworzonych intencji 
* Dodawanie wstępnie utworzonych jednostek datetimeV2 i liczb
* Uczenie i publikowanie
* Wysyłanie zapytań do usługi LUIS i uzyskiwanie odpowiedzi w formie przewidywania

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji [Human Resources](luis-quickstart-intents-only.md) z poprzedniego samouczka, [zaimportuj](create-new-app.md#import-new-app) obiekt JSON do nowej aplikacji w witrynie usługi [LUIS](luis-reference-regions.md#luis-website) z repozytorium Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json).

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `prebuilts`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="add-prebuilt-intents"></a>Dodawanie wstępnie utworzonych intencji
Usługa LUIS zapewnia kilka wstępnie utworzonych intencji, których celem jest ułatwienie realizacji typowych intencji użytkownika.  

1. Upewnij się, że aplikacja znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Wybierz opcję **Add prebuilt domain intent** (Dodaj wstępnie skompilowaną intencję domeny). 

    [ ![Zrzut ekranu przedstawiający stronę Intents (Intencje) z wyróżnionym przyciskiem Add prebuilt domain intent (Dodaj wstępnie utworzoną skompilowaną domeny)](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Wyszukaj `Utilities`. 

    [ ![Zrzut ekranu przedstawiający okno dialogowe wstępnie utworzonych intencji z wyrazem Utilities (Narzędzia) w polu wyszukiwania](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Wybierz następujące intencje i wybierz opcję **Done** (Gotowe): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Dodawanie wstępnie utworzonych jednostek
Usługa LUIS zawiera kilka wstępnie utworzonych jednostek na potrzeby typowych działań związanych z wyodrębnianiem danych. 

1. Wybierz pozycję **Entities** (Jednostki) w menu nawigacji po lewej stronie.

    [ ![Zrzut ekranu przedstawiający listę Intents (Intencje) z przyciskiem Entities (Jednostki) wyróżnionym na lewym pasku nawigacyjnym](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Wybierz przycisk **Manage prebuilt entities** (Zarządzaj wstępnie skompilowanymi jednostkami).

    [![Zrzut ekranu z listą Entities (Jednostki) i wyróżnioną pozycją Manage prebuilt entities (Zarządzaj wstępnie skompilowanymi jednostkami)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Wybierz pozycję **number** (liczba) oraz **datetimeV2** z listy wstępnie skompilowanych jednostek, a następnie wybierz pozycję **Done (Gotowe)**.

    ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji
1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz). 

    ![Przycisk Train (Ucz)](./media/luis-quickstart-intents-only/train-button.png)

    Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Pasek stanu Trained (Nauczono)](./media/luis-quickstart-intents-only/trained.png)

2. W prawym górnym rogu witryny internetowej usługi LUIS wybierz przycisk **Publish** (Publikuj), aby otworzyć stronę Publish (Publikowanie). 

3. Domyślnie wybrane jest miejsce produkcyjne. Wybierz przycisk **Publish** (Publikuj) obok wybranego miejsca produkcyjnego. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    Nie musisz tworzyć klucza punktu końcowego usługi LUIS w witrynie Azure Portal przed opublikowaniem lub przetestowaniem adresu URL punktu końcowego. Każda aplikacja usługi LUIS ma bezpłatny klucz początkowy na potrzeby tworzenia. Zapewnia on nielimitowane funkcje tworzenia oraz [kilka trafień punktów końcowych](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą wypowiedzi
Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. Przejdź na koniec tego adresu URL i wprowadź ciąg `I want to cancel on March 3`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

Wynik przewidział intencję Utilities.Cancel i wyodrębnił dane dla 3 marca i liczby 3. 

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

Istnieją dwie wartości dla 3 marca, ponieważ wypowiedź nie określała, czy 3 marca jest datą w przeszłości, czy przyszłości. Aplikacja wywoływania LUIS musi wykonać założenie lub poprosić o wyjaśnienie, jeśli jest ono potrzebne. 

Dodając wstępnie skompilowane intencje i jednostki w prosty i szybki sposób, aplikacja kliencka może dodać funkcje zarządzania rozmowami i wyodrębniania wspólnych typów danych. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz menu z trzema kropkami (...) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie jednostki wyrażenia regularnego do aplikacji](luis-quickstart-intents-regex-entity.md)

