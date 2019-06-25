---
title: Poprawa wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Aktywna nauka można poprawić jakość bazy wiedzy w postaci sugerowanych alternatywnych pytania, oparte na użytkownika — wiadomości, do Twojego pary pytań i odpowiedzi. Albo dodanie ich do istniejącego pytania lub odrzucenia ich przejrzenie tych propozycji. Bazy wiedzy nie zmienia się automatycznie. Sugestie dotyczące wszelkie zmiany zaczęły obowiązywać, należy zaakceptować. Tego rodzaju sugestie dodać pytania, ale nie Zmień lub usuń istniejące pytania.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/19/2019
ms.author: diberry
ms.openlocfilehash: b73884e544ea1b8ee76c8a891048e6a8e17d6ab3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204093"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Użyj aktywnej nauki usprawniających bazy wiedzy

Aktywna nauka można poprawić jakość bazy wiedzy w postaci sugerowanych alternatywnych pytania, oparte na użytkownika — wiadomości, do Twojego pary pytań i odpowiedzi. Albo dodanie ich do istniejącego pytania lub odrzucenia ich przejrzenie tych propozycji. 

Bazy wiedzy nie zmienia się automatycznie. Aby zmiany zaczęły obowiązywać należy zaakceptować sugestie. Tego rodzaju sugestie dodać pytania, ale nie Zmień lub usuń istniejące pytania.

## <a name="what-is-active-learning"></a>Co to jest aktywna nauka?

Usługa QnA Maker uzyskuje informacje o nowych zmian pytanie jawne i niejawne opinii.
 
