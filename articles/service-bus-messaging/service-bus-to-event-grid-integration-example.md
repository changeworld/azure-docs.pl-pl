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
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: spelluru
ms.openlocfilehash: 4e1ea3d822c8b032617b7f202f1c176aeb966210
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60333405"
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Przykłady integracji usług Azure Service Bus i Event Grid

W tym artykule przedstawiamy sposób konfigurowania aplikacji logiki i funkcji platformy Azure odbierających komunikaty na podstawie procesu odbierania zdarzenia z usługi Azure Event Grid. Wykonasz następujące czynności:
 
* Utworzenie prostej funkcji testowej platformy Azure na potrzeby debugowania i wyświetlania początkowego przepływu zdarzeń z usługi Event Grid. Wykonaj ten krok niezależnie od tego, czy wykonasz pozostałe.
* Utworzenie funkcji platformy Azure na potrzeby odbierania i przetwarzania komunikatów usługi Azure Service Bus na podstawie zdarzeń usługi Event Grid.
* Korzystanie z funkcji Logic Apps w usłudze Azure App Service.

Na potrzeby tworzonego przykładu przyjęto, że temat usługi Service Bus ma dwie subskrypcje. Przyjęto także, że utworzono subskrypcję usługi Event Grid na potrzeby wysyłania zdarzeń tylko dla jednej subskrypcji usługi Service Bus. 

W ramach przykładu komunikaty są wysyłane do tematu usługi Service Bus, a następnie jest weryfikowane utworzenie zdarzenia dla tej subskrypcji usługi Service Bus. Funkcja lub aplikacja logiki odbiera komunikaty z subskrypcji usługi Service Bus, a następnie przetwarza je.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że zostały wykonane kroki opisane w dwóch następnych sekcjach.

### <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

Utwórz przestrzeń nazw usługi Service Bus w warstwie Premium i temat usługi Service Bus, który ma dwie subskrypcje.

### <a name="send-a-message-to-the-service-bus-topic"></a>Wysyłanie komunikatu do tematu usługi Service Bus

Komunikat można wysłać do tematu usługi Service Bus za pomocą dowolnej metody. W przykładowym kodzie na końcu tej procedury założono, że używasz programu Visual Studio 2017.

