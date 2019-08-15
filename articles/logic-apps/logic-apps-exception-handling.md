---
title: Obsługa błędów i wyjątków — Azure Logic Apps | Microsoft Docs
description: Informacje o wzorcach obsługi błędów i wyjątków w Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 3f812c1142b5cd40169f7340163295b0f7ea6a4d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "60996603"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Obsługa błędów i wyjątków w Azure Logic Apps

Sposób, w jaki każda architektura integracji odpowiednio obsługuje przestoje lub problemy spowodowane przez systemy zależne mogą stanowić wyzwanie. Aby ułatwić tworzenie niezawodnych i odpornych integracji, które bezpiecznie obsługują problemy i błędy, Logic Apps zapewnia środowisko pierwszej klasy do obsługi błędów i wyjątków. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Zasady ponawiania prób

Aby uzyskać najbardziej podstawowy wyjątek i obsługę błędów, można użyć *zasad ponawiania* w dowolnej akcji lub wyzwalaczu, jeśli jest obsługiwany. Zasady ponawiania określają, czy i w jaki sposób akcja lub wyzwalacz ponawiają próbę żądania, gdy pierwotne żądanie przekroczy limit czasu lub zakończy się niepowodzeniem, co oznacza każde żądanie, które powoduje wystąpienie odpowiedzi 408, 429 lub 5xx. Jeśli nie zostaną użyte żadne inne zasady ponawiania, zostaną użyte zasady domyślne. 

Oto typy zasad ponawiania prób: 

| Type | Opis | 
|------|-------------| 
| **Domyślne** | Te zasady wysyłają do czterech ponownych prób w wykładniczo rosnących odstępach czasu, które są skalowane o 7,5 sekund, ale są ograniczone do zakresu od 5 do 45 sekund. | 
| **Interwał wykładniczy**  | Te zasady czekają losowy interwał wybrany z wykładniczo rosnącego zakresu przed wysłaniem kolejnego żądania. | 
| **Stały interwał**  | Te zasady czekają określony interwał przed wysłaniem kolejnego żądania. | 
| **Brak**  | Nie wysyłaj ponownie żądania. | 
||| 

