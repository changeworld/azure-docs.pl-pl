---
title: Metadane z interfejsem API GenerateAnswer — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umożliwia dodawanie metadanych w postaci par klucz/wartość do zestawów pytań/odpowiedzi. Wyniki można filtrować do zapytań użytkowników i przechowywać dodatkowe informacje, które mogą być używane w konwersacjach uzupełniających.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 6a8cbabfd4e47c50d2c2e6f4a23c50a931e645a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220645"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Uzyskaj odpowiedź za pomocą interfejsu API generateanswer i metadanych

Aby uzyskać przewidywaną odpowiedź na pytanie użytkownika, użyj interfejsu API GenerateAnswer. Podczas publikowania bazy wiedzy można zobaczyć informacje o tym, jak korzystać z tego interfejsu API na stronie **Publikowania.** Można również skonfigurować interfejs API do filtrowania odpowiedzi na podstawie tagów metadanych i przetestować bazę wiedzy z punktu końcowego za pomocą parametru ciągu zapytania testowego.

Program QnA Maker umożliwia dodawanie metadanych w postaci par kluczy i wartości do zestawów pytań i odpowiedzi. Następnie można użyć tych informacji do filtrowania wyników do zapytań użytkowników i do przechowywania dodatkowych informacji, które mogą być używane w konwersacjach uzupełniających. Aby uzyskać więcej informacji, zobacz [Baza wiedzy](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Przechowywanie pytań i odpowiedzi za pomocą jednostki QnA

Ważne jest, aby zrozumieć, w jaki sposób QnA Maker przechowuje dane pytań i odpowiedzi. Na poniższej ilustracji przedstawiono jednostkę QnA:

![Ilustracja jednostki QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Każda jednostka QnA ma unikatowy i trwały identyfikator. Identyfikator można użyć do aktualizacji do określonej jednostki QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Uzyskaj prognozy odpowiedzi za pomocą interfejsu API GenerateAnswer

Użyj [GenerateAnswer interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) w bot lub aplikacji do zapytania bazy wiedzy z pytaniem użytkownika, aby uzyskać najlepsze dopasowanie z zestawów pytań i odpowiedzi.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikowanie w celu uzyskania punktu końcowego GenerateAnswer

Po opublikowaniu bazy wiedzy, albo z [portalu QnA Maker](https://www.qnamaker.ai), lub za pomocą [interfejsu API,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)można uzyskać szczegółowe informacje o generateanswer punktu końcowego.

Aby uzyskać szczegółowe informacje o punkcie końcowym:
1. Zaloguj się [https://www.qnamaker.ai](https://www.qnamaker.ai)do .
1. W **mojej bazie wiedzy**wybierz opcję Wyświetl **kod** dla swojej bazy wiedzy.
    ![Zrzut ekranu przedstawiający moje bazy wiedzy](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Pobierz szczegóły punktu końcowego GenerateAnswer.

    ![Zrzut ekranu przedstawiający szczegóły punktu końcowego](../media/qnamaker-how-to-metadata-usage/view-code.png)

Szczegóły punktu końcowego można również uzyskać na karcie **Ustawienia** bazy wiedzy.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfiguracja żądania GenerateAnswer

Wywołać GenerateAnswer z żądaniem HTTP POST. Przykładowy kod, który pokazuje, jak wywołać GenerateAnswer, zobacz [przewodnika Szybki start](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

Żądanie POST wykorzystuje:

* Wymagane [parametry URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Wymagana właściwość `Authorization`nagłówka, dla zabezpieczeń
* Wymagane [właściwości ciała](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

Adres URL GenerateAnswer ma następujący format:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Pamiętaj, aby ustawić właściwość nagłówka `Authorization` HTTP `EndpointKey` z wartością ciągu z miejscem końcowym, a następnie kluczem punktu końcowego znajdującym się na stronie **Ustawienia.**

Przykładowy obiekt JSON wygląda następująco:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Dowiedz się więcej o [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Poprzedni JSON zażądał tylko odpowiedzi, które są na 30% lub powyżej wyniku progowego.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Właściwości odpowiedzi GenerateAnswer

[Odpowiedź](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) jest obiektem JSON, w tym wszystkie informacje potrzebne do wyświetlenia odpowiedzi i następnego obrotu w konwersacji, jeśli są dostępne.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
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

Poprzedni JSON odpowiedział odpowiedzią z wynikiem 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Użyj QnA Maker z botem w języku C #

Struktura botów zapewnia dostęp do właściwości programu QnA Maker za pomocą [interfejsu API getAnswer:](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Poprzedni JSON zażądał tylko odpowiedzi, które są na 30% lub powyżej wyniku progowego.

Bot pomocy technicznej ma [przykład](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) z tego kodu.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Użyj programu QnA Maker z botem w pliku Node.js

Struktura botów zapewnia dostęp do właściwości programu QnA Maker za pomocą [interfejsu API getAnswer:](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Poprzedni JSON zażądał tylko odpowiedzi, które są na 30% lub powyżej wyniku progowego.

Bot pomocy technicznej ma [przykład](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) z tego kodu.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Używanie metadanych do filtrowania odpowiedzi według niestandardowych tagów metadanych

Dodawanie metadanych umożliwia filtrowanie odpowiedzi przez te tagi metadanych. Dodaj kolumnę metadanych z menu **Opcje widoku.** Dodaj metadane do bazy wiedzy, **+** wybierając ikonę metadanych, aby dodać parę metadanych. Ta para składa się z jednego klucza i jednej wartości.

![Zrzut ekranu przedstawiający dodawanie metadanych](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrowanie wyników za pomocą strictFilters dla tagów metadanych

Rozważ pytanie użytkownika "Kiedy ten hotel jest zamknięty?", gdzie intencja jest implikowana dla restauracji "Paradise".

Ponieważ wyniki są wymagane tylko dla restauracji "Paradise", można ustawić filtr w GenerateAnswer wywołania metadanych "Nazwa restauracji". Poniższy przykład pokazuje następującą następujące czynności:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Używanie wyników pytań i odpowiedzi do zachowania kontekstu konwersacji

Odpowiedź na GenerateAnswer zawiera odpowiednie informacje o metadanych dopasowanego zestawu pytań i odpowiedzi. Te informacje można użyć w aplikacji klienckiej do przechowywania kontekstu poprzedniej konwersacji do użycia w późniejszych konwersacjach.

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

## <a name="match-questions-only-by-text"></a>Dopasowywanie tylko pytań według tekstu

Domyślnie QnA Maker przeszukuje pytania i odpowiedzi. Jeśli chcesz przeszukiwać tylko pytania, aby wygenerować odpowiedź, użyj `RankerType=QuestionOnly` w treści POST żądania GenerateAnswer.

Można przeszukiwać opublikowaną kb, `isTest=false`używając lub w `isTest=true`testowej kb przy użyciu .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Typowe błędy HTTP

|Code|Wyjaśnienie|
|:--|--|
|2xx|Powodzenie|
|400|Parametry żądania są niepoprawne, co oznacza, że brakuje wymaganych parametrów, zniekształconych lub jest zbyt dużych|
|400|Treść żądania jest niepoprawna, co oznacza, że json jest brak, zniekształcony lub zbyt duży|
|401|Nieprawidłowy klucz|
|403|Zabronione - nie masz odpowiednich uprawnień|
|404|KB nie istnieje|
|410|Ten interfejs API jest przestarzały i nie jest już dostępny|

## <a name="next-steps"></a>Następne kroki

Strona **Publikowania** zawiera również informacje, aby [wygenerować odpowiedź](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) z Listonosz lub cURL.

> [!div class="nextstepaction"]
> [Tworzenie bota bazy wiedzy](../tutorials/integrate-qnamaker-luis.md)
