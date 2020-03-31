---
title: Odwołanie do dewelopera języka JavaScript dla funkcji platformy Azure
description: Dowiedz się, jak tworzyć funkcje za pomocą języka JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: 345df8e1ea88caa6f8dbe941245c1f989c3e81c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276831"
---
# <a name="azure-functions-javascript-developer-guide"></a>Przewodnik dla deweloperów języka JavaScript w usłudze Azure Functions

Ten przewodnik zawiera informacje o zawiłościach pisania usługi Azure Functions w języku JavaScript.

Funkcja JavaScript jest eksportowana, `function` która jest wykonywana po wyzwoleniu[(wyzwalacze są konfigurowane w pliku function.json](functions-triggers-bindings.md)). Pierwszy argument przekazany do każdej `context` funkcji jest obiektem, który jest używany do odbierania i wysyłania danych wiązania, rejestrowania i komunikowania się ze środowiska wykonawczego.

W tym artykule założono, że zostały już odczytane [odwołanie dewelopera usługi Azure Functions.](functions-reference.md) Ukończ program Szybki start funkcji, aby utworzyć pierwszą funkcję, używając [programu Visual Studio Code](functions-create-first-function-vs-code.md) lub [portalu.](functions-create-first-azure-function.md)

W tym artykule można również pojmujeć [tworzenie aplikacji TypeScript](#typescript).

## <a name="folder-structure"></a>Struktura folderów

Wymagana struktura folderów dla projektu JavaScript wygląda następująco. Tę wartość domyślną można zmienić. Aby uzyskać więcej informacji, zobacz [scriptFile](#using-scriptfile) sekcji poniżej.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

W katalogu głównym projektu znajduje się udostępniony plik [host.json,](functions-host-json.md) który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma folder z własnym plikiem kodu (.js) i plikiem konfiguracji powiązania (function.json). Nazwa katalogu `function.json`nadrzędnego 's jest zawsze nazwą funkcji.

Rozszerzenia powiązania wymagane w [wersji 2.x](functions-versions.md) środowiska wykonawczego Functions `extensions.csproj` są zdefiniowane w pliku, `bin` z rzeczywistymi plikami biblioteki w folderze. Podczas tworzenia lokalnie należy [zarejestrować rozszerzenia powiązania](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w witrynie Azure portal, ta rejestracja jest wykonywana dla Ciebie.

## <a name="exporting-a-function"></a>Eksportowanie funkcji

Funkcje JavaScript muszą [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) być [`exports`](https://nodejs.org/api/modules.html#modules_exports)eksportowane za pośrednictwem (lub ). Wyeksportowana funkcja powinna być funkcją JavaScript, która jest wykonywana po wyzwoleniu.

Domyślnie środowisko uruchomieniowe Functions wyszukuje funkcję w `index.js`trybie ,, gdzie `index.js` udostępnia ten sam katalog nadrzędny, co odpowiadający mu `function.json`program . W przypadku domyślnym eksportowana funkcja powinna być jedynym eksportem `run` z `index`jej pliku lub eksportu o nazwie lub . Aby skonfigurować lokalizację pliku i nazwę eksportu funkcji, przeczytaj o [konfigurowaniu punktu wejścia funkcji](functions-reference-node.md#configure-function-entry-point) poniżej.

Wyeksportowana funkcja jest przekazywana szereg argumentów podczas wykonywania. Pierwszy argument, który ma `context` jest zawsze obiekt. Jeśli funkcja jest synchroniczowa (nie zwraca obietnicy), `context` należy przekazać `context.done` obiekt, ponieważ wywołanie jest wymagane do prawidłowego użycia.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Eksportowanie funkcji asynchronizowania
Podczas korzystania [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) z deklaracji lub zwykły [JavaScript Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) w wersji 2.x środowiska wykonawczego [`context.done`](#contextdone-method) funkcji, nie trzeba jawnie wywołać wywołania zwrotnego, aby zasygnalizować, że funkcja została ukończona. Funkcja zostanie ukończona po zakończeniu eksportowana funkcja asynchroniza/Obietnica. W przypadku funkcji przeznaczonych dla środowiska uruchomieniowego [`context.done`](#contextdone-method) w wersji 1.x należy nadal wywoływać, gdy kod jest wykonywany.

Poniższy przykład jest prostą funkcją, która rejestruje, że został wyzwolony i natychmiast kończy wykonywanie.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Podczas eksportowania funkcji asynchronii, można również skonfigurować `return` powiązanie danych wyjściowych do podjęcia wartości. Jest to zalecane, jeśli masz tylko jedno powiązanie danych wyjściowych.

Aby przypisać dane `return`wyjściowe `name` za `$return` `function.json`pomocą programu , zmień właściwość na w .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

W takim przypadku funkcja powinna wyglądać następująco:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Powiązania 
W języku JavaScript [powiązania](functions-triggers-bindings.md) są konfigurowane i definiowane w funkcji function.json. Funkcje współdziałają z powiązaniami na wiele sposobów.

### <a name="inputs"></a>Dane wejściowe
Dane wejściowe są podzielone na dwie kategorie w usłudze Azure Functions: jeden jest dane wejściowe wyzwalacza, a drugi jest dodatkowe dane wejściowe. Wyzwalacz i inne powiązania wejściowe `direction === "in"`(powiązania) mogą być odczytywane przez funkcję na trzy sposoby:
 - **_[Zalecane]_ Jak parametry przekazywane do funkcji.** Są one przekazywane do funkcji w tej samej kolejności, w jakiej są zdefiniowane w *pliku function.json*. Właściwość zdefiniowana `name` w *function.json* nie musi odpowiadać nazwie parametru, chociaż powinna.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Jako członkowie [`context.bindings`](#contextbindings-property) obiektu.** Każdy element członkowski `name` jest nazwany przez właściwość zdefiniowaną w *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Jako dane wejściowe [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) przy użyciu obiektu JavaScript.** Jest to zasadniczo taka sama jak przekazywanie danych wejściowych jako parametrów, ale pozwala na dynamiczne obsługę danych wejściowych.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Dane wyjściowe
Dane wyjściowe `direction === "out"`(powiązania) mogą być zapisywane przez funkcję na wiele sposobów. We wszystkich przypadkach `name` właściwość powiązania zgodnie z definicją w *function.json* odpowiada nazwie elementu członkowskiego obiektu zapisanego w funkcji. 

Powiązania danych można przypisać do danych wyjściowych w jeden z następujących sposobów (nie łącz tych metod):

- **_[Zalecane dla wielu wyjść]_ Zwracanie obiektu.** Jeśli używasz funkcji zwracanej asynchronii/obietnicy, można zwrócić obiekt z przypisanymi danymi wyjściowymi. W poniższym przykładzie powiązania danych wyjściowych są nazywane "httpResponse" i "queueOutput" w *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Jeśli używasz funkcji synchronicznego, można zwrócić [`context.done`](#contextdone-method) ten obiekt za pomocą (patrz przykład).
- **_[Zalecane dla pojedynczego wyjścia]_ Zwracanie wartości bezpośrednio i przy użyciu nazwy wiązania $return.** Działa to tylko w przypadku funkcji zwracanych asynchronii/obietnicy. Zobacz przykład [podczas eksportowania funkcji asynchronii](#exporting-an-async-function). 
- **Przypisywanie wartości `context.bindings` do** Wartości można przypisywać bezpośrednio do context.bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Typ danych powiązań

Aby zdefiniować typ danych dla powiązania `dataType` wejściowego, należy użyć właściwości w definicji powiązania. Na przykład, aby odczytać zawartość żądania HTTP w formacie `binary`binarnym, użyj typu:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Dostępne `dataType` opcje `binary`to: `stream` `string`, , i .

## <a name="context-object"></a>obiekt kontekstowy
Środowisko wykonawcze używa `context` obiektu do przekazywania danych do i z funkcji i do komunikowania się ze środowiska wykonawczego. Obiekt kontekstu może służyć do odczytu i ustawiania danych z `context.done` powiązań, zapisywania dzienników i przy użyciu wywołania zwrotnego, gdy wyeksportowana funkcja jest synchroniczowa.

Obiekt `context` jest zawsze pierwszym parametrem funkcji. Należy go uwzględnić, ponieważ ma `context.done` ważne `context.log`metody, takie jak i . Obiekt można nazwać dowolną (na przykład `ctx` `c`lub ).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings, właściwość

```js
context.bindings
```

Zwraca nazwany obiekt, który jest używany do odczytu lub przypisywania danych wiązania. Dane dotyczące wprowadzania i wyzwalania można `context.bindings`uzyskać, odczytając właściwości na . Dane powiązania danych wyjściowych można przypisać, dodając dane do`context.bindings`

Na przykład następujące definicje powiązania w pliku function.json umożliwiają dostęp `context.bindings.myInput` do zawartości kolejki i `context.bindings.myOutput`przypisywanie wyjść do kolejki przy użyciu programu .

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Można zdefiniować dane powiązania danych `context.done` wyjściowych przy `context.binding` użyciu metody zamiast obiektu (patrz poniżej).

### <a name="contextbindingdata-property"></a>context.bindingDana właściwość

```js
context.bindingData
```

Zwraca nazwany obiekt zawierający metadane wyzwalacza`invocationId`i `sys.methodName` `sys.utcNow`dane `sys.randGuid`wywołania funkcji ( , , , ). Na przykład metadanych wyzwalacza zobacz [przykład tego centrum zdarzeń](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>metoda context.done

```js
context.done([err],[propertyBag])
```

Umożliwia środowisko wykonawcze wiedzieć, że kod został ukończony. Gdy funkcja używa [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaracji, nie trzeba używać `context.done()`. Wywołanie zwrotne `context.done` jest niejawnie wywoływane. Funkcje asynchronizowe są dostępne w węźle 8 lub nowszej wersji, która wymaga wersji 2.x środowiska wykonawczego Functions.

Jeśli funkcja nie jest funkcją asynchronizacza, **należy wywołać,** `context.done` aby poinformować środowisko wykonawcze, że funkcja jest zakończona. Termin wykonania, jeśli brakuje.

Metoda `context.done` umożliwia przekazywanie z powrotem zarówno błąd zdefiniowany przez użytkownika do środowiska wykonawczego i JSON obiekt zawierający dane powiązania danych wyjściowych. Właściwości przekazywane `context.done` do zastępowanie niczego `context.bindings` ustawionego na obiekcie.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>metoda context.log  

```js
context.log(message)
```

Umożliwia zapisywanie w dziennikach funkcji przesyłania strumieniowego na domyślnym poziomie śledzenia. Na `context.log`dostępne są dodatkowe metody rejestrowania, które umożliwiają zapisywanie dzienników funkcji na innych poziomach śledzenia:


| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **błąd(_komunikat_)**   | Zapisuje do rejestrowania poziomu błędu lub niższe.   |
| **warn(_wiadomość_)**    | Zapisuje do rejestrowania poziomu ostrzeżenia lub niższe. |
| **info(_wiadomość_)**    | Zapisuje do rejestrowania na poziomie informacji lub niższe.    |
| **pełne(_wiadomość_)** | Zapisuje do pełnej rejestrowania poziomu.           |

Poniższy przykład zapisuje dziennik na poziomie śledzenia ostrzeżenia:

```javascript
context.log.warn("Something has happened."); 
```

Próg poziomu śledzenia można [skonfigurować dla rejestrowania](#configure-the-trace-level-for-console-logging) w pliku host.json. Aby uzyskać więcej informacji na temat pisania dzienników, zobacz [zapisywanie danych wyjściowych śledzenia](#writing-trace-output-to-the-console) poniżej.

Przeczytaj [monitorowanie usługi Azure Functions,](functions-monitoring.md) aby dowiedzieć się więcej na temat przeglądania i wykonywania zapytań dzienników funkcji.

## <a name="writing-trace-output-to-the-console"></a>Zapisywanie danych wyjściowych śledzenia na konsoli 

W funkcji, należy `context.log` użyć metod do zapisu danych wyjściowych śledzenia do konsoli. W functions v2.x, dane `console.log` wyjściowe śledzenia przy użyciu są przechwytywane na poziomie aplikacji funkcji. Oznacza to, że `console.log` dane wyjściowe z nie są powiązane z wywołaniem określonej funkcji i nie są wyświetlane w dziennikach określonej funkcji. Jednak one propagują do usługi Application Insights. W functions v1.x nie `console.log` można zapisywać w konsoli.

Podczas wywoływania `context.log()`wiadomość jest zapisywana na konsoli na domyślnym poziomie śledzenia, który jest poziomem śledzenia _informacji._ Następujący kod zapisuje do konsoli na poziomie śledzenia informacji:

```javascript
context.log({hello: 'world'});  
```

Ten kod jest odpowiednikiem powyższego kodu:

```javascript
context.log.info({hello: 'world'});  
```

Ten kod zapisuje do konsoli na poziomie błędu:

```javascript
context.log.error("An error has occurred.");  
```

Ponieważ _błąd_ jest najwyższym poziomem śledzenia, ten ślad jest zapisywany do danych wyjściowych na wszystkich poziomach śledzenia, tak długo, jak rejestrowanie jest włączone.

Wszystkie `context.log` metody obsługują ten sam format parametru, który jest obsługiwany przez metodę Node.js [util.format](https://nodejs.org/api/util.html#util_util_format_format). Należy wziąć pod uwagę następujący kod, który zapisuje dzienniki funkcji przy użyciu domyślnego poziomu śledzenia:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Można również napisać ten sam kod w następującym formacie:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurowanie poziomu śledzenia dla rejestrowania konsoli

Funkcje 1.x umożliwia zdefiniowanie poziomu śledzenia progu do zapisu w konsoli, co ułatwia kontrolowanie sposobu zapisywania śladów w konsoli za pomocą funkcji. Aby ustawić próg dla wszystkich śladów zapisanych `tracing.consoleLevel` w konsoli, należy użyć właściwości w pliku host.json. To ustawienie dotyczy wszystkich funkcji w aplikacji funkcji. W poniższym przykładzie ustawia próg śledzenia, aby włączyć pełne rejestrowanie:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Wartości **konsoliPoziom** odpowiada nazwom `context.log` metod. Aby wyłączyć rejestrowanie wszystkich śladów na konsoli, ustaw **konsolęPoziomuj,** aby _wyłączyć_. Aby uzyskać więcej informacji, zobacz [host.json reference](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Wyzwalacze i powiązania HTTP

Wyzwalacze HTTP i webhook oraz powiązania wyjściowe HTTP używają obiektów żądań i odpowiedzi do reprezentowania wiadomości HTTP.  

### <a name="request-object"></a>Obiekt żądania

Obiekt `context.req` (żądanie) ma następujące właściwości:

| Właściwość      | Opis                                                    |
| ------------- | -------------------------------------------------------------- |
| _Ciała_        | Obiekt, który zawiera treść żądania.               |
| _Nagłówki_     | Obiekt, który zawiera nagłówki żądań.                   |
| _Metoda_      | Metoda HTTP żądania.                                |
| _oryginałUrl_ | Adres URL żądania.                                        |
| _params_      | Obiekt, który zawiera parametry routingu żądania. |
| _query_       | Obiekt, który zawiera parametry kwerendy.                  |
| _rawBody (ciało raw)_     | Treść wiadomości jako ciąg.                           |


### <a name="response-object"></a>Obiekt odpowiedzi

Obiekt `context.res` (odpowiedź) ma następujące właściwości:

| Właściwość  | Opis                                               |
| --------- | --------------------------------------------------------- |
| _Ciała_    | Obiekt, który zawiera treść odpowiedzi.         |
| _Nagłówki_ | Obiekt, który zawiera nagłówki odpowiedzi.             |
| _isRaw (IsRaw)_   | Wskazuje, że formatowanie jest pomijane dla odpowiedzi.    |
| _Stan_  | Kod stanu HTTP odpowiedzi.                     |
| _Pliki cookie_ | Tablica obiektów plików cookie HTTP, które są ustawione w odpowiedzi. Obiekt pliku cookie `name`HTTP `value`ma , i inne `maxAge` właściwości `sameSite`plików cookie, takie jak lub . |

### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi 

Podczas pracy z wyzwalaczami HTTP można uzyskać dostęp do obiektów żądania i odpowiedzi HTTP na wiele sposobów:

+ **Od `req` `res` i właściwości `context` obiektu.** W ten sposób można użyć wzorca konwencjonalnego, aby uzyskać dostęp do danych `context.bindings.name` HTTP z obiektu kontekstu, zamiast używać pełnego wzorca. W poniższym przykładzie `req` pokazano, jak uzyskać dostęp do obiektów i `res` obiektów na: `context`

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Z nazwanych powiązań wejściowych i wyjściowych.** W ten sposób wyzwalacz HTTP i powiązania działają tak samo jak inne powiązania. Poniższy przykład ustawia obiekt odpowiedzi `response` przy użyciu nazwanego powiązania: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Tylko odpowiedź]_ Dzwoniąc `context.res.send(body?: any)`.** Odpowiedź HTTP jest tworzona z wejściem `body` jako treści odpowiedzi. `context.done()`jest niejawnie wywoływana.

+ **_[Tylko odpowiedź]_ Dzwoniąc `context.done()`.** Specjalny typ powiązania HTTP zwraca odpowiedź, która `context.done()` jest przekazywana do metody. Następujące powiązanie wyjściowe `$return` HTTP definiuje parametr wyjściowy:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Skalowanie i współbieżność

Domyślnie usługa Azure Functions automatycznie monitoruje obciążenie aplikacji i w razie potrzeby tworzy dodatkowe wystąpienia hosta dla pliku Node.js. Funkcje używają wbudowanych (nie konfigurowalne przez użytkownika) progów dla różnych typów wyzwalaczy, aby zdecydować, kiedy dodać wystąpienia, takie jak wiek wiadomości i rozmiar kolejki dla QueueTrigger. Aby uzyskać więcej informacji, zobacz [Jak działają plany zużycie i premia](functions-scale.md#how-the-consumption-and-premium-plans-work).

To zachowanie skalowania jest wystarczające dla wielu aplikacji Node.js. W przypadku aplikacji związanych z procesorem CPU można dodatkowo zwiększyć wydajność przy użyciu wielu procesów procesu roboczego języka.

Domyślnie każde wystąpienie hosta funkcji ma proces roboczy jednego języka. Można zwiększyć liczbę procesów roboczych na hoście (do 10) przy użyciu ustawienia [aplikacji FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) Usługa Azure Functions następnie próbuje równomiernie rozpowszechniać wywołania funkcji równoczesnych między tymi pracownikami. 

FUNCTIONS_WORKER_PROCESS_COUNT ma zastosowanie do każdego hosta, który funkcja tworzy podczas skalowania w poziomie aplikacji w celu zaspokojenia popytu. 

## <a name="node-version"></a>Wersja węzła

W poniższej tabeli przedstawiono bieżące obsługiwane wersje node.js dla każdej głównej wersji środowiska wykonawczego functions według systemu operacyjnego:

| Wersja funkcji | Wersja węzła (Windows) | Wersja węzła (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (zablokowany przez środowisko wykonawcze) | Nie dotyczy |
| 2.x  | ~8<br/>~10 (zalecane)<br/>~12<sup>*</sup> | ~8 (zalecane)<br/>~10  |
| 3.x | ~10<br/>~12 (zalecane)  | ~10<br/>~12 (zalecane) |

<sup>*</sup>Węzeł ~12 jest obecnie dozwolony w wersji 2.x środowiska wykonawczego Funkcji. Jednak aby uzyskać najlepszą wydajność, zaleca się użycie funkcji środowiska wykonawczego w wersji 3.x z węzłem ~12. 

Możesz zobaczyć bieżącą wersję, której używa środowisko wykonawcze, sprawdzając `process.version` powyższe ustawienie aplikacji lub drukując z dowolnej funkcji. Kieruj reklamy na wersję na platformie Azure, ustawiając [ustawienie aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) `~10`WEBSITE_NODE_DEFAULT_VERSION na obsługiwaną wersję LTS, taką jak .

## <a name="dependency-management"></a>Zarządzanie zależnościami
Aby korzystać z bibliotek społecznościowych w kodzie JavaScript, jak pokazano w poniższym przykładzie, należy upewnić się, że wszystkie zależności są zainstalowane w aplikacji funkcji na platformie Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Należy zdefiniować `package.json` plik w katalogu głównym aplikacji funkcji. Definiowanie pliku umożliwia wszystkie funkcje w aplikacji współużytkować te same pakiety w pamięci podręcznej, co daje najlepszą wydajność. Jeśli wystąpi konflikt wersji, można go rozwiązać, dodając `package.json` plik w folderze określonej funkcji.  

Podczas wdrażania aplikacji funkcji z `package.json` kontroli źródła, każdy plik obecny `npm install` w repozytorium, wyzwoli w jego folderze podczas wdrażania. Ale podczas wdrażania za pośrednictwem portalu lub interfejsu wiersza polecenia, trzeba będzie ręcznie zainstalować pakiety.

Istnieją dwa sposoby instalowania pakietów w aplikacji funkcji: 

### <a name="deploying-with-dependencies"></a>Wdrażanie z zależnościami
1. Zainstaluj wszystkie wymagane pakiety lokalnie, uruchamiając `npm install`program .

2. Wdrażanie kodu i upewnij `node_modules` się, że folder jest uwzględniony we wdrożeniu. 


### <a name="using-kudu"></a>Korzystanie z Kudu
1. Przejdź do pozycji `https://<function_app_name>.scm.azurewebsites.net` (Plik > Nowy > Inny).

2. Kliknij przycisk **Konsola debugowania** > **CMD**.

3. Przejdź `D:\home\site\wwwroot`do , a następnie przeciągnij plik package.json do folderu **wwwroot** w górnej połowie strony.  
    Pliki można przesyłać do aplikacji funkcji również w inny sposób. Aby uzyskać więcej informacji, zobacz [Jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). 

4. Po przesłaniu pliku package.json uruchom `npm install` polecenie w **konsoli zdalnego wykonywania Kudu**.  
    Ta akcja pobiera pakiety wskazane w pliku package.json i ponownie uruchamia aplikację funkcji.

## <a name="environment-variables"></a>Zmienne środowiskowe

W [funkcji, ustawienia aplikacji](functions-app-settings.md), takich jak parametry połączenia usługi, są widoczne jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp `process.env`do tych ustawień za pomocą , `context.log()` jak pokazano `AzureWebJobsStorage` `WEBSITE_SITE_NAME` tutaj w drugim i trzecim wywołaniu, gdzie rejestrujemy zmienne środowiskowe i środowiskowe:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Podczas uruchamiania lokalnie ustawienia aplikacji są odczytywane z pliku projektu [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="configure-function-entry-point"></a>Konfigurowanie punktu wejścia funkcji

Właściwości `function.json` `scriptFile` i `entryPoint` może służyć do konfigurowania lokalizacji i nazwy eksportowanej funkcji. Te właściwości mogą być ważne podczas transpiled JavaScript.

### <a name="using-scriptfile"></a>Używanie elementu `scriptFile`

Domyślnie funkcja JavaScript jest `index.js`wykonywana z pliku, który współużytkuje ten sam katalog nadrzędny, co odpowiadający `function.json`mu plik .

`scriptFile`można użyć do uzyskania struktury folderów, która wygląda następująco:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

For powinien zawierać `scriptFile` właściwość wskazującą plik z wyeksportowanymi funkcją do uruchomienia. `myNodeFunction` `function.json`

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Używanie elementu `entryPoint`

W `scriptFile` (lub `index.js`), funkcja musi `module.exports` być eksportowane przy użyciu w celu znalezienia i uruchomienia. Domyślnie funkcja wykonywana po wyzwoleniu jest jedynym eksportem z tego `run`pliku, eksportem o nazwie lub eksportem o nazwie `index`.

Można to skonfigurować `entryPoint` `function.json`za pomocą w , jak w poniższym przykładzie:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

W functions v2.x, `this` który obsługuje parametr w funkcjach użytkownika, kod funkcji może być wówczas jak w poniższym przykładzie:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

W tym przykładzie należy pamiętać, że mimo że obiekt jest eksportowany, nie ma żadnych gwarancji zachowania stanu między egzekucjami.

## <a name="local-debugging"></a>Debugowanie lokalne

Po uruchomieniu `--inspect` z parametrem proces Node.js nasłuchuje klienta debugowania na określonym porcie. W usłudze Azure Functions 2.x można określić argumenty do przekazania do procesu Node.js, który uruchamia kod, dodając zmienną środowiskową lub ustawienie `languageWorkers:node:arguments = <args>`aplikacji . 

Aby debugować lokalnie, dodaj `"languageWorkers:node:arguments": "--inspect=5858"` w `Values` pliku [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) i dołącz debuger do portu 5858.

Podczas debugowania przy użyciu `--inspect` programu VS Code `port` parametr jest automatycznie dodawany przy użyciu wartości w pliku launch.json projektu.

W wersji 1.x `languageWorkers:node:arguments` ustawienie nie będzie działać. Port debugowania można wybrać [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) za pomocą parametru w narzędziach azure functions core.

## <a name="typescript"></a>TypeScript

Gdy jest kierowana na wersję 2.x środowiska wykonawczego funkcji, zarówno [usługi Azure Functions for Visual Studio Code,](functions-create-first-function-vs-code.md) jak i narzędzia podstawowe usług Azure Functions [Core](functions-run-local.md) umożliwiają tworzenie aplikacji z funkcjami przy użyciu szablonu obsługującego projekty aplikacji funkcji TypeScript. Szablon generuje `package.json` i `tsconfig.json` projektuje pliki, które ułatwiają transpile, uruchamianie i publikowanie funkcji JavaScript z kodu TypeScript za pomocą tych narzędzi.

Wygenerowany `.funcignore` plik służy do wskazania, które pliki są wykluczone, gdy projekt jest publikowany na platformie Azure.  

Pliki TypeScript (ts) są transpilowane do plików JavaScript `dist` (.js) w katalogu wyjściowym. Szablony TypeScript używają `function.json` [ `scriptFile` parametru,](#using-scriptfile) aby wskazać lokalizację odpowiedniego pliku `dist` .js w folderze. Lokalizacja wyjściowa jest ustawiana `outDir` przez szablon `tsconfig.json` przy użyciu parametru w pliku. Jeśli zmienisz to ustawienie lub nazwę folderu, środowisko wykonawcze nie będzie w stanie znaleźć kodu do uruchomienia.

> [!NOTE]
> Eksperymentalna obsługa języka TypeScript istnieje w wersji 1.x środowiska wykonawczego Functions. Wersja eksperymentalna transpiles pliki TypeScript do plików JavaScript, gdy funkcja jest wywoływana. W wersji 2.x ta eksperymentalna obsługa została zastąpiona przez metodę opartą na narzędziach, która wykonuje transpilację przed zainicjowaniem hosta i podczas procesu wdrażania.

Sposób lokalnego tworzenia i wdrażania z projektu TypeScript zależy od narzędzia programistycznego.

### <a name="visual-studio-code"></a>Visual Studio Code

Rozszerzenie [usługi Azure Functions for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) umożliwia tworzenie funkcji przy użyciu języka TypeScript. Podstawowe narzędzia jest wymaganiem rozszerzenia usługi Azure Functions.

Aby utworzyć aplikację funkcji TypeScript w `TypeScript` programie Visual Studio Code, wybierz jako język podczas tworzenia aplikacji funkcji.

Po naciśnięciu **klawisza F5,** aby uruchomić aplikację lokalnie, transpilacja odbywa się przed zainicjowaniem hosta (func.exe). 

Podczas wdrażania aplikacji funkcji na platformie Azure za pomocą przycisku **Wdrażanie do funkcji aplikacji...** rozszerzenie usługi Azure Functions najpierw generuje gotową do produkcji kompilację plików JavaScript z plików źródłowych TypeScript.

### <a name="azure-functions-core-tools"></a>Podstawowe narzędzia funkcji platformy Azure

Istnieje kilka sposobów, w których projekt TypeScript różni się od projektu JavaScript podczas korzystania z narzędzi podstawowych.

#### <a name="create-project"></a>Tworzenie projektu

Aby utworzyć projekt aplikacji funkcji TypeScript przy użyciu narzędzi podstawowych, należy określić opcję języka TypeScript podczas tworzenia aplikacji funkcji. Można to zrobić w jeden z następujących sposobów:

- Uruchom `func init` polecenie, `node` wybierz jako stos językowy, a następnie wybierz opcję `typescript`.

- Uruchom polecenie `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Uruchom lokalnie

Aby uruchomić kod aplikacji funkcji lokalnie przy użyciu narzędzi Core `func host start`Tools, użyj następujących poleceń zamiast: 

```command
npm install
npm start
```

Polecenie `npm start` jest równoważne następującym poleceniu:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Przed użyciem [`func azure functionapp publish`] polecenia do wdrożenia na platformie Azure należy utworzyć gotową do produkcji kompilację plików JavaScript z plików źródłowych TypeScript. 

Następujące polecenia przygotowują i publikują projekt TypeScript przy użyciu narzędzi Core Tools: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

W tym poleceniu zastąp `<APP_NAME>` nazwą aplikacji funkcji.

## <a name="considerations-for-javascript-functions"></a>Zagadnienia dotyczące funkcji JavaScript

Podczas pracy z funkcjami JavaScript należy pamiętać o zagadnieniach w poniższych sekcjach.

### <a name="choose-single-vcpu-app-service-plans"></a>Wybieranie planów usługi aplikacji z jednym procesorem wirtualnym

Podczas tworzenia aplikacji funkcji, która korzysta z planu usługi App Service, zaleca się wybranie planu pojedynczego procesora wirtualnego, a nie planu z wieloma procesorami wirtualnymi. Obecnie funkcja uruchamia funkcje JavaScript bardziej efektywnie na maszynach wirtualnych z pojedynczym procesorem wirtualnym, a przy użyciu większych maszyn wirtualnych nie powoduje oczekiwanej poprawy wydajności. W razie potrzeby można ręcznie skalować w poziomie, dodając więcej wystąpień maszyn wirtualnych z pojedynczym procesorem wirtualnym lub włączyć skalowanie automatyczne. Aby uzyskać więcej informacji, zobacz [Skalowanie liczby wystąpień ręcznie lub automatycznie](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Zimny początek

Podczas tworzenia usługi Azure Functions w modelu hostingu bezserwerowego, zimne uruchomienia są rzeczywistością. *Zimny start* odnosi się do faktu, że gdy aplikacja funkcji uruchamia się po raz pierwszy po okresie braku aktywności, trwa dłużej, aby uruchomić. W przypadku funkcji JavaScript z dużymi drzewami zależności w szczególności zimny start może być znaczący. Aby przyspieszyć proces zimnego rozruchu, [uruchom swoje funkcje jako plik pakietu,](run-functions-from-deployment-package.md) jeśli to możliwe. Wiele metod wdrażania należy domyślnie uruchomić z modelu pakietu, ale jeśli występują duże zimne uruchomienia i nie są uruchomione w ten sposób, ta zmiana może zaoferować znaczną poprawę.

### <a name="connection-limits"></a>Limity połączeń

Korzystając z klienta specyficznego dla usługi w aplikacji usługi Azure Functions, nie należy tworzyć nowego klienta z każdym wywołaniem funkcji. Zamiast tego należy utworzyć pojedynczy, statyczny klient w zakresie globalnym. Aby uzyskać więcej informacji, zobacz [zarządzanie połączeniami w usłudze Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Używać `async` i`await`

Podczas pisania funkcji platformy Azure w języku `async` JavaScript należy napisać kod przy użyciu słów kluczowych i. `await` Pisanie kodu `async` `await` przy użyciu i zamiast `.then` `.catch` wywołań zwrotnych lub i obietnice pomaga uniknąć dwóch typowych problemów:
 - Zgłaszanie nieprzechośnych wyjątków, które [ulegają awarii procesu Node.js,](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)potencjalnie wpływając na wykonywanie innych funkcji.
 - Nieoczekiwane zachowanie, takie jak brakujące dzienniki z context.log, spowodowane przez wywołania asynchroniczne, które nie są poprawnie oczekiwane.

W poniższym przykładzie metoda `fs.readFile` asynchroniza jest wywoływana z funkcją wywołania zwrotnego typu error-first jako drugim parametrem. Ten kod powoduje oba problemy wymienione powyżej. Wyjątek, który nie jest jawnie złowionych w prawidłowym zakresie rozbił cały proces (problem #1). Wywołanie `context.done()` poza zakresem funkcji wywołania zwrotnego oznacza, że wywołanie funkcji może zakończyć się przed odczytaniem pliku (problem #2). W tym przykładzie wywoływanie `context.done()` zbyt wcześnie powoduje `Data from file:`brakujące wpisy dziennika, zaczynając od .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Używanie `async` słów `await` kluczowych i pomaga uniknąć obu tych błędów. Należy użyć funkcji [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) narzędzia Node.js, aby przekształcić funkcje typu error-first callback style w oczekujące funkcje.

W poniższym przykładzie wszelkie nieobsłużone wyjątki zgłaszane podczas wykonywania funkcji nie tylko indywidualne wywołanie, które zgłosiło wyjątek. Słowo `await` kluczowe oznacza, `readFileAsync` że `readFile` kroki następujące tylko wykonać po zakończeniu. Z `async` `await`i , również nie trzeba `context.done()` wywoływać wywołania zwrotnego.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

+ [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
+ [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
+ [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md)

["func azure functionapp publikować"]: functions-run-local.md#project-file-deployment
