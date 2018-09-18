---
title: Usługa LUIS i QnAMaker — Bot integracji
titleSuffix: Azure Cognitive Services
description: Samouczek krok po kroku dotyczące integrowania usługi QnA Maker i LUIS w robota.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: nstulasi
ms.openlocfilehash: cfadaa836eef0763c9dd56c71d177995ab658c3e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736040"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integracja usługi QnA Maker i LUIS, aby rozprowadzić bazy wiedzy
Wraz z rozwojem dużej wiedzy usługi QnA Maker staje się trudne w utrzymaniu go jako monolityczny jeden zestaw i ma konieczności dzielenia bazy wiedzy knowledge base na mniejsze części logiczne.

Mimo że jest bardzo proste tworzenie wielu baz wiedzy w usługi QnA Maker, konieczne będzie logikę przekierowywać przychodzące pytanie do odpowiedniej bazy wiedzy. Można to zrobić za pomocą usługi LUIS.

## <a name="architecture"></a>Architektura

![Architektura usługi luis usługi QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

W powyższym scenariuszu Usługa QnA Maker najpierw pobiera celem przychodzące zapytania z modelu usługi LUIS, a następnie użyj, przekazać go do poprawne usługi QnA Maker wiedzy.

## <a name="prerequisites"></a>Wymagania wstępne
- Zaloguj się do [LUIS](https://www.luis.ai/) portalu i [tworzenie aplikacji](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
- [Dodawanie intencji](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) zgodnie z danego scenariusza.
- [Szkolenie](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) i [publikowania](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) aplikacją usługi LUIS.
- Zaloguj się do [QnA Maker](https://qnamaker.ai) i [tworzenie](https://www.qnamaker.ai/Create) baz wiedzy zgodnie z danego scenariusza.
- Testowanie i publikowanie bazy wiedzy.

## <a name="qna-maker--luis-bot"></a>Usługa QnA Maker + Bot usługi LUIS
1. Najpierw utwórz bota aplikacji sieci Web przy użyciu szablonu usługi LUIS, połączyć go z aplikacją usługi LUIS utworzoną wcześniej i modyfikowania intencji. Zobacz szczegółowy opis kroków [tutaj](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Dodaj zależności do górnej części pliku, przy użyciu innych zależności:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Dodaj poniższe klasy do wywoływania usługi QnA Maker:

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
        public class Metadata
        {
            public string name { get; set; }
            public string value { get; set; }
        }
    
        public class Answer
        {
            public IList<string> questions { get; set; }
            public string answer { get; set; }
            public double score { get; set; }
            public int id { get; set; }
            public string source { get; set; }
            public IList<object> keywords { get; set; }
            public IList<Metadata> metadata { get; set; }
        }
    
        public class QnAAnswer
        {
            public IList<Answer> answers { get; set; }
        }
        /* END - QnA Maker Response Class */
    ```

3. Przejdź do https://qnamaker.ai -> Moja baz wiedzy -> Wyświetl kod odpowiedniej bazy wiedzy. Uzyskaj następujące informacje:

    ![Usługa QnA Maker HTTP żądania](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Dla każdej z baz wiedzy, należy utworzyć wystąpienia klasy QnAMakerService:
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Wymaga odpowiedniej wiedzy intencji.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
            }
    ```

## <a name="build-the-bot"></a>Tworzenie bota
1. W edytorze kodu, kliknij prawym przyciskiem myszy `build.cmd` i wybierz **uruchamiane z poziomu konsoli**.

    ![Uruchamianie z poziomu konsoli](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Widok kodu jest zastępowany okno terminalu pokazujący postęp i wyniki kompilacji.

    ![Kompilacja konsoli](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testowanie robota
W witrynie Azure portal wybierz **testowania w czatów internetowych** do testowania robota. Typ wiadomości z różnymi intencjami, aby uzyskać odpowiedzi z odpowiedniej wiedzy.

![test rozmów w sieci Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Utwórz plan ciągłości działania dotyczące usługi QnA Maker](../How-To/business-continuity-plan.md)