Aby uzyskać informacje na temat limitów ponowień zasad, zobacz [Logic Apps limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Zmień zasady ponawiania

Aby wybrać inne zasady ponawiania, wykonaj następujące kroki: 

1. Otwórz aplikację logiki w Projektancie aplikacji logiki. 

2. Otwórz **Ustawienia** dla akcji lub wyzwalacza.

3. Jeśli akcja lub wyzwalacz obsługuje zasady ponawiania, w obszarze **zasady ponawiania**wybierz odpowiedni typ. 

Można też ręcznie określić zasady ponawiania w `inputs` sekcji dla akcji lub wyzwalacza, który obsługuje zasady ponawiania. Jeśli nie określisz zasad ponawiania, akcja będzie używać zasad domyślnych.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Wymagane*

| Value | Type | Opis |
|-------|------|-------------|
| <*retry-typ zasad*> | String | Typ zasad ponawiania próby, którego chcesz użyć `default`: `none`, `fixed`, lub`exponential` | 
| <*Ponawianie interwału*> | String | Interwał ponawiania, w którym wartość musi używać [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Domyślny interwał minimalny to `PT5S` i maksymalny interwał to. `PT1D` Korzystając z zasad interwału wykładniczego, można określić różne wartości minimalne i maksymalne. | 
| <*Ponawianie prób*> | Integer | Liczba ponownych prób, które muszą zawierać się w przedziale od 1 do 90 | 
||||

*Obowiązkowe*

| Value | Type | Opis |
|-------|------|-------------|
| <*minimalny interwał*> | String | Dla zasad interwału wykładniczego najmniejszy interwał dla losowo wybranego interwału w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maksimum — interwał*> | String | Dla zasad interwałów wykładniczych największy interwał dla losowo wybranego interwału w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Poniżej znajduje się więcej informacji na temat różnych typów zasad.

<a name="default-retry"></a>

### <a name="default"></a>Domyślny

Jeśli nie określisz zasad ponawiania, akcja będzie używać zasad domyślnych, które są w rzeczywistości [zasadami interwału wykładniczego](#exponential-interval) , które wysyłają do czterech ponownych prób przy wykładniczym zwiększeniu interwałów, które są skalowane przez 7,5 sekund. Interwał jest limitem od 5 do 45 sekund. 

Chociaż nie zostało to jawnie zdefiniowane w akcji lub wyzwalaczu, poniżej przedstawiono sposób zachowania zasad domyślnych w przykładowej akcji HTTP:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Brak

Aby określić, że akcja lub wyzwalacz nie ponowią prób żądań zakończonych niepowodzeniem, ustaw wartość opcji < `none`retry-*Policy-Type*> na.

### <a name="fixed-interval"></a>Stały interwał

Aby określić, że akcja lub wyzwalacz czekają określony interwał przed wysłaniem kolejnego żądania, należy ustawić > `fixed`< retry *-Policy-Type*.

*Przykład*

Ta zasada ponawiania próbuje pobrać najnowsze wiadomości dwa razy po pierwszym niepomyślnym żądaniu z opóźnieniem 30-sekundowym między kolejnymi próbami:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Interwał wykładniczy

Aby określić, że akcja lub wyzwalacz czekają losowy interwał przed wysłaniem kolejnego żądania, należy ustawić > `exponential`< retry *-Policy-Type*. Interwał losowy jest wybierany z wykładniczo rosnącego zakresu. Opcjonalnie można również przesłonić domyślne minimalne i maksymalne interwały, określając własne interwały minimalne i maksymalne.

**Zakresy zmiennych losowych**

W tej tabeli przedstawiono sposób, w jaki Logic Apps generuje jednorodną zmienną losową w określonym zakresie dla każdego ponowienia próby, włącznie z liczbą ponownych prób:

| Liczba ponownych prób | Minimalny interwał | Maksymalny interwał |
|--------------|------------------|------------------|
| 1 | Max (0, <> minimalnym interwałem) | min (interwał, <*Maksymalny interwał*>) |
| 2 | Max (interwał, <*minimalny interwał*>) | minimum (2 * interwał, <*Maksymalny interwał*>) |
| 3 | Max (2 * interwał, <*minimalny interwał*>) | minimum (4 * interwał, <*Maksymalny interwał*>) |
| 4 | Max (4 * interwał, <*minimalny interwał*>) | minimum (8 * interwał, <*Maksymalny interwał*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Błędy catch i Handle z właściwością RunAfter

Każda akcja aplikacji logiki deklaruje akcje, które muszą zakończyć się przed rozpoczęciem tej akcji, podobnie jak w przypadku określania kolejności kroków w przepływie pracy. W definicji akcji właściwość **runAfter** definiuje tę kolejność i jest obiektem opisującym, które akcje i stan akcji wykonują akcję.

Domyślnie wszystkie akcje dodane w Projektancie aplikacji logiki są ustawiane do uruchomienia po poprzednim kroku, gdy wynik poprzedniego kroku zakończy się **pomyślnie**. Można jednak dostosować **runAfter** tak, aby akcje zostać wywołane podczas poprzedniej akcji powoduje jako **,** **pomijane**, lub kombinacji tych wartości. Na przykład, aby dodać element do określonego tematu Service Bus po niepowodzeniu określonej akcji **Insert_Row** , można użyć tej przykładowej definicji **runAfter** :

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

**RunAfter** uruchomiony, jeśli właściwość ma wartość **Insert_Row** stanu akcji jest. Aby uruchomić akcję, gdy stan działania to **zakończyło się pomyślnie**, lub **pomijane**, należy użyć następującej składni:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Akcje, które są wykonywane i kończone pomyślnie po pomyślnym wykonaniu tej akcji, są oznaczone jako **zakończone powodzeniem**. To zachowanie oznacza, że jeśli pomyślnie przechwytuje wszystkie błędy w przepływie pracy, sam przebieg zostanie oznaczony jako **zakończony powodzeniem**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Oceń akcje z zakresami i ich wynikami

Podobnie jak w przypadku wykonywania pojedynczych akcji za pomocą właściwości **runAfter** , można grupować akcje w obrębie [zakresu](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Zakresy można używać w celu logicznego grupowania akcji, oceny stanu zagregowanego zakresu i wykonywania akcji na podstawie tego stanu. Po zakończeniu wszystkich akcji w zakresie, sam zakres uzyskuje własny stan. 

Aby sprawdzić stan zakresu, można użyć takich samych kryteriów używanych do sprawdzenia stanu uruchomienia aplikacji logiki, takich jak **zakończyło się pomyślnie**, i tak dalej. 

Domyślnie, gdy wszystkie akcje tego zakresu zakończyły się powodzeniem, stan zakresu jest oznaczony jako **zakończony powodzeniem**. Jeśli powoduje ostatecznych działań w zakresie lub **przerwania**, stan zakresu jest oznaczony jako **nie powiodło się**. 

Przechwytują wyjątki w **nie powiodło się** zakres i wykonywania działań, które obsługi tych błędów, można użyć **runAfter** właściwości, dla którego zakresu. W ten sposób, jeśli *jakiekolwiek* akcje w zakresie zakończą się niepowodzeniem i używasz właściwości **runAfter** dla tego zakresu, można utworzyć pojedynczą akcję w celu przechwycenia błędów.

Aby uzyskać ograniczenia dotyczące zakresów, zobacz [limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Pobierz kontekst i wyniki dla niepowodzeń

Chociaż przechwytywanie błędów z zakresu jest przydatne, można również zastanowić się, że kontekst pomaga zrozumieć, które akcje nie powiodły się, oraz wszelkie zwrócone błędy lub kody stanu. `@result()` Wyrażenie zawiera kontekst dotyczący wyniku wszystkich akcji w zakresie.

`@result()` Wyrażenie akceptuje pojedynczy parametr (nazwa zakresu) i zwraca tablicę wszystkich wyników akcji z tego zakresu. Te obiekty akcji zawierają te same atrybuty, co  **\@obiekt Actions ()** , takie jak godzina rozpoczęcia akcji, czas zakończenia, stan, dane wejściowe, identyfikatory korelacji i wyjścia. Aby wysłać kontekst dla wszystkich akcji, które zakończyły się niepowodzeniem w zakresie, można łatwo sparować  **\@funkcję wynik ()** z właściwością **runAfter** .

Aby uruchomić akcję dla każdej akcji w zakresie, który ma wynik **Niepowodzenie** i filtrować tablicę wyników w dół do akcji zakończonych niepowodzeniem, można sparować  **\@wynik ()** z akcją **[Filtruj tablicę](../connectors/connectors-native-query.md)** i pętlą [**for each**](../logic-apps/logic-apps-control-flow-loops.md) . Można użyć przefiltrowanej tablicy wyników i wykonać akcję dla każdej awarii przy użyciu pętli **for each** . 

Oto przykład, a następnie szczegółowy opis, który wysyła żądanie HTTP POST z treścią odpowiedzi dla wszystkich akcji, które zakończyły się niepowodzeniem w zakresie "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Oto szczegółowy przewodnik, w którym opisano, co się dzieje w tym przykładzie:

1. Aby uzyskać wynik ze wszystkich akcji w ramach elementu "My_Scope", Akcja **filtrowania tablicy** używa tego wyrażenia filtru:`@result('My_Scope')`

2. Warunek dla **tablicy filtru** to każdy `@result()` element o stanie równy niepowodzenie. Ten warunek filtruje tablicę, która ma wszystkie wyniki akcji z "My_Scope" w dół do tablicy z wynikami akcji zakończonych niepowodzeniem.

3. Wykonaj pętlę **for each** w odfiltrowanych danych wyjściowych *tablicy* . Ten krok wykonuje akcję dla każdego wyniku działania zakończonego niepowodzeniem, który został wcześniej przefiltrowany.

   Jeśli pojedyncza akcja w zakresie nie powiodła się, akcje w pętli **for each** są wykonywane tylko raz. 
   Wiele akcji zakończonych niepowodzeniem powoduje jedną akcję na awarię.

4. Wyślij http post **dla każdej** treści odpowiedzi elementu, która jest `@item()['outputs']['body']` wyrażeniem. 

   Kształt elementu jest taki sam `@actions()` jak kształt i można go analizować w taki sam sposób. `@result()`

5. Uwzględnij dwa nagłówki niestandardowe z nazwą akcji zakończonej`@item()['name']`niepowodzeniem () i`@item()['clientTrackingId']`identyfikatorem śledzenia klienta, który nie powiódł się.

`@result()` Oto przykład pojedynczego elementu, który pokazuje właściwości **name**, **Body**i **clientTrackingId** , które są analizowane w poprzednim przykładzie. Na zewnątrz **dla każdej** akcji `@result()` zwraca tablicę tych obiektów.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Aby wykonać różne wzorce obsługi wyjątków, można użyć wyrażeń opisanych wcześniej w tym artykule. Możesz wykonać akcję obejmującą pojedynczą obsługę wyjątków poza zakresem, który akceptuje całą przefiltrowaną tablicę błędów, i Usuń **dla każdej** akcji. Możesz również dodać inne przydatne właściwości z  **\@odpowiedzi wynik ()** , jak opisano wcześniej.

## <a name="azure-diagnostics-and-metrics"></a>Diagnostyka Azure i metryki

Poprzednie wzorce to doskonałe rozwiązanie do obsługi błędów i wyjątków w ramach przebiegu, ale można również identyfikować błędy niezależne od samego uruchomienia i odpowiadać na nie. 
[Diagnostyka Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) zapewnia prosty sposób wysyłania wszystkich zdarzeń przepływu pracy, w tym wszystkich stanów uruchamiania i akcji, do konta usługi Azure Storage lub centrum zdarzeń utworzonego za pomocą usługi Azure Event Hubs. 

Aby oszacować Stany uruchamiania, można monitorować dzienniki i metryki lub publikować je w dowolnym preferowanym narzędziu do monitorowania. Jedną z potencjalnych opcji jest przesyłanie strumieniowe wszystkich zdarzeń za pomocą Event Hubs do [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). W Stream Analytics można pisać zapytania na żywo na podstawie wszelkich anomalii, średnich lub błędów z dzienników diagnostycznych. Za pomocą Stream Analytics można wysyłać informacje do innych źródeł danych, takich jak kolejki, tematy, SQL, Azure Cosmos DB lub Power BI.

## <a name="next-steps"></a>Następne kroki

* [Zobacz, jak klient kompiluje obsługę błędów w Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Znajdź więcej Logic Apps przykładów i scenariuszy](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
