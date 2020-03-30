---
title: Obsługa błędów i wyjątków w przepływach pracy
description: Dowiedz się, jak radzić sobie z błędami i wyjątkami, które mają miejsce w zautomatyzowanych zadaniach i przepływach pracy utworzonych przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284033"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Obsługa błędów i wyjątków w usłudze Azure Logic Apps

Sposób, w jaki każda architektura integracji odpowiednio obsługuje przestoje lub problemy spowodowane przez systemy zależne, może stanowić wyzwanie. Aby ułatwić tworzenie niezawodnych i elastycznych integracji, które bezpiecznie obsługiwać problemy i awarie, Logic Apps zapewnia pierwszorzędne środowisko obsługi błędów i wyjątków.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Zasady ponawiania prób

Aby uzyskać najbardziej podstawowe wyjątki i obsługę błędów, można użyć *zasad ponawiania prób* w dowolnej akcji lub wyzwalaczu, gdzie jest obsługiwana, na przykład, zobacz [akcja HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Zasady ponawiania określa, czy i jak akcja lub wyzwalacz ponawia żądanie, gdy czas oczekiwania oryginalnego żądania lub nie powiedzie się, czyli każde żądanie, które powoduje odpowiedź 408, 429 lub 5xx. Jeśli nie jest używana żadna inna zasada ponawiania próby, używana jest zasada domyślna.

Oto typy zasad ponawiania prób:

| Typ | Opis |
|------|-------------|
| **Domyślny** | Ta zasada wysyła do czterech ponownych prób w *interwałach wykładniczo zwiększających,* które skalują się o 7,5 sekundy, ale są ograniczone między 5 i 45 sekund. |
| **Interwał wykładniczy**  | Ta zasada czeka losowy interwał wybrany z wykładniczo rosnącego zakresu przed wysłaniem następnego żądania. |
| **Stały interwał**  | Ta zasada czeka określony interwał przed wysłaniem następnego żądania. |
| **Brak**  | Nie wysuwaj ponownie żądania. |
|||

Aby uzyskać informacje o limitach zasad ponawiania prób, zobacz [Limity i konfiguracja aplikacji logiki](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Zmienianie zasad ponawiania prób

Aby wybrać inną zasadę ponawiania prób, wykonaj następujące czynności:

1. Otwórz aplikację logiki w logic app designer.

1. Otwórz **ustawienia** dla akcji lub wyzwalacza.

1. Jeśli akcja lub wyzwalacz obsługuje zasady ponawiania prób, w obszarze **Zasady ponawiania próby**wybierz odpowiedni typ.

Można też ręcznie określić zasady ponawiania próby w `inputs` sekcji dla akcji lub wyzwalacza obsługującego zasady ponawiania prób. Jeśli nie określisz zasad ponawiania próby, akcja użyje zasad domyślnych.

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
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*ponów próbę-policy-type*> | Ciąg | Typ zasad ponawiania próby, `none` `fixed`którego chcesz użyć: `default`, , , lub`exponential` |
| <*interwał ponawiania prób*> | Ciąg | Interwał ponawiania próby, w którym wartość musi używać [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Domyślny minimalny `PT5S` interwał to `PT1D`i maksymalny interwał to . Korzystając z zasad interwału wykładniczego, można określić różne wartości minimalne i maksymalne. |
| <*ponowie próbę prób*> | Liczba całkowita | Liczba prób ponawiania, która musi wynosić od 1 do 90 |
||||

*Opcjonalne*

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*minimalny interwał*> | Ciąg | W przypadku zasad interwału wykładniczego najmniejszy interwał dla losowo wybranego interwału w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*maksymalny interwał*> | Ciąg | Dla zasad interwału wykładniczego największy interwał dla losowo wybranego interwału w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Oto więcej informacji na temat różnych typów zasad.

<a name="default-retry"></a>

### <a name="default"></a>Domyślne

Jeśli nie określisz zasad ponawiania, akcja używa zasad domyślnych, która jest w rzeczywistości [wykładniczym interwałem zasady,](#exponential-interval) która wysyła do czterech ponownych prób w interwale wykładniczo zwiększające, które są skalowane przez 7,5 sekundy. Interwał jest ograniczony od 5 do 45 sekund.

Chociaż nie jest jawnie zdefiniowane w akcji lub wyzwalacza, oto jak zasady domyślne zachowuje się w przykładowej akcji HTTP:

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

Aby określić, że akcja lub wyzwalacz nie ponawia próby nieudanych żądań, należy `none`ustawić> typu <*ponawiania zasad* na .

### <a name="fixed-interval"></a>Stały interwał

Aby określić, że akcja lub wyzwalacz czeka określony interwał przed wysłaniem następnego żądania, ustaw>> *typu <ponawiania zasad* na `fixed`.

*Przykład*

Ta zasada ponawiania próby, aby uzyskać najnowsze wiadomości dwa razy po pierwszym żądaniu nie powiodło się z 30-sekundowym opóźnieniem między każdą próbą:

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

Aby określić, że akcja lub wyzwalacz czeka losowy interwał przed wysłaniem następnego żądania, ustaw <*> typu ponawiania zasad* na `exponential`. Losowy interwał jest wybierany z wykładniczo rosnącego zakresu. Opcjonalnie można również zastąpić domyślne minimalne i maksymalne interwały, określając własne minimalne i maksymalne interwały.

**Losowe zakresy zmiennych**

W tej tabeli pokazano, jak aplikacje logiki generuje jednolitą zmienną losową w określonym zakresie dla każdego ponowić próbę do liczby ponownych prób włącznie:

| Ponowić numer próby | Minimalny interwał | Maksymalny interwał |
|--------------|------------------|------------------|
| 1 | max(0, <*> minimalnego interwału)* | min(interwał<*maksymalny interwał*>) |
| 2 | max(interwał, <*> minimalnego interwału)* | min(2 * interwał, <*maksymalny interwał*>) |
| 3 | max (2 * interwał, <*> minimalnych interwałów)* | min(4 * interwał, <*maksymalny interwał*>) |
| 4 | max(4 * interwał, <*> minimalnego interwału)* | min(8 * interwał, <*maksymalny interwał*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Przechwytywnie i obsługa błędów przez zmianę zachowania "uruchom po"

Podczas dodawania akcji w Logic App Designer, niejawnie zadeklarować kolejność do użycia do uruchamiania tych akcji. Po zakończeniu działania akcja jest oznaczona stanem, `Succeeded` `Failed`takim `Skipped`jak `TimedOut`, , lub . W każdej definicji `runAfter` akcji właściwość określa akcję poprzednika, która musi najpierw zakończyć i stany dozwolone dla tego poprzednika przed uruchomieniem akcji następnika. Domyślnie akcja dodana w projektancie jest uruchamiana dopiero `Succeeded` po zakończeniu pracy poprzednika ze stanem.

Gdy akcja zgłasza nieobsługiowany błąd lub wyjątek, akcja jest `Failed` `Skipped`oznaczona, a każda akcja następnika jest oznaczona . Jeśli to zachowanie występuje dla akcji, która ma równoległe gałęzie, aparat aplikacji logiki następuje innych gałęzi, aby określić ich stanu ukończenia. Na przykład jeśli gałąź kończy się `Skipped` akcją, stan ukończenia tej gałęzi jest oparty na stanie poprzednika tej pominiętej akcji. Po zakończeniu uruchamiania aplikacji logiki aparat określa stan całego uruchomienia, oceniając wszystkie stany gałęzi. Jeśli którakolwiek gałąź zakończy się `Failed`niepowodzeniem, oznaczona jest cała aplikacja logiki .

![Przykłady, które pokazują, jak oceniane są stany uruchamiania](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Aby upewnić się, że akcja może nadal działać pomimo stanu [poprzednika, dostosuj zachowanie "uruchom po" akcji,](#customize-run-after) aby obsłużyć nieudane stany poprzednika.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Dostosowywanie zachowania "uruchom po"

Zachowanie "uruchom po" akcji można dostosować, tak aby akcja była uruchamiana, `Succeeded`gdy `Failed` `Skipped`stan `TimedOut`poprzednika ma taki status , , , , ani dowolne z tych stanów. Na przykład, aby wysłać wiadomość `Add_a_row_into_a_table` e-mail `Failed`po `Succeeded`oznaczeniu akcji poprzednika usługi Excel Online, zamiast , zmień zachowanie "uruchom po", wykonując jeden z następujących kroków:

* W widoku projektu wybierz przycisk elipsy (**...**), a następnie wybierz pozycję **Konfiguruj uruchom po**.

  ![Konfigurowanie zachowania "uruchom po" dla akcji](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  Kształt akcji pokazuje stan domyślny wymagany dla akcji poprzednika, czyli **Dodaj wiersz do tabeli** w tym przykładzie:

  ![Domyślne zachowanie "uruchom po" dla akcji](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Zmień zachowanie "uruchom po" na odpowiedni stan, który w tym przykładzie **nie powiódł się:**

  ![Zmień zachowanie "uruchom po" na "nie powiodło się"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Aby określić, że akcja jest uruchamiana, niezależnie od tego, `Failed` `Skipped` czy akcja poprzednia jest oznaczona jako , czy `TimedOut`też wybierz inne stany:

  ![Zmień zachowanie "uruchom po", aby mieć inny stan](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* W widoku kodu w definicji JSON akcji `runAfter` edytuj właściwość, która jest zgodna z tą składnią:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  W tym przykładzie `runAfter` zmień `Succeeded` `Failed`właściwość z:

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Aby określić, że akcja jest uruchamiana, niezależnie od tego, `Failed` `Skipped` czy akcja poprzednia jest oznaczona jako , czy `TimedOut`, dodaj inne stany:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Ocena działań z zakresami i ich wynikami

Podobnie jak w kolejnych `runAfter` krokach po poszczególnych akcjach z właściwością, można grupować akcje razem wewnątrz [zakresu](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Zakresów można używać, gdy chcesz logicznie grupować akcje razem, oceniać stan agregacji zakresu i wykonywać akcje na podstawie tego stanu. Po zakończeniu wszystkich akcji w zakresie, sam zakres uzyskuje swój własny stan.

Aby sprawdzić stan zakresu, można użyć tych samych kryteriów, których używasz do sprawdzania `Succeeded` `Failed`stanu uruchomienia aplikacji logiki, takich jak , i tak dalej.

Domyślnie, gdy wszystkie akcje zakresu powiedzą się, stan `Succeeded`zakresu jest oznaczony . Jeśli ostateczna akcja w `Failed` zakresie `Aborted`powoduje oznaczenie stanu `Failed`zakresu.

Aby wychwyć `Failed` wyjątki w zakresie i uruchomić akcje, `runAfter` które `Failed` obsługują te błędy, można użyć właściwości dla tego zakresu. W ten sposób, jeśli *wszelkie* akcje w `runAfter` zakresie nie powiodą się i używasz właściwości dla tego zakresu, można utworzyć pojedynczą akcję, aby wyłapać błędy.

Aby uzyskać limity dotyczące zakresów, zobacz [Limity i config](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Uzyskaj kontekst i wyniki dla awarii

Mimo że przechwytywanie błędów z zakresu jest przydatne, można również kontekst, aby pomóc zrozumieć dokładnie, które akcje nie powiodło się oraz wszelkie błędy lub kody stanu, które zostały zwrócone.

Funkcja [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) zapewnia kontekst o wynikach ze wszystkich akcji w zakresie. Funkcja `result()` akceptuje pojedynczy parametr, który jest nazwą zakresu i zwraca tablicę, która zawiera wszystkie wyniki akcji z tego zakresu. Te obiekty akcji zawierają te `actions()` same atrybuty co obiekt, takie jak czas rozpoczęcia akcji, czas zakończenia, stan, dane wejściowe, identyfikatory korelacji i dane wyjściowe. Aby wysłać kontekst dla wszystkich akcji, które nie `@result()` powiodły `runAfter` się w zakresie, można łatwo sparować wyrażenie z właściwością.

Aby uruchomić akcję dla każdej akcji w `Failed` zakresie, który ma wynik i filtrować tablicę wyników `@result()` do akcji nie powiodło się, można sparować wyrażenie z akcji [**Filter Array**](logic-apps-perform-data-operations.md#filter-array-action) i [**dla każdej**](../logic-apps/logic-apps-control-flow-loops.md) pętli. Można wziąć tablicy wyników filtrowanych i wykonać akcję `For_each` dla każdego błędu za pomocą pętli.

Oto przykład, a następnie szczegółowe wyjaśnienie, które wysyła żądanie HTTP POST z treścią odpowiedzi dla wszystkich akcji, które nie powiodły się w zakresie "My_Scope":

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

Oto szczegółowy przewodnik, który opisuje, co się dzieje w tym przykładzie:

1. Aby uzyskać wynik ze wszystkich akcji wewnątrz "My_Scope", akcja **Filter Array** używa tego wyrażenia filtru:`@result('My_Scope')`

1. Warunkiem **tablicy filtrów** jest dowolny `@result()` element `Failed`o stanie równym . Ten warunek filtruje tablicę, która ma wszystkie wyniki akcji z "My_Scope" w dół do tablicy z tylko wyniki akcji nie powiodło się.

1. Wykonaj `For_each` akcję pętli na *filtrowanych* wyjściach tablicy. Ten krok wykonuje akcję dla każdego wyniku akcji nie powiodło się, który został wcześniej odfiltrowany.

   Jeśli pojedyncza akcja w zakresie zakończy `For_each` się niepowodzeniem, akcje w pętli są uruchamiane tylko raz. Wiele akcji nie powiodło się spowodować jedną akcję na niepowodzenie.

1. Wyślij wpis HTTP `For_each` w treści odpowiedzi elementu, która jest wyrażeniem. `@item()['outputs']['body']`

   Kształt `@result()` elementu jest taki `@actions()` sam jak kształt i może być analizowany w ten sam sposób.

1. Dołącz dwa niestandardowe nagłówki o nazwie`@item()['name']`akcji, która nie powiodła`@item()['clientTrackingId']`się ( ) i identyfikator śledzenia klienta nie powiodło się ( ).

Dla odwołania, oto przykład pojedynczego `@result()` elementu, `name`pokazujący `body` `clientTrackingId` , i właściwości, które są analizowane w poprzednim przykładzie. Poza `For_each` akcją `@result()` zwraca tablicę tych obiektów.

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

Aby wykonać różne wzorce obsługi wyjątków, można użyć wyrażeń opisanych wcześniej w tym artykule. Można wykonać jedną akcję obsługi wyjątków poza zakresem, który akceptuje całą tablicę `For_each` filtrowanych błędów i usunąć akcję. Można również dołączyć inne przydatne `\@result()` właściwości z odpowiedzi, jak opisano wcześniej.

## <a name="set-up-azure-monitor-logs"></a>Konfigurowanie dzienników usługi Azure Monitor

Poprzednie wzorce są doskonałym sposobem obsługi błędów i wyjątków w ramach przebiegu, ale można również zidentyfikować i odpowiedzieć na błędy niezależnie od samego uruchomienia. [Usługa Azure Monitor](../azure-monitor/overview.md) umożliwia wysyłanie wszystkich zdarzeń przepływu pracy, w tym wszystkich stanów uruchamiania i akcji, do [obszaru roboczego usługi Log Analytics,](../azure-monitor/platform/data-platform-logs.md) [konta magazynu platformy Azure](../storage/blobs/storage-blobs-overview.md)lub usługi Azure [Event Hubs.](../event-hubs/event-hubs-about.md)

Aby ocenić stany uruchamiania, można monitorować dzienniki i metryki lub opublikować je w dowolnym narzędziu monitorowania, które wolisz. Jedną z potencjalnych opcji jest przesyłanie strumieniowe wszystkich zdarzeń za pośrednictwem centrów zdarzeń do [usługi Azure Stream Analytics.](https://azure.microsoft.com/services/stream-analytics/) W usłudze Stream Analytics można pisać zapytania na żywo na podstawie wszelkich anomalii, średnich lub błędów z dzienników diagnostycznych. Usługi Stream Analytics umożliwiają wysyłanie informacji do innych źródeł danych, takich jak kolejki, tematy, sql, usługa Azure Cosmos DB lub usługa Power BI.

## <a name="next-steps"></a>Następne kroki

* [Zobacz, jak klient tworzy obsługę błędów za pomocą usługi Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Znajdź więcej przykładów i scenariuszy aplikacji logiki](../logic-apps/logic-apps-examples-and-scenarios.md)
