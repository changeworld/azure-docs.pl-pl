---
title: Scenariusz — aplikacje logiki wyzwalacza z funkcjami platformy Azure i usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Tworzy funkcję, aby uruchomić aplikację logiki, używając funkcji platformy Azure i usługi Azure Service Bus
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 6bc845e4ec329d308ed87770d0dec6a7d5e447c7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030957"
---
# <a name="scenario-trigger-a-logic-app-with-azure-functions-and-azure-service-bus"></a>Scenariusz: Wyzwolenia aplikacji logiki z funkcjami platformy Azure i usługi Azure Service Bus

Azure Functions umożliwia tworzenie wyzwalacza dla aplikacji logiki, gdy należy wdrożyć odbiornika długotrwałe lub zadania. Można na przykład tworzy funkcję, która nasłuchuje kolejki i natychmiast uruchomić aplikację logiki, jako wyzwalacz wypychania.

## <a name="build-the-logic-app"></a>Tworzenie aplikacji logiki
W tym przykładzie należy funkcji uruchomione dla każdej aplikacji logiki, który ma być wywołany. Najpierw należy utworzyć aplikację logiki, która zawiera wyzwalacz żądania HTTP. Funkcja wywołań tego punktu końcowego przy każdym odebraniu komunikatu w kolejce.  

1. Tworzenie aplikacji logiki.
2. Wybierz **ręczny — po odebraniu żądania HTTP** wyzwalacza.
   Opcjonalnie można określić schematu JSON do użycia z komunikat z kolejki przy użyciu narzędzia, takiego jak [jsonschema.net](http://jsonschema.net). Wklej schematu w wyzwalaczu. Schematy pomocy projektanta zrozumieć kształtu danych i przepływu właściwości łatwiej przez przepływ pracy.
2. Dodaj wszelkie dodatkowe kroki, które ma być wykonywana po odebraniu komunikatu w kolejce. Na przykład wysłać wiadomość e-mail za pośrednictwem usługi Office 365.  
3. Zapisz aplikację logiki, aby wygenerować adresu URL wywołania zwrotnego dla wyzwalacza w tej aplikacji logiki. Adres URL jest wyświetlana na karcie wyzwalacza.

![Wywołania zwrotnego adresu URL jest wyświetlana na karcie wyzwalacza][1]

## <a name="build-the-function"></a>Tworzenie funkcji
Następnie należy utworzyć funkcję, która działa jako wyzwalacz i nasłuchuje w kolejce.

1. W [portalu Azure Functions](https://functions.azure.com/), wybierz pozycję **nową funkcję**, a następnie wybierz **ServiceBusQueueTrigger - C#** szablonu.
   
    ![Portalu Azure Functions][2]
2. Skonfiguruj połączenie z kolejki usługi Service Bus, która używa zestawu SDK usługi Azure Service Bus `OnMessageReceive()` odbiornika.
3. Zapis podstawowych funkcji do wywołania punktu końcowego logiki aplikacji (wcześniej utworzone) za pomocą komunikatu w kolejce jako wyzwalacz. W tym miejscu jest pełny przykład funkcji. W przykładzie użyto `application/json` typ zawartości komunikatu, ale można zmienić tego typu, w razie potrzeby.
   
   ```
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

Aby przetestować, należy dodać komunikatu w kolejce za pomocą narzędzia, takiego jak [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). Zobacz aplikacji logiki wyzwalać natychmiast po funkcji odbiera wiadomości.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png
