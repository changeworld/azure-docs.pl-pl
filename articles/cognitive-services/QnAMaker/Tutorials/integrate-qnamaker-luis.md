---
title: Usługa LUIS i QnAMaker — Bot integracji
titleSuffix: Azure Cognitive Services
description: Wraz z rozwojem dużej wiedzy usługi QnA Maker staje się trudne w utrzymaniu go jako monolityczny jeden zestaw i ma konieczności dzielenia bazy wiedzy knowledge base na mniejsze części logiczne.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/11/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6605aa268a7ee7fe75254df5dbe96e9dfbc71d79
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272415"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Korzystanie z bot z usługami QnA Maker i LUIS w celu dystrybucji bazy wiedzy
Wraz z rozwojem dużej wiedzy usługi QnA Maker staje się trudne w utrzymaniu go jako monolityczny jeden zestaw i ma konieczności dzielenia bazy wiedzy knowledge base na mniejsze części logiczne.

Mimo że jest bardzo proste tworzenie wielu baz wiedzy w usługi QnA Maker, konieczne będzie logikę przekierowywać przychodzące pytanie do odpowiedniej bazy wiedzy. Można to zrobić za pomocą usługi LUIS.

W tym artykule użyto zestawu SDK programu platformy Bot Framework w wersji 3. Przeczytaj ten [artykułu platformy Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), jeśli interesuje Cię wersja zestawu SDK w wersji 4 platformy Bot Framework tych informacji.

## <a name="architecture"></a>Architektura

![QnA Maker z architekturą Language Understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

W powyższym scenariuszu Usługa QnA Maker najpierw pobiera celem przychodzące zapytania z modelu usługi LUIS, a następnie użyj, przekazać go do poprawne usługi QnA Maker wiedzy.

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Zaloguj się do [LUIS](https://www.luis.ai/) portalu.
1. [Tworzenie aplikacji](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Dodawanie intencji](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) dla każdej usługi QnA Maker bazie wiedzy knowledge base. Wypowiedzi przykład powinien odpowiadać na pytania w przypadku usługi QnA Maker baz wiedzy.
1. [Uczenie aplikacji usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) i [publikowanie aplikacji usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) aplikacją usługi LUIS.
1. W **Zarządzaj** sekcji, zanotuj usługi LUIS identyfikator aplikacji, klucza punktu końcowego usługi LUIS i hostowania regionu. Te wartości będą potrzebne później. 

## <a name="create-qna-maker-knowledge-bases"></a>Tworzenie bazy wiedzy usługi QnA Maker

1. Zaloguj się do [usługi QnA Maker](https://qnamaker.ai).
1. [Utwórz](https://www.qnamaker.ai/Create) baz wiedzy, dla każdego intencji w aplikacji usługi LUIS.
1. Testowanie i publikowanie bazy wiedzy. Gdy należy opublikować każdy KB, Zanotuj identyfikator bazy wiedzy, hosta (poddomeny przed _.azurewebsites.net/qnamaker_) i klucza punktu końcowego autoryzacji. Te wartości będą potrzebne później. 

    W tym artykule przyjęto założenie, że artykułów bazy wiedzy są tworzone w tej samej subskrypcji platformy Azure usługa QnA Maker.

    ![Usługa QnA Maker HTTP żądania](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web Apps

1. [Utwórz "podstawowe" bot aplikacji sieci Web](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) , która automatycznie zawiera aplikację Luis. Wybierz C# język programowania.

1. Po utworzeniu bot aplikacji sieci web w witrynie Azure portal wybierz bot aplikacji sieci web.
1. Wybierz **ustawienia aplikacji** w internetowej aplikacji bot service nawigacji, a następnie przewiń w dół do **ustawienia aplikacji** sekcji dostępne ustawienia.
1. Zmiana **LuisAppId** wartość utworzony w poprzedniej sekcji następnie wybierz aplikację usługi LUIS **Zapisz**.


## <a name="change-code-in-basicluisdialogcs"></a>Zmień kod w BasicLuisDialog.cs
1. Z **zarządzania Bot** sekcji nawigacji bot aplikacji sieci web w witrynie Azure portal, wybierz opcję **kompilacji**.
2. Wybierz **Otwórz online Edytor kodu**. Zostanie otwarty na nowej karcie przeglądarki online środowiska edycji. 
3. W **WWWROOT** zaznacz **okien dialogowych** katalogu, a następnie otwórz **BasicLuisDialog.cs**.
4. Dodaj zależności do góry **BasicLuisDialog.cs** pliku:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Dodaj poniższe klasy do deserializacji odpowiedzi usługi QnA Maker:

    ```csharp
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
    ```


6. Dodaj poniższą klasę, aby wysyłać żądania HTTP do usługi QnA Maker. Należy zauważyć, że **autoryzacji** wartości nagłówka zawiera słowo, `EndpointKey` z odstępem po słowie. Wynik JSON jest przeprowadzona na poprzednim klasy i zwracany jest pierwszej odpowiedzi.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Modyfikowanie klasy BasicLuisDialog. Przeznaczenie każdej usługi LUIS powinny zawierać metody ozdobione **LuisIntent**. Parametr dekoracją jest rzeczywistą nazwą intencji usługi LUIS. Nazwa metody, która zostanie nadany _powinien_ być nazwą metody konwersji LUIS czytelności i łatwość konserwacji, ale nie musi być taka sama na projekt lub czasu wykonywania.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
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
