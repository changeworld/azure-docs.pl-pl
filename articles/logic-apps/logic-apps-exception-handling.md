---
title: Obsługi błędów i wyjątków — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat wzorców dla błędów i obsługa wyjątków w usłudze Azure Logic Apps
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996603"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Obsługa błędów i wyjątków w usłudze Azure Logic Apps

Sposób, w jaki dowolnej architekturze integracji prawidłowo obsługuje przestojów ani problemy spowodowane przez systemów zależnych może stanowić wyzwanie. Aby ułatwić tworzenie integracji niezawodne i odporne na błędy, które bezpiecznie obsługiwać problemów i błędów, Logic Apps oferuje najwyższej jakości środowisko do obsługi błędów i wyjątków. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Zasady ponawiania prób

Najbardziej podstawowa wyjątku oraz obsługi błędów, można użyć *zasady ponawiania* w dowolnej akcji lub wyzwalacza, jeśli są obsługiwane. Zasady ponawiania Określa, czy i jak akcję lub wyzwalacz ponawiać próbę żądania, gdy upłynie limit czasu żądania oryginalny lub kończy się niepowodzeniem, czyli każde żądanie, które powoduje 408, 429 lub 5xx odpowiedzi. Jeśli nie zasady ponawiania jest używany, domyślna zasada jest używany. 

Poniżej przedstawiono typy zasad ponawiania prób: 

| Type | Opis | 
|------|-------------| 
| **Domyślne** | Ta zasada wysyła do czterech ponownych prób na *wykładniczo zwiększa* interwałów, które możesz zmieniać skalę, 7.5 sekund, ale są ograniczone do zakresu od 5 do 45 sekund. | 
| **Interwał wykładniczy**  | Ta zasada czeka losowo wybranym interwałem wybrane z wykładniczo rosnącym zakresu przed wysłaniem następnego żądania. | 
| **Stały interwał**  | Ta zasada czeka przez określony przed wysłaniem następnego żądania. | 
| **Brak**  | Nie ponownie wysłać żądanie. | 
||| 

