---
title: Metadane z GenerateAnswer interfejsu API — usługa QnA Maker
titleSuffix: Azure Cognitive Services
description: Narzędzie QnA Maker umożliwia dodanie metadanych, w postaci par klucz wartość do zestawów z pytań i odpowiedzi. Możesz filtrować wyniki na zapytania użytkowników i przechowywać dodatkowe informacje, które mogą być używane w kolejnych konwersacji.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/30/2019
ms.author: tulasim
ms.openlocfilehash: b18d47b4b09c6fa9c4d5f0ef87d7ebe73f151c60
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693229"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Uzyskaj odpowiedzi za pomocą interfejsu API GenerateAnswer i metadane

Aby uzyskać dostęp do przewidywanych odpowiedź na pytanie użytkownika, należy użyć interfejsu API GenerateAnswer. Podczas publikowania bazy wiedzy, znajdują się informacje o tym, jak używać tego interfejsu API na **Publikuj** strony. Można również skonfigurować interfejs API, aby filtrować odpowiedzi na podstawie tagów metadanych i przetestować wiedzy z punktu końcowego za pomocą parametru ciągu zapytania testu.

Narzędzie QnA Maker umożliwia dodanie metadanych, w postaci par kluczy i wartości, aby rodzaje pytań i odpowiedzi. Następnie można użyć tych informacji, aby filtrować wyniki na zapytania użytkowników i do przechowywania dodatkowych informacji, które mogą być używane w kolejnych konwersacje. Aby uzyskać więcej informacji, zobacz [wiedzy](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Store pytań i odpowiedzi z jednostką pytań i odpowiedzi

Należy zrozumieć, jak usługa QnA Maker przechowuje dane pytań i odpowiedzi. Na poniższej ilustracji przedstawiono jednostki pytań i odpowiedzi:

![Ilustracja jednostki pytań i odpowiedzi](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Każda jednostka pytań i odpowiedzi ma identyfikator unikatowy i trwałe. Identyfikator służy do wprowadzania aktualizacji danego podmiotu pytań i odpowiedzi.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Uzyskiwać prognozy odpowiedzi za pomocą interfejsu API GenerateAnswer

Zapytanie bazy wiedzy do pytania użytkownika za pomocą interfejsu API GenerateAnswer w aplikacji lub bot, aby uzyskać najlepsze dopasowanie z pytaniami i odpowiedziami zestawów.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikować można pobrać punktu końcowego GenerateAnswer 

Po opublikowaniu w bazie wiedzy, albo z [portalu narzędzia QnA Maker](https://www.qnamaker.ai), lub za pomocą [API](https://go.microsoft.com/fwlink/?linkid=2092179), możesz uzyskać szczegółowe informacje o punkcie końcowym usługi GenerateAnswer.

Aby uzyskać informacje dotyczące punktu końcowego:
1. Zaloguj się do witryny [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. W **moich baz wiedzy**, wybierz opcję **Wyświetl kod** dla Twojej bazy wiedzy.
    ![Zrzut ekranu Moje bazy wiedzy](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Pobranie szczegółowych informacji o GenerateAnswer punktu końcowego.

    ![Zrzut ekranu przedstawiający szczegóły punktu końcowego](../media/qnamaker-how-to-metadata-usage/view-code.png)

Możesz też pobrać Twoje szczegóły punktu końcowego z **ustawienia** kartę bazy wiedzy.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfiguracja żądania GenerateAnswer

Możesz wywołać GenerateAnswer za pomocą żądania HTTP POST. Przykładowy kod przedstawia sposób wywołania GenerateAnswer, zobacz [przewodników Szybki Start](../quickstarts/csharp.md).

**Adres URL żądania** ma następujący format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Właściwości żądania HTTP|Name (Nazwa)|Type|Przeznaczenie|
|--|--|--|--|
|Parametr trasy adresu URL|Identyfikator bazy wiedzy|string|Identyfikator GUID bazy wiedzy.|
|Parametr trasy adresu URL|Host punktu końcowego interfejsu QnAMaker|string|Nazwa hosta punktu końcowego, wdrożonych w ramach subskrypcji platformy Azure. Ta opcja jest dostępna na **ustawienia** strony po opublikowaniu w bazie wiedzy knowledge base. |
|nagłówek|Content-Type|string|Typ nośnika treści wysłanej do interfejsu API. Wartość domyślna to: "|
|nagłówek|Autoryzacja|string|Klucz punktu końcowego (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Treść wpisu|Obiekt JSON|JSON|Pytanie przy użyciu ustawień.|


Treść kodu JSON ma kilka ustawień:

|Właściwość treść JSON|Wymagane|Type|Przeznaczenie|
|--|--|--|--|
|`question`|Wymagane|string|Pytanie użytkownika mają być wysyłane do bazy wiedzy.|
|`top`|Opcjonalne|liczba całkowita|Liczba wyników w rankingu do uwzględnienia w danych wyjściowych. Wartość domyślna to 1.|
|`userId`|Opcjonalne|string|Unikatowy identyfikator, aby zidentyfikować użytkownika. Ten identyfikator będą rejestrowane w dziennikach rozmowy.|
|`scoreThreshold`|Opcjonalne|liczba całkowita|Zostaną zwrócone tylko odpowiedzi z oceną zaufania powyżej wartości progowej. Wartość domyślna to 0.|
|`isTest`|Opcjonalne|Boolean|Jeśli ustawionej na wartość true, zwraca wyniki z `testkb` indeksu wyszukiwania zamiast opublikowanych indeksu.|
|`strictFilters`|Opcjonalne|string|Jeśli zostanie określony, informuje narzędzie QnA Maker, aby zwrócić tylko odpowiedzi, które mają określonych metadanych. Użyj `none` oznacza odpowiedzi powinien mieć żadnych filtrów metadanych. |
|`RankerType`|Opcjonalne|string|Jeśli zostanie określony jako `QuestionOnly`, informuje narzędzie QnA Maker, aby wyszukać tylko pytania. Jeśli nie zostanie określony, narzędzie QnA Maker wyszukuje pytań i odpowiedzi.

Przykładowy kod JSON wygląda następująco:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
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
|metadata.value|Wartość metadanych. (string, maksymalna długość: 100, które są wymagane)|


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

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Umożliwia filtrowanie odpowiedzi według tagów niestandardowych metadanych metadanych

Dodanie metadanych umożliwia filtrowanie odpowiedzi przez te tagi metadanych. Dodawanie kolumny metadanych z **opcje wyświetlania** menu. Dodaj metadane do bazy wiedzy, wybierając metadanych **+** ikonę, aby dodać parę metadanych. Ta para składa się z jednego klucza i jedną wartość.

![Zrzut ekranu przedstawiający dodawanie metadanych](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrowanie wyników za pomocą strictFilters tagów metadanych

Należy wziąć pod uwagę pytanie użytkownika "Kiedy jest to hotelu Zamknij?", której celem jest implikowane dla restauracji "Paradise."

Ponieważ wyniki są wymagane tylko w przypadku restauracji "Paradise", można ustawić filtr w wywołaniu GenerateAnswer na metadanych "Restauracji Name". Poniższy przykład przedstawia to:

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

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Użyj wyników pytań i odpowiedzi, aby zachować kontekst konwersacji

Odpowiedź na GenerateAnswer zawiera odpowiednie informacje o metadanych zestawu dopasowane pytania i odpowiedzi. Te informacje w aplikacji klienckiej służy do przechowywania kontekście poprzedniej rozmowy dla danego użyj nowszej konwersacji. 

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

## <a name="match-questions-only-by-text"></a>Odpowiada pytania tylko według tekstu

Domyślnie narzędzie QnA Maker przeszukuje pytań i odpowiedzi. Jeśli chcesz przeszukiwać tylko pytania do generowania odpowiedzi, użyj `RankerType=QuestionOnly` w treści żądania GenerateAnswer POST.

Możesz wyszukać opublikowanych kb, za pomocą `isTest=false`, lub za pomocą testu kb `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Kolejne kroki

**Publikuj** strona zawiera również informacje potrzebne do generowania odpowiedzi za pomocą [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) i [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](./create-knowledge-base.md)
