---
title: Interfejsy API protokołu HTTP w Durable Functions — Azure
description: Dowiedz się, jak zaimplementować interfejsy API protokołu HTTP w rozszerzeniu Durable Functions, aby Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 11ae418ddbe007c6fd5aa44ef22ed7fddec9c702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087271"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Interfejsy API protokołu HTTP w Durable Functions (Azure Functions)

Rozszerzenie zadania trwałego uwidacznia zestaw interfejsów API protokołu HTTP, które mogą być używane do wykonywania następujących zadań:

* Pobierz stan wystąpienia aranżacji.
* Wyślij zdarzenie do oczekującego wystąpienia aranżacji.
* Przerwij uruchomione wystąpienie aranżacji.

Każdy z tych interfejsów API protokołu HTTP jest operacją elementu webhook, która jest obsługiwana bezpośrednio przez rozszerzenie zadania trwałego. Nie są one specyficzne dla żadnej funkcji w aplikacji funkcji.

> [!NOTE]
> Te operacje mogą być również wywoływane bezpośrednio przy użyciu interfejsów API zarządzania wystąpieniami w klasie [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) . Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Odnajdowanie adresów URL interfejsu API protokołu HTTP

Klasa [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) uwidacznia interfejs API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) , którego można użyć do wygenerowania ładunku odpowiedzi HTTP zawierającego linki do wszystkich obsługiwanych operacji. Oto przykład funkcji wyzwalacza HTTP, która pokazuje, jak używać tego interfejsu API:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Te przykładowe funkcje generują następujące dane odpowiedzi JSON. Typ danych wszystkich pól to `string`.

| Pole                   |Opis                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |Identyfikator wystąpienia aranżacji. |
| **`statusQueryGetUri`**     |Adres URL stanu wystąpienia aranżacji. |
| **`sendEventPostUri`**      |Adres URL "zgłoś zdarzenie" wystąpienia aranżacji. |
| **`terminatePostUri`**      |Adres URL "Przerwij" wystąpienia aranżacji. |
| **`purgeHistoryDeleteUri`** |Adres URL "Przeczyść historię" wystąpienia aranżacji. |
| **`rewindPostUri`**         |przeglądania Adres URL "przewijania do tyłu" wystąpienia aranżacji. |

Oto przykład odpowiedzi:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "purgeHistoryDeleteUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> Format adresów URL elementu webhook może się różnić w zależności od używanej wersji hosta Azure Functions. Powyższy przykład dotyczy hosta Azure Functions 2. x.

## <a name="async-operation-tracking"></a>Śledzenie operacji asynchronicznych

Wspomniana wcześniej odpowiedź HTTP została zaprojektowana w celu ułatwienia wdrożenia długotrwałych asynchronicznych interfejsów API protokołu HTTP przy użyciu Durable Functions. Jest to czasami nazywane *wzorcem klienta sondowania*. Przepływ klient/serwer działa w następujący sposób:

1. Klient wysyła żądanie HTTP w celu uruchomienia długotrwałego procesu, takiego jak funkcja programu Orchestrator.
2. Docelowy wyzwalacz http zwraca odpowiedź HTTP 202 z `Location` nagłówkiem `statusQueryGetUri` o wartości.
3. Klient sonduje adres URL w `Location` nagłówku. Nadal widzisz odpowiedzi HTTP 202 z `Location` nagłówkiem.
4. Po zakończeniu wystąpienia (lub niepowodzenia) punkt końcowy w `Location` nagłówku zwraca http 200.

Ten protokół umożliwia koordynowanie długotrwałych procesów z zewnętrznymi klientami lub usługami, które obsługują sondowanie punktu końcowego http i po `Location` nagłówku. Podstawowe elementy są już wbudowane w Durable Functions interfejsów API protokołu HTTP.

> [!NOTE]
> Domyślnie wszystkie działania oparte na protokole HTTP zapewniane przez [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) obsługują standardowy wzorzec operacji asynchronicznej. Ta funkcja umożliwia osadzenie długotrwałej funkcji trwałej w ramach przepływu pracy Logic Apps. Więcej szczegółów na Logic Apps temat obsługi asynchronicznych wzorców HTTP można znaleźć w temacie [Azure Logic Apps akcje przepływu pracy i](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)informacje o wyzwalaczach.

