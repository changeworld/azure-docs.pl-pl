---
title: Dokumentacja dewelopera języka JavaScript dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie funkcji przy użyciu języka JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: b0e078e3e7f18e3370ff1bcd90935e7fece265f0
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391184"
---
# <a name="azure-functions-javascript-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions JavaScript

Środowiska JavaScript dla usługi Azure Functions można łatwo eksportować funkcji, który jest przekazywany jako `context` obiektu do komunikowania się ze środowiskiem uruchomieniowym i odbieranie i wysyłanie danych za pomocą powiązania.

W tym artykule założono, że zostały już przeczytane [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md).

## <a name="exporting-a-function"></a>Eksportowanie funkcji
Wszystkie funkcje języka JavaScript należy wyeksportować pojedynczy `function` za pośrednictwem `module.exports` środowiska uruchomieniowego Znajdź funkcję i uruchomimy ją. Ta funkcja zawsze musi zawierać `context` obiektu.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Powiązania `direction === "in"` są przekazywane jako argumenty funkcji, co oznacza, że można użyć [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) dynamicznie obsługi nowych danych wejściowych (na przykład za pomocą `arguments.length` Iterowanie wszystkich danych wejściowych). Ta funkcja jest wygodne, gdy masz tylko wyzwalacz oraz brak dodatkowych danych wejściowych, ponieważ dane wyzwalacza przewidywalny dostęp bez odwołania do usługi `context` obiektu.

Argumenty zawsze są przekazywane do funkcji w kolejności, w jakiej występują one w *function.json*, nawet jeśli nie określisz je w instrukcji eksportu. Na przykład, jeśli masz `function(context, a, b)` i zmień go na `function(context, a)`, nadal można pobrać wartości `b` w kodzie funkcji, odwołując się do `arguments[2]`.

Wszystkie powiązania, niezależnie od tego, w kierunku, również są przekazywane `context` obiektu (zobacz poniższy skrypt). 

## <a name="context-object"></a>Obiekt kontekstu
Środowisko wykonawcze używa `context` obiekt do przekazywania danych do i z funkcji i umożliwienie komunikowania się ze środowiskiem uruchomieniowym.

