---
title: 'Samouczek: Przeglądanie wypowiedzi punktów końcowych — usługa LUIS'
titleSuffix: Azure Cognitive Services
description: W tym samouczku ulepsz prognozowanie aplikacji, weryfikując lub korygując wypowiedzi odebrane za pośrednictwem punktu końcowego usługi LUIS HTTP, o którym nie ma pewności. Niektóre wypowiedzi mogą wymagać weryfikacji pod kątem intencji, a inne — pod kątem jednostki.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 06f51ca83449b39861e7565cc9accc29efbece3f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843977"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Samouczek: Napraw niewiążące prognozy, przeglądając wypowiedzi punktów końcowych
W tym samouczku ulepsz prognozowanie aplikacji, weryfikując lub korygując wypowiedzi odebrane za pośrednictwem punktu końcowego HTTPS usługi LUIS, o którym usługa LUIS nie ma pewności. Należy przejrzeć wypowiedzi punktu końcowego jako regularną część zaplanowanej konserwacji usługi LUIS.

Ten proces przeglądu umożliwia usługi LUIS, aby dowiedzieć się domeny aplikacji. Usługa LUIS wybiera wypowiedzi, które pojawiają się na liście recenzji. Ta lista ma następujące cechy:

* Jest specyficzna dla aplikacji.
* Ma za zadanie zwiększyć dokładność przewidywania aplikacji.
* Powinna być regularnie przeglądana.

Przeglądając wypowiedzi punktu końcowego, weryfikujesz lub poprawiasz przewidzianą intencję wypowiedzi.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Przeglądanie wypowiedzi punktu końcowego
> * Trenuj i publikuj aplikację
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

Aby zaimportować aplikację, należy wykonać następujące czynności.

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true).

1. W [portalu usługi LUIS w wersji zapoznawczej](https://preview.luis.ai)zaimportuj plik .json do nowej aplikacji.

1. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `review`.

    > [!TIP]
    > Klonowanie do nowej wersji jest najlepszym rozwiązaniem przed zmodyfikowaniem aplikacji. Po zakończeniu wersji wyeksportuj wersję (jako plik .json lub .lu) i sprawdź plik do systemu kontroli źródła.


1. Aby wyszkolić aplikację, wybierz opcję **Pociąg**.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikowanie aplikacji w celu uzyskania do niej dostępu z punktu końcowego HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Dodawanie wypowiedzi w punkcie końcowym

W tej aplikacji masz intencje i jednostki, ale nie masz żadnego użycia punktu końcowego. To użycie punktu końcowego jest wymagane do poprawy aplikacji z przeglądu wypowiedź punktu końcowego.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Użyj punktu końcowego, aby dodać następujące wypowiedzi.

    |Wypowiedź punktu końcowego|Aligned intent (Intencja wyrównana)|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Jest tylko jedna pula wypowiedzi do przejrzenia, niezależnie od aktywnie edytowanej wersji i od wersji aplikacji opublikowanej w punkcie końcowym.

## <a name="review-endpoint-utterances"></a>Przeglądanie wypowiedzi punktu końcowego

Przejrzyj wypowiedzi punktu końcowego dla poprawnie wyrównanej intencji. Chociaż istnieje pojedyncza pula wypowiedzi do przeglądu we wszystkich wersjach, proces poprawnego wyrównania intencji dodaje wypowiedź przykładu tylko do bieżącego _modelu aktywnego._

1. W sekcji **Kompilacja** portalu wybierz **opcję Przejrzyj wypowiedzi punktów końcowych** z lewej nawigacji. Lista jest przefiltrowana pod kątem intencji **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przycisku Review endpoint utterances (Przejrzyj wypowiedzi punktu końcowego) w lewym obszarze nawigacji](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Ta wypowiedź , `I'm looking for a job with Natural Language Processing`nie jest w poprawnym zamiarze.

1.  Aby wyrównać tę wypowiedź, w wierszu wypowiedź `GetJobInformation`wybierz poprawną **opcję Wyrównany zamiar** . Dodaj zmienioną wypowiedź do aplikacji, wybierając znacznik wyboru.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przycisku Review endpoint utterances (Przejrzyj wypowiedzi punktu końcowego) w lewym obszarze nawigacji](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Przejrzyj pozostałe wypowiedzi w tym zamiarze, korygując wyrównane intencje w razie potrzeby. Użyj tabeli wypowiedź początkowa w tym samouczku, aby wyświetlić wyrównane intencji.

    Lista **wypowiedzi punktu końcowego przeglądu** nie powinny już mieć skorygowane wypowiedzi. Jeśli pojawi się więcej wypowiedzi, kontynuuj pracę za pośrednictwem listy, poprawiając wyrównane intencje, dopóki lista nie będzie pusta.

    Wszelkie korekty etykietowania jednostki odbywa się po intencji jest wyrównany, na stronie Szczegóły intencji.

1. Przeszkol i ponownie opublikuj aplikację.

## <a name="get-intent-prediction-from-endpoint"></a>Pobierz przewidywanie intencji z punktu końcowego

Aby sprawdzić poprawnie wyrównane wypowiedzi przykład poprawiła przewidywania aplikacji, spróbuj wypowiedź blisko skorygowanej wypowiedź.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Przejdź na koniec tego adresu URL i wprowadź ciąg `Are there any natural language processing jobs in my department right now?`. Ostatnim parametrem querystring jest `q` **kwerenda**wypowiedź .

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   Teraz, gdy nie pewny wypowiedzi są poprawnie wyrównane, prawidłowy zamiar został przewidziany z **wysokim wynikiem**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Czy zamiast przeglądania można dodać więcej wypowiedzi?
Możesz się zastanawiać, dlaczego nie dodać więcej przykładowych wypowiedzi. Jaki jest cel przeglądania wypowiedzi punktu końcowego? W rzeczywistej aplikacji usługi LUIS wypowiedzi punktu końcowego pochodzą od użytkowników, a ich dobór i kolejność słów różnią się od użytych. Gdyby użyto tych samych słów w tej samej kolejności, pierwotne przewidywanie miałoby wyższą wartość procentową.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Dlaczego intencja o najwyższej ocenie znajduje się na liście wypowiedzi?
Niektóre wypowiedzi punktu końcowego będą miały wysoki współczynnik przewidywania na liście do przeglądu. Mimo to należy przejrzeć i zweryfikować te wypowiedzi. Znajdują się one na liście, ponieważ różnica między intencją o najwyższej ocenie i intencją drugą w kolejności jest zbyt mała. Różnica między dwiema najważniejszymi intencjami powinna wynosić około 15%.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przejrzano wypowiedzi przesłane w punkcie końcowym, które usługa LUIS uznała za niepewne. Zweryfikowanie i przeniesienie tych wypowiedzi do poprawnych intencji jako przykładowych wypowiedzi spowoduje, że dokładność przewidywania usługi LUIS zwiększy się.

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać wzorców](luis-tutorial-pattern.md)
