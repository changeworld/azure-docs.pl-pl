---
title: Niestandardowe programy obsługi usług Azure Functions (wersja zapoznawcza)
description: Dowiedz się, jak używać usługi Azure Functions w dowolnym języku lub wersji środowiska wykonawczego.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479256"
---
# <a name="azure-functions-custom-handlers-preview"></a>Niestandardowe programy obsługi usług Azure Functions (wersja zapoznawcza)

Każda aplikacja Functions jest wykonywana przez program obsługi specyficzne dla języka. Chociaż usługa Azure Functions domyślnie obsługuje wiele [programów obsługi języka,](./supported-languages.md) istnieją przypadki, w których można chcieć dodatkowej kontroli nad środowiskiem wykonywania aplikacji. Programy obsługi niestandardowej daje ten dodatkowy kontrolka.

Programy obsługi niestandardowej są lekkie serwery sieci web, które odbierają zdarzenia z hosta functions. Każdy język, który obsługuje podstawowe http można zaimplementować program obsługi niestandardowej.

Programy obsługi niestandardowej najlepiej nadają się do sytuacji, w których chcesz:

- Implementowanie aplikacji Functions w języku wykraczacm poza oficjalnie obsługiwane języki
- Implementowanie aplikacji Functions w wersji językowej lub środowiska wykonawczego, które nie są domyślnie obsługiwane
- Mają szczegółową kontrolę nad środowiskiem wykonywania aplikacji

W niestandardowych programach obsługi wszystkie [wyzwalacze oraz powiązania wejściowe i wyjściowe](./functions-triggers-bindings.md) są obsługiwane za pośrednictwem [pakietów rozszerzeń.](./functions-bindings-register.md)

## <a name="overview"></a>Omówienie

Na poniższym diagramie przedstawiono relację między hostem funkcji a serwerem sieci web zaimplementowanym jako program obsługi niestandardowej.

