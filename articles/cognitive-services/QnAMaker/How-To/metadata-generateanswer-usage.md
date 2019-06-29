---
title: Metadane z GenerateAnswer interfejsu API — usługa QnA Maker
titleSuffix: Azure Cognitive Services
description: Narzędzie QnA Maker umożliwia dodanie metadanych, w postaci par klucz wartość do zestawów z pytań i odpowiedzi. Możesz filtrować wyniki na zapytania użytkowników i przechowywać dodatkowe informacje, które mogą być używane w kolejnych konwersacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 99c076d7f26638833b568935e766cf319d21945e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443472"
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

Możesz użyć [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) bot lub aplikacji do wykonywania zapytań bazy wiedzy przy użyciu pytanie użytkownika, aby uzyskać najlepsze dopasowanie z pytaniami i odpowiedziami ustawia.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikować można pobrać punktu końcowego GenerateAnswer 

Po opublikowaniu w bazie wiedzy, albo z [portalu narzędzia QnA Maker](https://www.qnamaker.ai), lub za pomocą [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), możesz uzyskać szczegółowe informacje o punkcie końcowym usługi GenerateAnswer.

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

Używa żądania POST:

* Wymagane [parametry identyfikatora URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Wymagane [właściwości nagłówka](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, dotyczące zabezpieczeń
* Wymagane [treści właściwości](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

Adres URL GenerateAnswer ma następujący format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Pamiętaj, aby ustawić właściwości nagłówka HTTP `Authorization` z wartością ciągu `EndpointKey ` kropki na miejsce następnie klucza punktu końcowego w **ustawienia** strony.

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

[Odpowiedzi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful_query) jest obiektem JSON oraz wszystkie informacje potrzebne do wyświetlania odpowiedzi, a następnie włącz konwersacji, jeśli jest dostępny.

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

## <a name="use-qna-maker-with-a-bot-in-c"></a>Użyj usługi QnA Maker z botem wC#

Platformy bot framework umożliwia dostęp do właściwości usługi QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Bot pomocy technicznej ma [przykład](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) przy użyciu tego kodu.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Użyj usługi QnA Maker z botem w środowisku Node.js

Platformy bot framework umożliwia dostęp do właściwości usługi QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Bot pomocy technicznej ma [przykład](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) przy użyciu tego kodu.

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
