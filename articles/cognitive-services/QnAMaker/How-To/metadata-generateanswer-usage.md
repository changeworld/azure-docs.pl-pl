---
title: Metadane z GenerateAnswer interfejsu API — usługa QnA Maker
titleSuffix: Azure Cognitive Services
description: Narzędzie QnA Maker umożliwia dodanie metadanych, w postaci par klucz wartość do zestawów z pytań i odpowiedzi. Informacja ta może służyć do filtrowania wyników na zapytania użytkowników i do przechowywania dodatkowych informacji, które mogą być używane w kolejnych konwersacje.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.openlocfilehash: c18ededc428b215720f8a6a6857a2eabd93bff8b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683612"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>Uzyskaj odpowiedzi wiedzy, za pomocą interfejsu API GenerateAnswer i metadane

Aby uzyskać dostęp do przewidywanych odpowiedź na pytanie użytkownika, należy użyć interfejsu API GenerateAnswer. Podczas publikowania wiedzy tych informacji, aby używać tego interfejsu API są wyświetlane na stronie publikowania. Można również skonfigurować interfejs API, aby filtrować odpowiedzi na podstawie tagów metadanych i przetestować wiedzy z punktu końcowego za pomocą parametru ciągu zapytania testu.

Narzędzie QnA Maker umożliwia dodanie metadanych, w postaci par kluczy i wartości, do Twojego zestawów pytań i odpowiedzi. Informacja ta może służyć do filtrowania wyników na zapytania użytkowników i do przechowywania dodatkowych informacji, które mogą być używane w kolejnych konwersacje. Aby uzyskać więcej informacji, zobacz [wiedzy](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>Przechowywanie pytań i odpowiedzi z jednostką pytań i odpowiedzi

Najpierw ważne jest zrozumienie, jak usługa QnA Maker przechowuje dane pytań i odpowiedzi. Na poniższej ilustracji przedstawiono jednostki pytań i odpowiedzi:

![Jednostki pytań i odpowiedzi](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Każda jednostka pytań i odpowiedzi ma identyfikator unikatowy i trwałe. Identyfikator może służyć do wykonywania aktualizacji danego podmiotu pytań i odpowiedzi.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Uzyskiwać prognozy odpowiedzi za pomocą interfejsu API GenerateAnswer

Zapytanie bazy wiedzy przy użyciu pytanie użytkownika, aby uzyskać najlepsze dopasowanie z zestawów pytań i odpowiedzi za pomocą interfejsu API GenerateAnswer w aplikacji lub Botów.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikować można pobrać punktu końcowego GenerateAnswer 

Po opublikowaniu wiedzy, albo z [portalu narzędzia QnA Maker](https://www.qnamaker.ai), lub za pomocą [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), możesz uzyskać szczegółowe informacje o punkcie końcowym usługi GenerateAnswer.

Aby uzyskać informacje dotyczące punktu końcowego:
1. Zaloguj się do witryny [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. W **moich baz wiedzy**, kliknij pozycję **Wyświetl kod** dla Twojej bazy wiedzy.
    ![Moje bazy wiedzy](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Pobranie szczegółowych informacji o GenerateAnswer punktu końcowego.

    ![szczegóły punktu końcowego](../media/qnamaker-how-to-metadata-usage/view-code.png)

Możesz też pobrać Twoje szczegóły punktu końcowego z **ustawienia** kartę bazy wiedzy.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfiguracja żądania GenerateAnswer

Możesz wywołać GenerateAnswer za pomocą żądania HTTP POST. Przykładowy kod przedstawia sposób wywołania GenerateAnswer, zobacz [przewodników Szybki Start](../quickstarts/csharp.md).

**Adres URL żądania** ma następujący format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Właściwości żądania HTTP|Name (Nazwa)|Typ|Przeznaczenie|
|--|--|--|--|
|Parametr trasy adresu URL|Identyfikator bazy wiedzy|string|Identyfikator GUID bazy wiedzy.|
|Parametr trasy adresu URL|Host punktu końcowego interfejsu QnAMaker|string|Nazwa hosta punktu końcowego, wdrożonych w ramach subskrypcji platformy Azure. Jest on dostępny na stronie ustawień, po opublikowaniu w bazie wiedzy knowledge base. |
|Nagłówek|Content-Type|string|Typ nośnika treści wysłanej do interfejsu API. Wartość domyślna to: "|
|Nagłówek|Autoryzacja|string|Klucz punktu końcowego (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Treść wpisu|Obiekt JSON|JSON|Pytanie przy użyciu ustawień|


Treść kodu JSON ma kilka ustawień:

|Właściwość treść JSON|Wymagane|Typ|Przeznaczenie|
|--|--|--|--|
|`question`|wymagane|string|Pytanie użytkownika mają być wysyłane do bazy wiedzy.|
|`top`|opcjonalne|liczba całkowita|Liczba wyników w rankingu do uwzględnienia w danych wyjściowych. Wartość domyślna to 1.|
|`userId`|opcjonalne|string|Unikatowy identyfikator, aby zidentyfikować użytkownika. Ten identyfikator będą rejestrowane w dziennikach rozmowy.|
|`isTest`|opcjonalne|wartość logiczna|Jeśli ustawionej na wartość true, zwraca wyniki z `testkb` indeksu wyszukiwania zamiast opublikowanych indeksu.|
|`strictFilters`|opcjonalne|string|Jeśli zostanie określony, informuje narzędzie QnA Maker, aby zwrócić tylko odpowiedzi, które mają określonych metadanych. Użyj `none` oznacza odpowiedzi powinien mieć żadnych filtrów metadanych. |

Przykładowy kod JSON wygląda następująco:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Właściwości odpowiedzi GenerateAnswer

Odpowiedź oznaczająca Powodzenie zwraca stan 200 i odpowiedź w formacie JSON. 

|Właściwość odpowiedzi (posortowane według wyników)|Przeznaczenie|
|--|--|
|wynik|Wynik klasyfikacji od 0 do 100.|
|Identyfikator|Unikatowy identyfikator przypisany do odpowiedzi.|
|Pytania|Pytania, dostarczone przez użytkownika.|
|Odpowiedź|Odpowiedź na pytanie.|
|source|Nazwa źródła, z której wyjęto lub zapisany w bazie wiedzy knowledge base odpowiedź.|
|metadane|Metadane skojarzone z odpowiedzią.|
|metadata.name|Nazwa metadanych. (string, maksymalna długość: 100, które są wymagane)|
|METADATA.Value: Wartość metadanych. (string, maksymalna długość: 100, które są wymagane)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>Przy użyciu metadanych pozwala na filtrowanie odpowiedzi według tagów niestandardowych metadanych

Dodanie metadanych umożliwia filtrowanie odpowiedzi przez te tagi metadanych. Należy wziąć pod uwagę poniższe często zadawane pytania dotyczące danych. Dodaj metadane do bazy wiedzy, klikając ikonę metadanych.

![Dodawanie metadanych](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrowanie wyników za pomocą strictFilters tagów metadanych

Należy wziąć pod uwagę pytanie użytkownika "Podczas odbywa się to hotelu Zamknij?" których celem jest implikowane dla restauracji "Paradise."

Ponieważ wyniki są wymagane tylko w przypadku restauracji "Paradise", można ustawić filtr w wywołaniu GenerateAnswer na metadanych "Restauracji Name", w następujący sposób.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

< nazwa = "Zachowaj kontekstu" ></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Użyj wyników pytań i odpowiedzi, aby zachować kontekst konwersacji

Odpowiedź na GenerateAnswer zawiera odpowiednie informacje o metadanych zestawu zgodnych pytania/odpowiedzi. Te informacje można w aplikacji klienckiej do przechowywania kontekście poprzedniej rozmowy dla danego użyj nowszej konwersacji. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Strona publikowania zawiera również informacje potrzebne do generowania odpowiedzi za pomocą [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) i [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](./create-knowledge-base.md)