`context` Obiekt zawsze jest pierwszy parametr do funkcji i musi być uwzględniony, ponieważ ma ona metody takie jak `context.done` i `context.log`, które są wymagane do korzystania ze środowiska uruchomieniowego poprawnie. Niezależnie od rodzaju chcesz można nazwać obiektu (na przykład `ctx` lub `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>właściwość Context.Bindings

```
context.bindings
```
Zwraca obiekt o nazwie, która zawiera wszystkie dane wejściowe i wyjściowe. Na przykład następująca definicja powiązania w swojej *function.json* umożliwia dostęp do zawartości kolejki z `context.bindings.myInput` obiektu. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
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

### <a name="contextdone-method"></a>Metoda context.Done
```
context.done([err],[propertyBag])
```

Informuje środowisko uruchomieniowe, które kodu zostało zakończone. Należy wywołać `context.done`, lub inne środowisko wykonawcze nie wie, że funkcji zostało ukończone i wykonywania przekroczy limit czasu. 

`context.done` Metoda umożliwia przesłać zarówno błędach zdefiniowane przez użytkownika do środowiska uruchomieniowego i zbioru właściwości właściwości, które zastąpienie właściwości na `context.bindings` obiektu.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Metoda context.log  

```
context.log(message)
```
Pozwala na zapis w dziennikach konsoli przesyłania strumieniowego na domyślny poziom śledzenia. Na `context.log`, dodatkowe opcje rejestrowania metody są dostępne, które pozwalają na zapisywanie w dzienniku konsoli na innych poziomach śledzenia:


| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **Błąd (_komunikat_)**   | Zapisuje poziom błędów rejestrowania lub niższą.   |
| **Ostrzeżenie (_komunikat_)**    | Zapisuje poziom ostrzeżeń rejestrowania lub niższą. |
| **info(_message_)**    | Zapisuje informacje o poziomie rejestrowania lub niższą.    |
| **pełne (_komunikat_)** | Zapisuje pełne rejestrowanie na poziomie.           |

Poniższy przykład zapisuje do konsoli, gdy poziom śledzenia Ostrzeżenie:

```javascript
context.log.warn("Something has happened."); 
```
Możesz ustawić próg poziom śledzenia dla rejestrowania w pliku host.json lub go wyłączyć.  Aby uzyskać więcej informacji na temat zapisywać w dziennikach, zobacz następną sekcję.

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

Inne opcje `dataType` są `stream` i `string`.

## <a name="writing-trace-output-to-the-console"></a>Zapisywanie danych wyjściowych śledzenia w konsoli programu 

W przypadku funkcji używasz `context.log` metody można zapisywać dane wyjściowe śledzenia do konsoli. W tym momencie nie można użyć `console.log` do zapisu do konsoli.

Gdy wywołujesz `context.log()`, wiadomości są zapisywane do konsoli na domyślny poziom śledzenia, który jest _informacje_ poziom śledzenia. Poniższy kod zapisuje do konsoli, gdy poziom śledzenia informacje:

```javascript
context.log({hello: 'world'});  
```

Powyższy kod jest równoważny z następującym kodem:

```javascript
context.log.info({hello: 'world'});  
```

Poniższy kod, zapisuje się do konsoli na poziomie błędu:

```javascript
context.log.error("An error has occurred.");  
```

Ponieważ _błąd_ znajduje się ślad najwyższego poziomu, to śledzenia są zapisywane dane wyjściowe na wszystkich poziomach śledzenia tak długo, jak rejestrowanie jest włączone.  


Wszystkie `context.log` metody obsługują ten sam format parametru, który jest obsługiwany przez Node.js [metoda util.format](https://nodejs.org/api/util.html#util_util_format_format). Rozważmy poniższy kod, który zapisuje je w konsoli przy użyciu domyślnego poziomu śledzenia:

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

`request` Obiekt ma następujące właściwości:

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

`response` Obiekt ma następujące właściwości:

| Właściwość  | Opis                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Obiekt, który zawiera treści odpowiedzi.         |
| _Nagłówki_ | Obiekt, który zawiera nagłówki odpowiedzi.             |
| _isRaw_   | Wskazuje, że formatowanie jest pomijana dla odpowiedzi.    |
| _status_  | Kod stanu HTTP odpowiedzi.                     |

### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi 

Podczas pracy z wyzwalaczami HTTP można uzyskiwanie dostępu do obiektów żądań i odpowiedzi HTTP w jednym z trzech sposobów:

+ Nazwane danych wejściowych i powiązania danych wyjściowych. W ten sposób wyzwalacza HTTP i powiązania działać tak samo, jak inne powiązanie. W poniższym przykładzie ustawiono obiekt odpowiedzi za pomocą nazwane `response` powiązania: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ Z `req` i `res` właściwości `context` obiektu. W ten sposób można użyć wzorca konwencjonalne HTTP dostępu do danych z obiektu kontekstu, zamiast pełnego `context.bindings.name` wzorca. Poniższy przykład pokazuje, jak uzyskać dostęp do `req` i `res` obiektów na `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Przez wywołanie metody `context.done()`. Specjalny rodzaj powiązanie HTTP zwraca odpowiedź, który jest przekazywany do `context.done()` metody. Powiązanie danych wyjściowych następujące HTTP definiuje `$return` parametr danych wyjściowych:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    To powiązanie danych wyjściowych oczekuje, że trzeba będzie podać odpowiedzi, po wywołaniu `done()`, wykonując następujące czynności:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Węzeł wersji i pakiet zarządzania

W poniższej tabeli przedstawiono wersja Node.js używana przez środowisko uruchomieniowe usługi Functions Każda główna wersja:

| Funkcje wersji | Wersja Node.js | 
|---|---|
| 1.x | 6.11.2 (zablokowane przez środowisko uruchomieniowe) |
| 2.x  | _Aktywne LTS_ i _bieżącego_ wersje środowiska Node.js (8.11.1 i 10.6.0 zalecane). Ustaw wersję przy użyciu WEBSITE_NODE_DEFAULT_VERSION [ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings).|

Zobaczyć bieżącą wersję środowiska uruchomieniowego używanej przez usługę Drukowanie `process.version` z żadnej funkcji.

Poniższe kroki pozwalają na wybranie pakietów w aplikacji funkcji: 

1. Przejdź do pozycji `https://<function_app_name>.scm.azurewebsites.net` (Plik > Nowy > Inny).

2. Kliknij przycisk **Debug Console** > **CMD**.

3. Przejdź do `D:\home\site\wwwroot`, a następnie przeciągnij plik package.json do **wwwroot** folder, w górnej połowie strony.  
    Mogą także przekazywać pliki do aplikacji funkcji w inny sposób. Aby uzyskać więcej informacji, zobacz [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). 

4. Po przekazaniu pliku package.json Uruchom `npm install` polecenia w pliku **konsoli zdalne wykonywanie kodu Kudu**.  
    Ta akcja pobiera pakiety wskazane w pliku package.json i ponowne uruchamianie aplikacji funkcji.

Po zainstalowaniu pakietów, potrzebujesz, możesz zaimportować je do funkcji przez wywołanie metody `require('packagename')`, jak w poniższym przykładzie:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Należy zdefiniować `package.json` pliku w katalogu głównym aplikacji funkcji. Definiowanie pliku umożliwia wszystkich funkcji w aplikacji i udostępniać te same pakiety pamięci podręcznej, co daje najlepszą wydajność. Jeśli wystąpi konflikt wersji, można rozwiązać go, dodając `package.json` pliku w folderze określoną funkcję.  

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
## <a name="considerations-for-javascript-functions"></a>Zagadnienia dotyczące funkcji języka JavaScript

Podczas pracy z funkcjami JavaScript, należy pamiętać o uwagi w poniższych sekcjach.

### <a name="choose-single-vcpu-app-service-plans"></a>Wybierz pojedynczego vCPU planów usługi App Service

Podczas tworzenia aplikacji funkcji, która korzysta z planu usługi App Service, firma Microsoft zaleca, wybierz pozycję plan pojedynczego vCPU, a nie planu z wielu procesorów wirtualnych. Obecnie funkcje funkcje języka JavaScript bardziej efektywna na maszynach wirtualnych procesorów vCPU pojedynczego oraz przy użyciu większych maszyn wirtualnych nie generuje ulepszenia wydajności. Gdy jest to konieczne, można ręcznie skalować w poziomie, dodając kolejne wystąpienia maszyn wirtualnych procesorów wirtualnych na jednym lub można włączyć automatycznego skalowania. Aby uzyskać więcej informacji, zobacz [ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Obsługa TypeScript i CoffeeScript
Ponieważ bezpośredniej pomocy technicznej jeszcze nie istnieje dla kompilacji automatycznie TypeScript lub CoffeeScript za pośrednictwem środowiska uruchomieniowego, obsługę takich musi być obsługiwane spoza środowiska uruchomieniowego, w czasie wdrażania. 

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)

