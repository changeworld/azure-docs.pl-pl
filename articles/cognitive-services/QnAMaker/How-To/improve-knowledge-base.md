---
title: Poprawa bazy wiedzy - QnA Maker
description: Popraw jakość swojej bazy wiedzy dzięki aktywnemu uczeniu się. Przeglądaj, akceptuj lub odrzucaj, dodawaj bez usuwania lub zmieniania istniejących pytań.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 8b2176731ce456b70521cc9208435d0b19943ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053084"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Akceptowanie pytań sugerowanych w aktywnej nauce w bazie wiedzy


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Usługa Active Learning zmienia bazę wiedzy lub usługę wyszukiwania po zatwierdzeniu sugestii, a następnie zapisywanie i szkolenie. Jeśli zatwierdzisz sugestię, zostanie ono dodane jako pytanie alternatywne.

## <a name="turn-on-active-learning"></a>Włączanie aktywnego uczenia się

Aby wyświetlić sugerowane pytania, musisz [włączyć aktywne uczenie się](use-active-learning.md) zasobu QnA Maker.

## <a name="view-suggested-questions"></a>Wyświetlanie sugerowanych pytań

1. Aby wyświetlić sugerowane pytania, na stronie **Edytuj** pozycję Baza wiedzy wybierz pozycję **Opcje widoku**, a następnie wybierz pozycję Pokaż **aktywne sugestie dotyczące nauki**.

    [![W sekcji Edycja portalu wybierz pozycję Pokaż sugestie, aby wyświetlić nowe alternatywy związane z pytaniami aktywnego uczenia się.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtruj bazę wiedzy za pomocą par pytań i odpowiedzi, aby wyświetlić tylko sugestie, wybierając **pozycję Filtruj według sugestii**.

    [![Użyj przełącznika Filtruj według sugestii, aby wyświetlić tylko sugerowane przez aktywne uczenie się alternatywy pytań.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Każda para QnA sugeruje nowe alternatywy pytań `✔` ze znacznikiem wyboru, aby zaakceptować pytanie lub `x` odrzucić sugestie. Zaznacz znacznik wyboru, aby dodać pytanie.

    [![Wybierz lub odrzuć zalecane przez aktywne uczenie się alternatywy pytań, wybierając zielony znacznik wyboru lub czerwony znacznik usuwania.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Wszystkie _sugestie_ można dodać lub usunąć, zaznaczając pozycję **Dodaj wszystkie** lub **Odrzuć wszystkie** na pasku narzędzi kontekstowych.

1. Wybierz **pozycję Zapisz i trenuj,** aby zapisać zmiany w bazie wiedzy.

1. Wybierz **pozycję Publikuj,** aby zmiany były dostępne w [interfejsie API GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Gdy 5 lub więcej podobnych zapytań jest grupowanych, co 30 minut program QnA Maker sugeruje alternatywne pytania do zaakceptowania lub odrzucenia.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Przepływ architektury do korzystania z generateanswer i pociąg interfejsów API z bota

Bot lub inna aplikacja kliencka powinna używać następującego przepływu architektury do korzystania z aktywnego uczenia się:

* Bot [pobiera odpowiedź z bazy wiedzy](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) z GenerateAnswer `top` INTERFEJSU API, przy użyciu właściwości, aby uzyskać szereg odpowiedzi.
* Bot określa wyraźne opinie:
    * Korzystanie z własnej [niestandardowej logiki biznesowej,](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)odfiltrować niskie wyniki.
    * W bot lub aplikacji klienckiej wyświetl listę możliwych odpowiedzi dla użytkownika i uzyskaj wybraną odpowiedź użytkownika.
* Bot [wysyła wybraną odpowiedź z powrotem do QnA Maker](#bot-framework-sample-code) z train [API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Użyj właściwości top w generateanswer żądania, aby uzyskać kilka pasujących odpowiedzi

Podczas przesyłania pytania do programu QnA Maker `top` w celu uzyskania odpowiedzi właściwość treści JSON ustawia liczbę odpowiedzi do zwrócenia.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Użyj właściwości score wraz z logiką biznesową, aby uzyskać listę odpowiedzi, aby pokazać użytkownikowi

Gdy aplikacja kliencka (na przykład czat bot) odbiera odpowiedź, zwracane są 3 najważniejsze pytania. Użyj `score` właściwości do analizy bliskości między wynikami. Ten zakres bliskości zależy od własnej logiki biznesowej.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Działania następcze w przypadku pytań, które mają podobne wyniki

Aplikacja kliencka wyświetla pytania z opcją dla użytkownika, aby wybrać _pojedyncze pytanie,_ które większość reprezentuje ich intencji.

Gdy użytkownik wybierze jedno z istniejących pytań, aplikacja kliencka wysyła wybór użytkownika jako opinię przy użyciu interfejsu API train programu QnA Maker. Ta informacja zwrotna kończy aktywną pętlę sprzężenia zwrotnego nauki.

## <a name="train-api"></a>Interfejs API trenowania

Aktywne informacje zwrotne są wysyłane do programu QnA Maker z żądaniem post interfejsu API pociągu. Podpis interfejsu API jest:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Właściwość żądania HTTP|Nazwa|Typ|Przeznaczenie|
|--|--|--|--|
|Parametr trasy adresu URL|Identyfikator bazy wiedzy|ciąg|Identyfikator GUID dla twojej bazy wiedzy.|
|Niestandardowa poddomena|Nazwa zasobu QnAMaker|ciąg|Nazwa zasobu jest używana jako niestandardowa poddomena dla programu QnA Maker. Jest to dostępne na stronie Ustawienia po opublikowaniu bazy wiedzy. Jest on wymieniony `host`jako .|
|Nagłówek|Content-Type|ciąg|Typ nośnika treści wysyłane do interfejsu API. Wartość domyślna to:`application/json`|
|Nagłówek|Autoryzacja|ciąg|Klucz punktu końcowego (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Treść postu|Obiekt JSON|JSON|Informacje zwrotne dotyczące szkoleń|

Obiekt JSON ma kilka ustawień:

|Właściwość obiektu JSON|Typ|Przeznaczenie|
|--|--|--|--|
|`feedbackRecords`|tablica|Lista opinii.|
|`userId`|ciąg|Identyfikator użytkownika osoby akceptującej sugerowane pytania. Format identyfikatora użytkownika zależy od Ciebie. Na przykład adres e-mail może być prawidłowym identyfikatorem użytkownika w architekturze. Element opcjonalny.|
|`userQuestion`|ciąg|Dokładny tekst zapytania użytkownika. Wymagany.|
|`qnaID`|numer|Identyfikator pytania, znaleziony w [odpowiedzi GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Przykładowy obiekt JSON wygląda następująco:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Pomyślna odpowiedź zwraca stan 204 i nie JSON treści odpowiedzi.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Wyparte wiele rekordów opinii w jednym wywołaniu

W aplikacji po stronie klienta, takich jak bot, można przechowywać dane, a następnie `feedbackRecords` wysłać wiele rekordów w jednej treści JSON w tablicy.

Przykładowy obiekt JSON wygląda następująco:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Przykładowy kod struktury bota

Kod struktury bota musi wywołać interfejs API pociągu, jeśli kwerenda użytkownika powinna być używana do aktywnego uczenia się. Istnieją dwa kawałki kodu do napisania:

* Określanie, czy kwerenda ma być używana do aktywnego uczenia się
* Wyślij zapytanie z powrotem do interfejsu API pociągu programu QnA Maker w celu aktywnego uczenia się

W [przykładzie bota azure,](https://aka.ms/activelearningsamplebot)oba te działania zostały zaprogramowane.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Przykładowy kod języka C# dla interfejsu API pociągu z platformą Bot Framework 4.x

Poniższy kod ilustruje sposób wysyłania informacji z powrotem do programu QnA Maker za pomocą interfejsu API pociągu. Ten [kompletny przykład kodu](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) jest dostępny w usłudze GitHub.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Przykładowy kod node.js dla interfejsu API pociągu z platformą Bot Framework 4.x

Poniższy kod ilustruje sposób wysyłania informacji z powrotem do programu QnA Maker za pomocą interfejsu API pociągu. Ten [kompletny przykład kodu](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) jest dostępny w usłudze GitHub.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktywne uczenie się jest zapisywane w wyeksportowanym baza wiedzy

Gdy aplikacja ma włączoną aktywną naukę `SuggestedQuestions` i eksportujesz aplikację, kolumna w pliku tsv zachowuje aktywne dane szkoleniowe.

Kolumna `SuggestedQuestions` jest obiektem JSON informacji `autosuggested`niejawnych `usersuggested` i jawnych opinii. Przykładem tego obiektu JSON dla pojedynczego `help` pytania przesłanego przez użytkownika jest:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Można również użyć interfejsu API pobierania zmian, aby przejrzeć te zmiany, używając REST lub dowolnego z pakietów SDK opartych na języku:
* [INTERFEJS API ODPOCZYNKU](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Po ponownym zaimportu tej aplikacji, aktywne uczenie się nadal zbierać informacje i polecam sugestie dla bazy wiedzy.



## <a name="best-practices"></a>Najlepsze rozwiązania

Aby uzyskać najlepsze rozwiązania dotyczące korzystania z aktywnego uczenia się, zobacz [Najważniejsze wskazówki](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Korzystanie z metadanych za pomocą interfejsu API GenerateAnswer](metadata-generateanswer-usage.md)
