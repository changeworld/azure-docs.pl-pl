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
ms.openlocfilehash: 46947579ea72e2199af116442472eec330b38009
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112279"
---
W tym samouczku Szybki start opisano sposób uzyskiwania odpowiedzi z bazy wiedzy przy użyciu narzędzia Postman.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja narzędzia [**Postman**](https://www.getpostman.com/).
* Musisz mieć
    * [Usługa QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Wyszkolona i opublikowana [baza wiedzy z pytaniami i odpowiedziami utworzonymi](../Quickstarts/add-question-metadata-portal.md) z przewodnika Szybki start jest skonfigurowana z metadanymi i czatem Chit.

> [!NOTE]
> Gdy jesteś gotowy do wygenerowania odpowiedzi na pytanie z bazy wiedzy, musisz [wyszkolić](../Quickstarts/create-publish-knowledge-base.md#save-and-train) i [opublikować](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) swoją bazę wiedzy. Po opublikowaniu bazy wiedzy na stronie **Publikowanie** zostaną wyświetlone ustawienia żądania HTTP do generowania odpowiedzi. Karta **Listonosz** pokazuje ustawienia wymagane do wygenerowania odpowiedzi.

## <a name="set-up-postman-for-requests"></a>Konfigurowanie listonosza dla żądań

Ten przewodnik Szybki start używa tych samych ustawień dla żądania **POSTMAN POST,** a następnie konfiguruje się na żądanie JSON, które wysyła do usługi na podstawie tego, czego próbujesz zbadać.

Ta procedura służy do konfigurowania listonosza, a następnie do odczytywania każdej kolejnej sekcji w celu skonfigurowania JSON obiektu POST.

1. Na stronie **Ustawienia** bazy wiedzy wybierz kartę **Listonosz,** aby wyświetlić konfigurację używaną do generowania odpowiedzi z bazy wiedzy. Skopiuj następujące informacje do użycia w postmanie.

    |Nazwa|Ustawienie|Cel i wartość|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Jest to metoda HTTP i trasa dla adresu URL.|
    |`Host`|`https://diberry-qna-s0-s.azurewebsites.net/qnamaker`|Jest to host adresu URL. Połącz wartości hosta i postu, aby uzyskać pełny adres URL generowaniaAnswer.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Wartość nagłówka do autoryzowania żądania na platformie Azure. |
    |`Content-type`|`application/json`|Wartość nagłówka zawartości.|
    ||`{"question":"<Your question>"}`|Treść żądania POST jako obiektu JSON. Ta wartość zmieni się w każdej poniższej sekcji w zależności od tego, co ma zrobić kwerenda.|

1. Otwórz listonosz i utwórz nowe podstawowe żądanie **POST** z opublikowanymi ustawieniami bazy wiedzy. W poniższych sekcjach zmień JSON treści POST, aby zmienić kwerendę na bazę wiedzy.

## <a name="use-metadata-to-filter-answer"></a>Filtrowanie odpowiedzi za pomocą metadanych

W poprzednim przewodniku Szybki start metadane zostały dodane do dwóch zestawów QnA w celu rozróżnienia między dwoma różnymi pytaniami. Dodaj metadane do kwerendy, aby ograniczyć filtr tylko do odpowiedniego zestawu QnA.

1. W polu Postman zmień tylko kwerendę `strictFilters` JSON, dodając właściwość z parą nazwa/wartość `service:qna_maker`pliku . Ciało JSON powinno być:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    Pytanie jest tylko jednym `size`słowem, które może zwrócić jeden z dwóch pytań i odpowiedzi. Tablica `strictFilters` informuje odpowiedzi, aby `qna_maker` zmniejszyć tylko odpowiedzi.

1. Odpowiedź zawiera tylko odpowiedź, która spełnia kryteria filtru.

    Następująca odpowiedź została sformatowana pod kątem czytelności:

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

1. W postman, zmień tylko obiekt JSON przez dodanie `debug` właściwości. JSON powinien być:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Odpowiedź zawiera istotne informacje na temat odpowiedzi. W następujących danych wyjściowych JSON niektóre szczegóły debugowania zostały zastąpione wielokropkiem.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
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

W postman, zmień tylko obiekt JSON przez dodanie `isTest` właściwości. JSON powinien być:

```json
{
    'question':'size',
    'isTest': true
}
```

Odpowiedź JSON używa tego samego schematu, co opublikowane zapytanie bazy wiedzy.

> [!NOTE]
> Jeśli test i opublikowane bazy wiedzy są dokładnie takie same, nadal mogą występować pewne niewielkie różnice, ponieważ indeks testu jest współużytkowany przez wszystkie bazy wiedzy w zasobie.

## <a name="query-for-a-chit-chat-answer"></a>Zapytanie o odpowiedź chit-chat

1. W postman, zmienić tylko obiekt JSON do instrukcji zakończenia konwersacji od użytkownika. JSON powinien być:

    ```json
    {
        'question':'thank you'
    }
    ```

1. Odpowiedź zawiera wynik i odpowiedź.

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

## <a name="use-threshold-and-default-answer"></a>Użyj progu i odpowiedzi domyślnej

Możesz poprosić o minimalny próg odpowiedzi. Jeśli próg nie zostanie osiągnięty, zwracana jest odpowiedź domyślna.

1. W postman, zmienić tylko obiekt JSON do instrukcji zakończenia konwersacji od użytkownika. JSON powinien być:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    Baza wiedzy nie powinna znaleźć tej odpowiedzi, ponieważ wynik pytania wynosi 71%, a zamiast tego zwraca domyślną odpowiedź podana podczas tworzenia bazy wiedzy.

    Zwrócona odpowiedź JSON, w tym wynik i odpowiedź to:

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

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
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