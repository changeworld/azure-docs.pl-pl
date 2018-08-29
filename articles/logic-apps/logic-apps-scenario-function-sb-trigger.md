---
title: Scenariusz — wyzwalacz aplikacji logiki za pomocą usługi Azure Functions i Azure Service Bus | Dokumentacja firmy Microsoft
description: Tworzenie funkcji, które mogą powodować aplikacji logiki przy użyciu usługi Azure Functions i Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 8d6f2ecaec7bf7ae7e4415ccd60fc6ec3ff487f3
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126176"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Scenariusz: Wyzwalacza aplikacji logiki za pomocą usługi Azure Functions i Azure Service Bus

Usługa Azure Functions umożliwia tworzenie wyzwalacza aplikacji logiki, gdy należy wdrożyć odbiornika długotrwałych lub zadania. Można na przykład utworzyć funkcję, która nasłuchuje kolejki i natychmiast wyzwolenie aplikacji logiki jako wyzwalacz wypychania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Aby można było utworzyć funkcję platformy Azure [tworzenie aplikacji funkcji](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

W tym przykładzie istnieje funkcja uruchomione dla każdej aplikacji logiki, który ma być wyzwalane. Najpierw należy utworzyć aplikację logiki, która zawiera wyzwalacz żądania HTTP. Po każdym odebraniu komunikatu w kolejce, funkcja wywołuje tego punktu końcowego.  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Tworzenie pustej aplikacji logiki. 

   Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [Szybki Start: tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W polu wyszukiwania wpisz "żądanie http". W obszarze listy wyzwalaczy wybierz następujący wyzwalacz: **zostanie odebrane żądanie po HTTP**

   ![Wybierz wyzwalacz](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. Aby uzyskać **żądania** wyzwalacza, można opcjonalnie wprowadzić schematu JSON do użytku z komunikatu w kolejce. Schematów JSON pomóc w Projektancie aplikacji logiki, poznać strukturę danych wejściowych danych i sprawia, że dane wyjściowe łatwiejsze do wyboru w całym przepływie pracy. 

   Aby określić schemat, wprowadź schemat w **schemat JSON treści żądania** polu, na przykład: 

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

1. Dodaj inne akcje, które ma być wykonywana po otrzymaniu komunikatu w kolejce. 

   Na przykład możesz wysłać wiadomość e-mail za pośrednictwem łącznika Office 365 Outlook.

1. Zapisz aplikację logiki, która generuje adres URL wywołania zwrotnego dla wyzwalacza w tej aplikacji logiki. Ten adres URL, który pojawia się w **HTTP POST URL** właściwości.

   ![Adres URL wygenerowanego wywołania zwrotnego dla wyzwalacza](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Utwórz funkcję platformy Azure

Następnie należy utworzyć funkcję, która działa jako wyzwalacz i nasłuchuje w kolejce. 

1. W witrynie Azure portal Otwórz i rozwiń aplikację funkcji, jeśli nie już otwarty. 

1. W obszarze nazwę swojej aplikacji funkcji, rozwiń węzeł **funkcji**. Na **funkcje** okienku wybierz **nową funkcję**. Wybierz ten szablon: **wyzwalacz kolejki usługi Service Bus — C#**
   
   ![Wybierz portal usługi Azure Functions](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Podaj nazwę dla wyzwalacza, a następnie skonfiguruj połączenie z kolejki usługi Service Bus, która korzysta z zestawu SDK Service Bus dla usługi Azure `OnMessageReceive()` odbiornika.

1. Napisz podstawowej funkcji do wywołania logiki utworzonej wcześniej punktu końcowego aplikacji komunikatu w kolejce jako wyzwalacz, na przykład przy użyciu: 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

   W tym przykładzie użyto `application/json` typ zawartości komunikatu, ale można zmienić tego typu zgodnie z potrzebami.

1. Aby przetestować tę funkcję, należy dodać komunikatu w kolejce przy użyciu narzędzia, takie jak [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer). 

   Wyzwalacze aplikacji logiki od razu w przypadku, gdy funkcja otrzyma komunikat.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