1. Sklonuj [repozytorium azure-service-bus z usługi GitHub](https://github.com/Azure/azure-service-bus/).

1. W programie Visual Studio przejdź do folderu *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* i otwórz plik *SBEventGridIntegration.sln*.

1. Przejdź do projektu **MessageSender** i wybierz pozycję **Program.cs**.

   ![8][]

1. Wypełnij nazwę tematu i parametry połączenia, a następnie wykonaj następujący kod aplikacji konsolowej:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Konfigurowanie funkcji testowej

Przed rozpoczęciem wykonywania całego scenariusza skonfiguruj co najmniej małą funkcję testową, której można użyć do debugowania i obserwowania przepływających zdarzeń.

1. W witrynie Azure Portal utwórz nową aplikację usługi Azure Functions. Aby poznać podstawy usługi Azure Functions, zobacz [dokumentację usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/).

1. W nowo utworzonej funkcji wybierz znak plus (+), aby dodać funkcję wyzwalacza HTTP:

    ![2][]
    
    Zostanie otwarte okno **Szybko rozpocznij pracę przy użyciu gotowej funkcji**.

    ![3][]

1. Wybierz przycisk **Element webhook i interfejs API**, wybierz pozycję **CSharp**, a następnie pozycję **Utwórz tę funkcję**.
 
1. Do funkcji wklej następujący kod:

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

1. Wybierz polecenie **Zapisz i uruchom**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid

W tej sekcji funkcja jest wiązana z przestrzenią nazw usługi Service Bus. W tym przykładzie użyj witryny Azure Portal. Opis sposobu użycia programu PowerShell lub interfejsu wiersza polecenia platformy Azure do wykonania tej procedury znajduje się w artykule [Omówienie integracji usług Azure Service Bus i Event Grid](service-bus-to-event-grid-integration-concept.md).

Aby utworzyć subskrypcję usługi Azure Event Grid, wykonaj następujące czynności:
1. W witrynie Azure Portal przejdź do przestrzeni nazw, a następnie w okienku po lewej stronie wybierz pozycję **Event Grid**.  
    Zostanie otwarte okno przestrzeni nazw z dwiema subskrypcjami usługi Event Grid wyświetlonymi w okienku po prawej stronie.

    ![20][]

1. Wybierz pozycję **Subskrypcja zdarzeń**.  
    Zostanie otwarte okno **Subskrypcja zdarzeń**. Poniższy obrazek przedstawia formularz subskrypcji funkcji lub elementu webhook platformy Azure bez zastosowanych filtrów.

    ![21][]

1. Wypełnij formularz tak jak pokazano, pamiętając o określeniu odpowiedniego filtru w polu **Filtr sufiksu**.

1. Wybierz pozycję **Utwórz**.

1. Wyślij komunikat do tematu usługi Service Bus tak jak opisano w sekcji „Wymagania wstępne”, a następnie za pośrednictwem funkcji monitorowania usługi Azure Functions zweryfikuj, czy zdarzenia przepływają.

Następny krok to powiązanie funkcji i przestrzeni nazw usługi Service Bus. W tym przykładzie użyj witryny Azure Portal. Opis sposobu użycia programu PowerShell lub interfejsu wiersza polecenia platformy Azure do wykonania tego kroku zawiera temat [Omówienie integracji usług Azure Service Bus i Event Grid](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Odbieranie komunikatów przy użyciu usługi Azure Functions

W poprzedniej sekcji przeprowadzono prosty scenariusz testowania i debugowania oraz sprawdzono, czy zdarzenia przepływają. 

W tej sekcji dowiesz się, jak odbierać i przetwarzać komunikaty po odebraniu zdarzenia.

Dodasz funkcję platformy Azure jak pokazano w następującym przykładzie, ponieważ funkcje usługi Service Bus w ramach usługi Azure Functions jeszcze natywnie nie obsługują nowej integracji usługi Event Grid.

1. W rozwiązaniu programu Visual Studio otwartym w wymaganiach wstępnych wybierz plik **ReceiveMessagesOnEvent.cs**. 

    ![10][]

1. Podaj parametry połączenia w następującym kodzie:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

1. W witrynie Azure Portal pobierz profil publikowania dla funkcji platformy Azure utworzonej w sekcji „Konfigurowanie funkcji testowej”.

    ![11][]

1. W programie Visual Studio kliknij prawym przyciskiem myszy element **SBEventGridIntegration** i wybierz pozycję **Publikuj**. 

1. W okienku **Publikowanie** dla profilu publikowania pobranego wcześniej wybierz polecenie **Importuj profil**, a następnie **Publikuj**.

    ![12][]

1. Po opublikowaniu nowej funkcji platformy Azure utwórz nową subskrypcję usługi Azure Event Grid wskazującą na nową funkcję platformy Azure.  
    Upewnij się, że w polu **Kończy się na** zastosowano poprawny filtr, którego wartością powinna być nazwa subskrypcji usługi Service Bus.

1. Wyślij komunikat do tematu usługi Azure Service Bus utworzonego wcześniej, a następnie monitoruj dziennik usługi Azure Functions w witrynie Azure Portal w celu upewnienia się, że zdarzenia przepływają i komunikaty są odbierane.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Odbieranie komunikatów za pomocą usługi Logic Apps

Połącz aplikację logiki z usługami Azure Service Bus i Azure Event Grid w następujący sposób:

1. Utwórz nową aplikację logiki w witrynie Azure Portal i wybierz pozycję **Event Grid** jako akcję uruchamiania.

    ![13][]

    Zostanie otwarte okno projektanta usługi Logic Apps.

    ![14][]

1. Dodaj informacje w następujący sposób:

    a. W polu **Nazwa zasobu** podaj własną nazwę przestrzeni nazw. 

    b. W obszarze **Opcje zaawansowane** w polu **Filtr sufiksu** podaj filtr dla subskrypcji.

1. Następnie dodaj akcję odbierania usługi Service Bus, aby odbierać komunikaty z subskrypcji tematu.  
    Ostateczna akcja jest pokazana na poniższym obrazku:

    ![15][]

1. Dodaj ukończone zdarzenie jak pokazano na poniższym obrazku:

    ![16][]

1. Zapisz aplikację logiki i wyślij komunikat do tematu usługi Service Bus jak wspomniano w sekcji „Wymagania wstępne”.  
    Obserwuj wykonywanie aplikacji logiki. Aby wyświetlić więcej danych dotyczących wykonania, wybierz pozycję **Przegląd**, a następnie wyświetl dane w polu **Historia przebiegów**.

    ![17][]

    ![18][]

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