![Omówienie programu obsługi niestandardowej usługi Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Zdarzenia wyzwalają żądanie wysłane do hosta funkcji. Zdarzenie przenosi nieprzetworzony ładunek HTTP (dla funkcji wyzwalanych przez HTTP bez powiązań) lub ładunek, który przechowuje dane powiązania wejściowego dla funkcji.
- Host functions następnie proxy żądania do serwera sieci web, wystawiając [ładunek żądania](#request-payload).
- Serwer sieci web wykonuje poszczególne funkcje i zwraca [ładunek odpowiedzi](#response-payload) do hosta functions.
- Funkcje hosta proxy odpowiedzi jako ładunku wiązania danych wyjściowych do obiektu docelowego.

Aplikacja usługi Azure Functions zaimplementowana jako program obsługi niestandardowej musi skonfigurować pliki *host.json* i *function.json* zgodnie z kilkoma konwencjami.

## <a name="application-structure"></a>Struktura aplikacji

Aby zaimplementować program obsługi niestandardowej, potrzebujesz następujących aspektów do aplikacji:

- Plik *host.json* w katalogu głównym aplikacji
- Plik *function.json* dla każdej funkcji (wewnątrz folderu, który pasuje do nazwy funkcji)
- Polecenie, skrypt lub plik wykonywalny, który uruchamia serwer sieci Web

Na poniższym diagramie pokazano, jak te pliki wyglądają w systemie plików dla funkcji o nazwie "order".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Konfigurowanie

Aplikacja jest konfigurowana za pośrednictwem pliku *host.json.* Ten plik informuje hosta functions, gdzie wysyłać żądania, wskazując serwer sieci web zdolny do przetwarzania zdarzeń HTTP.

Niestandardowy program obsługi jest definiowany przez skonfigurowanie pliku *host.json* ze `httpWorker` szczegółami dotyczącymi uruchamiania serwera sieci web za pośrednictwem sekcji.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

Sekcja `httpWorker` wskazuje cel zdefiniowany przez `defaultExecutablePath`plik . Miejsce docelowe wykonania może być poleceniem, plikiem wykonywalnym lub plikiem, w którym zaimplementowano serwer sieci web.

W przypadku aplikacji `defaultExecutablePath` skryptowych wskazuje środowisko wykonawcze `defaultWorkerPath` języka skryptu i wskazuje lokalizację pliku skryptu. W poniższym przykładzie pokazano, jak aplikacja JavaScript w pliku Node.js jest skonfigurowana jako program obsługi niestandardowej.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Można również przekazać argumenty `arguments` przy użyciu tablicy:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Argumenty są niezbędne dla wielu konfiguracji debugowania. Zobacz [debugowanie](#debugging) sekcji, aby uzyskać więcej szczegółów.

> [!NOTE]
> Plik *host.json* musi znajdować się na tym samym poziomie w strukturze katalogów co uruchomiony serwer sieci web. Niektóre języki i narzędzia mogą domyślnie nie umieszczać tego pliku w katalogu głównym aplikacji.

#### <a name="bindings-support"></a>Obsługa powiązań

Standardowe wyzwalacze wraz z powiązaniami wejściowymi i wyjściowymi są dostępne przez odwoływanie się do [pakietów rozszerzeń](./functions-bindings-register.md) w pliku *host.json.*

### <a name="function-metadata"></a>Metadane funkcji

W przypadku użycia z programem obsługi niestandardowej zawartość *function.json* nie różnią się od sposobu definiowania funkcji w dowolnym innym kontekście. Jedynym wymaganiem jest to, że pliki *function.json* muszą znajdować się w folderze o nazwie, aby dopasować nazwę funkcji.

### <a name="request-payload"></a>Żądanie ładunku

Ładunek żądania dla czystych funkcji HTTP jest nieprzetworzonym ładunkiem żądania HTTP. Funkcje CZYSTEGO HTTP są definiowane jako funkcje bez powiązań wejściowych lub wyjściowych, które zwracają odpowiedź HTTP.

Każdy inny typ funkcji, która zawiera dane wejściowe, powiązania danych wyjściowych lub jest wyzwalany za pośrednictwem źródła zdarzeń innych niż HTTP mają ładunek żądania niestandardowego.

Poniższy kod reprezentuje ładunek żądania próbki. Ładunek zawiera strukturę JSON `Data` z `Metadata`dwoma członkami: i .

Element `Data` członkowski zawiera klucze, które pasują do nazw danych wejściowych i wyzwalaczy zgodnie z definicją w tablicy powiązań w pliku *function.json.*

Element `Metadata` członkowski zawiera [metadane wygenerowane ze źródła zdarzeń](./functions-bindings-expressions-patterns.md#trigger-metadata).

Biorąc pod uwagę powiązania zdefiniowane w następującym pliku *function.json:*

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Ładunek żądania podobny do tego przykładu jest zwracany:

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>Ładowność odpowiedzi

Zgodnie z konwencją odpowiedzi na funkcje są formatowane jako pary klucz/wartość. Obsługiwane klucze obejmują:

| <nobr>Klucz ładunku</nobr>   | Typ danych | Uwagi                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Przechowuje wartości odpowiedzi `bindings` zdefiniowane przez tablicę plik *function.json.*<br /><br />Na przykład jeśli funkcja jest skonfigurowana z powiązaniem danych wyjściowych `Outputs` magazynu obiektów `blob`blob o nazwie "blob", a następnie zawiera klucz o nazwie , który jest ustawiony na wartość obiektu blob. |
| `Logs`        | tablica     | Komunikaty są wyświetlane w dziennikach wywołania funkcji.<br /><br />Podczas uruchamiania na platformie Azure komunikaty są wyświetlane w usłudze Application Insights. |
| `ReturnValue` | ciąg    | Służy do udzielania odpowiedzi, gdy `$return` dane wyjściowe są skonfigurowane tak, jak w pliku *function.json.* |

Zobacz [przykład przykładu dla przykładowego ładunku](#bindings-implementation).

## <a name="examples"></a>Przykłady

Programy obsługi niestandardowej można zaimplementować w dowolnym języku, który obsługuje zdarzenia HTTP. Podczas gdy usługa Azure Functions [w pełni obsługuje javascript i node.js,](./functions-reference-node.md)poniższe przykłady pokazują, jak zaimplementować niestandardowy program obsługi przy użyciu języka JavaScript w node.js na potrzeby instrukcji.

> [!TIP]
> Będąc przewodnikiem do nauki, jak zaimplementować niestandardowy program obsługi w innych językach, przykłady oparte na node.js pokazane tutaj mogą być również przydatne, jeśli chcesz uruchomić aplikację Functions w nieobsługiwanej wersji node.js.

## <a name="http-only-function"></a>Funkcja tylko http

W poniższym przykładzie pokazano, jak skonfigurować funkcję wyzwalaną przez protokół HTTP bez dodatkowych powiązań lub wyjść. Scenariusz zaimplementowany w tym `http` przykładzie `GET` zawiera `POST` funkcję o nazwie, która akceptuje lub .

Poniższy fragment kodu reprezentuje sposób składania żądania do funkcji.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Wdrażanie

W folderze o nazwie *http*plik *function.json* konfiguruje funkcję wyzwalaną przez protokół HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Funkcja jest skonfigurowana do `GET` `POST` akceptowania zarówno i żądań, `res`a wartość wyniku jest dostarczana za pomocą argumentu o nazwie .

W katalogu głównym aplikacji plik *host.json* jest skonfigurowany do uruchamiania `server.js` pliku Node.js i wskazywał plik.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Plik *server.js* implementuje serwer www i funkcję HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

W tym przykładzie express jest używany do tworzenia serwera sieci web do `FUNCTIONS_HTTPWORKER_PORT`obsługi zdarzeń HTTP i jest ustawiony do nasłuchiwać żądań za pośrednictwem .

Funkcja jest definiowana na `/hello`ścieżce . `GET`żądania są obsługiwane przez zwrócenie prostego obiektu `POST` JSON, a żądania `req.body`mają dostęp do treści żądania za pośrednictwem .

Trasa dla funkcji zamówienia `/hello` w `/api/hello` tym miejscu jest i nie dlatego, że host functions jest proxy żądania do programu obsługi niestandardowej.

>[!NOTE]
>Nie `FUNCTIONS_HTTPWORKER_PORT` jest publiczny port przeznaczony do wywołania funkcji. Ten port jest używany przez hosta functions do wywołania niestandardowego programu obsługi.

## <a name="function-with-bindings"></a>Funkcja z powiązaniami

Scenariusz zaimplementowany w tym `order` przykładzie `POST` zawiera funkcję o nazwie, która akceptuje z ładunkiem reprezentującym zamówienie produktu. Gdy zamówienie jest księgowane w funkcji, tworzony jest komunikat Magazynu kolejek i zwracana jest odpowiedź HTTP.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Wdrażanie

W folderze o nazwie *kolejność*plik *function.json* konfiguruje funkcję wyzwalaną przez http.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

Ta funkcja jest zdefiniowana jako [funkcja wyzwalana http,](./functions-bindings-http-webhook-trigger.md) która zwraca [odpowiedź HTTP](./functions-bindings-http-webhook-output.md) i wyprowadza komunikat [magazynu kolejki.](./functions-bindings-storage-queue-output.md)

W katalogu głównym aplikacji plik *host.json* jest skonfigurowany do uruchamiania `server.js` pliku Node.js i wskazywał plik.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Plik *server.js* implementuje serwer www i funkcję HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

W tym przykładzie express jest używany do tworzenia serwera sieci web do `FUNCTIONS_HTTPWORKER_PORT`obsługi zdarzeń HTTP i jest ustawiony do nasłuchiwać żądań za pośrednictwem .

Funkcja jest definiowana na `/order` ścieżce .  Trasa dla funkcji zamówienia `/order` w `/api/order` tym miejscu jest i nie dlatego, że host functions jest proxy żądania do programu obsługi niestandardowej.

Ponieważ `POST` żądania są wysyłane do tej funkcji, dane są udostępniane za pośrednictwem kilku punktów:

- Treść wniosku jest dostępna za pośrednictwem`req.body`
- Dane opublikowane w funkcji są dostępne za pośrednictwem`req.body.Data.req.Body`

Odpowiedź funkcji jest sformatowana na parę klucz/wartość, gdzie `Outputs` element członkowski posiada wartość JSON, gdzie klucze są zgodne z wyjściami zdefiniowanymi w pliku *function.json.*

Ustawiając `message` równe wiadomości, która pochodzi z `res` żądania i oczekiwanej odpowiedzi HTTP, ta funkcja wysyła komunikat do magazynu kolejki i zwraca odpowiedź HTTP.

## <a name="debugging"></a>Debugowanie

Aby debugować funkcję niestandardowej aplikacji obsługi, należy dodać argumenty odpowiednie dla języka i środowiska wykonawczego, aby włączyć debugowanie.

Na przykład do debugowania aplikacji Node.js, `--inspect` flaga jest przekazywana jako argument w pliku *host.json.*

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> Konfiguracja debugowania jest częścią pliku *host.json,* co oznacza, że może być konieczne usunięcie niektórych argumentów przed wdrożeniem w systemie produkcyjnym.

Za pomocą tej konfiguracji można uruchomić proces hosta funkcji za pomocą następującego polecenia:

```bash
func host start
```

Po uruchomieniu procesu można dołączyć debuger i trafić punkty przerwania.

### <a name="visual-studio-code"></a>Visual Studio Code

Poniższy przykład przedstawia przykładową konfigurację, która pokazuje, jak skonfigurować plik *launch.json,* aby połączyć aplikację z debugerem kodu programu Visual Studio.

W tym przykładzie jest node.js, więc może być trzeba zmienić ten przykład dla innych języków lub środowiska wykonawczego.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Wdrażanie

Niestandardowy program obsługi można wdrożyć w prawie każdej opcji hostingu usługi Azure Functions (zobacz [ograniczenia).](#restrictions) Jeśli program obsługi wymaga niestandardowych zależności (takich jak środowisko wykonawcze języka), może być konieczne użycie [kontenera niestandardowego.](./functions-create-function-linux-custom-image.md)

## <a name="restrictions"></a>Ograniczenia

- Programy obsługi niestandardowej nie są obsługiwane w planach zużycia systemu Linux.
- Serwer sieci web musi uruchomić się w ciągu 60 sekund.

## <a name="samples"></a>Samples

Zapoznaj się z [niestandardowymi przykładami obsługi repozytorium GitHub,](https://github.com/Azure-Samples/functions-custom-handlers) aby uzyskać przykłady sposobu implementowania funkcji w różnych językach.