Aby uzyskać informacji dotyczących ograniczeń zasad ponawiania, zobacz [Logic Apps, limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Zmień zasady ponawiania

Aby wybrać różnych zasad ponawiania, wykonaj następujące kroki: 

1. Otwórz aplikację logiki w Projektancie aplikacji logiki. 

2. Otwórz **ustawienia** dla akcję lub wyzwalacz.

3. Jeśli obsługuje akcję lub wyzwalacz zasady ponawiania, w obszarze **ponów zasad**, wybierz typ, który ma. 

Lub można ręcznie określić zasady ponawiania w `inputs` sekcji akcję lub wyzwalacz, który obsługuje zasad ponawiania. Jeśli nie określisz zasady ponawiania akcji używa domyślnych zasad.

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

| Wartość | Type | Opis |
|-------|------|-------------|
| <*retry-policy-type*> | String | Typ zasad ponawiania, którego chcesz użyć: `default`, `none`, `fixed`, lub `exponential` | 
| <*retry-interval*> | String | Interwał ponawiania, w którym należy użyć wartości [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Minimalna odbywa się domyślnie `PT5S` i maksymalny interwał `PT1D`. Gdy używasz zasady o interwale wykładniczym, można określić różne wartości minimalne i maksymalne. | 
| <*retry-attempts*> | Liczba całkowita | Liczba ponownych prób, które musi należeć do zakresu od 1 do 90 | 
||||

*Opcjonalne*

| Wartość | Type | Opis |
|-------|------|-------------|
| <*minimum-interval*> | String | Dla zasady o interwale wykładniczym, najmniejszy interwał losowo wybranych interwału w [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maximum-interval*> | String | Dla zasady o interwale wykładniczym, największy interwał losowo wybranych interwał w [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Poniżej przedstawiono więcej informacji na temat typów inne zasady.

<a name="default-retry"></a>

### <a name="default"></a>Domyślne

Jeśli nie określisz zasady ponawiania akcji używa zasad domyślnych, który jest faktycznie [zasady o interwale wykładniczym](#exponential-interval) wysyłającą maksymalnie czterech ponownych prób na wykładniczo interwałów, które są skalowane przy 7.5 sekund. Interwał wynosi od 5 do 45 sekund. 

Chociaż nie zostały jawnie zdefiniowany w ramach akcji lub wyzwalacza, poniżej przedstawiono sposób działania zasad domyślnych w przykładzie Akcja HTTP:

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

Aby określić, że żądania zakończone niepowodzeniem nie ponów akcję lub wyzwalacz, ustaw <*typ zasad ponawiania*> Aby `none`.

### <a name="fixed-interval"></a>Stały interwał

Aby określić, że akcję lub wyzwalacz czeka przez określony przed wysłaniem następnego żądania, ustaw <*typ zasad ponawiania*> Aby `fixed`.

*Przykład*

Te zasady ponawiania prób uzyskać najnowsze wiadomości, dwa razy więcej po pierwsze żądanie nie powiodło się z 30-sekundowe opóźnienie między kolejnymi próbami:

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

Aby określić, że akcję lub wyzwalacz czeka losowo wybranym interwałem przed wysłaniem następnego żądania, ustaw <*typ zasad ponawiania*> Aby `exponential`. Losowo wybranym interwałem jest wybierana z wykładniczo rosnącym zakresu. Opcjonalnie można także zastąpić domyślne interwały minimalną i maksymalną, określając własne interwałów minimalną i maksymalną.

**Zmienna losowa zakresów**

W poniższej tabeli przedstawiono, jak Logic Apps generuje zmienną losową rozkładu w określonym zakresie dla każdego ponawiania, w tym liczbę ponownych prób:

| Ponów próbę wykonania numer | Minimalny interwał | Maksymalny interwał |
|--------------|------------------|------------------|
| 1 | MAX (0, <*minimalny interwał*>) | min (interwału, <*maksymalny interwał*>) |
| 2 | MAX (interwału, <*minimalny interwał*>) | min (2 * interwał <*maksymalny interwał*>) |
| 3 | MAX (2 * interwał <*minimalny interwał*>) | min (4 * interwał <*maksymalny interwał*>) |
| 4 | MAX (4 * interwał <*minimalny interwał*>) | min (8 * interwał <*maksymalny interwał*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Przechwytywanie i obsługę błędów z właściwością RunAfter

Każda akcja aplikacji logiki deklaruje akcje, które musi zostać zakończone przed uruchomieniem tej akcji, podobne do określania kolejności kroków w przepływie pracy. W definicji działania **runAfter** właściwość definiuje ta kolejność i jest obiektem, który opisuje, w których akcje i stany akcji wykonuj akcję.

Domyślnie wszystkie akcje, które dodajesz w Projektancie aplikacji logiki są ustawione na uruchamianie po poprzednim kroku, gdy jest wynik poprzedniego kroku **Powodzenie**. Można jednak dostosować **runAfter** tak, aby akcje zostać wywołane podczas poprzedniej akcji powoduje jako **,** **pomijane**, lub kombinacji tych wartości. Na przykład, aby dodać element do określonego tematu usługi Service Bus po określonym **Insert_Row** akcji kończy się niepowodzeniem, można użyć w tym przykładzie **runAfter** definicji:

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
> Akcje, które są uruchamiane i zakończy się pomyślnie po poprzedniej akcji zakończyło się niepowodzeniem, są oznaczone jako **Powodzenie**. To zachowanie oznacza, że możesz pomyślnie catch, wszystkie błędy w przepływie pracy, uruchom sam jest oznaczony jako **Powodzenie**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Akcje z zakresami, ich wyników oceny

Podobny do uruchamiania kroki po poszczególnych akcji przy użyciu **runAfter** właściwości możesz zgrupować akcji wewnątrz [zakres](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Zakresów można użyć, gdy chcesz logicznie pogrupować akcje, ocenić zakres zagregowany stan i wykonywać akcje na podstawie tego stanu. Po wszystkie akcje w zakresie zakończą działanie, zakres, sama pobiera swój własny status. 

Aby sprawdzić stan zakresu, można użyć takich samych kryteriów używanych do sprawdzenia stanu uruchomienia aplikacji logiki, takich jak **zakończyło się pomyślnie**, i tak dalej. 

Domyślnie, gdy zakresem wszystkie akcje powiedzie się, stan zakresu jest on oznaczony **Powodzenie**. Jeśli powoduje ostatecznych działań w zakresie lub **przerwania**, stan zakresu jest oznaczony jako **nie powiodło się**. 

Przechwytują wyjątki w **nie powiodło się** zakres i wykonywania działań, które obsługi tych błędów, można użyć **runAfter** właściwości, dla którego zakresu. W ten sposób, jeśli *wszelkie* akcje w zakresie kończą się niepowodzeniem, a używasz **runAfter** właściwości dla tego zakresu, można utworzyć jedną akcję w celu przechwytywania błędów.

Limity zakresów, zobacz [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Pobieranie kontekstu i wyników błędów

Mimo że wychwytywanie błędów z zakresu jest przydatne, również można kontekstu, które pomagają zrozumieć, dokładnie akcje, które nie powiodło się oraz wszelkie błędy lub kodów stanu, które zostały zwrócone. `@result()` Wyrażenie dostarcza kontekst dotyczący wyniku wszystkie akcje w zakresie.

`@result()` Wyrażenie akceptuje pojedynczy parametr (nazwa zakresu) i zwraca tablicę wszystkich wyników akcji z tego zakresu. Te obiekty działania obejmują takie same atrybuty jak  **\@actions()** obiektu, takiego jak akcja ma czas rozpoczęcia, czas zakończenia, stanu, danych wejściowych, identyfikatory korelacji i danych wyjściowych. Aby wysłać kontekst dla akcji, które nie powiodło się w zakresie, może łatwo łączyć  **\@result()** funkcją **runAfter** właściwości.

Aby uruchomić akcję dla każdej akcji w zakresie, który ma **nie powiodło się** wyniku i filtrowania tablicy wyników w dół, aby akcje zakończone niepowodzeniem, Sparuj  **\@result()** z **[ Filtrowanie tablicy](../connectors/connectors-native-query.md)** akcji i [ **dla każdego** ](../logic-apps/logic-apps-control-flow-loops.md) pętli. Możesz pobrać tablicy przefiltrowanych wyników i wykonaj akcję dla każdej awarii za pomocą **dla każdego** pętli. 

Oto przykład, a następnie szczegółowy opis, który wysyła żądanie HTTP POST z treści odpowiedzi dla wszystkich działań, które nie powiodły się w zakresie "My_Scope":

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

Poniżej przedstawiono szczegółowy przewodnik opisujący, co się stanie, w tym przykładzie:

1. Aby uzyskać wynik z wszystkich akcji wewnątrz "My_Scope" **Filtruj tablicę** Akcja używa tego wyrażenia filtru: `@result('My_Scope')`

2. Warunek dla **Filtruj tablicę** jest dowolnym `@result()` element, który ma stan równa **niepowodzenie**. Ten warunek filtrowanie tablicy, która zawiera wszystkich wyników akcji z "My_Scope" do tablicy przy użyciu tylko wyniki Akcja zakończona niepowodzeniem.

3. Wykonaj **dla każdego** pętla akcji na *filtrowane tablicy* danych wyjściowych. W tym kroku wykonuje akcję dla każdego wyniku akcji nie powiodło się, które wcześniej zostały przefiltrowane.

   W przypadku niepowodzenia jednej akcji w zakresie akcje **dla każdego** pętli, które są wykonywane tylko raz. 
   Wiele zakończonych niepowodzeniem akcje mogą spowodować jedną akcję na błąd.

4. Wysyłanie żądania HTTP POST w **dla każdego** elementu treść odpowiedzi, czyli `@item()['outputs']['body']` wyrażenia. 

   `@result()` Kształt element jest taka sama jak `@actions()` kształt i można go przeanalizować taki sam sposób.

5. Obejmują dwa Nagłówki niestandardowe o nazwie Akcja zakończona niepowodzeniem (`@item()['name']`) i nieudane Uruchom klienta, identyfikator śledzenia (`@item()['clientTrackingId']`).

Odwołanie, Oto przykład pojedynczej `@result()` elementu, przedstawiający **nazwa**, **treści**, i **clientTrackingId** właściwości, które są analizowane w ciągu poprzednich przykład. Poza metodą **dla każdego** akcji `@result()` zwraca informacje o tych obiektów.

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

Aby wykonać różne obsługi wzorców wyjątków, można użyć wyrażenia opisany wcześniej w tym artykule. Może wybrać opcję wykonania jednego wyjątków, Obsługa akcji poza zakres, który akceptuje całej tablicy filtrowane błędów ale Usuń **dla każdego** akcji. Możesz również uwzględnić inne właściwości przydatne z  **\@result()** odpowiedzi, jak opisano wcześniej.

## <a name="azure-diagnostics-and-metrics"></a>Narzędzie diagnostyczne systemu Azure i metryk

Poprzednie wzorce to doskonały sposób obsługi błędów i wyjątków w ramach przebiegu, ale można również zidentyfikować i reagować na błędy, niezależnie od samego przebiegu. 
[Diagnostyka Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) to prosty sposób, aby wysłać wszystkie zdarzenia przepływu pracy, w tym wszystkie stany uruchomienia i akcji, do konta usługi Azure Storage lub Centrum zdarzeń utworzone za pomocą usługi Azure Event Hubs. 

Aby ocenić stan uruchomienia, Monitoruj dzienniki i metryki lub opublikować je do dowolnego narzędzia do monitorowania, którego chcesz używać. Jedną z opcji potencjalnych dotyczy wszystkich zdarzeń za pomocą usługi Event Hubs do usługi stream [usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). W usłudze Stream Analytics możesz pisać zapytania na żywo, na podstawie wszelkie anomalie, średnie lub niepowodzenia z dzienników diagnostycznych. Stream Analytics służy do wysyłania informacji do innych źródeł danych, takich jak kolejki, tematy, SQL, Azure Cosmos DB i usługi Power BI.

## <a name="next-steps"></a>Kolejne kroki

* [Zobacz, jak klient tworzy dodanymi komentarzami z usługą Azure Logic Apps.](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Znajdź więcej aplikacji logiki, przykłady i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
