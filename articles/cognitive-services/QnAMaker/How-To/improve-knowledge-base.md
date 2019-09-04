---
title: Poprawianie bazy wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa Active Learning pozwala ulepszyć jakość bazy wiedzy, sugerując alternatywne pytania na podstawie przesłanych przez użytkowników do pary pytań i odpowiedzi. Te sugestie można przejrzeć, dodając je do istniejących pytań lub odrzucając je. Baza wiedzy nie zmienia się automatycznie. Aby zmiana zaczęła obowiązywać, musisz zaakceptować sugestie. Te sugestie dodają pytania, ale nie zmieniają ani nie usuwają istniejących pytań.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: ae186209395078ed56a046aafdbe01bb513fc3a0
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277431"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Korzystanie z usługi Active Learning w celu ulepszania bazy wiedzy

Usługa Active Learning pozwala ulepszyć jakość bazy wiedzy, sugerując alternatywne pytania na podstawie przesłanych przez użytkowników do pary pytań i odpowiedzi. Te sugestie można przejrzeć, dodając je do istniejących pytań lub odrzucając je. 

Baza wiedzy nie zmienia się automatycznie. Aby zmiany zaczęły obowiązywać, należy zaakceptować sugestie. Te sugestie dodają pytania, ale nie zmieniają ani nie usuwają istniejących pytań.

## <a name="what-is-active-learning"></a>Co to jest aktywna nauka?

QnA Maker poznaje nowe odmiany pytań z niejawną i jawną opinią.
 
