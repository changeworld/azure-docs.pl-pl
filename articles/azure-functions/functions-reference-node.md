---
title: Dokumentacja dewelopera języka JavaScript dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie funkcji przy użyciu języka JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 2eea1a1d30558765a2f8320b0b23efdbe3368807
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140957"
---
# <a name="azure-functions-javascript-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions JavaScript

Ten przewodnik zawiera informacje dotyczące niewymagającego pisania usługi Azure Functions za pomocą języka JavaScript.

Funkcja języka JavaScript jest wyeksportowany `function` , który jest wykonywany po wyzwoleniu ([wyzwalacze są konfigurowane w function.json](functions-triggers-bindings.md)). Pierwszy argument przekazany do funkcji, co jest `context` obiektu, który jest używany do odbierania i wysyłania danych powiązania, rejestrowanie i komunikowania się ze środowiskiem uruchomieniowym.

W tym artykule założono, że już znasz [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md). Wykonaj szybkiego startu usługi Functions do utworzenia pierwszej funkcji przy użyciu [programu Visual Studio Code](functions-create-first-function-vs-code.md) lub [w portalu](functions-create-first-azure-function.md).

W tym artykule obsługuje również [programowania aplikacji TypeScript](#typescript).

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów wymagane dla projektu w języku JavaScript wygląda podobnie do poniższego. Można zmienić to ustawienie domyślne. Aby uzyskać więcej informacji, zobacz [plik_skryptu](#using-scriptfile) poniższej sekcji.

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

W folderze głównym projektu jest wspólny [host.json](functions-host-json.md) pliku, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma folder z pliku z kodem (js) i plik konfiguracji powiązania (function.json). Nazwa `function.json`w katalogu nadrzędnym jest zawsze nazwę funkcji.

Rozszerzenia powiązania wymagane w [wersji 2.x](functions-versions.md) funkcji środowiska uruchomieniowego są zdefiniowane w `extensions.csproj` pliku z plikami rzeczywistej biblioteki w `bin` folderu. Podczas tworzenia lokalnie, należy najpierw [zarejestrować rozszerzeń powiązania](./functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Podczas tworzenia funkcji w witrynie Azure portal, rejestracja odbywa się za Ciebie.

## <a name="exporting-a-function"></a>Eksportowanie funkcji

Funkcje języka JavaScript musi być eksportowany za pośrednictwem [ `module.exports` ](https://nodejs.org/api/modules.html#modules_module_exports) (lub [ `exports` ](https://nodejs.org/api/modules.html#modules_exports)). Funkcji wyeksportowanej powinna być funkcja języka JavaScript, który jest wykonywany po wyzwoleniu.

Domyślnie środowisko uruchomieniowe usługi Functions szuka funkcji w `index.js`, gdzie `index.js` udziałów tego samego katalogu nadrzędnego odpowiadającymi mu dostawcami `function.json`. W przypadku domyślnej eksportowanych funkcji powinna być tylko eksportu z jego pliku lub Eksportuj nazwane `run` lub `index`. Aby skonfigurować lokalizację pliku i wyeksportować nazwę funkcji, przeczytaj temat [Konfigurowanie swoją funkcję punktu wejścia](functions-reference-node.md#configure-function-entry-point) poniżej.

Wykonanie eksportowanych funkcji są przekazywane liczbę argumentów. Pierwszy argument, potrzebny jest zawsze `context` obiektu. Jeśli funkcja jest synchroniczne (nie zwraca obietnicą), należy przekazać `context` obiektu, co wywołanie metody `context.done` jest wymagany do poprawnego użycia.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Eksportowanie funkcji asynchronicznej
Korzystając z [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaracji lub JavaScript zwykły [obietnic](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) w wersji 2.x środowisko uruchomieniowe usługi Functions, nie trzeba jawnie wywołać [ `context.done` ](#contextdone-method) wywołania zwrotnego do sygnalizowania, że funkcja została zakończona. Funkcja kończy się po zakończeniu async eksportowanych funkcji/Promise. W przypadku funkcji przeznaczonych dla środowiska uruchomieniowego w wersji 1.x nadal należy wywołać [ `context.done` ](#contextdone-method) po zakończeniu kodu wykonywanego.

Poniższy przykład jest prostą funkcją, która rejestruje zostało wyzwolone i natychmiast kończy wykonywanie.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Podczas eksportowania funkcji asynchronicznej, można również skonfigurować powiązania danych wyjściowych, aby móc `return` wartość. Jest to zalecane, jeśli masz tylko jedno powiązanie danych wyjściowych.

Aby przypisać dane wyjściowe przy użyciu `return`, zmień `name` właściwości `$return` w `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

W takim przypadku funkcja powinna wyglądać podobnie jak w poniższym przykładzie:

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
W języku JavaScript [powiązania](functions-triggers-bindings.md) są konfigurowane i zdefiniowane w funkcji function.json. Funkcje wchodzić w interakcje z powiązaniami na wiele sposobów.

### <a name="inputs"></a>Dane wejściowe
Dane wejściowe są podzielone na dwie kategorie w usłudze Azure Functions: jeden z nich to dane wejściowe wyzwalacza, a drugi to dodatkowe dane wejściowe. Wyzwalacz i inne dane wejściowe powiązania (vazby prvku `direction === "in"`) mogą być odczytywane przez funkcję na trzy sposoby:
 - **_[Zalecane]_  Jako parametry przekazywane do funkcji.** Są one przekazywane do funkcji w tej samej kolejności, które są zdefiniowane w *function.json*. `name` Właściwości zdefiniowanych w *function.json* nie musi być zgodna z nazwą parametru, mimo że powinien on.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Jako elementy członkowskie [ `context.bindings` ](#contextbindings-property) obiektu.** Każdy element członkowski jest nazwany przez `name` właściwości zdefiniowanych w *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Jako dane wejściowe, przy użyciu języka JavaScript [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) obiektu.** To jest zasadniczo taki sam jak przekazywanie danych wejściowych jako parametrów, ale pozwala na dynamiczne obsługi danych wejściowych.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Dane wyjściowe
Dane wyjściowe (vazby prvku `direction === "out"`) mogą być zapisywane przez funkcję na wiele sposobów. We wszystkich przypadkach `name` właściwości powiązania, zgodnie z definicją w *function.json* odpowiada nazwie elementu członkowskiego obiektu zapisywane w funkcji. 

Dane można przypisać do powiązania danych wyjściowych, w jednym z następujących metod (nie połączyć te metody):

- **_[Zalecane w przypadku wiele wyjść]_  Zwrócenie obiektu.** Jeśli używasz async/Promise zwraca funkcja może zwrócić obiekt z danych wyjściowych przypisane. W poniższym przykładzie powiązania danych wyjściowych są nazywane "httpResponse" i "queueOutput" w *function.json*.

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

  Jeśli używasz funkcji synchronicznej, można zwrócić tego obiektu przy użyciu [ `context.done` ](#contextdone-method) (Zobacz przykład).
- **_[Zalecane w przypadku pojedynczego wyjścia]_  Zwracanie wartości bezpośrednio i przy użyciu $return powiązania nazwy.** To działa tylko w przypadku zwracania funkcje asynchroniczne/Promise. Zobacz przykład w [eksportowanie funkcji asynchronicznej](#exporting-an-async-function). 
- **Przypisywanie wartości do `context.bindings`**  wartości można przypisać bezpośrednio do context.bindings.

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

### <a name="bindings-data-type"></a>Typ danych powiązania

Aby zdefiniować typ danych dla powiązania danych wejściowych, użyj `dataType` właściwości w definicji powiązania. Na przykład, aby odczytać zawartość żądania HTTP w formacie binarnym, użyj typu `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Opcje dla `dataType` są: `binary`, `stream`, i `string`.

## <a name="context-object"></a>Obiekt kontekstu
Środowisko wykonawcze używa `context` obiekt do przekazywania danych do i z funkcji i umożliwienie komunikowania się ze środowiskiem uruchomieniowym. Obiekt kontekstu może być używany do odczytywania i ustawienie danych z powiązań, zapisywanie dzienników i przy użyciu `context.done` wywołania zwrotnego, jeśli wyeksportowanej funkcji ma być synchroniczne.

`context` Obiekt zawsze jest pierwszy parametr do funkcji. Należy uwzględniony, ponieważ ma ona ważna metody takie jak `context.done` i `context.log`. Niezależnie od rodzaju chcesz można nazwać obiektu (na przykład `ctx` lub `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>właściwość Context.Bindings

```js
context.bindings
```

Zwraca obiekt o nazwie, który jest używany do odczytu lub przypisać powiązanie danych. Dane wejściowe i wyzwalacza wiązania danych może zostać oceniony przez odczytywanie właściwości na `context.bindings`. Dane wyjściowe powiązanie danych może być przypisana przez dodawanie danych do `context.bindings`

Na przykład, następujące definicje powiązania w swojej function.json pozwalają uzyskać dostęp do zawartości kolejki z `context.bindings.myInput` i przypisz dane wyjściowe do kolejki za pomocą `context.bindings.myOutput`.

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

Można zdefiniować przy użyciu danych powiązania danych wyjściowych `context.done` zamiast metody `context.binding` obiektu (patrz poniżej).

### <a name="contextbindingdata-property"></a>Właściwość context.bindingData

```js
context.bindingData
```

Zwraca obiekt o nazwie, który zawiera dane wywołania funkcji i metadanych wyzwalacza (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Na przykład wyzwalacz metadanych zobacz [przykład usługi event hubs](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Metoda context.Done

```js
context.done([err],[propertyBag])
```

Umożliwia środowiska uruchomieniowego wiedzieć, że Twój kod została zakończona. Gdy funkcja [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaracji, nie trzeba używać `context.done()`. `context.done` Wywołania zwrotnego jest wywoływany niejawnie. Funkcje asynchroniczne są dostępne w środowisku Node 8 lub nowszym, który wymaga wersji 2.x środowisko uruchomieniowe usługi Functions.

Jeśli funkcja nie jest funkcją async **musi wywołać** `context.done` poinformować w czasie wykonywania, że funkcja została zakończona. Czasy wykonania, gdy jest Brak.

`context.done` Metoda umożliwia przesłać zarówno błędach zdefiniowane przez użytkownika do środowiska uruchomieniowego i obiektem JSON zawierającym dane wyjściowe powiązanie danych. Właściwości są przekazywane do `context.done` zastąpić niczego nastavit `context.bindings` obiektu.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Metoda context.log  

```js
context.log(message)
```

Pozwala na zapis w dziennikach funkcji przesyłania strumieniowego na domyślny poziom śledzenia. Na `context.log`, dodatkowe opcje rejestrowania metody są dostępne, które pozwalają na zapisywanie dzienników funkcji na innych poziomach śledzenia:


| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **Błąd (_komunikat_)**   | Zapisuje poziom błędów rejestrowania lub niższą.   |
| **Ostrzeżenie (_komunikat_)**    | Zapisuje poziom ostrzeżeń rejestrowania lub niższą. |
| **info(_message_)**    | Zapisuje informacje o poziomie rejestrowania lub niższą.    |
| **pełne (_komunikat_)** | Zapisuje pełne rejestrowanie na poziomie.           |

Poniższy przykład zapisuje dziennik na poziom śledzenia Ostrzeżenie:

```javascript
context.log.warn("Something has happened."); 
```

Możesz [skonfigurowania progu poziom śledzenia dla rejestrowania](#configure-the-trace-level-for-console-logging) w pliku host.json. Aby uzyskać więcej informacji na temat zapisywania dzienników, zobacz [zapisywania danych wyjściowych śledzenia](#writing-trace-output-to-the-console) poniżej.

Odczyt [monitorowania usługi Azure Functions](functions-monitoring.md) Aby dowiedzieć się więcej na temat przeglądania i zapytania o dzienniki funkcji.

## <a name="writing-trace-output-to-the-console"></a>Zapisywanie danych wyjściowych śledzenia w konsoli programu 

W przypadku funkcji używasz `context.log` metody można zapisywać dane wyjściowe śledzenia do konsoli. W v2.x funkcji śledzenia danych wyjściowych za pomocą `console.log` są przechwytywane na poziomie aplikacji funkcji. Oznacza to, że dane wyjściowe z `console.log` nie są związane z wywołania określonych funkcji i nie są wyświetlane w dziennikach określonych funkcji. Jednak są propagowane do usługi Application Insights. W funkcji v1.x, nie można użyć `console.log` do zapisu do konsoli.

Gdy wywołujesz `context.log()`, wiadomości są zapisywane do konsoli na domyślny poziom śledzenia, który jest _informacje_ poziom śledzenia. Poniższy kod zapisuje do konsoli, gdy poziom śledzenia informacje:

```javascript
context.log({hello: 'world'});  
```

Ten kod jest odpowiednikiem w powyższym kodzie:

```javascript
context.log.info({hello: 'world'});  
```

Ten kod zapisuje do konsoli na poziomie błędu:

```javascript
context.log.error("An error has occurred.");  
```

Ponieważ _błąd_ znajduje się ślad najwyższego poziomu, to śledzenia są zapisywane dane wyjściowe na wszystkich poziomach śledzenia tak długo, jak rejestrowanie jest włączone.

Wszystkie `context.log` metody obsługują ten sam format parametru, który jest obsługiwany przez Node.js [metoda util.format](https://nodejs.org/api/util.html#util_util_format_format). Należy wziąć pod uwagę następujący kod, który zapisuje dzienniki funkcji za pomocą domyślnego poziomu śledzenia:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Można również napisać ten sam kod w następującym formacie:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Skonfiguruj poziom śledzenia dla rejestrowania konsoli

Funkcje 1.x pozwala zdefiniować próg poziom śledzenia dla pisania w konsoli, która ułatwia kontrolować sposób, w jaki dane śledzenia są zapisywane w konsoli funkcji. Aby ustawić próg wszystkie ślady wyświetlony w konsoli, należy użyć `tracing.consoleLevel` właściwości w pliku host.json. To ustawienie ma zastosowanie do wszystkich funkcji w aplikacji funkcji. W poniższym przykładzie ustawiono wartość progową śledzenia, aby włączyć pełne rejestrowanie:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Wartości typu **consoleLevel** odnoszą się do nazw `context.log` metody. Aby wyłączyć wszystkie rejestrowanie śledzenia do konsoli, należy ustawić **consoleLevel** do _poza_. Aby uzyskać więcej informacji, zobacz [dokumentacja pliku host.JSON](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP wyzwalaczy i powiązań

HTTP i wyzwalaczy elementu webhook protokołu HTTP wyjściowe i powiązania reprezentują wiadomości HTTP za pomocą obiektów żądań i odpowiedzi.  

### <a name="request-object"></a>Obiekt "Request"

`context.req` (Żądanie), po której obiekt ma następujące właściwości:

| Właściwość      | Opis                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Obiekt, który zawiera treść żądania.               |
| _headers_     | Obiekt, który zawiera nagłówki żądania.                   |
| _— Metoda_      | Metoda HTTP żądania.                                |
| _originalUrl_ | Adres URL żądania.                                        |
| _params_      | Obiekt zawierający parametry routingu żądania. |
| _Zapytanie_       | Obiekt zawierający parametry zapytania.                  |
| _rawBody_     | Treść wiadomości w formie ciągu.                           |


### <a name="response-object"></a>Obiekt odpowiedzi

`context.res` Obiektu (odpowiedź) ma następujące właściwości:

| Właściwość  | Opis                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Obiekt, który zawiera treści odpowiedzi.         |
| _headers_ | Obiekt, który zawiera nagłówki odpowiedzi.             |
| _isRaw_   | Wskazuje, że formatowanie jest pomijana dla odpowiedzi.    |
| _status_  | Kod stanu HTTP odpowiedzi.                     |

### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi 

Podczas pracy z wyzwalaczami HTTP można uzyskiwanie dostępu do obiektów żądań i odpowiedzi HTTP, kilka sposobów:

+ **Z `req` i `res` właściwości `context` obiektu.** W ten sposób można użyć wzorca konwencjonalne HTTP dostępu do danych z obiektu kontekstu, zamiast pełnego `context.bindings.name` wzorca. Poniższy przykład pokazuje, jak uzyskać dostęp do `req` i `res` obiektów na `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Nazwane danych wejściowych i powiązania danych wyjściowych.** W ten sposób wyzwalacza HTTP i powiązania działać tak samo, jak inne powiązanie. W poniższym przykładzie ustawiono obiekt odpowiedzi za pomocą nazwane `response` powiązania: 

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
+ **_[Tylko odpowiedzi]_  Przez wywołanie metody `context.res.send(body?: any)`.** Odpowiedź HTTP jest tworzony przy użyciu danych wejściowych `body` jako treść odpowiedzi. `context.done()` jest wywoływany niejawnie.

+ **_[Tylko odpowiedzi]_  Przez wywołanie metody `context.done()`.** Specjalny rodzaj powiązanie HTTP zwraca odpowiedź, który jest przekazywany do `context.done()` metody. Powiązanie danych wyjściowych następujące HTTP definiuje `$return` parametr danych wyjściowych:

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

## <a name="node-version"></a>Wersja węzła

W poniższej tabeli przedstawiono wersja Node.js używana przez środowisko uruchomieniowe usługi Functions Każda główna wersja:

| Funkcje wersji | Wersja Node.js | 
|---|---|
| 1.x | 6.11.2 (zablokowane przez środowisko uruchomieniowe) |
| 2.x  | _Aktywne LTS_ i parzystych _bieżącego_ wersje środowiska Node.js (8.11.1 i 10.14.1 zalecane). Ustaw wersję przy użyciu WEBSITE_NODE_DEFAULT_VERSION [ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings).|

Zobaczyć bieżącą wersję używanego środowiska uruchomieniowego, sprawdzając powyższe ustawienie aplikacji lub drukowanie `process.version` z żadnej funkcji.

## <a name="dependency-management"></a>Zarządzanie zależnościami
Aby można było używać biblioteki społeczności w kodzie JavaScript, jak pokazano w poniższym przykładzie, należy się upewnić, że wszystkie zależności są zainstalowane na aplikację funkcji na platformie Azure.

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
> Należy zdefiniować `package.json` pliku w katalogu głównym aplikacji funkcji. Definiowanie pliku umożliwia wszystkich funkcji w aplikacji i udostępniać te same pakiety pamięci podręcznej, co daje najlepszą wydajność. Jeśli wystąpi konflikt wersji, można rozwiązać go, dodając `package.json` pliku w folderze określoną funkcję.  

W przypadku wdrażania aplikacji funkcji z kontroli źródła, wszelkie `package.json` plików w repozytorium, wywoła `npm install` w folderze podczas wdrażania. Jednak w przypadku wdrażania przy użyciu portalu lub interfejsu wiersza polecenia, trzeba będzie ręcznie zainstalować pakiety.

Istnieją dwa sposoby, aby zainstalować pakiety na aplikację funkcji: 

### <a name="deploying-with-dependencies"></a>Wdrażanie przy użyciu zależności
1. Zainstaluj wszystkie wymagane pakiety lokalnie przez uruchomienie `npm install`.

2. Wdróż swój kod i upewnij się, że `node_modules` folderu jest uwzględnione we wdrożeniu. 


### <a name="using-kudu"></a>Przy użyciu narzędzia Kudu
1. Przejdź do pozycji `https://<function_app_name>.scm.azurewebsites.net` (Plik > Nowy > Inny).

2. Kliknij przycisk **Debug Console** > **CMD**.

3. Przejdź do `D:\home\site\wwwroot`, a następnie przeciągnij plik package.json do **wwwroot** folder, w górnej połowie strony.  
    Mogą także przekazywać pliki do aplikacji funkcji w inny sposób. Aby uzyskać więcej informacji, zobacz [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). 

4. Po przekazaniu pliku package.json Uruchom `npm install` polecenia w pliku **konsoli zdalne wykonywanie kodu Kudu**.  
    Ta akcja pobiera pakiety wskazane w pliku package.json i ponowne uruchamianie aplikacji funkcji.

## <a name="environment-variables"></a>Zmienne środowiskowe

W przypadku funkcji [ustawienia aplikacji](functions-app-settings.md), takie jak połączenia z usługą ciągów, są widoczne jako zmiennych środowiskowych podczas wykonywania. Możesz uzyskać dostęp te ustawienia przy użyciu `process.env`, jak pokazano w `GetEnvironmentVariable` funkcji:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Podczas uruchamiania lokalnego, ustawienia aplikacji są odczytywane z [local.settings.json](functions-run-local.md#local-settings-file) pliku projektu.

## <a name="configure-function-entry-point"></a>Konfigurowanie punktu wejścia funkcji

`function.json` Właściwości `scriptFile` i `entryPoint` mogą być używane do konfigurowania lokalizację i nazwę wyeksportowanego funkcji. Te właściwości mogą być ważne, gdy Twój kod JavaScript jest transpiled.

### <a name="using-scriptfile"></a>za pomocą `scriptFile`

Domyślnie funkcja JavaScript jest wykonywana z `index.js`, plik, który współużytkuje tego samego katalogu nadrzędnego odpowiadającymi mu dostawcami `function.json`.

`scriptFile` można uzyskać strukturę folderów, która wygląda podobnie jak w poniższym przykładzie:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`function.json` Dla `myNodeFunction` powinien zawierać `scriptFile` właściwość wskazuje plik za pomocą funkcji eksportowanych do uruchomienia.

```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>za pomocą `entryPoint`

W `scriptFile` (lub `index.js`), funkcja musi zostać wyeksportowane przy użyciu `module.exports` Aby znaleźć i uruchomić. Domyślnie funkcja, która jest wykonywana po wyzwoleniu jest tylko Eksport z tego pliku eksportu o nazwie `run`, lub Eksportuj nazwane `index`.

Można to skonfigurować przy użyciu `entryPoint` w `function.json`, jak w poniższym przykładzie:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

W funkcji v2.x, który obsługuje `this` parametr funkcji użytkownika, kod funkcji może być następnie jak w poniższym przykładzie:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

W tym przykładzie należy zauważyć, że mimo, że obiekt jest eksportowany, istnieją żadnej gwarancji dla zachowania stanu między wykonaniami.

## <a name="local-debugging"></a>Debugowanie lokalne

Podczas pracy z usługą `--inspect` parametr procesu Node.js nasłuchuje klientowi debugowania na określonym porcie. W usłudze Azure Functions 2.x, można określić argumenty do przekazania do procesu Node.js, która uruchamia kod poprzez dodanie zmiennej środowiskowej lub ustawienia aplikacji `languageWorkers:node:arguments = <args>`. 

Aby debugować lokalnie, należy dodać `"languageWorkers:node:arguments": "--inspect=5858"` w obszarze `Values` w swojej [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) pliku i dołączyć debuger do portu 5858.

Podczas debugowania za pomocą programu VS Code `--inspect` zostanie automatycznie dodany parametr przy użyciu `port` wartości w pliku launch.json projektu.

W wersji 1.x, ustawienie `languageWorkers:node:arguments` nie będzie działać. Można wybrać port debugowania za pomocą [ `--nodeDebugPort` ](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parametru podstawowych narzędzi usługi Azure Functions.

## <a name="typescript"></a>TypeScript

Kiedy docelowy jest w wersji 2.x środowisko uruchomieniowe usługi Functions, zarówno [usługi Azure Functions dla programu Visual Studio Code](functions-create-first-function-vs-code.md) i [podstawowych narzędzi usługi Azure Functions](functions-run-local.md) umożliwiają tworzenie aplikacji funkcji przy użyciu szablonu, które obsługują Projekty aplikacji funkcji języka TypeScript. Szablon generuje `package.json` i `tsconfig.json` pliki projektu, które ułatwiają transpiluj, uruchamianie i publikowanie funkcje języka JavaScript z poziomu kodu TypeScript za pomocą tych narzędzi.

Wygenerowany `.funcignore` plik jest używany do wskazania, pliki, które są wyłączone, gdy projekt zostanie opublikowany na platformie Azure.  

Pliki TypeScript (TS) są transpiled w plikach JavaScript (js) `dist` katalogu wyjściowego. Szablony TypeScript używają [ `scriptFile` parametru](#using-scriptfile) w `function.json` wskazującą położenie odpowiedni plik js w `dist` folderu. Lokalizacja danych wyjściowych jest ustawiana przez szablon przy użyciu `outDir` parametru w `tsconfig.json` pliku. W przypadku zmiany tego ustawienia lub nazwę folderu, środowisko uruchomieniowe nie będzie mógł znaleźć kod w celu uruchomienia.

> [!NOTE]
> Eksperymentalna Obsługa TypeScript istnieje wersji 1.x środowisko uruchomieniowe usługi Functions. Transpiles wersji doświadczalnej pliki TypeScript w plikach JavaScript po wywołaniu funkcji. W wersji 2.x, ta obsługa eksperymentalna została zastąpiona przez metodę oparte na narzędziu, który wykonuje transpilation, zanim host jest zainicjowany i podczas procesu wdrażania.

Sposób, w jaki lokalnie tworzenia i wdrażania z projektu TypeScript zależy od narzędzia do programowania.

### <a name="visual-studio-code"></a>Visual Studio Code

[Usługi Azure Functions dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) rozszerzenie umożliwia tworzenie funkcji przy użyciu TypeScript. Podstawowe narzędzia jest to wymagane rozszerzenia usługi Azure Functions.

Aby utworzyć aplikację funkcji TypeScript w programie Visual Studio Code, możesz po prostu wybierz `TypeScript` po utworzeniu aplikacji funkcji i prośba o wybranie języka.

Po naciśnięciu klawisza **F5** Aby uruchomić aplikację lokalnie, transpilation jest gotowy, zanim host (func.exe) jest zainicjowany. 

Podczas wdrażania aplikacji funkcji na platformie Azure przy użyciu **Wdróż do aplikacji funkcji...**  przycisku rozszerzenia usługi Azure Functions najpierw generuje kompilacji gotowe do produkcji, plików JavaScript przy użyciu plików źródłowych TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Aby utworzyć TypeScript projektu aplikacji funkcji przy użyciu podstawowych narzędzi, należy określić opcję języka typescript, podczas tworzenia aplikacji funkcji. Można to zrobić w jednym z następujących sposobów:

- Uruchom `func init` polecenia select `node` jako stos języka, a następnie wybierz `typescript`.

- Uruchom polecenie `func init --worker-runtime typescript`.

Aby uruchomić kod aplikacji funkcji lokalnie przy użyciu podstawowych narzędzi, użyj `npm start` polecenia, zamiast `func host start`. `npm start` Polecenie jest odpowiednikiem następujących poleceń:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

Przed użyciem [ `func azure functionapp publish` ] polecenia do wdrożenia na platformie Azure, należy najpierw uruchomić `npm run build:production` polecenia. To polecenie tworzy gotowych do produkcji kompilacji plików JavaScript przy użyciu plików źródłowych TypeScript, które można wdrożyć przy użyciu [ `func azure functionapp publish` ].

## <a name="considerations-for-javascript-functions"></a>Zagadnienia dotyczące funkcji języka JavaScript

Podczas pracy z funkcjami JavaScript, należy pamiętać o zagadnienia w poniższych sekcjach.

### <a name="choose-single-vcpu-app-service-plans"></a>Wybierz pojedynczego vCPU planów usługi App Service

Podczas tworzenia aplikacji funkcji, która korzysta z planu usługi App Service, firma Microsoft zaleca, wybierz pozycję plan pojedynczego vCPU, a nie planu z wielu procesorów wirtualnych. Obecnie funkcje funkcje języka JavaScript bardziej efektywna na maszynach wirtualnych procesorów vCPU pojedynczego oraz przy użyciu większych maszyn wirtualnych nie generuje ulepszenia wydajności. Gdy jest to konieczne, można ręcznie skalować w poziomie, dodając kolejne wystąpienia maszyn wirtualnych procesorów wirtualnych na jednym lub można włączyć automatycznego skalowania. Aby uzyskać więcej informacji, zobacz [ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Zimny Start

Po rozpoczęciu tworzenia usługi Azure Functions w bez użycia serwera modelu hostingu zimnych są mogą stać się rzeczywistością. *Zimnego* odnosi się do faktu, że podczas uruchamiania aplikacji funkcji po raz pierwszy po okresie braku aktywności, zajmuje więcej czasu uruchamiania. Dla funkcji języka JavaScript przy użyciu drzewa duże zależności w szczególności zimnego mogą być znaczące. Aby przyspieszyć proces zimnego [uruchamiać swoje funkcje jako plik pakietu](run-functions-from-deployment-package.md) gdy jest to możliwe. Wiele metod wdrożenia za pomocą polecenia Uruchom z modelu pakietu domyślnie, ale jeśli występują duże zimnych startów i nie działają w ten sposób, ta zmiana może oferować wprowadzono znaczące ulepszenia.

### <a name="connection-limits"></a>Limity połączeń

Korzystając z klienta specyficzne dla usługi w aplikacji usługi Azure Functions, nie należy tworzyć nowych klientów za pomocą każdego wywołania funkcji. Zamiast tego utworzyć pojedynczy statyczny klienta w zakresie globalnym. Aby uzyskać więcej informacji, zobacz [Zarządzanie połączeniami w usłudze Azure Functions](manage-connections.md).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

+ [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
+ [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
+ [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)

["functionapp func azure publish"]: functions-run-local.md#project-file-deployment
