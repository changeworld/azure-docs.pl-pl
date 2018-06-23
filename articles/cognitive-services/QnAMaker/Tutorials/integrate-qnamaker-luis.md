---
title: Integracja Maker — strona główna i LUIS - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: samouczek krok po kroku dotyczące integrowania Maker — strona główna i LUIS
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 0a0eeb3815b793ed81f60b2b239bc459e5574788
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348692"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integracja Maker — strona główna i LUIS rozpowszechniać bazy wiedzy
Jak duże rozwojem Maker — strona główna bazy wiedzy staje się trudne w utrzymaniu go jako wbudowanymi jeden zestaw i zachodzi potrzeba podzielić na mniejsze fragmenty logicznej bazy wiedzy knowledge base.

Jest prosta do utworzenia wielu baz wiedzy w programie — strona główna Maker, konieczne będzie logikę do kierowania przychodzące pytanie do odpowiedniej wiedzy. Można to zrobić za pomocą LUIS.

## <a name="architecture"></a>Architektura

![Architektura luis Maker — strona główna](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

W powyższym scenariuszu Maker — strona główna najpierw pobiera celem zapytania przychodzącego z modelu LUIS, a następnie używać, aby go rozesłać do poprawne Maker — strona główna bazy wiedzy.

## <a name="prerequisites"></a>Wymagania wstępne
- Zaloguj się do [LUIS](https://www.luis.ai/) portalu i [Utwórz aplikację](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Dodaj intencje](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) zgodnie z danego scenariusza.
- [Train](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) i [publikowania](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) LUIS aplikacji.
- Zaloguj się do [Maker — strona główna](https://qnamaker.ai) i [je tworzyć]() podstawowych zgodnie z danego scenariusza.
- [Test]() i [publikowania]() bazy wiedzy.

## <a name="qna-maker--luis-bot"></a>Maker — strona główna + LUIS Bot
1. Najpierw utwórz bot aplikacji sieci Web przy użyciu szablonu LUIS, połączyć ją z aplikacją LUIS utworzoną wcześniej i modyfikowanie lokalizacji docelowych. Zobacz szczegółowe kroki [tutaj](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Dodaj zależności do górnej części pliku, z innych zależności:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Dodaj poniżej klasy do wywoływania usługi Maker — strona główna:

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

3. Przejdź do https://qnamaker.ai -> Moje baz wiedzy -> Kod widoku odpowiednie bazy wiedzy. Uzyskaj następujące informacje:

    ![Żądanie HTTP Maker — strona główna](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Utworzenie wystąpienia klasy QnAMakerService dla każdego z bazy wiedzy:
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Wymaga odpowiedniej wiedzy celem.
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

## <a name="build-the-bot"></a>Tworzenie bot
1. W edytorze kodu, kliknij prawym przyciskiem myszy `build.cmd` i wybierz **uruchomić z konsoli**.

    ![uruchomić z konsoli](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Widok kodu jest zastępowany terminali okna pokazującego postęp i wyniki kompilacji.

    ![Kompilacja konsoli](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testowanie bot
W portalu Azure wybierz **testów w sieci Web rozmowę** do testowania bot. Wpisz komunikaty z różnych lokalizacji docelowych można pobrać odpowiedzi z odpowiedniej wiedzy.

![test rozmów w sieci Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie planu ciągłości biznesowej dla Maker — strona główna](../How-To/business-continuity-plan.md)
