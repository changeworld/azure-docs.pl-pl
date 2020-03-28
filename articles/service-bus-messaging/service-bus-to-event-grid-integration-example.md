---
title: 'Samouczek: przykłady integracji usługi Azure Service Bus to Event Grid'
description: 'Samouczek: W tym artykule przedstawiono przykłady obsługi wiadomości usługi Service Bus i integracji z siatką zdarzeń.'
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
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: fef325b67c38eda09a05dac9d74bd5b97df164cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067763"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Samouczek: odpowiadanie na zdarzenia usługi Azure Service Bus odebrane za pośrednictwem usługi Azure Event Grid przy użyciu funkcji platformy Azure i aplikacji logiki azure
W tym samouczku dowiesz się, jak reagować na zdarzenia usługi Azure Service Bus, które są odbierane za pośrednictwem usługi Azure Event Grid przy użyciu funkcji azure i usługi Azure Logic Apps. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Service Bus
> * Przygotowanie przykładowej aplikacji do wysyłania wiadomości
> * Konfigurowanie funkcji testowej na platformie Azure
> * Łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid
> * Wysyłanie wiadomości do tematu Usługi Service Bus
> * Odbieranie komunikatów przy użyciu usługi Azure Functions
> * Odbieranie komunikatów za pomocą usługi Logic Apps

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że zainstalowano następujące elementy:

