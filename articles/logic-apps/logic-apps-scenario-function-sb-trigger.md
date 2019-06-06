---
title: Wywoływanie lub wyzwalacza aplikacji logiki za pomocą usługi Azure Functions i Azure Service Bus
description: Tworzenie usługi Azure functions, takich jak wywoływanie lub wyzwalacza aplikacji logiki przy użyciu usługi Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494978"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Wywoływanie lub wyzwalacza aplikacji logiki przy użyciu usługi Azure Functions i Azure Service Bus

Możesz użyć [usługi Azure Functions](../azure-functions/functions-overview.md) wyzwolenie aplikacji logiki, gdy należy wdrożyć odbiornika długotrwałych lub zadania. Na przykład można utworzyć funkcję platformy Azure, która nasłuchuje w sprawie [usługi Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) kolejki i natychmiast generowane aplikację logiki jako wyzwalacz wypychania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* W przypadku przestrzeni nazw usługi Azure Service Bus. Jeśli nie masz obszaru nazw, a [najpierw utworzyć przestrzeń nazw](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Aplikacja funkcji platformy Azure, który jest kontenerem dla funkcji platformy Azure. Jeśli masz aplikację funkcji [najpierw utworzyć aplikację funkcji](../azure-functions/functions-create-first-azure-function.md)i upewnij się, że wybrano jako stosu środowiska uruchomieniowego .NET.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

W tym scenariuszu istnieje funkcja uruchamiania poszczególnych aplikacji logiki, która ma zostać wyzwolone. Najpierw należy utworzyć aplikację logiki, która rozpoczyna się od wyzwalacza żądania HTTP. Po każdym odebraniu komunikatu w kolejce, funkcja wywołuje tego punktu końcowego.  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Tworzenie pustej aplikacji logiki.

   Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W polu wyszukiwania wpisz "żądanie http". Z listy wyzwalaczy wybierz następujący wyzwalacz: **Po odebraniu żądania HTTP**

   ![Wybierz wyzwalacz](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Za pomocą wyzwalacza żądania można opcjonalnie wprowadzić schematu JSON za pomocą komunikatu w kolejce. Schematów JSON pomóc w Projektancie aplikacji logiki, poznać strukturę dla danych wejściowych i ułatwić dane wyjściowe do użycia w przepływie pracy.

1. Aby określić schemat, wprowadź schemat w **schemat JSON treści żądania** polu, na przykład:

   ![Określ schemat JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Jeśli nie masz schematu, ale masz przykładowy ładunek w formacie JSON, można wygenerować schematu z tym ładunku.

   1. W wyzwalaczu żądania wybierz **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W obszarze **wpisz lub wklej przykładowy ładunek JSON**, wprowadź przykładowy ładunek, a następnie wybierz **gotowe**.

      ![Wprowadź przykładowy ładunek](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

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

1. Dodaj inne czynności, które mają być uruchamiane po otrzymaniu komunikatu w kolejce.

   Na przykład możesz wysłać wiadomość e-mail za pośrednictwem łącznika Office 365 Outlook.

1. Zapisz aplikację logiki, która generuje adres URL wywołania zwrotnego dla wyzwalacza w tej aplikacji logiki. Później możesz użyć tego adresu URL wywołania zwrotnego w kodzie wyzwalacz kolejki usługi Azure Service Bus.

   Wywołania zwrotnego adresu URL, który pojawia się w **HTTP POST URL** właściwości.

   ![Adres URL wygenerowanego wywołania zwrotnego dla wyzwalacza](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Utwórz funkcję platformy Azure

Następnie należy utworzyć funkcję, która działa jako wyzwalacz i nasłuchuje w kolejce.

1. W witrynie Azure portal Otwórz i rozwiń aplikację funkcji, jeśli nie już otwarty. 

1. W obszarze nazwę swojej aplikacji funkcji, rozwiń węzeł **funkcji**. Na **funkcje** okienku wybierz **nową funkcję**.

   ![Rozwiń "Funkcji" i wybierz polecenie "Nowa funkcja"](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Wybierz ten szablon na podstawie tego, czy utworzyć nową aplikację funkcji, w którym .NET jest wybrany jako stosu środowiska uruchomieniowego lub używasz istniejącej aplikacji funkcji.

   * W przypadku nowych aplikacji funkcji wybierz ten szablon: **Wyzwalacz kolejki usługi Service Bus**

     ![Wybierz szablon dla nowej aplikacji funkcji](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Dla istniejących aplikacji funkcji wybierz ten szablon: **Wyzwalacz kolejki usługi Service Bus —C#**

     ![Wybierz szablon dla istniejącej aplikacji funkcji](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Na **wyzwalacz kolejki usługi Azure Service Bus** okienku, podaj nazwę dla wyzwalacza i konfigurowanie **połączenie usługi Service Bus** dla tej kolejki, który używa zestawu SDK platformy Azure usługi Service Bus `OnMessageReceive()` odbiornika i wybierz polecenie **Tworzenie**.

1. Napisz podstawowej funkcji do wywołania logiki utworzonej wcześniej punktu końcowego aplikacji za pomocą komunikatu w kolejce jako wyzwalacz. W tym przykładzie użyto `application/json` typ zawartości komunikatu, ale można zmienić tego typu zgodnie z potrzebami. Jeśli to możliwe można ponownie użyć wystąpienia klientów HTTP. Aby uzyskać więcej informacji, zobacz [Zarządzanie połączeniami w usłudze Azure Functions](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. Aby przetestować tę funkcję, należy dodać komunikatu w kolejce przy użyciu narzędzia, takie jak [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

   Wyzwalacze aplikacji logiki od razu w przypadku, gdy funkcja otrzyma komunikat.

## <a name="next-steps"></a>Kolejne kroki

[Wywoływanie, wyzwalanie lub zagnieżdżanie przepływów pracy za pomocą punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md)