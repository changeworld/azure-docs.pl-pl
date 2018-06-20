---
title: Interfejsy API protokołu HTTP w funkcjach trwałe - Azure
description: Dowiedz się, jak wdrożyć interfejsy API protokołu HTTP w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 3c000e268c4c926991c3f1928f226065a436c6d2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264889"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Interfejsy API protokołu HTTP w funkcji trwałe (funkcje platformy Azure)

Rozszerzenie zadania trwałe udostępnia zestaw interfejsów API protokołu HTTP, który może służyć do wykonywania następujących zadań:

* Pobierz stan wystąpienia aranżacji.
* Wysyła zdarzenie do wystąpienia aranżacji oczekiwania.
* Przerwanie uruchomione wystąpienie aranżacji.


Każdy z poniższych interfejsów API HTTP jest operacją elementu webhook jest obsługiwany bezpośrednio przez rozszerzenie zadania trwałe. Nie są one właściwe dla dowolnej funkcji w funkcji aplikacji.

> [!NOTE]
> Te operacje może również wywoływany bezpośrednio za pomocą interfejsów API zarządzania wystąpienia na [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy. Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Adres URL HTTP interfejsu API odnajdywania

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy ujawnia [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) interfejsu API, który może służyć do generowania odpowiedzi HTTP ładunek zawierający łącza do obsługiwanych operacji. Oto przykład funkcja wyzwalacza HTTP, który demonstruje sposób użycia tego interfejsu API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Ta funkcja przykład tworzy następujące dane JSON w odpowiedzi. Typ danych we wszystkich polach jest `string`.

| Pole             |Opis                           |
|-------------------|--------------------------------------|
| id                |Identyfikator wystąpienia aranżacji. |
| statusQueryGetUri |Adres URL stan wystąpienia aranżacji. |
| sendEventPostUri  |Adres URL "Zgłoś zdarzenia" wystąpienia aranżacji. |
| terminatePostUri  |"Zakończ" adres URL wystąpienia aranżacji. |

Oto przykład odpowiedzi:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Format adresów URL elementu webhook mogą się różnić w zależności od posiadanej wersji programu host usługi Azure Functions. Powyższy przykład dotyczy hosta Azure funkcji 2.0.

## <a name="async-operation-tracking"></a>Operacja asynchroniczna śledzenia

Odpowiedź HTTP wspomniano ułatwia wdrażanie asynchronicznej HTTP długotrwałe interfejsów API za pomocą funkcji trwałe. Jest to czasami określane jako *sondowania wzorca odbiorców*. Przepływ klient/serwer działa w następujący sposób:

1. Klient generuje żądanie HTTP do uruchomienia procesu wymagającą dużo czasu, takich jak funkcja programu orchestrator.
2. Wyzwalacz docelowych HTTP zwraca odpowiedź HTTP 202 z `Location` nagłówek o `statusQueryGetUri` wartość.
3. Klient sonduje adres URL w `Location` nagłówka. Nadal odpowiedzi HTTP 202 z `Location` nagłówka.
4. Gdy wystąpienie zakończeniu (lub nie powiedzie się), punktu końcowego w `Location` nagłówka zwraca 200 protokołu HTTP.

Ten protokół umożliwia koordynowanie procesy długotrwałe z klientami zewnętrznymi lub usług, które obsługują sondowania punktu końcowego HTTP i wykonując `Location` nagłówka. Podstawowe elementy już są wbudowane w trwałe funkcje API HTTP.

> [!NOTE]
> Domyślnie wszystkie działania oparte na protokole HTTP pochodzącymi [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) obsługują wzorzec standardowych operacji asynchronicznej. Ta funkcja umożliwia osadzanie funkcję trwałego długotrwałe jako część przepływu pracy aplikacji logiki. Więcej informacji na temat aplikacji logiki obsługę wzorców asynchronicznych HTTP można znaleźć w [Azure Logic Apps przepływu pracy akcji i wyzwalaczy dokumentacji](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Dokumentacja interfejsu API HTTP

Wszystkie interfejsy API protokołu HTTP implementowana przez rozszerzenie podejmij następujące parametry. Typ danych wszystkie parametry jest `string`.

| Parametr  | Typ parametru  | Opis |
|------------|-----------------|-------------|
| instanceId | Adres URL             | Identyfikator wystąpienia aranżacji. |
| taskHub    | Ciąg zapytania    | Nazwa [Centrum zadań](durable-functions-task-hubs.md). Jeśli nie zostanie określony, przyjęto, że nazwa Centrum zadania bieżącej aplikacji funkcji. |
| połączenie | Ciąg zapytania    | **Nazwa** ciągu połączenia dla konta magazynu. Jeśli nie zostanie określona, przyjmowana jest domyślnego ciągu połączenia dla aplikacji funkcja. |
| systemKey  | Ciąg zapytania    | Klucz autoryzacji wymaganych do wywołania interfejsu API. |
| showHistory| Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiono `true`, historii wykonywania aranżacji zostaną uwzględnione w ładunku odpowiedzi.| 
| showHistoryOutput| Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiono `true`, działanie wyjściowe zostaną uwzględnione w historię wykonywania aranżacji.| 

`systemKey` jest klucz autoryzacji wygenerowana automatycznie przez hosta usługi Azure Functions. W szczególności nieograniczony dostęp do rozszerzenia zadania trwałe interfejsów API i można zarządzać w taki sam sposób jak [innych kluczy o autoryzacji](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Najprostszym sposobem, aby odnaleźć `systemKey` wartość jest za pomocą `CreateCheckStatusResponse` wspomniano interfejsu API.

Następne sekcje kilka obejmują określonych interfejsów API HTTP obsługiwane przez rozszerzenie i zawierają przykłady sposobu ich użycia.

### <a name="get-instance-status"></a>Pobierz stan wystąpienia

Pobiera stan wystąpienia określonego aranżacji.

#### <a name="request"></a>Żądanie

Funkcje 1.0 format żądania jest następujący:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Format 2.0 funkcji ma takie same parametry, ale ma nieco inny prefiks adresu URL:

```http
GET /runtime/webhooks/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Odpowiedź

Może być zwracany kilka wartości Kod stanu możliwe.

* **HTTP 200 (OK)**: określone wystąpienie jest w stanie ukończone.
* **HTTP 202 (zaakceptowane)**: określone wystąpienie jest w toku.
* **HTTP 400 (nieprawidłowe żądanie)**: określone wystąpienie nie powiodło się lub zostało przerwane.
* **HTTP 404 (nie znaleziono)**: określone wystąpienie nie istnieje lub nie została uruchomiona uruchomiona.

Ładunek odpowiedzi dla **200 protokołu HTTP** i **HTTP 202** przypadków jest to obiekt JSON z następującymi polami:

| Pole           | Typ danych | Opis |
|-----------------|-----------|-------------|
| runtimeStatus   | ciąg    | Stanu działania wystąpienia. Wartości to *systemem*, *oczekujące*, *anulowane*, *zwolniony*, *Ukończone*. |
| wejście           | JSON      | Dane JSON, używane do inicjowania wystąpienia. |
| customStatus    | JSON      | Dane JSON używane na potrzeby stanu aranżacji niestandardowych. To pole jest `null` Jeżeli nie jest ustawiony. |
| output          | JSON      | Dane wyjściowe JSON wystąpienia. To pole jest `null` Jeśli wystąpienie nie jest w stanie ukończone. |
| createdTime     | ciąg    | Podczas tworzenia tego wystąpienia. Używa ISO 8601 rozszerzony notacji. |
| lastUpdatedTime | ciąg    | Czas, jaką trwała ostatniego wystąpienia. Używa ISO 8601 rozszerzony notacji. |
| historyEvents   | JSON      | Tablica JSON zawierający historii wykonywania aranżacji. To pole jest `null` chyba że `showHistory` ustawiono parametr ciągu zapytania `true`.  | 

Oto przykład odpowiedzi ładunek wyjść historii i działanie wykonanie orchestration (sformatowany dla czytelności) w tym:

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** odpowiedzi zawiera również **lokalizacji** nagłówka odpowiedzi, który odwołuje się do tego samego adresu URL jako `statusQueryGetUri` pola wymienione wcześniej.

### <a name="get-all-instances-status"></a>Pobierz stan dla wszystkich wystąpień

Możesz także zbadać stan dla wszystkich wystąpień. Usuń `instanceId` z żądania "Get wystąpień w stan". Parametry są takie same jak "Get stan wystąpienia". 

#### <a name="request"></a>Żądanie

Funkcje 1.0 format żądania jest następujący:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Format 2.0 funkcji ma takie same parametry, ale nieco inny prefiks adresu URL: 

```http
GET /runtime/webhooks/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>Odpowiedź

Oto przykład ładunków odpowiedzi, takich jak stan orchestration (sformatowany dla czytelności):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Ta operacja może być bardzo kosztowna pod względem we/wy magazynu Azure, jeśli istnieje wiele wierszy w tabeli wystąpień. Więcej informacji na temat wystąpienia tabeli znajdują się w [wydajności i skalowania w funkcjach trwałe (usługi Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-perf-and-scale#instances-table) dokumentacji.
> 

### <a name="raise-event"></a>Wywołaj zdarzenie

Wysyła powiadomienie o zdarzeniu do uruchomionego wystąpienia aranżacji.

#### <a name="request"></a>Żądanie

Funkcje 1.0 format żądania jest następujący:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Format 2.0 funkcji ma takie same parametry, ale ma nieco inny prefiks adresu URL:

```http
POST /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Parametry dla tego interfejsu API to domyślny zestaw wspomniano powyżej, a także następujące parametry unikatowy żądania:

| Pole       | Typ parametru  | TType danych | Opis |
|-------------|-----------------|-----------|-------------|
| EventName   | Adres URL             | ciąg    | Nazwa zdarzenia, które czeka na wystąpienie docelowe aranżacji. |
| {content}   | Żądanie zawartości | JSON      | Ładunek zdarzenia w formacie JSON. |

#### <a name="response"></a>Odpowiedź

Może być zwracany kilka wartości Kod stanu możliwe.

* **HTTP 202 (zaakceptowane)**: zgłoszono zdarzeń zaakceptowano do przetwarzania.
* **HTTP 400 (nieprawidłowe żądanie)**: zawartość żądania nie typu `application/json` lub nie jest poprawne dane JSON.
* **HTTP 404 (nie znaleziono)**: nie można odnaleźć określonego wystąpienia.
* **HTTP 410 (Gone)**: określone wystąpienie ma zakończone lub nie powiodło się i nie może przetwarzać zgłoszono zdarzeń.

Poniżej przedstawiono przykładowe żądanie, która wysyła ciągu JSON `"incr"` do wystąpienia oczekiwania na zdarzenie o nazwie **operacji**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

### <a name="terminate-instance"></a>Przerwanie wystąpienia

Kończy uruchomione wystąpienie aranżacji.

#### <a name="request"></a>Żądanie

Funkcje 1.0 format żądania jest następujący:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Format 2.0 funkcji ma takie same parametry, ale ma nieco inny prefiks adresu URL:

```http
DELETE /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Parametry dla tego interfejsu API to wymienione wcześniej oraz następujący parametr unikatowy domyślny zestaw żądań.

| Pole       | Typ parametru  | Typ danych | Opis |
|-------------|-----------------|-----------|-------------|
| reason      | Ciąg zapytania    | ciąg    | Opcjonalny. Przyczyna przerywanie wystąpienia aranżacji. |

#### <a name="response"></a>Odpowiedź

Może być zwracany kilka wartości Kod stanu możliwe.

* **HTTP 202 (zaakceptowane)**: żądanie przerwania zaakceptowano do przetwarzania.
* **HTTP 404 (nie znaleziono)**: nie można odnaleźć określonego wystąpienia.
* **HTTP 410 (Gone)**: określone wystąpienie ma zakończone lub nie powiodło się.

Poniżej przedstawiono przykładowe żądanie, która kończy uruchomionego wystąpienia i określa przyczynę **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Informacje o sposobie obsługi błędów](durable-functions-error-handling.md)
