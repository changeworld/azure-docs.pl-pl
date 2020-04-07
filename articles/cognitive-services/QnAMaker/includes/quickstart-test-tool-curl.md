---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 4bd483e40e3a85a2934e58abdf46d09b17a33ed4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758325"
---
Ten szybki start oparty na cURL przeprowadzi Cię przez uzyskanie odpowiedzi z bazy wiedzy.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsze [**cURL**](https://curl.haxx.se/).
* Musisz mieć
    * [Usługa QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Wyszkolona i opublikowana baza wiedzy z pytaniami i odpowiedziami z poprzedniego [przewodnika Szybki start,](../Quickstarts/add-question-metadata-portal.md)skonfigurowana z metadanymi i czatem Chit.

> [!NOTE]
> Gdy jesteś gotowy do wygenerowania odpowiedzi na pytanie z bazy wiedzy, musisz [wyszkolić](../Quickstarts/create-publish-knowledge-base.md#save-and-train) i [opublikować](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) swoją bazę wiedzy. Po opublikowaniu bazy wiedzy na stronie **Publikowanie** zostaną wyświetlone ustawienia żądania HTTP do generowania odpowiedzi. Karta **cURL** pokazuje ustawienia wymagane do wygenerowania odpowiedzi z narzędzia wiersza polecenia.

## <a name="use-metadata-to-filter-answer"></a>Filtrowanie odpowiedzi za pomocą metadanych

Użyj bazy wiedzy z poprzedniego szybkiego zapytania o odpowiedź na podstawie metadanych.

1. Na stronie **Ustawienia** bazy wiedzy wybierz kartę **CURL,** aby wyświetlić przykładowe polecenie cURL używane do generowania odpowiedzi z bazy wiedzy.
1. Skopiuj polecenie do edytowalnego środowiska (takiego jak plik tekstowy), aby można było edytować polecenie. Edytuj wartość pytania w następujący sposób, `service:qna_maker` tak aby metadane są używane jako filtr dla par QnA.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Pytanie jest tylko jedno `size`słowo, które może zwrócić jedną z dwóch par QnA. Tablica `strictFilters` informuje odpowiedzi, aby `qna_maker` zmniejszyć tylko odpowiedzi.

1. Odpowiedź zawiera tylko odpowiedź, która spełnia kryteria filtru. Następująca odpowiedź cURL została sformatowana pod kątem czytelności:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Jeśli istnieje zestaw pytań i odpowiedzi, który nie spełnia wyszukiwanego hasła, ale spełnia filtr, nie zostanie zwrócony. Zamiast tego zwracana `No good match found in KB.` jest ogólna odpowiedź.

## <a name="use-debug-query-property"></a>Użyj właściwości kwerendy debugowania

Informacje debugowania pomaga zrozumieć, jak zwrócona odpowiedź została określona. Chociaż jest to pomocne, nie jest to konieczne. Aby wygenerować odpowiedź z `debug` informacjami debugowania, dodaj właściwość:

```json
Debug: {Enable:true}
```

1. Edytuj polecenie cURL, aby dołączyć właściwość debugowania, aby wyświetlić więcej informacji.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```

1. Odpowiedź zawiera istotne informacje na temat odpowiedzi. W następujących danych wyjściowych JSON niektóre szczegóły debugowania zostały zastąpione wielokropkiem dla zwięzłości.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Korzystanie z bazy wiedzy testowej

Jeśli chcesz uzyskać odpowiedź z bazy wiedzy testowej, użyj właściwości `isTest` body.

Właściwość jest wartością logiczną.

```json
isTest:true
```

Polecenie cURL wygląda następująco:

```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

Odpowiedź JSON używa tego samego schematu, co opublikowane zapytanie bazy wiedzy.

> [!NOTE]
> Jeśli test i opublikowane bazy wiedzy są dokładnie takie same, nadal mogą występować pewne niewielkie różnice, ponieważ indeks testu jest współużytkowany przez wszystkie bazy wiedzy w zasobie.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Użyj cURL do kwerendy dla odpowiedzi Chit-chat

1. W terminalu z obsługą cURL użyj instrukcji zakończenia konwersacji `Thank you` bota od użytkownika, takiej jak pytanie. Nie ma żadnych innych właściwości do skonfigurowania.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```

1. Uruchom polecenie cURL i odbierz odpowiedź JSON, w tym wynik i odpowiedź.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Ponieważ pytanie `Thank you` było dokładnie zgodne z pytaniem w konwersacji, usługa QnA Maker jest całkowicie pewna odpowiedzi (ocena 100%). Program QnA Maker zwrócił również wszystkie powiązane pytania, a także właściwość metadanych zawierającą informacje o tagu metadanych chit-chat.

## <a name="use-curl-with-threshold-and-default-answer"></a>Używanie cURL z odpowiedzią progową i domyślną

Możesz poprosić o minimalny próg odpowiedzi. Jeśli próg nie zostanie osiągnięty, zwracana jest odpowiedź domyślna.

1. Użyj następującego polecenia cURL, zastępując własną nazwą zasobu, identyfikatorem bazy wiedzy i `size` kluczem punktu końcowego, aby poprosić o odpowiedź z progiem 80% lub lepszym. Baza wiedzy nie powinna znaleźć tej odpowiedzi, ponieważ wynik pytania wynosi 71%, a zamiast tego zwraca domyślną odpowiedź podana podczas tworzenia bazy wiedzy.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Uruchom polecenie cURL i odbierz odpowiedź JSON, w tym wynik i odpowiedź.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker zwrócił `0`wynik , co oznacza brak zaufania. Zwrócono również odpowiedź domyślną.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Ponieważ pytanie `Thank you` było dokładnie zgodne z pytaniem w konwersacji, usługa QnA Maker jest całkowicie pewna odpowiedzi (ocena 100%). Program QnA Maker zwrócił również wszystkie powiązane pytania, a także właściwość metadanych zawierającą informacje o tagu metadanych chit-chat.

## <a name="use-curl-with-threshold-and-default-answer"></a>Używanie cURL z odpowiedzią progową i domyślną

Możesz poprosić o minimalny próg odpowiedzi. Jeśli próg nie zostanie osiągnięty, zwracana jest odpowiedź domyślna.

1. Dodaj `threshold` właściwość, aby poprosić o odpowiedź `size` z progiem 80% lub lepszym. Baza wiedzy nie powinna znaleźć tej odpowiedzi, ponieważ wynik pytania wynosi 71%. Wynik zwraca domyślną odpowiedź podana podczas tworzenia bazy wiedzy.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Uruchom polecenie cURL i odbierz odpowiedź JSON.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker zwrócił `0`wynik , co oznacza brak zaufania. Zwrócono również odpowiedź domyślną.

1. Zmień wartość progową na 60% i ponownie zażądaj kwerendy:

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```

    Zwrócony JSON znalazł odpowiedź.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```