## <a name="http-api-reference"></a>Dokumentacja interfejsu API protokołu HTTP

Wszystkie interfejsy API protokołu HTTP implementowane przez rozszerzenie przyjmują następujące parametry. Typem danych wszystkich parametrów jest `string`.

| Parametr        | Typ parametru  | Opis |
|------------------|-----------------|-------------|
| **`taskHub`**    | Ciąg zapytania    | Nazwa [centrum zadań](durable-functions-task-hubs.md). Jeśli nie zostanie określony, założono nazwę centrum zadań bieżącej aplikacji funkcji. |
| **`connection`** | Ciąg zapytania    | **Nazwa** parametrów połączenia dla konta magazynu. Jeśli nie zostanie określony, przyjmuje się domyślne parametry połączenia dla aplikacji funkcji. |
| **`systemKey`**  | Ciąg zapytania    | Klucz autoryzacji wymagany do wywołania interfejsu API. |

`systemKey`jest kluczem autoryzacji generowanym automatycznie przez hosta Azure Functions. Zapewnia ona specjalny dostęp do interfejsów API rozszerzenia zadania trwałego i można nimi zarządzać w taki sam sposób jak [inne klucze autoryzacji](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Najprostszym sposobem odnajdowania `systemKey` wartości jest `CreateCheckStatusResponse` użycie interfejsu API wymienionego wcześniej.

W następnych sekcjach znajdują się określone interfejsy API protokołu HTTP obsługiwane przez rozszerzenie i przedstawiono przykłady ich użycia.

### <a name="get-instance-status"></a>Pobierz stan wystąpienia

Pobiera stan określonego wystąpienia aranżacji.

#### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole                   | Typ parametru  | Opis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | Identyfikator wystąpienia aranżacji. |
| **`showInput`**         | Ciąg zapytania    | Opcjonalny parametr. W przypadku ustawienia `false`wartości dane wejściowe funkcji nie zostaną uwzględnione w ładunku odpowiedzi.|
| **`showHistory`**       | Ciąg zapytania    | Opcjonalny parametr. W przypadku wybrania tej opcji `true`historia wykonywania aranżacji zostanie uwzględniona w ładunku odpowiedzi.|
| **`showHistoryOutput`** | Ciąg zapytania    | Opcjonalny parametr. W przypadku ustawienia `true`wartości dane wyjściowe funkcji zostaną uwzględnione w historii wykonywania aranżacji.|
| **`createdTimeFrom`**   | Ciąg zapytania    | Opcjonalny parametr. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień, które zostały utworzone w lub po danej sygnaturze czasowej ISO8601.|
| **`createdTimeTo`**     | Ciąg zapytania    | Opcjonalny parametr. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień, które zostały utworzone w czasie lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**     | Ciąg zapytania    | Opcjonalny parametr. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień na podstawie ich stanu czasu wykonywania. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz temat [wykonywanie zapytań dotyczących wystąpień](durable-functions-instance-management.md) . |

#### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 200 (OK)** : Określone wystąpienie jest w stanie ukończenia.
* **HTTP 202 (zaakceptowane)** : Określone wystąpienie jest w toku.
* **HTTP 400 (złe żądanie)** : Określone wystąpienie nie powiodło się lub zostało przerwane.
* **HTTP 404 (nie znaleziono)** : Określone wystąpienie nie istnieje lub nie zostało uruchomione.
* **HTTP 500 (wewnętrzny błąd serwera)** : Określone wystąpienie nie powiodło się z powodu nieobsługiwanego wyjątku.

Ładunek odpowiedzi dla przypadków **http 200** i **http 202** jest obiektem JSON z następującymi polami:

| Pole                 | Typ danych | Opis |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | ciąg    | Stan środowiska uruchomieniowego wystąpienia. Wartości to *systemem*, *oczekujące*, *anulowane*, *zwolniony*, *Ukończone*. |
| **`input`**           | JSON      | Dane JSON używane do inicjowania wystąpienia. To pole jest `null` `showInput` ustawione na `false`wartość.|
| **`customStatus`**    | JSON      | Dane JSON używane dla niestandardowego stanu aranżacji. To pole jest `null` nieskonfigurowane. |
| **`output`**          | JSON      | Dane wyjściowe JSON wystąpienia. To pole jest `null` , jeśli wystąpienie nie jest w stanie ukończone. |
| **`createdTime`**     | ciąg    | Godzina utworzenia wystąpienia. Używa rozszerzonej notacji ISO 8601. |
| **`lastUpdatedTime`** | ciąg    | Godzina, o której wystąpienie zostało ostatnio utrwalone. Używa rozszerzonej notacji ISO 8601. |
| **`historyEvents`**   | JSON      | Tablica JSON zawierająca historię wykonywania aranżacji. To pole jest `null` , `showHistory` chyba że parametr ciągu zapytania jest ustawiony `true`na. |

Oto przykładowy ładunek odpowiedzi, w tym historia wykonywania aranżacji i dane wyjściowe działań (sformatowane pod kątem czytelności):

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

Odpowiedź **http 202** zawiera również nagłówek odpowiedzi **lokalizacji** , który odwołuje się do tego samego `statusQueryGetUri` adresu URL, co pole wymienione wcześniej.

### <a name="get-all-instances-status"></a>Pobierz stan wszystkich wystąpień

Można także zbadać stan wszystkich wystąpień, usuwając `instanceId` z żądania "Pobierz stan wystąpienia". W takim przypadku parametry podstawowe są takie same, jak "Pobierz stan wystąpienia". Parametry ciągu zapytania do filtrowania są również obsługiwane.

Należy pamiętać, że `connection` `code` są one opcjonalne. Jeśli masz anonimową autoryzację w funkcji, kod nie jest wymagany.
Jeśli nie chcesz używać innych parametrów połączenia magazynu innego niż zdefiniowane w ustawieniu aplikacji AzureWebJobsStorage, możesz bezpiecznie zignorować parametr ciągu zapytania połączenia.

#### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole                   | Typ parametru  | Opis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | Identyfikator wystąpienia aranżacji. |
| **`showInput`**         | Ciąg zapytania    | Opcjonalny parametr. W przypadku ustawienia `false`wartości dane wejściowe funkcji nie zostaną uwzględnione w ładunku odpowiedzi.|
| **`showHistory`**       | Ciąg zapytania    | Opcjonalny parametr. W przypadku wybrania tej opcji `true`historia wykonywania aranżacji zostanie uwzględniona w ładunku odpowiedzi.|
| **`showHistoryOutput`** | Ciąg zapytania    | Opcjonalny parametr. W przypadku ustawienia `true`wartości dane wyjściowe funkcji zostaną uwzględnione w historii wykonywania aranżacji.|
| **`createdTimeFrom`**   | Ciąg zapytania    | Opcjonalny parametr. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień, które zostały utworzone w lub po danej sygnaturze czasowej ISO8601.|
| **`createdTimeTo`**     | Ciąg zapytania    | Opcjonalny parametr. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień, które zostały utworzone w czasie lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**     | Ciąg zapytania    | Opcjonalny parametr. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień na podstawie ich stanu czasu wykonywania. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz temat [wykonywanie zapytań dotyczących wystąpień](durable-functions-instance-management.md) . |
| **`top`**               | Ciąg zapytania    | Opcjonalny parametr. Gdy ta wartość jest określona, ogranicza liczbę wystąpień zwracanych przez zapytanie. |

#### <a name="response"></a>Odpowiedź

Oto przykład ładunków odpowiedzi, w tym stanu aranżacji (sformatowany do czytelności):

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
> Ta operacja może być bardzo kosztowna w odniesieniu do operacji we/wy usługi Azure Storage, jeśli w tabeli wystąpień istnieje wiele wierszy. Więcej szczegółów dotyczących tabeli wystąpień można znaleźć w dokumentacji dotyczącej [wydajności i skalowania w Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .
>

Jeśli istnieje więcej wyników, w nagłówku odpowiedzi zwracany jest token kontynuacji.  Nazwa nagłówka to `x-ms-continuation-token`.

Jeśli ustawisz wartość tokenu kontynuacji w następnym nagłówku żądania, możesz uzyskać następną stronę wyników. Ta nazwa nagłówka żądania jest również `x-ms-continuation-token`.

### <a name="purge-single-instance-history"></a>Przeczyść historię pojedynczego wystąpienia

Usuwa historię i powiązane artefakty dla określonego wystąpienia aranżacji.

#### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Identyfikator wystąpienia aranżacji. |

#### <a name="response"></a>Odpowiedź

Można zwrócić następujące wartości kodów stanu HTTP.

* **HTTP 200 (OK)** : Historia wystąpień została pomyślnie przeczyszczona.
* **HTTP 404 (nie znaleziono)** : Określone wystąpienie nie istnieje.

Ładunek odpowiedzi dla przypadku **HTTP 200** jest obiektem JSON z następującym polem:

| Pole                  | Typ danych | Opis |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | Liczba usuniętych wystąpień. W przypadku pojedynczego wystąpienia, ta wartość powinna zawsze być `1`. |

Oto przykładowy ładunek odpowiedzi (sformatowany na potrzeby czytelności):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Przeczyść historię wielu wystąpień

Można również usunąć historię i powiązane artefakty dla wielu wystąpień w centrum zadań, usuwając `{instanceId}` z żądania "Przeczyść historię pojedynczego wystąpienia". Aby selektywnie przeczyścić historię wystąpień, Użyj tych samych filtrów opisanych w żądaniu "Pobierz wszystkie wystąpienia stanu".

#### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole                 | Typ parametru  | Opis |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Ciąg zapytania    | Filtruje listę przeczyszczonych wystąpień, które zostały utworzone w czasie lub po danej sygnaturze czasowej ISO8601.|
| **`createdTimeTo`**   | Ciąg zapytania    | Opcjonalny parametr. Jeśli ta wartość jest określona, filtruje listę przeczyszczonych wystąpień, które zostały utworzone w czasie lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**   | Ciąg zapytania    | Opcjonalny parametr. Jeśli ta wartość jest określona, program filtruje listę przeczyszczonych wystąpień na podstawie ich stanu czasu wykonywania. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz temat [wykonywanie zapytań dotyczących wystąpień](durable-functions-instance-management.md) . |

> [!NOTE]
> Ta operacja może być bardzo kosztowna w odniesieniu do operacji we/wy usługi Azure Storage, jeśli w tabelach wystąpień i/lub historia występuje wiele wierszy. Więcej informacji o tych tabelach można znaleźć w dokumentacji dotyczącej [wydajności i skalowania w Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .

#### <a name="response"></a>Odpowiedź

Można zwrócić następujące wartości kodów stanu HTTP.

* **HTTP 200 (OK)** : Historia wystąpień została pomyślnie przeczyszczona.
* **HTTP 404 (nie znaleziono)** : Nie znaleziono wystąpień pasujących do wyrażenia filtru.

Ładunek odpowiedzi dla przypadku **HTTP 200** jest obiektem JSON z następującym polem:

| Pole                   | Typ danych | Opis |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | Liczba usuniętych wystąpień. |

Oto przykładowy ładunek odpowiedzi (sformatowany na potrzeby czytelności):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Zgłoś zdarzenie

Wysyła komunikat z powiadomieniem o zdarzeniu do uruchomionego wystąpienia aranżacji.

#### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Identyfikator wystąpienia aranżacji. |
| **`eventName`**   | URL             | Nazwa zdarzenia, na które oczekuje wystąpienie aranżacji docelowej. |
| **`{content}`**   | Żądaj zawartości | Ładunek zdarzenia w formacie JSON. |

#### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (zaakceptowane)** : Zgłoszone zdarzenie zostało zaakceptowane do przetwarzania.
* **HTTP 400 (złe żądanie)** : Zawartość żądania nie jest typu `application/json` lub jest nieprawidłowa w formacie JSON.
* **HTTP 404 (nie znaleziono)** : Nie znaleziono określonego wystąpienia.
* **HTTP 410 (usunięty)** : Określone wystąpienie zostało ukończone lub zakończyło się niepowodzeniem i nie można przetworzyć żadnych zgłoszonych zdarzeń.

Oto przykładowe żądanie, które wysyła ciąg `"incr"` json do wystąpienia oczekującego na zdarzenie o nazwie **Operation**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

### <a name="terminate-instance"></a>Przerwij wystąpienie

Kończy uruchomione wystąpienie aranżacji.

#### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry żądania dla tego interfejsu API zawierają zestaw domyślny opisany wcześniej, a także następujący unikatowy parametr.

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Identyfikator wystąpienia aranżacji. |
| **`reason`**      | Ciąg zapytania    | Opcjonalna. Przyczyna zakończenia wystąpienia aranżacji. |

#### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (zaakceptowane)** : Żądanie zakończenia zostało zaakceptowane do przetwarzania.
* **HTTP 404 (nie znaleziono)** : Nie znaleziono określonego wystąpienia.
* **HTTP 410 (usunięty)** : Określone wystąpienie zostało ukończone lub zakończyło się niepowodzeniem.

Oto przykładowe żądanie kończące uruchomione wystąpienie i określające powód **debugowania**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

### <a name="rewind-instance-preview"></a>Przewiń do tyłu (wersja zapoznawcza)

Przywraca niepowodzenie wystąpienia aranżacji do stanu uruchomienia przez odtwarzanie najnowszych operacji zakończonych niepowodzeniem.

#### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry żądania dla tego interfejsu API zawierają zestaw domyślny opisany wcześniej, a także następujący unikatowy parametr.

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | Identyfikator wystąpienia aranżacji. |
| **`reason`**      | Ciąg zapytania    | Opcjonalna. Przyczyna odwinięcia wystąpienia aranżacji. |

#### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (zaakceptowane)** : Żądanie przewijania do tyłu zostało zaakceptowane do przetwarzania.
* **HTTP 404 (nie znaleziono)** : Nie znaleziono określonego wystąpienia.
* **HTTP 410 (usunięty)** : Określone wystąpienie zostało zakończone lub zostało zakończone.

Oto przykładowe żądanie, które powoduje odwracanie wystąpienia zakończonego niepowodzeniem i określa przyczynę naprawienia:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

### <a name="signal-entity-preview"></a>Jednostka sygnału (wersja zapoznawcza)

Wysyła jednokierunkową wiadomość operacji do [jednostki trwałej](durable-functions-types-features-overview.md#entity-functions). Jeśli jednostka nie istnieje, zostanie utworzona automatycznie.

#### <a name="request"></a>Żądanie

Żądanie HTTP jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | Typ jednostki. |
| **`entityKey`**   | URL             | Unikatowa nazwa jednostki. |
| **`op`**          | Ciąg zapytania    | Opcjonalny. Nazwa operacji zdefiniowanej przez użytkownika do wywołania. |
| **`{content}`**   | Żądaj zawartości | Ładunek zdarzenia w formacie JSON. |

Oto przykładowe żądanie, które wysyła zdefiniowany przez użytkownika komunikat "Dodaj" do `Counter` jednostki o nazwie. `steps` Zawartość wiadomości jest wartością `5`. Jeśli jednostka jeszcze nie istnieje, zostanie utworzona przez to żądanie:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

#### <a name="response"></a>Odpowiedź

Ta operacja ma kilka możliwych odpowiedzi:

* **HTTP 202 (zaakceptowane)** : Operacja sygnału została zaakceptowana do przetwarzania asynchronicznego.
* **HTTP 400 (złe żądanie)** : Zawartość żądania nie jest typu `application/json`, nie jest prawidłowym kodem JSON lub ma nieprawidłową `entityKey` wartość.
* **HTTP 404 (nie znaleziono)** : Nie znaleziono `entityType` określonego.

Pomyślne żądanie HTTP nie zawiera żadnej zawartości odpowiedzi. Żądanie HTTP, które nie powiodło się, może zawierać informacje o błędzie w formacie JSON w treści odpowiedzi.

### <a name="query-entity-preview"></a>Obiekt zapytania (wersja zapoznawcza)

Pobiera stan określonej jednostki.

#### <a name="request"></a>Żądanie

Żądanie HTTP jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>Odpowiedź

Ta operacja ma dwie możliwe odpowiedzi:

* **HTTP 200 (OK)** : Określona jednostka istnieje.
* **HTTP 404 (nie znaleziono)** : Nie znaleziono określonej jednostki.

Pomyślna odpowiedź zawiera stan Zserializowany przez kod JSON jednostki jako jego zawartość.

#### <a name="example"></a>Przykład
Poniżej znajduje się przykład żądania HTTP, które pobiera stan istniejącej `Counter` jednostki o nazwie: `steps`

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Jeśli jednostka zawiera po prostu kilka kroków zapisanych `currentValue` w polu, zawartość odpowiedzi może wyglądać podobnie do następującej (sformatowanej pod kątem czytelności): `Counter`

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać błędy](durable-functions-error-handling.md)
