---
title: Przykłady integracji usług Azure Service Bus i Event Grid | Microsoft Docs
description: Ten artykuł zawiera przykłady integracji komunikatów usługi Service Bus z usługą Event Grid.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b29798bb87b7c5c677e7d80e552e45e8d1290541
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786856"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Odpowiadanie na zdarzenia usługi Azure Service Bus odebranych za pośrednictwem usługi Azure Event Grid przy użyciu usługi Azure Functions i Azure Logic Apps
W tym samouczku dowiesz się, jak reagować na zdarzenia usługi Azure Service Bus, które są odbierane za pośrednictwem usługi Azure Event Grid przy użyciu usługi Azure Functions i Azure Logic Apps. Wykonasz następujące czynności:
 
- Utwórz test funkcji platformy Azure do debugowania i wyświetlania początkowego przepływu zdarzeń z usługi Event Grid.
- Utworzenie funkcji platformy Azure na potrzeby odbierania i przetwarzania komunikatów usługi Azure Service Bus na podstawie zdarzeń usługi Event Grid.
- Tworzenie aplikacji logiki w celu reagowania na zdarzenia usługi Event Grid

Po utworzeniu usługi Service Bus, usługa Event Grid, usługi Azure Functions i Logic Apps artefaktów, należy wykonać następujące czynności: 

1. Wysyłanie komunikatów do tematu usługi Service Bus. 
2. Sprawdź, czy subskrypcje tematu Odebrano te komunikaty
3. Sprawdź, czy funkcja lub aplikacja logiki dla zdarzenia, który otrzymał zdarzenie. 

## <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus
Postępuj zgodnie z instrukcjami w tym samouczku: [Szybki start: Użyj witryny Azure portal do utworzenia tematu usługi Service Bus i subskrypcji do tematu](service-bus-quickstart-topics-subscriptions-portal.md) do wykonywania następujących zadań:

- Tworzenie **premium** przestrzeni nazw usługi Service Bus. 
- Pobieranie parametrów połączenia. 
- Tworzenie tematu usługi Service Bus.
- Utwórz dwie subskrypcje tematu. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Przygotowanie przykładowej aplikacji w celu wysyłania komunikatów
Komunikat można wysłać do tematu usługi Service Bus za pomocą dowolnej metody. Przykładowy kod na końcu tej procedury przyjęto założenie, że używasz programu Visual Studio 2017.

