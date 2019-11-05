---
title: 'Samouczek: Analiza tonacji — LUIS'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację, która pokazuje, jak uzyskać pozytywne, negatywne i neutralne tonacje z wypowiedzi. Tonację określa się na podstawie całej wypowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 07afd197e514adb0f2fc65c11e9fec552aa05b99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492658"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Samouczek: pobieranie tonacji z wypowiedź

W tym samouczku utworzysz aplikację, która pokazuje, jak określić pozytywne, negatywne i neutralne tonacje z wypowiedzi. Tonację określa się na podstawie całej wypowiedzi.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie nowej aplikacji
> * Dodawanie analizy tonacji jako ustawienia publikowania
> * Szkolenie aplikacji
> * Publikowanie aplikacji
> * Uzyskiwanie tonacji wypowiedzi z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Analiza tonacji to ustawienie publikowania

Następujące wypowiedzi przedstawiają przykłady tonacji:

|Opinia|Wynik|Wypowiedź|
|:--|:--|:--|
|pozytywna|0,91 |John W. Smith zostało świetnie w prezentacji w Paryżu.|
|pozytywna|0,84 |The Seattle engineers did fabulous work on the Parker sales pitch.|

Analiza tonacji to ustawienie publikowania, które ma zastosowanie do każdej wypowiedzi. Nie ma potrzeby odnajdywania słów wskazujących tonację w ramach wypowiedzi i oznaczania ich. 

Ponieważ jest to ustawienie publikowania, nie widać go na stronach intencji i jednostek. Jest ono widoczne w okienku [interactive test](luis-interactive-test.md#view-sentiment-results) (Test interaktywny) lub podczas testowania pod adresem URL punktu końcowego. 


## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Dodawanie wstępnie utworzonej jednostki PersonName 

1. Wybierz opcję **Kompiluj** z menu nawigacji.

1. Wybierz pozycję **Entities** (Jednostki) w menu nawigacji po lewej stronie.

1. Naciśnij przycisk **Add prebuilt entity** (Dodaj wstępnie utworzoną jednostkę).

1. Wybierz następującą jednostkę z listy wstępnie utworzonych jednostek, a następnie wybierz pozycję **Done** (Gotowe):

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Utwórz intencję, aby określić opinie pracowników

Dodanie nowej intencji pozwala na przechwycenie opinii pracowników firmy. 

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu.

1. Wybierz pozycję**Create new intent** (Utwórz nową intencję).

1. Nadaj nowej intencji nazwę `EmployeeFeedback`.

    ![Okno dialogowe tworzenia nowej intencji z nazwą EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Dodaj kilka wypowiedzi wskazujących, że pracownik robi coś dobrze lub że musi poprawić swoje wyniki w określonym obszarze:

    |Wypowiedzi|
    |--|
    |John Smith did a nice job of welcoming back a co-worker from maternity leave|
    |Jill Jones did a great job of comforting a co-worker in their time of grief.|
    |Bob Barnes didn't have all the required invoices for the paperwork.|
    |Todd Thomas turned in the required forms a month late with no signatures|
    |Katherine Kelly didn't make it to the important marketing off-site meeting.|
    |Denise Dillard missed the meeting for June reviews.|
    |Mark Mathews rocked the sales pitch at Harvard|
    |Walter Williams did a great job on the presentation at Stanford|

    Wybierz **Opcje widoku**, wybierz pozycję **Pokaż wartości jednostki** , aby wyświetlić nazwy.

    [![Zrzut ekranu aplikacji LUIS z przykładami wypowiedzi w intencji EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurowanie aplikacji do uwzględnienia analizy tonacji

1. Wybierz pozycję **Manage** (Zarządzaj) w prawym górnym okienku nawigacji, a następnie wybierz pozycję **Publish settings** (Ustawienia publikowania) z menu po lewej stronie.

1. Wybierz pozycję **Użyj analizy tonacji, aby określić, czy wypowiedź użytkownika ma wartość dodatnią, ujemną lub neutralną.** Aby włączyć to ustawienie. 

    ![Włączanie analizy tonacji jako ustawienia publikowania](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikowanie aplikacji w celu umożliwienia wysyłania zapytań z punktu końcowego do trenowanego modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Uzyskiwanie tonacji wypowiedzi z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Przejdź na koniec adresu URL w adresie i wprowadź następujący wypowiedź:

    `Jill Jones work with the media team on the public portal was amazing` 

    Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `EmployeeFeedback` z wyodrębnioną analizą tonacji.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    Wynik analizy sentimentAnalysis jest pozytywny i ma wartość 86%. 

    Wypróbuj inną wypowiedź, usuwając wartość `q` na pasku adresu przeglądarki: `William Jones did a terrible job presenting his ideas.` wynik tonacji wskazuje negatywną tonacji poprzez zwrócenie `0.18597582`niskiego wyniku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* Analiza tonacji jest udostępniana za pośrednictwem funkcji [analizy tekstu](../Text-Analytics/index.yml) usługi Cognitive Service. Ta funkcja jest ograniczona do [obsługiwanych języków](luis-language-support.md##languages-supported) przez analizę tekstu.
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Następne kroki
W tym samouczku dodaliśmy analizę tonacji jako ustawienie publikowania, aby wyodrębnić wartości tonacji z wypowiedzi jako całości.

> [!div class="nextstepaction"] 
> [Przejrzyj wypowiedzi punktu końcowego w aplikacji HR](luis-tutorial-review-endpoint-utterances.md) 

