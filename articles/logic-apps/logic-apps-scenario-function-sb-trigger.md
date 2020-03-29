---
title: Wywoływanie aplikacji logiki za pomocą usługi Azure Functions
description: Tworzenie funkcji platformy Azure, które wywołują lub wyzwalają aplikacje logiki, nasłuchiwanie usługi Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428702"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Wywoływanie lub wyzwalanie aplikacji logiki przy użyciu usług Azure Functions i usługi Azure Service Bus

Usługi [azure usługi](../azure-functions/functions-overview.md) wyzwolić aplikację logiki, gdy trzeba wdrożyć odbiornika długo działającego lub zadania. Na przykład można utworzyć funkcję platformy Azure, która nasłuchuje w kolejce [usługi Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) i natychmiast uruchamia aplikację logiki jako wyzwalacz wypychania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Obszar nazw usługi Azure Service Bus. Jeśli nie masz obszaru nazw, [najpierw utwórz obszar nazw](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Aplikacja funkcji platformy Azure, która jest kontenerem dla funkcji platformy Azure. Jeśli nie masz aplikacji funkcji, [najpierw utwórz aplikację funkcji](../azure-functions/functions-create-first-azure-function.md)i upewnij się, że wybierzesz .NET jako stos środowiska wykonawczego.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

W tym scenariuszu masz funkcję z uruchomioną każdą aplikację logiki, którą chcesz wyzwolić. Najpierw utwórz aplikację logiki, która rozpoczyna się od wyzwalacza żądania HTTP. Funkcja wywołuje ten punkt końcowy za każdym razem, gdy zostanie odebrany komunikat kolejki.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i utwórz pustą aplikację logiki.

   Jeśli jesteś nowym użytkownikem aplikacji logiki, przejrzyj [przewodnik Szybki start: utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W polu wyszukiwania wpisz `http request`. Z listy wyzwalaczy wybierz wyzwalacz **po odebraniu żądania HTTP.**

   ![Wybierz wyzwalacz](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Za pomocą wyzwalacza żądania można opcjonalnie wprowadzić schemat JSON do użycia z komunikatem kolejki. Schematy JSON pomagają projektantowi aplikacji logiki zrozumieć strukturę danych wejściowych i ułatwić korzystanie z danych wyjściowych w przepływie pracy.

1. Aby określić schemat, wprowadź schemat w polu **Schemat JSON treści żądania,** na przykład:

   ![Określanie schematu JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Jeśli nie masz schematu, ale masz ładunek próbki w formacie JSON, można wygenerować schemat z tego ładunku.

   1. W wyzwalaczu Żądanie wybierz opcję **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W **obszarze Wprowadzanie lub wklejanie przykładowego ładunku JSON**wprowadź przykładową ładowność, a następnie wybierz pozycję **Gotowe**.

      ![Wprowadź ładunek próbki](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Ten przykładowy ładunek generuje ten schemat, który pojawia się w wyzwalaczu:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Dodaj inne akcje, które chcesz uruchomić po otrzymaniu wiadomości kolejki.

   Na przykład można wysłać wiadomość e-mail za pomocą łącznika programu Outlook usługi Office 365.

1. Zapisz aplikację logiki, która generuje adres URL wywołania zwrotnego dla wyzwalacza w tej aplikacji logiki. Później użyj tego adresu URL wywołania zwrotnego w kodzie wyzwalacza kolejki usługi Azure Service Bus.

   Adres URL wywołania zwrotnego pojawi się we właściwości **ADRESU URL HTTP POST.**

   ![Wygenerowany adres URL wywołania zwrotnego dla wyzwalacza](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Tworzenie funkcji platformy Azure

Następnie utwórz funkcję, która działa jako wyzwalacz i nasłuchuje kolejki.

1. W witrynie Azure portal otwórz i rozwiń aplikację funkcji, jeśli nie jest jeszcze otwarta. 

1. Pod nazwą aplikacji funkcji rozwiń pozycję **Funkcje**. W okienku **Funkcje** wybierz pozycję **Nowa funkcja**.

   ![Rozwiń "Funkcje" i wybierz "Nowa funkcja"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Wybierz ten szablon na podstawie tego, czy utworzono nową aplikację funkcji, w której wybrano opcję .NET jako stos środowiska uruchomieniowego, czy używasz istniejącej aplikacji funkcji.

   * W przypadku nowych aplikacji funkcyjnych wybierz ten szablon: **Wyzwalacz kolejki usługi Service Bus**

     ![Wybierz szablon dla nowej aplikacji funkcji](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * W przypadku istniejącej aplikacji funkcji wybierz ten szablon: **Wyzwalacz kolejki usługi Service Bus — C#**

     ![Wybierz szablon dla istniejącej aplikacji funkcji](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. W okienku **wyzwalania kolejki usługi Azure Service Bus** podaj nazwę wyzwalacza i skonfiguruj połączenie **magistrali usług Service Bus** dla kolejki, która używa odbiornika zestawu SDK `OnMessageReceive()` usługi Azure Service Bus, a następnie wybierz pozycję **Utwórz**.

1. Napisz podstawową funkcję, aby wywołać poprzednio utworzony punkt końcowy aplikacji logiki przy użyciu komunikatu kolejki jako wyzwalacza. Zanim napiszesz swoją funkcję, zapoznaj się z tymi zagadnieniami:

   * W tym przykładzie `application/json` użyto typu zawartości wiadomości, ale można zmienić ten typ w razie potrzeby.
   
   * Ze względu na możliwe równocześnie uruchomione funkcje, duże woluminy lub duże obciążenia, `using` należy unikać tworzenia wystąpienia [klasy HTTPClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) z instrukcją i bezpośrednio tworzenia wystąpień HTTPClient na żądanie. Aby uzyskać więcej informacji, zobacz [Używanie funkcji HttpClientFactory do implementowania odpornych żądań HTTP.](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)
   
   * Jeśli to możliwe, ponownie użyć wystąpienia klientów HTTP. Aby uzyskać więcej informacji, zobacz [Zarządzanie połączeniami w usłudze Azure Functions](../azure-functions/manage-connections.md).

   W tym przykładzie [ `Task.Run` ](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) użyto metody w trybie [asynchroniczne.](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne za pomocą asynchanu i poczekaj](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Aby przetestować tę funkcję, dodaj komunikat kolejki za pomocą narzędzia, takiego jak [Eksplorator usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

   Aplikacja logiki wyzwala natychmiast po odebraniu wiadomości przez funkcję.

## <a name="next-steps"></a>Następne kroki

* [Przepływy pracy wywoływania, wyzwalania lub zagnieżdżania przy użyciu punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md)