1. Sklonuj [repozytorium azure-service-bus z usługi GitHub](https://github.com/Azure/azure-service-bus/).
2. W programie Visual Studio przejdź do folderu *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* i otwórz plik *SBEventGridIntegration.sln*.
3. Przejdź do projektu **MessageSender** i wybierz pozycję **Program.cs**.
4. Wypełnij nazwę tematu usługi Service Bus i parametry połączenia, które masz w poprzednim kroku:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Skompiluj i uruchom program ma wysyłać wiadomości do tematu usługi Service Bus. 

## <a name="set-up-a-test-function-on-azure"></a>Konfigurowanie funkcji testowej na platformie Azure 
Przed rozpoczęciem wykonywania całego scenariusza, należy skonfigurować co najmniej małą funkcję testową, które służy do debugowania i obserwowania zdarzenia, które znajdują się w przepływie. Postępuj zgodnie z instrukcjami wyświetlanymi w [tworzenie pierwszej funkcji w witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md) artykuł, aby wykonać następujące zadania: 

1. Tworzenie aplikacji funkcji.
2. Tworzenie funkcji wyzwalanej przez protokół HTTP. 

Następnie wykonaj następujące czynności: 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions V2](#tab/v2)

1. Rozwiń **funkcje** w drzewie wyświetlać i wybierać funkcji. Zastąp kod funkcji następującym kodem: 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. Wybierz polecenie **Zapisz i uruchom**.

    ![Dane wyjściowe aplikacji — funkcja](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Wybierz **Pobierz adres URL funkcji** i zanotuj adres URL. 

    ![Pobierz adres URL funkcji](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions V1](#tab/v1)

1. Konfigurowanie funkcji, należy użyć **V1** wersji: 
    1. Wybierz aplikację funkcji w widoku drzewa i wybierz **ustawień aplikacji funkcji**. 

        ![Ustawienia aplikacji funkcji]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Wybierz **około 1** dla **wersji środowiska uruchomieniowego**. 
2. Rozwiń **funkcje** w drzewie wyświetlać i wybierać funkcji. Zastąp kod funkcji następującym kodem: 

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Wybierz polecenie **Zapisz i uruchom**.

    ![Dane wyjściowe aplikacji — funkcja](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Wybierz **Pobierz adres URL funkcji** i zanotuj adres URL. 

    ![Pobierz adres URL funkcji](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid
W tej sekcji możesz powiązać razem funkcji i przestrzeni nazw usługi Service Bus przy użyciu witryny Azure portal. 

Aby utworzyć subskrypcję usługi Azure Event Grid, wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do obszaru nazw, a następnie w okienku po lewej stronie wybierz **zdarzenia**. Zostanie otwarte okno przestrzeni nazw z dwiema subskrypcjami usługi Event Grid wyświetlonymi w okienku po prawej stronie. 
    
    ![Usługa Service Bus — strona zdarzenia](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Wybierz **+ Subskrypcja zdarzeń** na pasku narzędzi. 
3. Na **Utwórz subskrypcję zdarzeń** wykonaj następujące czynności:
    1. Wprowadź **nazwa** dla subskrypcji. 
    2. Wybierz **Webhook** dla **typ punktu końcowego**. 

        ![Usługa Service Bus — subskrypcję usługi Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Wybrana opcja **wybierz punkt końcowy**, wklej adres URL funkcji, a następnie wybierz **potwierdzenie wyboru**. 

        ![Function - wybierz punkt końcowy](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Przełącz się do **filtry** wprowadź nazwę **pierwszej subskrypcji** do tematu usługi Service Bus utworzonego wcześniej, a następnie wybierz **Utwórz** przycisku. 

        ![Filtr subskrypcji zdarzeń](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Upewnij się, czy widzisz subskrypcji zdarzeń na liście.

    ![Subskrypcja zdarzeń na liście](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Wysyłanie komunikatów do tematu usługi Service Bus
1. Uruchom z .NET C# aplikację, która wysyła komunikaty do tematu usługi Service Bus. 

    ![Dane wyjściowe z aplikacji konsoli](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na stronie aplikacji funkcji platformy Azure, rozwiń węzeł **funkcje**, rozwiń węzeł usługi **funkcji**i wybierz **Monitor**. 

    ![Monitor — funkcja](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Odbieranie komunikatów przy użyciu usługi Azure Functions
W poprzedniej sekcji przeprowadzono prosty scenariusz testowania i debugowania oraz sprawdzono, czy zdarzenia przepływają. 

W tej sekcji dowiesz się, jak odbierać i przetwarzać komunikaty po odebraniu zdarzenia.

### <a name="publish-a-function-from-visual-studio"></a>Publikowanie funkcji w programie Visual Studio
1. W tym samym rozwiązaniu Visual Studio (**SBEventGridIntegration**) który został otwarty, wybierz **ReceiveMessagesOnEvent.cs** w **SBEventGridIntegration** projektu. 
2. Wprowadź parametry połączenia usługi Service Bus w poniższym kodzie:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Pobierz **profil publikowania** dla funkcji:
    1. Wybierz aplikację funkcji. 
    2. Wybierz **Przegląd** kartę, jeśli nie została jeszcze wybrana. 
    3. Wybierz **Pobierz profil publikowania** na pasku narzędzi. 

        ![Pobierz profil funkcji publikowania](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Zapisz plik do folderu projektu. 
4. W programie Visual Studio kliknij prawym przyciskiem myszy element **SBEventGridIntegration** i wybierz pozycję **Publikuj**. 
5. Wybierz *Start** na **Publikuj** strony. 
6. Na **wybierz lokalizację docelową publikowania** strony, wykonaj następujące czynności, wybierz **Importuj profil**. 

    ![Visual Studio — przycisk Importuj profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Wybierz **pliku profilu publikowania** pobrane wcześniej. 
8. Wybierz **Publikuj** na **Publikuj** strony. 

    ![Visual Studio — publikowanie](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Upewnij się, że nowa funkcja platformy Azure **ReceiveMessagesOnEvent**. Jeśli to konieczne, należy odświeżyć stronę. 

    ![Upewnij się, że zostanie utworzona nowa funkcja](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Pobierz adres URL na nową funkcję i zanotuj go. 

### <a name="event-grid-subscription"></a>Subskrypcji usługi Event Grid

1. Usuń istniejącą subskrypcję usługi Event Grid:
    1. Na **usługi Service Bus Namespace** wybierz opcję **zdarzenia** w menu po lewej stronie. 
    2. Wybierz istniejącą subskrypcję zdarzeń. 
    3. Na **subskrypcji zdarzeń** wybierz opcję **Usuń**.
2. Postępuj zgodnie z instrukcjami wyświetlanymi w [łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid](#connect-the-function-and-namespace-via-event-grid) sekcję, aby utworzyć subskrypcję usługi Event Grid przy użyciu nowego adresu URL funkcji.
3. Postępuj zgodnie z instrukcjami w [wysyłać komunikaty do tematu usługi Service Bus](#send-messages-to-the-service-bus-topic) sekcji, aby wysyłać komunikaty do tematu i monitorowania funkcji. 

## <a name="receive-messages-by-using-logic-apps"></a>Odbieranie komunikatów za pomocą usługi Logic Apps
Łączenie aplikacji logiki za pomocą usługi Azure Service Bus i usługi Azure Event Grid, wykonaj następujące czynności:

1. Tworzenie aplikacji logiki w witrynie Azure portal.
    1. Wybierz **+ Utwórz zasób**, wybierz opcję **integracji**, a następnie wybierz pozycję **aplikacji logiki**. 
    2. Na **aplikacji logiki — tworzenie** wpisz **nazwa** dla aplikacji logiki.
    3. Wybierz swoją **subskrypcję** platformy Azure. 
    4. Wybierz **Użyj istniejącej** dla **grupy zasobów**, a następnie wybierz grupę zasobów, użytym do innych zasobów (np. funkcję platformy Azure, przestrzeń nazw magistrali usług), które zostały utworzone wcześniej. 
    5. Wybierz **lokalizacji** dla aplikacji logiki. 
    6. Wybierz **Utwórz** do tworzenia aplikacji logiki. 
2. Na **Projektant aplikacji logiki** wybierz opcję **pusta aplikacja logiki** w obszarze **szablony**. 
3. W Projektancie wykonaj następujące czynności:
    1. Wyszukaj **usługi Event Grid**. 
    2. Wybierz **gdy zasobów wystąpi zdarzenie (wersja zapoznawcza) — usługi Azure Event Grid**. 

        ![Projektant aplikacji logiki — Wybieranie wyzwalacza usługi Event Grid](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Wybierz **Zaloguj**, wprowadź swoje poświadczenia platformy Azure i wybierz **Zezwalaj na dostęp**. 
5. Na **gdy wystąpi zdarzenie zasobu** wykonaj następujące czynności:
    1. Wybierz swoją subskrypcję platformy Azure. 
    2. Aby uzyskać **typ zasobu**, wybierz opcję **Microsoft.ServiceBus.Namespaces**. 
    3. Aby uzyskać **Nazwa zasobu**, wybierz przestrzeń nazw usługi Service Bus. 
    4. Wybierz **dodano nowy parametr**i wybierz **filtr sufiksu**. 
    5. Aby uzyskać **filtr sufiksu**, wprowadź nazwę drugiego subskrypcji tematu usługi Service Bus. 

        ![Projektant aplikacji logiki — konfigurowanie zdarzeń](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Wybierz **+ nowy krok** w projektancie, a następnie wykonaj następujące czynności:
    1. Wyszukaj **Service Bus**.
    2. Wybierz **usługi Service Bus** na liście. 
    3. Wybierz **komunikatów** w **akcje** listy. 
    4. Wybierz **Pobierz komunikaty z subskrypcji tematu (czasowa blokada)**. 

        ![Projektant aplikacji logiki — Akcja wiadomości get](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Wprowadź **nazwa połączenia**. Na przykład: **Pobierz komunikaty z subskrypcji tematu**i wybierz przestrzeń nazw usługi Service Bus. 

        ![Projektant aplikacji logiki — wybierz przestrzeń nazw usługi Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Wybierz **RootManageSharedAccessKey**.

        ![Projektant aplikacji logiki — wybierz klucz dostępu współdzielonego](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Wybierz pozycję **Utwórz**. 
    8. Wybierz temat i subskrypcję. 
    
        ![Projektant aplikacji logiki — wybierz tematu usługi Service Bus i subskrypcji](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Wybierz **+ nowy krok**, i wykonaj następujące czynności: 
    1. Wybierz pozycję **Service Bus**.
    2. Wybierz **Zakończ wiadomość w subskrypcji tematu** z listy akcji. 
    3. Wybierz usługi Service Bus **tematu**.
    4. Wybierz drugą **subskrypcji** do tematu.
    5. Dla **token blokady wiadomości**, wybierz opcję **Token blokady** z **zawartości dynamicznej**. 

        ![Projektant aplikacji logiki — wybierz tematu usługi Service Bus i subskrypcji](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Wybierz **Zapisz** na pasku narzędzi w Projektancie aplikacji logiki można zapisać aplikacji logiki. 
9. Postępuj zgodnie z instrukcjami w [wysyłać komunikaty do tematu usługi Service Bus](#send-messages-to-the-service-bus-topic) sekcji, aby wysyłać komunikaty do tematu. 
10. Przełącz się do **Przegląd** strony aplikacji logiki. Zobaczysz, że aplikacja logiki jest uruchamiany w **Historia przebiegów** wiadomości wysłane.

    ![Projektant aplikacji logiki — uruchomienia aplikacji logiki](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat usługi [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Dowiedz się więcej na temat usługi [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Dowiedz się więcej o [funkcji Logic Apps usługi Azure App Service](https://docs.microsoft.com/azure/logic-apps/).
* Dowiedz się więcej na temat usługi [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