* [Niejawne opinii](#how-qna-makers-implicit-feedback-works) — tematycznie rozumie, jeśli pytanie użytkownika ma wiele odpowiedzi za pomocą wyniki, które są bardzo podobne i uznaje jako opinii. Nie trzeba wykonywać żadnych czynności tak się stało.
* [Jawne opinii](#how-you-give-explicit-feedback-with-the-train-api) — w przypadku wielu odpowiedzi za pomocą niewielkie różnice w wyniki są zwracane z bazy wiedzy knowledge base, aplikacja kliencka pyta użytkownika, które pytanie jest poprawny pytanie. Jawne opinii użytkownika jest wysyłana do usługi QnA Maker z [Train API](#train-api). 

Obie metody zapewniają tematycznie przy użyciu podobnych zapytań, które są klastrowane.

## <a name="how-active-learning-works"></a>Jak aktywne uczenie działa

Aktywne uczenie jest wyzwalany w oparciu o wyniki najważniejsze odpowiedzi kilka zwrócone przez narzędzie QnA Maker. Jeśli różnice wynik znajdują się w zakresie małe, zapytanie będzie traktowany możliwe sugestii (jako alternatywne pytanie) dla każdego możliwe pary pytań i odpowiedzi. Po zaakceptowaniu sugerowanych pytań dla określonej pary pytań i odpowiedzi, zostanie ono odrzucone dla innych par. Należy pamiętać zapisać i szkolenie po przesyłać sugestie.

Aktywna nauka zapewnia najlepsze możliwe sugestie w przypadkach, gdzie punkty końcowe są objęte ilość uzasadnione i różne zapytania do użycia. 5 lub więcej podobne zapytań są klastrowane, co 30 minut narzędzie QnA Maker sugeruje pytania oparte na użytkownikach do projektanta bazy wiedzy knowledge base, aby zaakceptować lub odrzucić. Wszystkie sugestie są zgrupowane razem wg podobieństwa i najbardziej oczekiwanych alternatywne pytania są wyświetlane na podstawie częstotliwości określonej zapytań przez użytkowników końcowych.

Po pytania są sugerowane w portalu narzędzia QnA Maker, należy przejrzeć i zaakceptować lub odrzucić te sugestie. Nie ma interfejs API umożliwiający zarządzanie sugestie.

## <a name="how-qna-makers-implicit-feedback-works"></a>Jak działa usługa QnA Maker niejawne opinii

Narzędzie QnA Maker niejawne opinii używa algorytmu ustalić wyniku odległości między elementami, a następnie aktywne uczenie sugestie. Algorytm ustalania odległości między elementami nie jest proste obliczenia. Zakresy w poniższym przykładzie nie są przeznaczone do rozwiązany, ale powinny służyć jako przewodnik Aby zrozumieć wpływ tylko algorytmu.

Gdy wynik zapytania jest wysoce pewność, takie jak 80%, zakres wyniki, które są uważane za dla aktywne uczenie są szerokie w przybliżeniu w ciągu 10%. Jak zmniejsza współczynnik ufności, takich jak 40%, zakres wyniki zmniejsza to także około w 4%. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Jak podać jawne informacji zwrotnych za pomocą interfejsu API Train

Należy pamiętać, że usługi QnA Maker pobiera jawne opinię o tym, jakie odpowiedzi był najlepszą odpowiedź. Sposób ustalania najlepszą odpowiedź zależy od użytkownika i mogą obejmować:

* Opinie użytkowników, wybierając jedną z odpowiedzi.
* Logika biznesowa, np. Określanie dopuszczalne ocena zakresu.  
* Kombinacja obu użytkownika opinii i logiki biznesowej.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Uaktualnienie z wersji środowiska uruchomieniowego na korzystanie z aktywnej nauki

Aktywne uczenie jest obsługiwana w wersji środowiska uruchomieniowego 4.4.0 i nowszych. Jeśli wiedzy został utworzony we wcześniejszej wersji, [uaktualnić swoje środowisko uruchomieniowe](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) Aby użyć tej funkcji. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Włącz aktywne uczenie z sugestii

Aktywna nauka jest domyślnie wyłączona. Włącz ją wyświetlić sugerowane pytania. Po włączeniu aktywne uczenie należy do wysyłania informacji z aplikacji klienckiej do usługi QnA Maker. Aby uzyskać więcej informacji, zobacz [architektury przepływ przy użyciu GenerateAnswer i interfejsów API szkolenie z bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Wybierz **Publikuj** publikowanie bazy wiedzy knowledge base. Aktywna nauka zapytania są zbierane z tylko endpoint prognoz GenerateAnswer interfejsu API. Zapytania, które okienko testowania, w portalu narzędzia QnA Maker nie wpływa na aktywne uczenie.

1. Aby włączyć aktywne uczenie się na w portalu narzędzia QnA Maker, przejdź do prawym górnym rogu wybierz swoje **nazwa**, przejdź do [ **ustawienia usługi**](https://www.qnamaker.ai/UserSettings).  

    ![Włącz aktywne uczenie alternatywy sugerowanych pytań, na stronie ustawień usługi. Wybierz swoją nazwę użytkownika w menu w prawym górnym rogu, a następnie wybierz pozycję Ustawienia usługi.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Znajdź usługę QnA Maker, a następnie przełącz **aktywne uczenie**. 

    [![Na stronie Ustawienia usługi Włącz opcję Aktywne uczenie funkcji. Jeśli nie jest możliwe włączyć tę funkcję, może być konieczne uaktualnienie usługi.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Gdy **aktywne uczenie** jest włączona, bazie wiedzy knowledge base sugeruje nowe pytania w regularnych odstępach czasu, w oparciu o przesłane przez użytkownika pytań. Możesz wyłączyć **aktywne uczenie** przełączając ustawienia ponownie.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Zaakceptuj sugestii aktywne uczenie bazy wiedzy knowledge base

1. Aby można było wyświetlić sugerowane pytania na **Edytuj** bazy wiedzy knowledge base, wybierz **opcje wyświetlania**, a następnie wybierz **Pokaż sugestie dotyczące aktywne uczenie**. 

    [![W sekcji edycji w portalu wybierz Pokaż sugestie, aby można było wyświetlić aktywne uczenie nowe pytanie alternatyw.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtruj wiedzy za pomocą pary pytań i odpowiedzi, aby pokazać tylko sugestie, wybierając **Filtruj według sugestii**.

    [![Użyj filtru za pomocą przełącznika sugestie, aby wyświetlić tylko aktywne uczenie sugerowanych pytań alternatyw.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Każdej pary pytań i odpowiedzi zasugeruje nowe alternatywy zapytania ze znacznikiem wyboru `✔` , aby zaakceptować pytanie lub `x` do odrzucenia sugestie. Kliknij znacznik wyboru, aby dodać pytanie. 

    [![Wybierz lub Odrzuć alternatywy sugerowanych pytań aktywne uczenie, wybierając zielony znacznik wyboru lub usuń czerwony znak.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Można dodawać lub usuwać _wszystkie sugestie_ , wybierając **Dodaj wszystkie** lub **Odrzuć wszystkie** kontekstowych pasku narzędzi.

1. Wybierz **Zapisz i szkolenie** można zapisać zmian w bazie wiedzy knowledge base.

1. Wybierz **Publikuj** do umożliwienia wprowadzania zmian powinna być dostępna z [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    5 lub więcej podobne zapytań są klastrowane, co 30 minut narzędzie QnA Maker sugeruje alternatywne pytania o zaakceptowanie lub odrzucenie.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Architektury przepływ przy użyciu GenerateAnswer i interfejsów API szkolenie z robota

Bot lub innych aplikacji klienckiej należy używać następujący przepływ architektury na korzystanie z aktywnej nauki:

* Bot [pobiera odpowiedź z bazy wiedzy knowledge base](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) przy użyciu interfejsu API GenerateAnswer, za pomocą `top` właściwości do pobrania liczba odpowiedzi.
* Bot określa jawne opinii:
    * Za pomocą własnego [niestandardowej logiki biznesowej](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), odfiltrować wyniki niski.
    * Bot lub aplikacja kliencka wyświetlić listę możliwych odpowiedzi dla użytkownika i Uzyskaj odpowiedzi wybranego użytkownika.
* Bot [wysyła wybrane odpowiedź z powrotem do usługi QnA Maker](#bot-framework-sample-code) z [Train API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Umożliwia właściwość top w żądaniu GenerateAnswer kilku pasujących odpowiedzi

Podczas przesyłania zapytania do usługi QnA Maker odpowiedź `top` właściwości treść JSON Ustawia liczbę odpowiedzi do zwrócenia. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Pobierz listę odpowiedzi, aby wyświetlić użytkowników za pomocą właściwości oceny wraz z logiką biznesową

Gdy aplikacja kliencka (takie jak czatbot) odbiera odpowiedź, 3 najważniejsze pytania są zwracane. Użyj `score` właściwości do analizowania odległości między wyniki. Ten zakres odległości jest określany przez logikę biznesową. 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Monitowania aplikacji klienta, gdy wyniki podobne pytania

Aplikacja kliencka zawiera pytania z opcją dla użytkownika wybrać _pojedynczego zapytania_ najlepiej odpowiadającej reprezentuje zamiar. 

Po użytkownik wybiera jeden z istniejących pytań, aplikacja kliencka wysyła wybrany przez użytkownika jako opinii przy użyciu interfejsu API Train usługi QnA Maker. Ta opinia zakończeniu aktywnej nauki sprzężenia zwrotnego. 

## <a name="train-api"></a>Szkolenie interfejsu API

Aktywne uczenie opinie są wysyłane do usługi QnA Maker żądania POST API pociągu. Podpis interfejsu API jest:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Właściwości żądania HTTP|Name (Nazwa)|Typ|Przeznaczenie|
|--|--|--|--|
|Parametr trasy adresu URL|Identyfikator bazy wiedzy|string|Identyfikator GUID bazy wiedzy.|
|Host poddomeny|Nazwa zasobu QnAMaker|string|Nazwa hosta usługi QnA Maker w subskrypcji platformy Azure. Jest on dostępny na stronie ustawień, po opublikowaniu w bazie wiedzy knowledge base. |
|nagłówek|Content-Type|string|Typ nośnika treści wysłanej do interfejsu API. Wartość domyślna to: `application/json`|
|nagłówek|Autoryzacja|string|Klucz punktu końcowego (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Treść wpisu|Obiekt JSON|JSON|Opinie szkolenia|

Treść kodu JSON ma kilka ustawień:

|Właściwość treść JSON|Typ|Przeznaczenie|
|--|--|--|--|
|`feedbackRecords`|array|Lista opinii.|
|`userId`|string|Identyfikator użytkownika osoby, akceptując sugerowane pytania. Format Identyfikatora użytkownika zależy od użytkownika. Na przykład adres e-mail może być prawidłowy identyfikator użytkownika w ramach architektury. Opcjonalny.|
|`userQuestion`|string|Tekstu do dokładnego dopasowania zapytania użytkownika. Wymagany.|
|`qnaID`|numer|Identyfikator zapytania, znaleziono w [odpowiedzi GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Przykładowy kod JSON wygląda następująco:

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

Stan 204 i nie treści odpowiedzi JSON zwrócą pomyślne odpowiedzi. 

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch wiele rekordów opinii w pojedynczym wywołaniu

W aplikacji po stronie klienta, takich jak bot, możesz przechowywać dane, a następnie wysłać wiele rekordów w jednym treść JSON w `feedbackRecords` tablicy. 

Przykładowy kod JSON wygląda następująco:

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

## <a name="bot-framework-sample-code"></a>Bot framework przykładowy kod

Bot framework kodu musi wywołać interfejsu API szkolenie, jeśli kwerenda użytkownika powinna być używana dla aktywne uczenie. Istnieją dwa fragmenty kodu do zapisania:

* Określić, jeśli zapytanie należy używać w przypadku aktywne uczenie
* Wyślij zapytanie do usługi QnA Maker API szkolenie dla aktywne uczenie

W [próbka Azure Bot](https://aka.ms/activelearningsamplebot), oba te działania zaprogramowane. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Przykład C# kod dla interfejsu API szkolenie przy użyciu platformy Bot Framework 4.x

Poniższy kod ilustruje sposób wysyłania informacji do usługi QnA Maker przy użyciu interfejsu API pociągu. To [cały przykładowy kod](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) jest dostępna w witrynie GitHub.

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
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Przykładowy kod Node.js dla interfejsu API szkolenie przy użyciu platformy Bot Framework 4.x 

Poniższy kod ilustruje sposób wysyłania informacji do usługi QnA Maker przy użyciu interfejsu API pociągu. To [cały przykładowy kod](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) jest dostępna w witrynie GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktywna nauka jest zapisywany w bazie wiedzy wyeksportowanego

Gdy Twoja aplikacja ma aktywne uczenie włączone i wyeksportować aplikację, `SuggestedQuestions` kolumna w pliku tsv przechowuje dane aktywne uczenie. 

`SuggestedQuestions` Kolumny jest obiektem JSON informacje pośrednie, `autosuggested`, jawne i `usersuggested` opinii. Przykładem obiektu JSON do pojedynczego zapytania przesłane przez użytkownika z `help` jest:

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

Gdy możesz ponownie zaimportować tej aplikacji, aktywne uczenie nadal zbierać informacje i zaleca się sugestie dotyczące wiedzy. 

## <a name="best-practices"></a>Najlepsze praktyki

Aby uzyskać najlepsze rozwiązania związane z używaniem aktywne uczenie, zobacz [najlepsze praktyki](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Kolejne kroki
 
> [!div class="nextstepaction"]
> [Metadane za pomocą interfejsu API GenerateAnswer](metadata-generateanswer-usage.md)
