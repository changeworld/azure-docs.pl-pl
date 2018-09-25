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
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: 24f7faa0fb111e4e537a7db3f5e1eea709d1ca59
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957737"
---
# <a name="azure-functions-javascript-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions JavaScript
Ten przewodnik zawiera informacje dotyczące niewymagającego pisania usługi Azure Functions za pomocą języka JavaScript.

Funkcja języka JavaScript jest wyeksportowany `function` , zostanie wykonana po wyzwoleniu ([wyzwalacze są konfigurowane w function.json](functions-triggers-bindings.md)). Każda funkcja jest przekazywana `context` obiektu, który jest używany do odbierania i wysyłania danych powiązania, rejestrowanie i komunikowania się ze środowiskiem uruchomieniowym.

W tym artykule założono, że już znasz [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md). Zalecane jest również wykonano samouczek, w obszarze "Samouczki Szybki Start" Aby [tworzenie pierwszej funkcji](functions-create-first-function-vs-code.md).

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów wymagane dla projektu w języku JavaScript wygląda podobnie do poniższego. Należy pamiętać, że można zmienić to ustawienie domyślne: zobacz [plik_skryptu](functions-reference-node.md#using-scriptfile) sekcji poniżej, aby uzyskać więcej informacji.

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
 | - bin
```

W folderze głównym projektu jest wspólny [host.json](functions-host-json.md) pliku, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma folder z pliku z kodem (js) i plik konfiguracji powiązania (function.json).

Rozszerzenia powiązania wymagane w [wersji 2.x](functions-versions.md) funkcji środowiska uruchomieniowego są zdefiniowane w `extensions.csproj` pliku z plikami rzeczywistej biblioteki w `bin` folderu. Podczas tworzenia lokalnie, należy najpierw [zarejestrować rozszerzeń powiązania](functions-triggers-bindings.md#local-development-azure-functions-core-tools). Podczas tworzenia funkcji w witrynie Azure portal, rejestracja odbywa się za Ciebie.

## <a name="exporting-a-function"></a>Eksportowanie funkcji

Funkcje języka JavaScript musi być eksportowany za pośrednictwem [ `module.exports` ](https://nodejs.org/api/modules.html#modules_module_exports) (lub [ `exports` ](https://nodejs.org/api/modules.html#modules_exports)). W przypadku domyślnej eksportowanych funkcji powinny być tylko eksportu z jego pliku eksportu o nazwie `run`, lub Eksportuj nazwane `index`. Domyślna lokalizacja funkcji to `index.js`, gdzie `index.js` udziałów tego samego katalogu nadrzędnego odpowiednich `function.json`. Należy pamiętać, że nazwa `function.json`w katalogu nadrzędnym jest zawsze nazwę funkcji. 

Aby skonfigurować lokalizację pliku i wyeksportować nazwę funkcji, przeczytaj temat [Konfigurowanie swoją funkcję punktu wejścia](functions-reference-node.md#configure-function-entry-point) poniżej.

Punktem wejścia wyeksportowanej funkcji zawsze należy wykonać `context` obiektu jako pierwszego parametru.

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
// You can also use 'arguments' to dynamically handle inputs
module.exports = async function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
};
```

Wyzwalacze i powiązania danych wejściowych (vazby prvku `direction === "in"`) może być przekazywany do funkcji jako parametrów. Są one przekazywane do funkcji w tej samej kolejności, które są zdefiniowane w *function.json*. Można również dynamiczne obsługi danych wejściowych przy użyciu języka JavaScript [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) obiektu. Na przykład, jeśli masz `function(context, a, b)` i zmień go na `function(context, a)`, nadal można pobrać wartości `b` w kodzie funkcji, odwołując się do `arguments[2]`.

Wszystkie powiązania, niezależnie od tego, w kierunku, również są przekazywane `context` przy użyciu `context.bindings` właściwości.

## <a name="context-object"></a>Obiekt kontekstu
Środowisko wykonawcze używa `context` obiekt do przekazywania danych do i z funkcji i umożliwienie komunikowania się ze środowiskiem uruchomieniowym.

`context` Obiekt zawsze jest pierwszy parametr do funkcji i musi być uwzględniony, ponieważ ma ona metody takie jak `context.done` i `context.log`, które są wymagane do korzystania ze środowiska uruchomieniowego poprawnie. Niezależnie od rodzaju chcesz można nazwać obiektu (na przykład `ctx` lub `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>właściwość Context.Bindings

```
context.bindings
```
Zwraca obiekt o nazwie, która zawiera wszystkie dane wejściowe i wyjściowe. Na przykład, następujące definicje powiązania w swojej *function.json* pozwala korzystać z zawartości kolejki z `context.bindings.myInput` i przypisz dane wyjściowe do kolejki za pomocą `context.bindings.myOutput`.

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

Należy pamiętać, że można zdefiniować przy użyciu danych powiązania danych wyjściowych `context.done` zamiast metody `context.binding` obiektu (patrz poniżej).

### <a name="contextbindingdata-property"></a>Właściwość context.bindingData

```
context.bindingData
```
Zwraca obiekt o nazwie, który zawiera dane wywołania funkcji i metadanych wyzwalacza (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Na przykład wyzwalacz metadanych zobacz [przykład usługi event hubs](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Metoda context.Done
```
context.done([err],[propertyBag])
```

Informuje środowisko uruchomieniowe, które kodu zostało zakończone. Jeśli korzysta z funkcji JavaScript [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaracji (dostępne za pomocą środowiska Node 8 i nowsze w wersji funkcji 2.x), nie trzeba używać `context.done()`. `context.done` Wywołania zwrotnego jest wywoływany niejawnie.

Jeśli funkcja nie jest funkcją async **musi wywołać** `context.done` poinformować w czasie wykonywania, że funkcja została zakończona. Wykonanie przekroczy limit czasu w przypadku jej braku.

`context.done` Metoda umożliwia przesłać zarówno błędach zdefiniowane przez użytkownika do środowiska uruchomieniowego i obiektem JSON zawierającym dane wyjściowe powiązanie danych. Właściwości są przekazywane do `context.done` spowoduje zastąpienie niczego nastavit `context.bindings` obiektu.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Metoda context.log  

```
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

## <a name="binding-data-type"></a>Typ danych powiązania

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

## <a name="writing-trace-output-to-the-console"></a>Zapisywanie danych wyjściowych śledzenia w konsoli programu 

W przypadku funkcji używasz `context.log` metody można zapisywać dane wyjściowe śledzenia do konsoli. W v2.x funkcji śledzenia możesz za pomocą `console.log` są przechwytywane na poziomie aplikacji funkcji. Oznacza to, że dane wyjściowe z `console.log` nie są związane z wywołania określonych funkcji i dlatego nie są wyświetlane w dziennikach określonych funkcji. Będą one jednak propagowane do usługi Application Insights. W funkcji v1.x, nie można użyć `console.log` do zapisu do konsoli. 

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

Funkcje pozwala zdefiniować próg poziom śledzenia dla zapisu do konsoli, co ułatwia formant, który sposób śledzenia są zapisywane w konsoli z funkcji. Aby ustawić próg wszystkie ślady wyświetlony w konsoli, należy użyć `tracing.consoleLevel` właściwości w pliku host.json. To ustawienie ma zastosowanie do wszystkich funkcji w aplikacji funkcji. W poniższym przykładzie ustawiono wartość progową śledzenia, aby włączyć pełne rejestrowanie:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Wartości typu **consoleLevel** odnoszą się do nazw `context.log` metody. Aby wyłączyć wszystkie rejestrowanie śledzenia do konsoli, należy ustawić **consoleLevel** do _poza_. Aby uzyskać więcej informacji, zobacz [dokumentacja pliku host.JSON](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP wyzwalaczy i powiązań

HTTP i wyzwalaczy elementu webhook protokołu HTTP wyjściowe i powiązania reprezentują wiadomości HTTP za pomocą obiektów żądań i odpowiedzi.  

### <a name="request-object"></a>Obiekt "Request"

`context.req` (Żądanie), po której obiekt ma następujące właściwości:

| Właściwość      | Opis                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Obiekt, który zawiera treść żądania.               |
| _Nagłówki_     | Obiekt, który zawiera nagłówki żądania.                   |
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
| _Nagłówki_ | Obiekt, który zawiera nagłówki odpowiedzi.             |
| _isRaw_   | Wskazuje, że formatowanie jest pomijana dla odpowiedzi.    |
| _status_  | Kod stanu HTTP odpowiedzi.                     |

### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi 

Podczas pracy z wyzwalaczami HTTP można uzyskiwanie dostępu do obiektów żądań i odpowiedzi HTTP, kilka sposobów:

+ Z `req` i `res` właściwości `context` obiektu. W ten sposób można użyć wzorca konwencjonalne HTTP dostępu do danych z obiektu kontekstu, zamiast pełnego `context.bindings.name` wzorca. Poniższy przykład pokazuje, jak uzyskać dostęp do `req` i `res` obiektów na `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Nazwane danych wejściowych i powiązania danych wyjściowych. W ten sposób wyzwalacza HTTP i powiązania działać tak samo, jak inne powiązanie. W poniższym przykładzie ustawiono obiekt odpowiedzi za pomocą nazwane `response` powiązania: 

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
+ _[Tylko odpowiedzi]_  Przez wywołanie metody `context.res.send(body?: any)`. Odpowiedź HTTP jest tworzony przy użyciu danych wejściowych `body` jako treść odpowiedzi. `context.done()` jest wywoływany niejawnie.

+ _[Tylko odpowiedzi]_  Przez wywołanie metody `context.done()`. Specjalny rodzaj powiązanie HTTP zwraca odpowiedź, który jest przekazywany do `context.done()` metody. Powiązanie danych wyjściowych następujące HTTP definiuje `$return` parametr danych wyjściowych:

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
| 2.x  | _Aktywne LTS_ i _bieżącego_ wersje środowiska Node.js (8.11.1 i 10.6.0 zalecane). Ustaw wersję przy użyciu WEBSITE_NODE_DEFAULT_VERSION [ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings).|

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

Należy zauważyć, że należy zdefiniować `package.json` pliku w katalogu głównym aplikacji funkcji. Definiowanie pliku umożliwia wszystkich funkcji w aplikacji i udostępniać te same pakiety pamięci podręcznej, co daje najlepszą wydajność. Jeśli wystąpi konflikt wersji, można rozwiązać go, dodając `package.json` pliku w folderze określoną funkcję.  

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
Aby uzyskać zmiennej środowiskowej lub wartość ustawienia aplikacji, użyj `process.env`, jak pokazano w `GetEnvironmentVariable` funkcji:

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

## <a name="configure-function-entry-point"></a>Konfigurowanie punktu wejścia funkcji

`function.json` Właściwości `scriptFile` i `entryPoint` mogą być używane do konfigurowania lokalizację i nazwę wyeksportowanego funkcji. Mogą to być ważne, jeśli Twój kod JavaScript jest transpiled.

### <a name="using-scriptfile"></a>za pomocą `scriptFile`

Domyślnie funkcja JavaScript jest wykonywana z `index.js`, plik, który współużytkuje tego samego katalogu nadrzędnego odpowiadającymi mu dostawcami `function.json`.

`scriptFile` można uzyskać strukturę folderów, która wygląda w następujący sposób:
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

Można to skonfigurować przy użyciu `entryPoint` w `function.json`:
```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

W funkcji v2.x, który obsługuje `this` parametr funkcji użytkownika, kod funkcji może następnie być następująca:
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

W tym przykładzie należy zauważyć, że mimo, że obiekt jest eksportowany, nie istnieją żadne zawierających wokół zachowania stanu między wykonaniami.

## <a name="considerations-for-javascript-functions"></a>Zagadnienia dotyczące funkcji języka JavaScript

Podczas pracy z funkcjami JavaScript, należy pamiętać o zagadnienia w poniższych sekcjach.

### <a name="choose-single-vcpu-app-service-plans"></a>Wybierz pojedynczego vCPU planów usługi App Service

Podczas tworzenia aplikacji funkcji, która korzysta z planu usługi App Service, firma Microsoft zaleca, wybierz pozycję plan pojedynczego vCPU, a nie planu z wielu procesorów wirtualnych. Obecnie funkcje funkcje języka JavaScript bardziej efektywna na maszynach wirtualnych procesorów vCPU pojedynczego oraz przy użyciu większych maszyn wirtualnych nie generuje ulepszenia wydajności. Gdy jest to konieczne, można ręcznie skalować w poziomie, dodając kolejne wystąpienia maszyn wirtualnych procesorów wirtualnych na jednym lub można włączyć automatycznego skalowania. Aby uzyskać więcej informacji, zobacz [ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Obsługa TypeScript i CoffeeScript
Ponieważ bezpośredniej pomocy technicznej jeszcze nie istnieje dla kompilacji automatycznie TypeScript lub CoffeeScript za pośrednictwem środowiska uruchomieniowego, obsługę takich musi być obsługiwane spoza środowiska uruchomieniowego, w czasie wdrażania. 

### <a name="cold-start"></a>Zimny Start
Po rozpoczęciu tworzenia usługi Azure Functions w bez użycia serwera modelu hostingu zimnych są mogą stać się rzeczywistością. "Zimny start" odnosi się do faktu, że podczas uruchamiania aplikacji funkcji po raz pierwszy po okresie braku aktywności, będzie trwało dłużej mogła się uruchomić. Dla funkcji języka JavaScript przy użyciu drzewa duże zależności w szczególności może to powodować spowolnienie głównych. W celu przyspieszenia procesu, jeśli to możliwe, [uruchamiać swoje funkcje jako plik pakietu](run-functions-from-deployment-package.md). Wiele metod wdrażania, zoptymalizowany pod kątem ten model domyślnie, ale jeśli występują duże zimnych startów i nie są uruchomione na podstawie pliku pakietu, może to być ogromną poprawy jakości obsługi.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)

