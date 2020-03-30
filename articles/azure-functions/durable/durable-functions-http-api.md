---
title: Interfejsy API HTTP w funkcjach trwałych — funkcje platformy Azure
description: Dowiedz się, jak zaimplementować interfejsy API HTTP w rozszerzeniu Funkcje trwałe dla usługi Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278170"
---
# <a name="http-api-reference"></a>Odwołanie do interfejsu API HTTP

Rozszerzenie Funkcje trwałe udostępnia zestaw wbudowanych interfejsów API HTTP, które mogą być używane do wykonywania zadań zarządzania w [aranżacjach,](durable-functions-types-features-overview.md#orchestrator-functions) [encjach](durable-functions-types-features-overview.md#entity-functions)i [centrach zadań.](durable-functions-task-hubs.md) Te interfejsy API HTTP są elementami webhook rozszerzalności, które są autoryzowane przez hosta usługi Azure Functions, ale obsługiwane bezpośrednio przez rozszerzenie funkcje trwałe.

Wszystkie interfejsy API HTTP zaimplementowane przez rozszerzenie wymagają następujących parametrów. Typ danych wszystkich parametrów to `string`.

| Parametr        | Typ parametru  | Opis |
|------------------|-----------------|-------------|
| **`taskHub`**    | Ciąg zapytania    | Nazwa centrum [zadań](durable-functions-task-hubs.md). Jeśli nie zostanie określony, przyjmuje się nazwę centrum zadań bieżącej aplikacji funkcji. |
| **`connection`** | Ciąg zapytania    | **Nazwa** ciągu połączenia dla konta magazynu. Jeśli nie zostanie określony, zakłada się domyślny ciąg połączenia dla aplikacji funkcyjnej. |
| **`systemKey`**  | Ciąg zapytania    | Klucz autoryzacji wymagany do wywołania interfejsu API. |

`systemKey`jest kluczem autoryzacji automatycznie generowane przez hosta usługi Azure Functions. W szczególności udziela dostępu do interfejsów API rozszerzenia trwałe zadanie i może być zarządzany w taki sam sposób jak [inne klucze autoryzacji.](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API) Można wygenerować adresy `taskHub` `connection`URL, `systemKey` które zawierają poprawne , i wartości ciągów zapytań przy użyciu interfejsów `createCheckStatusResponse` `createHttpManagementPayload` API wiązania [klienta aranżacji,](durable-functions-bindings.md#orchestration-client) takich jak `CreateCheckStatusResponse` i `CreateHttpManagementPayload` interfejsy API w .NET lub i interfejsów API w języku JavaScript.

Kilka następnych sekcji obejmuje specyficzne interfejsy API HTTP obsługiwane przez rozszerzenie i zawierają przykłady, w jaki sposób mogą być używane.

## <a name="start-orchestration"></a>Rozpocznij aranżację

Rozpoczyna wykonywanie nowego wystąpienia określonej funkcji koordynatora.

### <a name="request"></a>Żądanie

W przypadku wersji 1.x środowiska wykonawczego Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

W wersji 2.x środowiska wykonawczego Functions format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujące unikatowe parametry:

| Pole              | Typ parametru  | Opis |
|--------------------|-----------------|-------------|
| **`functionName`** | Adres URL             | Nazwa funkcji koordynatora do uruchomienia. |
| **`instanceId`**   | Adres URL             | Parametr opcjonalny. Identyfikator wystąpienia aranżacji. Jeśli nie zostanie określony, funkcja koordynatora rozpocznie się od identyfikatora wystąpienia losowego. |
| **`{content}`**    | Żądanie zawartości | Element opcjonalny. Wejście funkcji orkiestry w formacie JSON. |

### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (Zaakceptowane)**: Określona funkcja koordynatora została zaplanowana do uruchomienia. Nagłówek `Location` odpowiedzi zawiera adres URL do sondowania stanu aranżacji.
* **HTTP 400 (Złe żądanie)**: Określona funkcja koordynatora nie istnieje, określony identyfikator wystąpienia był nieprawidłowy lub zawartość żądania była nieprawidłowa JSON.

Poniżej przedstawiono przykładowe żądanie, które uruchamia funkcję koordynatora `RestartVMs` i zawiera ładunek obiektu JSON:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

Ładunek odpowiedzi dla przypadków **HTTP 202** jest obiektem JSON z następującymi polami:

| Pole                       | Opis                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |Identyfikator wystąpienia aranżacji. |
| **`statusQueryGetUri`**     |Adres URL stanu wystąpienia aranżacji. |
| **`sendEventPostUri`**      |Adres URL "raise event" wystąpienia aranżacji. |
| **`terminatePostUri`**      |Adres URL "zakończenia" wystąpienia aranżacji. |
| **`purgeHistoryDeleteUri`** |Adres URL "historii przeczyszczenia" wystąpienia aranżacji. |
| **`rewindPostUri`**         |(wersja zapoznawcza) Adres URL "przewijania" wystąpienia aranżacji. |

Typ danych wszystkich pól `string`to .

Oto przykład ładunku odpowiedzi dla wystąpienia `abc123` aranżacji z jako jego identyfikator (sformatowany dla czytelności):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Odpowiedź HTTP ma być zgodna z *wzorcem konsumenta sondowania.* Zawiera również następujące znaczące nagłówki odpowiedzi:

* **Lokalizacja:** Adres URL punktu końcowego stanu. Ten adres URL zawiera `statusQueryGetUri` tę samą wartość co pole.
* **Ponów próbę po:** Liczba sekund oczekiwania między operacjami sondowania. Wartością domyślną jest `10`.

Aby uzyskać więcej informacji na temat asynchroniiowego wzorca sondowania HTTP, zobacz dokumentację [śledzenia operacji asynchronii HTTP.](durable-functions-http-features.md#async-operation-tracking)

## <a name="get-instance-status"></a>Uzyskaj stan wystąpienia

Pobiera stan określonego wystąpienia aranżacji.

### <a name="request"></a>Żądanie

W przypadku wersji 1.x środowiska wykonawczego Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

W wersji 2.x środowiska wykonawczego Functions format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujące unikatowe parametry:

| Pole                   | Typ parametru  | Opis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`showInput`**         | Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiona na `false`, wejście funkcji nie zostaną uwzględnione w ładunku odpowiedzi.|
| **`showHistory`**       | Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiona na `true`, historia wykonywania aranżacji zostaną uwzględnione w ładunku odpowiedzi.|
| **`showHistoryOutput`** | Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiona na `true`, dane wyjściowe funkcji zostaną uwzględnione w historii wykonywania aranżacji.|
| **`createdTimeFrom`**   | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę zwróconych wystąpień, które zostały utworzone w lub po podanej sygnatury czasowej ISO8601.|
| **`createdTimeTo`**     | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę zwróconych wystąpień, które zostały utworzone w lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**     | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę zwróconych wystąpień na podstawie ich stanu środowiska uruchomieniowego. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz artykuł [Wystąpienia kwerend.](durable-functions-instance-management.md) |

### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 200 (OK)**: Określone wystąpienie jest w stanie ukończonym.
* **HTTP 202 (Zaakceptowane)**: Określone wystąpienie jest w toku.
* **HTTP 400 (Złe żądanie)**: Określone wystąpienie nie powiodło się lub zostało zakończone.
* **HTTP 404 (Nie znaleziono)**: Określone wystąpienie nie istnieje lub nie zostało uruchomione.
* **HTTP 500 (Wewnętrzny błąd serwera)**: Określone wystąpienie nie powiodło się z nieobsługiwałem wyjątku.

Ładunek odpowiedzi dla przypadków **HTTP 200** i **HTTP 202** jest obiektem JSON z następującymi polami:

| Pole                 | Typ danych | Opis |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | ciąg    | Stan środowiska wykonawczego wystąpienia. Wartości obejmują *uruchomione,* *oczekujące,* *nie powiodło się,* *Anulowane,* *Zakończone,* *Zakończone*. |
| **`input`**           | JSON      | Dane JSON używane do inicjowania wystąpienia. To pole `null` jest, jeśli parametr ciągu `showInput` kwerendy jest ustawiony na `false`.|
| **`customStatus`**    | JSON      | Dane JSON używane dla stanu aranżacji niestandardowej. To pole `null` jest, jeśli nie jest ustawione. |
| **`output`**          | JSON      | Dane wyjściowe JSON wystąpienia. To pole `null` jest, jeśli wystąpienie nie jest w stanie ukończonym. |
| **`createdTime`**     | ciąg    | Czas, w którym zostało utworzone wystąpienie. Używa rozszerzonego notacji ISO 8601. |
| **`lastUpdatedTime`** | ciąg    | Czas, w którym wystąpienie ostatnio utrwalone. Używa rozszerzonego notacji ISO 8601. |
| **`historyEvents`**   | JSON      | Tablica JSON zawierająca historię wykonywania aranżacji. To pole `null` jest, chyba że parametr ciągu `showHistory` kwerendy jest ustawiony na `true`. |

Oto przykładowy ładunek odpowiedzi, w tym historia wykonania aranżacji i dane wyjściowe działania (sformatowane pod kątem czytelności):

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

Odpowiedź **HTTP 202** zawiera również nagłówek odpowiedzi **na lokalizację,** który odwołuje się do tego samego adresu URL, co `statusQueryGetUri` pole wymienione wcześniej.

## <a name="get-all-instances-status"></a>Uzyskaj stan wszystkich wystąpień

Można również zbadać stan wszystkich wystąpień, `instanceId` usuwając z żądania "Pobierz stan wystąpienia". W takim przypadku podstawowe parametry są takie same jak "Pobierz stan wystąpienia". Obsługiwane są również parametry ciągu zapytania do filtrowania.

Jedną z rzeczy, `connection` `code` o których należy pamiętać, jest to, że i są opcjonalne. Jeśli masz anonimową uwierzytelniania na `code` funkcji, a następnie nie jest wymagane.
Jeśli nie chcesz używać innego ciągu połączenia magazynu innego niż zdefiniowany w ustawieniu aplikacji AzureWebJobsStorage, możesz bezpiecznie zignorować parametr ciągu zapytania o połączenie.

### <a name="request"></a>Żądanie

W przypadku wersji 1.x środowiska wykonawczego Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

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

W wersji 2.x środowiska wykonawczego Functions format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

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

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujące unikatowe parametry:

| Pole                   | Typ parametru  | Opis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`showInput`**         | Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiona na `false`, wejście funkcji nie zostaną uwzględnione w ładunku odpowiedzi.|
| **`showHistory`**       | Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiona na `true`, historia wykonywania aranżacji zostaną uwzględnione w ładunku odpowiedzi.|
| **`showHistoryOutput`** | Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiona na `true`, dane wyjściowe funkcji zostaną uwzględnione w historii wykonywania aranżacji.|
| **`createdTimeFrom`**   | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę zwróconych wystąpień, które zostały utworzone w lub po podanej sygnatury czasowej ISO8601.|
| **`createdTimeTo`**     | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę zwróconych wystąpień, które zostały utworzone w lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**     | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę zwróconych wystąpień na podstawie ich stanu środowiska uruchomieniowego. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz artykuł [Wystąpienia kwerend.](durable-functions-instance-management.md) |
| **`top`**               | Ciąg zapytania    | Parametr opcjonalny. Po określeniu ogranicza liczbę wystąpień zwracanych przez kwerendę. |

### <a name="response"></a>Odpowiedź

Oto przykład ładunków odpowiedzi, w tym stan aranżacji (sformatowany pod kątem czytelności):

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
> Ta operacja może być bardzo kosztowne pod względem we/wy usługi Azure Storage, jeśli istnieje wiele wierszy w instances tabeli. Więcej szczegółów na temat tabeli wystąpienia można znaleźć w [wydajności i skalowania w dokumentacji funkcji trwałych (usługi Azure Functions).](durable-functions-perf-and-scale.md#instances-table)
>

Jeśli istnieje więcej wyników, token kontynuacji jest zwracany w nagłówku odpowiedzi.  Nazwa nagłówka to `x-ms-continuation-token`.

Jeśli ustawisz wartość tokenu kontynuacji w nagłówku następnego żądania, możesz uzyskać następną stronę wyników. Ta nazwa nagłówka żądania `x-ms-continuation-token`jest również .

## <a name="purge-single-instance-history"></a>Czyszczenie historii pojedynczego wystąpienia

Usuwa historię i powiązane artefakty dla określonego wystąpienia aranżacji.

### <a name="request"></a>Żądanie

W przypadku wersji 1.x środowiska wykonawczego Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

W wersji 2.x środowiska wykonawczego Functions format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | Adres URL             | Identyfikator wystąpienia aranżacji. |

### <a name="response"></a>Odpowiedź

Można zwrócić następujące wartości kodu stanu HTTP.

* **HTTP 200 (OK)**: Historia wystąpień została pomyślnie wyczyszczona.
* **HTTP 404 (Nie znaleziono)**: Określone wystąpienie nie istnieje.

Ładunek odpowiedzi dla sprawy **HTTP 200** jest obiektem JSON z następującym polem:

| Pole                  | Typ danych | Opis |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | liczba całkowita   | Liczba usuniętych wystąpień. W przypadku pojedynczego wystąpienia ta `1`wartość powinna być zawsze . |

Oto przykładowy ładunek odpowiedzi (sformatowany pod kątem czytelności):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Przeczyść historię wielu wystąpień

Można również usunąć historię i powiązane artefakty dla wielu wystąpień `{instanceId}` w centrum zadań, usuwając je z żądania "Przeczyść historię pojedynczych wystąpień". Aby selektywnie przeczyścić historię wystąpień, należy użyć tych samych filtrów opisanych w żądaniu "Pobierz wszystkie wystąpienia stanu".

### <a name="request"></a>Żądanie

W przypadku wersji 1.x środowiska wykonawczego Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

W wersji 2.x środowiska wykonawczego Functions format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujące unikatowe parametry:

| Pole                 | Typ parametru  | Opis |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Ciąg zapytania    | Filtruje listę wyczyszczonych wystąpień, które zostały utworzone w lub po podanej sygnatury czasowej ISO8601.|
| **`createdTimeTo`**   | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę wyczyszczonych wystąpień, które zostały utworzone w lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**   | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę usuniętych wystąpień na podstawie ich stanu środowiska uruchomieniowego. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz artykuł [Wystąpienia kwerend.](durable-functions-instance-management.md) |

> [!NOTE]
> Ta operacja może być bardzo kosztowne pod względem we/wy usługi Azure Storage, jeśli istnieje wiele wierszy w wystąpieniach i/lub historii tabel. Więcej szczegółów na temat tych tabel można znaleźć w [wydajności i skalowania w dokumentacji funkcji trwałych (Usługi azure).](durable-functions-perf-and-scale.md#instances-table)

### <a name="response"></a>Odpowiedź

Można zwrócić następujące wartości kodu stanu HTTP.

* **HTTP 200 (OK)**: Historia wystąpień została pomyślnie wyczyszczona.
* **HTTP 404 (Nie znaleziono)**: Nie znaleziono żadnych wystąpień, które pasują do wyrażenia filtru.

Ładunek odpowiedzi dla sprawy **HTTP 200** jest obiektem JSON z następującym polem:

| Pole                   | Typ danych | Opis |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | liczba całkowita   | Liczba usuniętych wystąpień. |

Oto przykładowy ładunek odpowiedzi (sformatowany pod kątem czytelności):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Wydarzenie podbicie

Wysyła komunikat o zdarzeniu do uruchomionego wystąpienia aranżacji.

### <a name="request"></a>Żądanie

W przypadku wersji 1.x środowiska wykonawczego Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

W wersji 2.x środowiska wykonawczego Functions format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`eventName`**   | Adres URL             | Nazwa zdarzenia, na które oczekuje wystąpienie aranżacji docelowej. |
| **`{content}`**   | Żądanie zawartości | Ładunek zdarzeń w formacie JSON. |

### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (Zaakceptowane)**: Zdarzenie podniesione zostało zaakceptowane do przetworzenia.
* **HTTP 400 (Złe żądanie)**: Zawartość `application/json` żądania nie była typu lub nie była prawidłowa JSON.
* **HTTP 404 (Nie znaleziono)**: Nie znaleziono określonego wystąpienia.
* **HTTP 410 (Przeminęło)**: Określone wystąpienie zostało ukończone lub nie powiodło się i nie może przetworzyć żadnych zgłoszonych zdarzeń.

Oto przykładowe żądanie, które wysyła `"incr"` ciąg JSON do wystąpienia oczekującego na **operację**o nazwie zdarzenia:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="terminate-instance"></a>Zakończenie wystąpienia

Kończy uruchomione wystąpienie aranżacji.

### <a name="request"></a>Żądanie

W przypadku wersji 1.x środowiska wykonawczego Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

W wersji 2.x środowiska wykonawczego Functions format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujący unikatowy parametr.

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`reason`**      | Ciąg zapytania    | Element opcjonalny. Powodem zakończenia wystąpienia aranżacji. |

### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (Zaakceptowane)**: Żądanie zakończenia zostało zaakceptowane do przetworzenia.
* **HTTP 404 (Nie znaleziono)**: Nie znaleziono określonego wystąpienia.
* **HTTP 410 (Przeminęło)**: Określone wystąpienie zostało zakończone lub nie powiodło się.

Oto przykładowe żądanie, które kończy uruchomione wystąpienie i określa przyczynę **błędnego:**

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="rewind-instance-preview"></a>Wystąpienie przewijania do tyłu (wersja zapoznawcza)

Przywraca nieudane wystąpienie aranżacji do stanu uruchomionego, odtwarzając najnowsze operacje, które nie powiodły się.

### <a name="request"></a>Żądanie

W przypadku wersji 1.x środowiska wykonawczego Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

W wersji 2.x środowiska wykonawczego Functions format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujący unikatowy parametr.

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`reason`**      | Ciąg zapytania    | Element opcjonalny. Powodem przewijania instancji aranżacji. |

### <a name="response"></a>Odpowiedź

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (Zaakceptowane)**: Żądanie przewijania do tyłu zostało zaakceptowane do przetworzenia.
* **HTTP 404 (Nie znaleziono)**: Nie znaleziono określonego wystąpienia.
* **HTTP 410 (Przeminęło)**: Określone wystąpienie zostało zakończone lub zostało zakończone.

Oto przykładowe żądanie, które przewija nieudane wystąpienie i określa przyczynę **stałej:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="signal-entity"></a>Jednostka sygnału

Wysyła komunikat operacji jednokierunkowej do [trwałego elementu](durable-functions-types-features-overview.md#entity-functions). Jeśli encja nie istnieje, zostanie utworzona automatycznie.

> [!NOTE]
> Trwałe elementy są dostępne począwszy od funkcji trwałych 2.0.

### <a name="request"></a>Żądanie

Żądanie HTTP jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`entityName`**  | Adres URL             | Nazwa (typ) jednostki. |
| **`entityKey`**   | Adres URL             | Klucz (unikatowy identyfikator) jednostki. |
| **`op`**          | Ciąg zapytania    | Element opcjonalny. Nazwa operacji zdefiniowanej przez użytkownika do wywołania. |
| **`{content}`**   | Żądanie zawartości | Ładunek zdarzeń w formacie JSON. |

Oto przykładowe żądanie, które wysyła zdefiniowaną przez `Counter` użytkownika `steps`wiadomość "Dodaj" do encji o nazwie . Zawartość wiadomości jest wartością `5`. Jeśli jednostka jeszcze nie istnieje, zostanie utworzona przez to żądanie:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Domyślnie z [jednostek opartych na klasach w .NET](durable-functions-dotnet-entities.md#defining-entity-classes), określając `op` wartość `delete` spowoduje usunięcie stanu jednostki. Jeśli jednostka definiuje operację `delete`o nazwie , jednak, że operacja zdefiniowana przez użytkownika będą wywoływane zamiast.

### <a name="response"></a>Odpowiedź

Ta operacja ma kilka możliwych odpowiedzi:

* **HTTP 202 (Zaakceptowany)**: Operacja sygnału została zaakceptowana do przetwarzania asynchroniiowego.
* **HTTP 400 (Złe żądanie)**: Zawartość `application/json`żądania nie była typu, nie `entityKey` była prawidłowa JSON lub miała nieprawidłową wartość.
* **HTTP 404 (Nie znaleziono)**: Nie znaleziono określonego. `entityName`

Pomyślne żądanie HTTP nie zawiera żadnej zawartości w odpowiedzi. Nieudane żądanie HTTP może zawierać informacje o błędzie w formacie JSON w zawartości odpowiedzi.

## <a name="get-entity"></a>Pobierz encję

Pobiera stan określonej jednostki.

### <a name="request"></a>Żądanie

Żądanie HTTP jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Odpowiedź

Ta operacja ma dwie możliwe odpowiedzi:

* **HTTP 200 (OK)**: Istnieje określona jednostka.
* **HTTP 404 (Nie znaleziono)**: Nie znaleziono określonej jednostki.

Pomyślna odpowiedź zawiera stan serializowany JSON jednostki jako jego zawartość.

### <a name="example"></a>Przykład
Poniższe przykładowe żądanie HTTP pobiera `Counter` stan `steps`istniejącej jednostki o nazwie:

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Jeśli `Counter` encja po prostu zawierała kilka `currentValue` kroków zapisanych w polu, zawartość odpowiedzi może wyglądać następująco (sformatowana pod kątem czytelności):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Lista jednostek

Można wyszukiwać dla wielu jednostek według nazwy jednostki lub według daty ostatniej operacji.

### <a name="request"></a>Żądanie

Żądanie HTTP jest sformatowane w następujący sposób (wiele wierszy jest wyświetlanych dla jasności):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Parametry żądania dla tego interfejsu API obejmują domyślny zestaw wymieniony wcześniej, a także następujące unikatowe parametry:

| Pole                       | Typ parametru  | Opis |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | Adres URL             | Element opcjonalny. Po określeniu filtruje listę zwróconych jednostek według ich nazwy jednostki (bez uwzględniania wielkości liter). |
| **`fetchState`**            | Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiona na `true`, stan jednostki zostaną uwzględnione w ładunku odpowiedzi. |
| **`lastOperationTimeFrom`** | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę zwróconych jednostek, które przetworzyły operacje po podanej sygnatury czasowej ISO8601. |
| **`lastOperationTimeTo`**   | Ciąg zapytania    | Parametr opcjonalny. Po określeniu filtruje listę zwróconych jednostek, które przetworzyły operacje przed daną sygnaturą czasową ISO8601. |
| **`top`**                   | Ciąg zapytania    | Parametr opcjonalny. Po określeniu ogranicza liczbę jednostek zwracanych przez kwerendę. |


### <a name="response"></a>Odpowiedź

Pomyślna odpowiedź HTTP 200 zawiera szeregowaną tablicę jednostek json i opcjonalnie stan każdej jednostki.

Domyślnie operacja zwraca pierwsze 100 jednostek, które odpowiadają kryteriom kwerendy. Obiekt wywołujący może określić wartość `top` parametru ciągu kwerendy, aby zwrócić inną maksymalną liczbę wyników. Jeśli istnieje więcej wyników poza to, co jest zwracane, token kontynuacji jest również zwracany w nagłówku odpowiedzi. Nazwa nagłówka to `x-ms-continuation-token`.

Jeśli ustawisz wartość tokenu kontynuacji w nagłówku następnego żądania, możesz uzyskać następną stronę wyników. Ta nazwa nagłówka żądania `x-ms-continuation-token`jest również .

### <a name="example---list-all-entities"></a>Przykład - lista wszystkich encji

Poniższe przykładowe żądanie HTTP zawiera listę wszystkich encji w Centrum zadań:

```http
GET /runtime/webhooks/durabletask/entities
```

Odpowiedź JSON może wyglądać następująco (sformatowana pod kątem czytelności):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Przykład - filtrowanie listy encji

Poniższy przykład żądania HTTP wyświetla tylko dwie `counter` pierwsze jednostki typu, a także pobiera ich stan:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

Odpowiedź JSON może wyglądać następująco (sformatowana pod kątem czytelności):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak monitorować trwałe funkcje za pomocą usługi Application Insights](durable-functions-diagnostics.md)