* [Niejawna opinia](#how-qna-makers-implicit-feedback-works) — ranga jest zrozumiała, gdy pytanie użytkownika ma wiele odpowiedzi z wynikami, które są bardzo bliskie i uważa za opinię. Nie musisz nic robić, aby to zrobić.
* [Jawne Opinie](#how-you-give-explicit-feedback-with-the-train-api) — w przypadku zwrócenia z bazy wiedzy wielu odpowiedzi z małą różnicą w ocenie, aplikacja kliencka prosi użytkownika o to, jakie pytanie jest odpowiednie. Jawne Opinie użytkownika są wysyłane do QnA Maker za pomocą [interfejsu API uczenia](#train-api). 

Obie metody zapewniają rangę z podobnymi zapytaniami, które są klastrowane.

## <a name="how-active-learning-works"></a>Jak działa aktywna nauka

Aktywne uczenie jest wyzwalane na podstawie ocen odpowiedzi z kilku najważniejszych wartości zwracanych przez QnA Maker. Jeśli różnice między wynikami są w niewielkim zakresie, zapytanie jest uznawane za możliwą sugestię (jako zapytanie alternatywne) dla każdej możliwej pary QnA. Po zaakceptowaniu sugerowanego pytania dotyczącego określonej pary QnA zostanie on odrzucony dla innych par. Należy pamiętać o zapisaniu i pouczeniu po zaakceptowaniu sugestii.

Usługa Active Learning daje najlepsze możliwe sugestie w przypadkach, gdy punkty końcowe uzyskują odpowiednią ilość i różne zapytania dotyczące użycia. Gdy co najmniej 5 podobnych zapytań jest klastrowane, co 30 minut QnA Maker sugeruje pytania oparte na użytkownikach do projektanta bazy wiedzy w celu ich zaakceptowania lub odrzucenia. Wszystkie sugestie są klastrowane według podobieństwa i najlepsze sugestie dotyczące alternatywnych pytań są wyświetlane na podstawie częstotliwości określonych zapytań przez użytkowników końcowych.

Po wybraniu pytań w portalu QnA Maker należy przejrzeć i zaakceptować lub odrzucić te sugestie. Brak interfejsu API do zarządzania sugestiami.

## <a name="how-qna-makers-implicit-feedback-works"></a>Jak działa niejawna opinia QnA Maker

Niejawna opinia QnA Maker używa algorytmu w celu określenia bliskości oceny, a następnie udostępnienia aktywnych sugestii szkoleniowych. Algorytm określający bliskość nie jest prostym wyliczeniem. Zakresów w poniższym przykładzie nie są przeznaczone do naprawienia, ale powinny być używane jako przewodnik, aby zrozumieć wpływ tylko algorytmu.

Gdy Ocena pytania ma duże wątpliwości, na przykład 80%, zakres ocen, które są brane pod uwagę w przypadku aktywnego uczenia, jest szeroki, około 10%. W miarę zmniejszania wyniku zaufania, takiego jak 40%, zakres ocen również zmniejsza się, około 4%. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Jak przekazać jawne informacje zwrotne za pomocą interfejsu API uczenia

Ważne jest, aby QnA Maker uzyskać jawną opinię na temat tego, która z odpowiedzi była najlepszą odpowiedzią. Najlepsza odpowiedź jest określana przez użytkownika i może obejmować:

* Opinie użytkowników, wybierając jedną z odpowiedzi.
* Logika biznesowa, na przykład określenie akceptowalnego zakresu oceny.  
* Kombinacja opinii użytkowników i logiki biznesowej.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Uaktualnij wersję środowiska uruchomieniowego, aby korzystać z usługi Active Learning

Usługa Active Learning jest obsługiwana w środowisku uruchomieniowym w wersji 4.4.0 i nowszych. Jeśli baza wiedzy została utworzona w starszej wersji, [Uaktualnij środowisko uruchomieniowe](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) , aby użyć tej funkcji. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Włącz aktywną naukę, aby zobaczyć sugestie

Aktywna nauka jest domyślnie wyłączona. Włącz tę opcję, aby zobaczyć sugerowane pytania. Po włączeniu aktywnego uczenia należy wysłać informacje z aplikacji klienckiej do QnA Maker. Aby uzyskać więcej informacji, zobacz temat [Architektura przepływu na potrzeby używania GenerateAnswer i uczenia interfejsów API z bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Wybierz pozycję **Publikuj** , aby opublikować bazę wiedzy. Aktywne zapytania szkoleniowe są zbierane tylko z punktu końcowego przewidywania interfejsu API GenerateAnswer. Zapytania do okienka test w portalu QnA Maker nie wpływają na aktywną naukę.

1. Aby włączyć aktywną naukę w portalu QnA Maker, przejdź do prawego górnego rogu, wybierz swoją **nazwę**i przejdź do pozycji [**Ustawienia usługi**](https://www.qnamaker.ai/UserSettings).  

    ![Na stronie ustawień usługi Włącz zaproponowane wątpliwości dotyczące aktywnego uczenia. Wybierz swoją nazwę użytkownika w prawym górnym menu, a następnie wybierz pozycję Ustawienia usługi.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Znajdź usługę QnA Maker a następnie Przełącz **aktywną naukę**. 

    [![Na stronie Ustawienia usługi Przełącz funkcję Active Learning. Jeśli nie możesz przełączać tej funkcji, może być konieczne uaktualnienie usługi.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Po włączeniu **aktywnej uczenia** baza wiedzy sugeruje nowe pytania w regularnych odstępach czasu na podstawie pytań przesłanych przez użytkownika. **Aktywną naukę** można wyłączyć, przełączając ponownie ustawienie.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Zaakceptowanie aktywnej sugestii szkoleniowej w bazie wiedzy

1. Aby wyświetlić sugerowane pytania, na stronie **Edytowanie** bazy wiedzy wybierz opcję **Wyświetl opcje**, a następnie wybierz pozycję **Pokaż aktywne sugestie dotyczące uczenia**. 

    [![W sekcji Edycja portalu wybierz pozycję Pokaż sugestie, aby zobaczyć alternatywy dla nowych pytań.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Przefiltruj bazę wiedzy przy użyciu par pytań i odpowiedzi, aby wyświetlić tylko sugestie, wybierając pozycję **Filtruj według sugestii**.

    [![Użyj przełącznika Filtruj według sugestii, aby wyświetlić tylko rozwiązania alternatywne dla sugerowanych pytań.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Każda para QNA sugeruje nowe alternatywy pytania ze znacznikiem `✔` wyboru,,,,, aby zaakceptować pytanie `x` lub aby odrzucić sugestie. Zaznacz znacznik wyboru, aby dodać pytanie. 

    [![Zaznacz lub Odrzuć zaproponowane pytania dotyczące aktywnego uczenia, zaznaczając zielony znacznik wyboru lub czerwony znacznik usuwający.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Możesz dodawać lub usuwać _wszystkie sugestie_ , wybierając pozycję **Dodaj wszystko** lub **Odrzuć wszystko** na kontekstowym pasku narzędzi.

1. Wybierz pozycję **Zapisz i pouczenie** , aby zapisać zmiany w bazie wiedzy.

1. Wybierz pozycję **Publikuj** , aby zezwolić na dostęp do zmian z [interfejsu API GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Gdy co najmniej 5 podobnych zapytań jest klastrowane, co 30 minut QnA Maker sugeruje alternatywne pytania, które należy zaakceptować lub odrzucić.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Przepływ architektury do korzystania z GenerateAnswer i uczenia interfejsów API z bot

Bot lub inna aplikacja kliencka powinna używać następującego przepływu architektury do korzystania z usługi Active Learning:

* Bot [Pobiera odpowiedź z bazy wiedzy](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) z interfejsem API GenerateAnswer przy użyciu `top` właściwości, aby uzyskać wiele odpowiedzi.
* Bot określa jawną opinię:
    * Korzystając z własnej [niestandardowej logiki biznesowej](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), odfiltruj niskie wyniki.
    * W bot lub aplikacji klienta Wyświetl listę możliwych odpowiedzi dla użytkownika i uzyskaj odpowiedź wybraną przez użytkownika.
* Bot [wysyła zaznaczoną odpowiedź z powrotem do QNA Maker](#bot-framework-sample-code) za pomocą [interfejsu API uczenia](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Użyj właściwości Top w żądaniu GenerateAnswer, aby uzyskać kilka pasujących odpowiedzi

Podczas przesyłania pytania do QNA Maker na potrzeby odpowiedzi `top` Właściwość treści JSON ustawia liczbę odpowiedzi do zwrócenia. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Korzystanie z właściwości Score wraz z logiką biznesową w celu uzyskania listy odpowiedzi na potrzeby wyświetlania użytkownika

Gdy aplikacja kliencka (na przykład Chat bot) otrzymuje odpowiedź, zwracane są 3 pierwsze pytania. Użyj właściwości `score` , aby przeanalizować bliskość między wynikami. Ten zakres bliskości jest określany przez własną logikę biznesową. 

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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Śledzenie aplikacji klienta, gdy pytania mają podobne wyniki

Aplikacja kliencka wyświetli pytania z opcją dla użytkownika, aby wybrać _pojedyncze pytanie_ , które reprezentuje ich zamiar. 

Gdy użytkownik wybierze jedno z istniejących pytań, aplikacja kliencka wysyła wybór użytkownika jako opinię za pomocą interfejsu API pouczenia QnA Maker. Ta opinia kończy aktywną pętlę opinii szkoleniowych. 

## <a name="train-api"></a>Interfejs API trenowania

Aktywne opinie dotyczące uczenia są wysyłane do QnA Maker za pomocą żądania POST interfejsu API uczenia. Podpis interfejsu API to:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Właściwość żądania HTTP|Name|Type|Cel|
|--|--|--|--|
|Parametr trasy adresu URL|Identyfikator bazy wiedzy|ciąg|Identyfikator GUID bazy wiedzy.|
|Poddomena hosta|Nazwa zasobu QnAMaker|ciąg|Nazwa hosta QnA Maker w ramach subskrypcji platformy Azure. Jest on dostępny na stronie Ustawienia po opublikowaniu bazy wiedzy. |
|nagłówek|Content-Type|ciąg|Typ nośnika treści wysyłanej do interfejsu API. Wartość domyślna to:`application/json`|
|nagłówek|Authorization|ciąg|Klucz punktu końcowego (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Opublikuj treść|Obiekt JSON|JSON|Opinie szkoleniowe|

Treść JSON ma kilka ustawień:

|Właściwość treści JSON|Type|Cel|
|--|--|--|--|
|`feedbackRecords`|array|Lista opinii.|
|`userId`|ciąg|Identyfikator użytkownika osoby akceptującej sugerowane pytania. Format identyfikatora użytkownika jest aktualny. Na przykład adres e-mail może być prawidłowym IDENTYFIKATORem użytkownika w danej architekturze. Opcjonalna.|
|`userQuestion`|ciąg|Dokładny tekst zapytania użytkownika. Wymagany.|
|`qnaID`|numer|Identyfikator pytania znaleziony w [odpowiedzi GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Przykładowa treść JSON wygląda następująco:

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

Pomyślna odpowiedź zwraca stan 204 i bez treści odpowiedzi JSON. 

### <a name="batch-many-feedback-records-into-a-single-call"></a>Tworzenie wsadowe wielu rekordów opinii w jednym wywołaniu

W aplikacji po stronie klienta, na przykład bot, można przechowywać dane, a następnie wysyłać wiele rekordów w jednej treści JSON w `feedbackRecords` tablicy. 

Przykładowa treść JSON wygląda następująco:

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

## <a name="bot-framework-sample-code"></a>Przykładowy kod bot Framework

Kod struktury bot musi wywoływać interfejs API uczenia, jeśli zapytanie użytkownika powinno być używane na potrzeby aktywnej nauki. Istnieją dwa fragmenty kodu do zapisu:

* Określ, czy zapytanie ma być używane na potrzeby aktywnego uczenia
* Wyślij zapytanie z powrotem do interfejsu API pouczenia QnA Maker na potrzeby aktywnego uczenia

W [przykładzie usługi Azure bot](https://aka.ms/activelearningsamplebot)zostały zaprogramowane obie te działania. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Przykładowy C# kod dla interfejsu API uczenia z bot Framework 4. x

Poniższy kod ilustruje sposób wysyłania informacji z powrotem do QnA Maker za pomocą interfejsu API uczenia. Ten [kompletny przykład kodu](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) jest dostępny w witrynie GitHub.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Przykładowy kod Node. js dla interfejsu API uczenia z bot Framework 4. x 

Poniższy kod ilustruje sposób wysyłania informacji z powrotem do QnA Maker za pomocą interfejsu API uczenia. Ten [kompletny przykład kodu](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) jest dostępny w witrynie GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Usługa Active Learning jest zapisywana w wyeksportowanej bazie wiedzy

Gdy aplikacja ma aktywne uczenie i eksportujesz aplikację, `SuggestedQuestions` kolumna w pliku TSV zachowuje aktywne dane szkoleniowe. 

Kolumna jest obiektem JSON informacji o niejawnych, `autosuggested`i jawnych `usersuggested` informacjach zwrotnych. `SuggestedQuestions` Przykładem tego obiektu JSON dla pojedynczego pytania `help` przesłanego przez użytkownika jest:

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

Po ponownym zaimportowaniu tej aplikacji aktywna nauka nadal zbiera informacje i zaleca sugestie dotyczące bazy wiedzy. 

## <a name="best-practices"></a>Najlepsze praktyki

Najlepsze rozwiązania dotyczące korzystania z usługi Active Learning można znaleźć w temacie [Best Practices](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Następne kroki
 
> [!div class="nextstepaction"]
> [Korzystanie z metadanych przy użyciu interfejsu API GenerateAnswer](metadata-generateanswer-usage.md)
