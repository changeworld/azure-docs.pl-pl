---
title: LUIS i QnAMaker - Integracja botów
titleSuffix: Azure Cognitive Services
description: Ponieważ baza wiedzy QnA Maker rośnie w dużych rozmiarach, trudno jest utrzymać ją jako pojedynczy zestaw monolityczny i istnieje potrzeba podzielenia bazy wiedzy na mniejsze fragmenty logiczne.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c01f5f41e61cd65855789bb753a7a297fe475885
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396341"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Użyj bota z QnA Maker i LUIS, aby dystrybuować swoją bazę wiedzy
Ponieważ baza wiedzy QnA Maker rośnie w dużych rozmiarach, trudno jest utrzymać ją jako pojedynczy zestaw monolityczny i istnieje potrzeba podzielenia bazy wiedzy na mniejsze fragmenty logiczne.

Chociaż tworzenie wielu baz wiedzy w aplikacji QnA Maker jest proste, ale potrzebna jest logika do kierowania pytania przychodzącego do odpowiedniej bazy wiedzy. Można to zrobić za pomocą usługi LUIS.

W tym artykule użyto sdk programu Bot Framework w wersji 3. Zapoznaj się z tym [artykułem Programu Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), jeśli jesteś zainteresowany wersją tego pliku SDK bot framework v4.

## <a name="architecture"></a>Architektura

![QnA Maker z architekturą rozumienia języka](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

W powyższym scenariuszu QnA Maker najpierw pobiera intencji pytania przychodzącego z modelu usługi LUIS, a następnie użyć go do kierowania go do poprawnej bazy wiedzy programu QnA Maker.

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Zaloguj się do portalu [usługi LUIS.](https://www.luis.ai/)
1. [Tworzenie aplikacji](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Dodaj intencję](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) dla każdej bazy wiedzy programu QnA Maker. Wypowiedzi przykład powinny odpowiadać pytania w bazach wiedzy QnA Maker.
1. [Trenuj aplikację usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) i [publikuj aplikację usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) jako aplikację usługi LUIS.
1. W sekcji **Zarządzanie** zanotuj identyfikator aplikacji usługi LUIS, klucz punktu końcowego usługi LUIS i [niestandardową nazwę domeny](../../cognitive-services-custom-subdomains.md). Te wartości będą potrzebne później.

## <a name="create-qna-maker-knowledge-bases"></a>Tworzenie baz wiedzy programu QnA Maker

1. Zaloguj się do [aplikacji QnA Maker](https://qnamaker.ai).
1. [Tworzenie](https://www.qnamaker.ai/Create) baz wiedzy dla każdej intencji w aplikacji usługi LUIS.
1. Przetestuj i opublikuj bazy wiedzy. Podczas publikowania każdej bazy wiedzy należy zanotować identyfikator KB, nazwę zasobu (niestandardową poddomenę przed _azurewebsites.net/qnamaker)_ i klucz punktu końcowego autoryzacji. Te wartości będą potrzebne później.

    W tym artykule przyjęto założenie, że wszystkie kbs są tworzone w tej samej subskrypcji programu Azure QnA Maker.

    ![Żądanie HTTP programu QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot aplikacji sieci Web

1. [Utwórz bota aplikacji sieci Web "Basic",](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) który automatycznie zawiera aplikację usługi LUIS. Wybierz język programowania języka języka C#.

1. Po utworzeniu bota aplikacji sieci web w witrynie Azure portal wybierz bota aplikacji sieci web.
1. Wybierz **pozycję Ustawienia aplikacji** w nawigacji usługi bota aplikacji sieci Web, a następnie przewiń w dół do sekcji Ustawienia **aplikacji** dostępnych ustawień.
1. Zmień **LuisAppId** na wartość aplikacji usługi LUIS utworzonej w poprzedniej sekcji, a następnie wybierz pozycję **Zapisz**.


## <a name="change-code-in-basicluisdialogcs"></a>Zmień kod w BasicLuisDialog.cs
1. W sekcji **Zarządzanie botami** nawigacji botów aplikacji sieci web w witrynie Azure portal wybierz pozycję **Buduj**.
2. Wybierz **otwórz edytor kodu online**. Zostanie otwarta nowa karta przeglądarki ze środowiskiem edycji online.
3. W sekcji **WWWROOT** wybierz katalog **Dialogi,** a następnie otwórz **BasicLuisDialog.cs**.
4. Dodaj zależności do górnej części pliku **BasicLuisDialog.cs:**

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

5. Dodaj poniższe klasy, aby zdesializować odpowiedź programu QnA Maker:

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


6. Dodaj następującą klasę, aby złożyć żądanie HTTP do usługi QnA Maker. Należy zauważyć, że wartość nagłówka `EndpointKey` **autoryzacji** zawiera wyraz, z spacją po słowie. Wynik JSON jest deserialized do poprzednich klas i zwracana jest pierwsza odpowiedź.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
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
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
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


7. Zmodyfikuj klasę BasicLuisDialog. Każda intencja usługi LUIS powinna mieć metodę ozdobioną **luisintent**. Parametrem do dekoracji jest rzeczywista nazwa intencji usługi LUIS. Nazwa metody, która jest dekorowana _powinna_ być nazwą intencji usługi LUIS dla czytelności i łatwości konserwacji, ale nie musi być taka sama w czasie projektowania lub wykonywania.

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
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

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


## <a name="build-the-bot"></a>Zbuduj bota
1. W edytorze kodu kliknij `build.cmd` prawym przyciskiem myszy i wybierz polecenie **Uruchom z konsoli**.

    ![uruchomić z konsoli](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Widok kodu jest zastępowany oknem terminala przedstawiającym postęp i wyniki kompilacji.

    ![kompilacja konsoli](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Przetestuj bota
W witrynie Azure portal wybierz pozycję **Testuj w czacie sieci Web,** aby przetestować bota. Wpisz wiadomości z różnych intencji, aby uzyskać odpowiedź z odpowiedniej bazy wiedzy.

![test czatu internetowego](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zintegruj swoją bazę wiedzy z wirtualnym agentem power](integrate-with-power-virtual-assistant-fallback-topic.md)