- [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](https://www.visualstudio.com/vs) lub nowszy.
- [Zestaw NET Core SDK](https://www.microsoft.com/net/download/windows), wersja 2.0 lub nowsza.

## <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus
Postępuj zgodnie z instrukcjami w tym [samouczku: Szybki start: Użyj witryny Azure Portal, aby utworzyć temat usługi Service Bus i subskrypcje tematu, aby](service-bus-quickstart-topics-subscriptions-portal.md) wykonać następujące zadania:

- Utwórz obszar nazw **usługi Premium** Service Bus. 
- Pobierz ciąg połączenia. 
- Tworzenie tematu usługi Service Bus.
- Utwórz dwie subskrypcje tematu. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Przygotowanie przykładowej aplikacji do wysyłania wiadomości
Komunikat można wysłać do tematu usługi Service Bus za pomocą dowolnej metody. Przykładowy kod na końcu tej procedury zakłada, że używasz programu Visual Studio 2017.

1. Sklonuj [repozytorium azure-service-bus z usługi GitHub](https://github.com/Azure/azure-service-bus/).
2. W programie Visual Studio przejdź do folderu *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* i otwórz plik *SBEventGridIntegration.sln*.
3. Przejdź do projektu **MessageSender** i wybierz pozycję **Program.cs**.
4. Wypełnij nazwę tematu usługi Service Bus i parametry połączenia, które otrzymałeś z poprzedniego kroku:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Skompiluj i uruchom program, aby wysłać wiadomości testowe do tematu usługi Service Bus. 

## <a name="set-up-a-test-function-on-azure"></a>Konfigurowanie funkcji testowej na platformie Azure 
Przed rozpoczęciem pracy nad całym scenariuszem należy skonfigurować co najmniej małą funkcję testową, której można użyć do debugowania i obserwowania zdarzeń, które są przepływające. Postępuj zgodnie z instrukcjami w [artykule Tworzenie pierwszej funkcji w witrynie Azure Portal,](../azure-functions/functions-create-first-azure-function.md) aby wykonać następujące zadania: 

1. Utwórz aplikację funkcyjną.
2. Utwórz funkcję wyzwalaną http. 

Następnie wykonaj następujące czynności: 


# <a name="azure-functions-v2"></a>[Funkcje platformy Azure w wersji 2](#tab/v2)

1. Rozwiń **funkcje** w widoku drzewa i wybierz funkcję. Zastąp kod funkcji następującym kodem: 

    ```csharp
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

    ![Wyjście aplikacji funkcji](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Wybierz **pozycję Pobierz adres URL funkcji** i zanotuj adres URL. 

    ![Pobierz adres URL funkcji](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1"></a>[Funkcje platformy Azure w wersji 1](#tab/v1)

1. Skonfiguruj funkcję do używania wersji **V1:** 
    1. Wybierz aplikację funkcji w widoku drzewa i wybierz pozycję **Ustawienia aplikacji Funkcji**. 

        ![Ustawienia aplikacji funkcji]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Wybierz **~1** dla **wersji runtime**. 
2. Rozwiń **funkcje** w widoku drzewa i wybierz funkcję. Zastąp kod funkcji następującym kodem: 

    ```csharp
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

    ![Wyjście aplikacji funkcji](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Wybierz **pozycję Pobierz adres URL funkcji** i zanotuj adres URL. 

    ![Pobierz adres URL funkcji](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid
W tej sekcji należy powiązać z funkcją i obszaru nazw usługi Service Bus przy użyciu witryny Azure portal. 

Aby utworzyć subskrypcję usługi Azure Event Grid, wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do obszaru nazw, a następnie w lewym okienku wybierz pozycję **Zdarzenia**. Zostanie otwarte okno przestrzeni nazw z dwiema subskrypcjami usługi Event Grid wyświetlonymi w okienku po prawej stronie. 
    
    ![Usługa Service Bus — strona z wydarzeniami](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Wybierz **+ subskrypcję zdarzeń** na pasku narzędzi. 
3. Na stronie **Tworzenie subskrypcji zdarzeń** wykonaj następujące czynności:
    1. Wprowadź **nazwę** subskrypcji. 
    2. Wybierz **pozycję Web Hook** dla typu punktu **końcowego**. 

        ![Usługa Service Bus — subskrypcja usługi Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Wybierz **pozycję Wybierz punkt końcowy**, wklej adres URL funkcji, a następnie wybierz pozycję **Potwierdź zaznaczenie**. 

        ![Funkcja - wybierz punkt końcowy](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Przełącz się na kartę **Filtry,** wprowadź nazwę **pierwszej subskrypcji** w temacie Usługi Service Bus utworzonej wcześniej, a następnie wybierz przycisk **Utwórz.** 

        ![Filtr subskrypcji zdarzeń](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Upewnij się, że na liście jest widoczna subskrypcja zdarzenia.

    ![Subskrypcja zdarzeń na liście](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Wysyłanie wiadomości do tematu Usługi Service Bus
1. Uruchom aplikację .NET C#, która wysyła komunikaty do tematu usługi Service Bus. 

    ![Dane wyjściowe aplikacji konsoli](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Na stronie aplikacji funkcji platformy Azure rozwiń węzeł **Funkcje**, rozwiń **funkcję**i wybierz **pozycję Monitoruj**. 

    ![Monitor, funkcja](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Odbieranie komunikatów przy użyciu usługi Azure Functions
W poprzedniej sekcji przeprowadzono prosty scenariusz testowania i debugowania oraz sprawdzono, czy zdarzenia przepływają. 

W tej sekcji dowiesz się, jak odbierać i przetwarzać komunikaty po odebraniu zdarzenia.

### <a name="publish-a-function-from-visual-studio"></a>Publikowanie funkcji w programie Visual Studio
1. W tym samym rozwiązaniu programu Visual Studio (**SBEventGridIntegration),** które zostało otwarte, wybierz **ReceiveMessagesOnEvent.cs** w projekcie **SBEventGridIntegration.** 
2. Wprowadź parametry połączenia usługi Service Bus w następującym kodzie:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Pobierz **profil publikowania** dla funkcji:
    1. Wybierz aplikację funkcji. 
    2. Wybierz kartę **Przegląd,** jeśli nie jest jeszcze zaznaczona. 
    3. Wybierz **pozycję Pobierz profil publikowania** na pasku narzędzi. 

        ![Pobierz profil publikowania dla tej funkcji](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Zapisz plik w folderze projektu. 
4. W programie Visual Studio kliknij prawym przyciskiem myszy element **SBEventGridIntegration** i wybierz pozycję **Publikuj**. 
5. Wybierz **pozycję Start** na stronie **Publikowania.** 
6. Na stronie **Wybierz miejsce docelowe publikowania** wykonaj następujące czynności, wybierając pozycję **Importuj profil**. 

    ![Visual Studio — przycisk Importuj profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Wybierz **pobrany** wcześniej plik profilu publikowania. 
8. Wybierz **pozycję Publikuj** na stronie **Publikowania.** 

    ![Visual Studio — publikowanie](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Upewnij się, że widzisz nową funkcję Platformy Azure **ReceiveMessagesOnEvent**. W razie potrzeby odśwież stronę. 

    ![Upewnij się, że nowa funkcja została utworzona](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Pobierz adres URL do nowej funkcji i zanotuj go. 

### <a name="event-grid-subscription"></a>Subskrypcja usługi Event Grid

1. Usuń istniejącą subskrypcję usługi Event Grid:
    1. Na stronie **Obszar nazw usługi Service Bus** wybierz pozycję **Zdarzenia** w menu po lewej stronie. 
    2. Wybierz istniejącą subskrypcję zdarzeń. 
    3. Na stronie **Subskrypcja zdarzeń** wybierz pozycję **Usuń**.
2. Postępuj zgodnie z instrukcjami w sekcji [Połącz funkcję i obszar nazw za pośrednictwem siatki zdarzeń,](#connect-the-function-and-namespace-via-event-grid) aby utworzyć subskrypcję usługi Event Grid przy użyciu nowego adresu URL funkcji.
3. Postępuj zgodnie z instrukcjami w [sekcji Wyślij wiadomości do tematu usługi Service Bus,](#send-messages-to-the-service-bus-topic) aby wysyłać wiadomości do tematu i monitorować tę funkcję. 

## <a name="receive-messages-by-using-logic-apps"></a>Odbieranie komunikatów za pomocą usługi Logic Apps
Połącz aplikację logiki z usługą Azure Service Bus i usługą Azure Event Grid, wykonując następujące kroki:

1. Tworzenie aplikacji logiki w witrynie Azure portal.
    1. Wybierz **+ Utwórz zasób**, wybierz **pozycję Integracja**, a następnie wybierz pozycję **Aplikacja logiki**. 
    2. Na aplikacji **logiki — tworzenie** strony wprowadź **nazwę** aplikacji logiki.
    3. Wybierz swoją **subskrypcję** platformy Azure. 
    4. Wybierz **pozycję Użyj istniejącego** dla **grupy zasobów**i wybierz grupę zasobów, która została użyta dla innych zasobów (takich jak funkcja platformy Azure, obszar nazw usługi Service Bus utworzony wcześniej. 
    5. Wybierz **lokalizację** dla aplikacji logiki. 
    6. Wybierz **pozycję Utwórz,** aby utworzyć aplikację logiki. 
2. Na stronie **Projektant aplikacji logiki** wybierz pozycję **Pusta aplikacja logiki** w obszarze **Szablony**. 
3. Na projektanta wykonaj następujące kroki:
    1. Wyszukaj **siatkę zdarzeń**. 
    2. Wybierz **pozycję, gdy wystąpi zdarzenie zasobu (wersja zapoznawcza) — usługa Azure Event Grid**. 

        ![Projektant aplikacji logiki — wybieranie wyzwalacza siatki zdarzeń](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Wybierz pozycję **Zaloguj się,** wprowadź poświadczenia platformy Azure i wybierz pozycję **Zezwalaj na dostęp**. 
5. Na stronie **Gdy wystąpi zdarzenie zasobu** wykonaj następujące czynności:
    1. Wybierz swoją subskrypcję platformy Azure. 
    2. W **obszarze Typ zasobu**wybierz pozycję **Microsoft.ServiceBus.Namespaces**. 
    3. W obszarze **Nazwa zasobu**wybierz obszar nazw usługi Service Bus. 
    4. Wybierz **pozycję Dodaj nowy parametr**i wybierz opcję Filtr **sufiksu**. 
    5. W przypadku **filtru sufiksów**wprowadź nazwę drugiej subskrypcji tematu usługi Service Bus. 
        ![Logic Apps Designer — konfigurowanie zdarzenia](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Wybierz **+ Nowy krok** w projektancie i wykonaj następujące kroki:
    1. Wyszukaj **usługę Service Bus**.
    2. Wybierz **pozycję Usługa Service Bus** na liście. 
    3. Wybierz pozycję **Pobierz wiadomości** na liście **Akcje.** 
    4. Wybierz **pozycję Pobierz wiadomości z subskrypcji tematu (zaglądanie do bloku)**. 

        ![Logic Apps Designer - pobierz akcję wiadomości](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Wprowadź **nazwę połączenia**. Na przykład: **Pobierz wiadomości z subskrypcji tematu**i wybierz obszar nazw usługi Service Bus. 

        ![Projektant aplikacji logiki — wybierz obszar nazw magistrali usług](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Wybierz pozycję **RootManageSharedAccessKey**.

        ![Projektant aplikacji logiki — wybierz klucz dostępu udostępnionego](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Wybierz **pozycję Utwórz**. 
    8. Wybierz temat i subskrypcję. 
    
        ![Logic Apps Designer — wybierz temat i subskrypcję usługi Service Bus](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Wybierz **+ Nowy krok**i wykonaj następujące czynności: 
    1. Wybierz pozycję **Service Bus**.
    2. Wybierz **pozycję Uzupełnij wiadomość w subskrypcji tematu** z listy akcji. 
    3. Wybierz **temat**usługi Service Bus .
    4. Wybierz drugą **subskrypcję** tematu.
    5. W przypadku **tokenu blokady wiadomości**wybierz pozycję **Zablokuj token** z **zawartości dynamicznej**. 

        ![Logic Apps Designer — wybierz temat i subskrypcję usługi Service Bus](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Wybierz **pozycję Zapisz** na pasku narzędzi w Projektancie aplikacji logiki, aby zapisać aplikację logiki. 
9. Postępuj zgodnie z instrukcjami w [sekcji Wysyłanie wiadomości do tematu usługi Service Bus,](#send-messages-to-the-service-bus-topic) aby wysyłać wiadomości do tematu. 
10. Przełącz się do strony **Przegląd** aplikacji logiki. Zostanie wyświetlna aplikacja logiki działa w **historii uruchamia** dla wiadomości wysłanych.

    ![Logic Apps Designer — aplikacja logiki jest uruchamiana](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Następne kroki

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
