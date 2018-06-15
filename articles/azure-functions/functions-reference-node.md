---
title: Dokumentacja dla deweloperów języka JavaScript dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu tworzenia funkcji przy użyciu języka JavaScript.
services: functions
documentationcenter: na
author: tdykstra
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
ms.author: tdykstra
ms.openlocfilehash: 78f29cd4a20861e40bb7f7f398979b8d93387a7b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936630"
---
# <a name="azure-functions-javascript-developer-guide"></a>Przewodnik dewelopera usługi Azure funkcji JavaScript

Obsługi języka JavaScript dla usługi Azure Functions można łatwo eksportować funkcję, która jest przekazywany jako `context` obiekt do komunikowania się ze środowiskiem uruchomieniowym i odbieranie i wysyłanie danych za pośrednictwem powiązania.

W tym artykule przyjęto założenie, że został już przeczytany [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md).

## <a name="exporting-a-function"></a>Eksportowanie funkcji
Wszystkie funkcje kodu JavaScript, należy wyeksportować pojedynczy `function` za pośrednictwem `module.exports` środowiska uruchomieniowego można znaleźć funkcji i uruchom go. Ta funkcja musi zawsze zawierać `context` obiektu.

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

Powiązania `direction === "in"` są przekazywane jako argumenty funkcji, co oznacza, że można używać [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) dynamicznie obsługi nowych danych wejściowych (na przykład za pomocą `arguments.length` do wykonywania iteracji wszystkich danych wejściowych). Ta funkcja jest wygodne tylko wyzwalacz i nie dodatkowe dane wejściowe, ponieważ może jednoznacznie uzyskać dostęp do danych wyzwalacza bez odwołania do Twojej `context` obiektu.

Argumenty zawsze są przekazywane do funkcji w kolejności ich występowania w *function.json*, nawet jeśli nie określisz ich w instrukcji eksportu. Na przykład, jeśli masz `function(context, a, b)` i zmień go na `function(context, a)`, nadal można pobrać wartości `b` w kodzie funkcji, odwołując się do `arguments[2]`.

Wszystkie powiązania, niezależnie od kierunku, również są przekazywane `context` obiektu (zobacz poniższy skrypt). 

## <a name="context-object"></a>Obiekt kontekstu
Środowisko wykonawcze używa `context` do przekazywania danych do i z funkcji i umożliwienie komunikowania się ze środowiskiem uruchomieniowym obiektów.

`context` Obiektu jest zawsze pierwszy parametr funkcji i muszą być uwzględnione, ponieważ ma ona metody takie jak `context.done` i `context.log`, które są wymagane do korzystania ze środowiska wykonawczego poprawnie. Niezależnie od chcesz można nazwę obiektu (na przykład `ctx` lub `c`).

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
Zwraca nazwanego obiektu, który zawiera wszystkie dane wejściowe i wyjściowe. Na przykład następujące definicji powiązania w Twojej *function.json* umożliwia dostęp do zawartości kolejki z `context.bindings.myInput` obiektu. 

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

### <a name="contextdone-method"></a>context.Done — metoda
```
context.done([err],[propertyBag])
```

Informuje o środowisko uruchomieniowe, które kodu zostało zakończone. Należy wywołać `context.done`, lub #else środowiska uruchomieniowego nie wie, że funkcja została ukończona wykonywanie upłynął limit czasu. 

`context.done` Metoda pozwala przesłać zarówno na użytkownika Błąd środowiska uruchomieniowego i zbiór właściwości właściwości, które zastępują właściwości na `context.bindings` obiektu.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>context.log — metoda  

```
context.log(message)
```
Umożliwia pisanie w dziennikach konsoli przesyłania strumieniowego na poziomie śledzenia domyślnego. Na `context.log`, dodatkowe opcje rejestrowania metody są dostępne, które pozwalają na zapisywanie w dzienniku konsoli na innych poziomach śledzenia:


| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **Błąd (_komunikat_)**   | Zapisuje poziom błędu rejestrowanie lub niższy.   |
| **Ostrzegaj (_komunikat_)**    | Zapisuje poziom ostrzeżeń rejestrowanie lub niższy. |
| **info(_message_)**    | Zapisuje informacje o poziomie rejestrowanie lub niższy.    |
| **pełne (_komunikat_)** | Zapisuje pełne rejestrowanie na poziomie.           |

Poniższe przykładowe polecenie zapisuje konsoli na poziomie śledzenia ostrzeżenia:

```javascript
context.log.warn("Something has happened."); 
```
Możesz ustawić próg poziom śledzenia dla rejestrowania w pliku host.json lub ją wyłączyć.  Aby uzyskać więcej informacji na temat zapisywać w dziennikach, zobacz następną sekcję.

## <a name="binding-data-type"></a>Typ danych powiązania

Aby określić typ danych dla powiązania wejściowego, użyj `dataType` właściwości w definicji powiązania. Na przykład można odczytać treści żądania HTTP w formacie binarnym, użyj typu `binary`:

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

W przypadku funkcji, użyj `context.log` metod do zapisywania danych wyjściowych śledzenia do konsoli. W tym momencie nie można użyć `console.log` można zapisać do konsoli.

Podczas wywoływania `context.log()`, wiadomość jest wyświetlony w konsoli na poziomie śledzenia domyślna, czyli _informacji_ poziom śledzenia. Zapisuje następujący kod do konsoli na poziomie śledzenia informacji:

```javascript
context.log({hello: 'world'});  
```

Poprzedni kod jest odpowiednikiem następującego kodu:

```javascript
context.log.info({hello: 'world'});  
```

Zapisuje następujący kod do konsoli na poziomie Błąd:

```javascript
context.log.error("An error has occurred.");  
```

Ponieważ _błąd_ śledzenia najwyższego poziomu, to śledzenia są zapisywane dane wyjściowe na wszystkich poziomach śledzenia tak długo, jak rejestrowanie jest włączone.  


Wszystkie `context.log` metody obsługują ten sam format parametru, który jest obsługiwany przez Node.js [metody util.format](https://nodejs.org/api/util.html#util_util_format_format). Rozważmy następujący kod, który zapisuje do konsoli przy użyciu domyślnego poziomu śledzenia:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Można również napisać ten sam kod w następującym formacie:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Skonfiguruj poziom śledzenia dla konsoli rejestrowania

Funkcje pozwala zdefiniować próg poziom śledzenia dla zapisywanie w konsoli, co ułatwia formant, który sposób dane śledzenia są zapisywane w konsoli z funkcji. Aby ustawić próg wszystkie ślady wyświetlony w konsoli, należy użyć `tracing.consoleLevel` właściwość w pliku host.json. To ustawienie ma zastosowanie do wszystkich funkcji w funkcji aplikacji. Poniższy przykład przedstawia próg śledzenia, aby włączyć pełne rejestrowanie:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Wartości typu **consoleLevel** odpowiadają nazwy `context.log` metody. Aby wyłączyć wszystkie rejestrowanie śledzenia do konsoli, należy ustawić **consoleLevel** do _poza_. Aby uzyskać więcej informacji, zobacz [odwołania host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP wyzwalaczy i powiązań

HTTP i wyzwalaczy elementu webhook HTTP wyjściowe i powiązania reprezentują wiadomości HTTP za pomocą obiektów żądania i odpowiedzi.  

### <a name="request-object"></a>Obiekt żądania

`request` Obiekt ma następujące właściwości:

| Właściwość      | Opis                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Obiekt, który zawiera treść żądania.               |
| _Nagłówki_     | Obiekt, który zawiera nagłówki żądania.                   |
| _— Metoda_      | Metoda HTTP żądania.                                |
| _originalUrl_ | Adres URL żądania.                                        |
| _Parametry_      | Obiekt zawierający parametry routingu żądania. |
| _Zapytania_       | Obiekt zawierający parametry zapytania.                  |
| _rawBody_     | Treść komunikatu jako ciąg.                           |


### <a name="response-object"></a>Obiekt odpowiedzi

`response` Obiekt ma następujące właściwości:

| Właściwość  | Opis                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Obiekt, który zawiera treść odpowiedzi.         |
| _Nagłówki_ | Obiekt, który zawiera nagłówki odpowiedzi.             |
| _isRaw_   | Wskazuje, że formatowanie zostało pominięte dla odpowiedzi.    |
| _status_  | Kod stanu HTTP odpowiedzi.                     |

### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi 

Podczas pracy z wyzwalaczy HTTP, aby dostęp do obiektów żądań i odpowiedzi HTTP w jednym z trzech sposobów:

+ Z danych wejściowych o nazwie i powiązania danych wyjściowych. W ten sposób wyzwalacza HTTP i powiązania pracy taki sam jak inne powiązanie. Poniższy przykład przedstawia obiekt odpowiedzi przy użyciu nazwane `response` powiązania: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ Z `req` i `res` właściwości `context` obiektu. W ten sposób można używać konwencjonalnej wzorca HTTP uzyskują dostęp do danych z obiektu context, zamiast w pełni `context.bindings.name` wzorca. Poniższy przykład przedstawia sposób uzyskiwania dostępu do `req` i `res` obiektów na `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Wywołując `context.done()`. Specjalny rodzaj powiązanie HTTP zwraca odpowiedź, który jest przekazywany do `context.done()` metody. Następujące HTTP powiązania wyjściowego definiuje `$return` parametru wyjściowego:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    To powiązanie danych wyjściowych oczekuje podania odpowiedzi podczas wywoływania `done()`w następujący sposób:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Zarządzanie version i package węzła

W poniższej tabeli przedstawiono wersji środowiska Node.js używane przez każdego wersji głównej funkcji środowiska uruchomieniowego:

| Funkcje wersji | Wersja Node.js | 
|---|---|
| 1.x | 6.11.2 (zablokowane przez środowisko uruchomieniowe) |
| 2.x  |> = 8.4.0 z bieżącym LTS 8.9.4 zalecane. Ustaw wersję za pomocą WEBSITE_NODE_DEFAULT_VERSION [ustawienie aplikacji](functions-how-to-use-azure-function-app-settings.md#settings).|

Można wyświetlić bieżącą wersję środowiska uruchomieniowego używanej przez usługę Drukowanie `process.version` z dowolnej funkcji.

Poniższe kroki umożliwiają dodanie pakietów w aplikacji funkcji: 

1. Przejdź do pozycji `https://<function_app_name>.scm.azurewebsites.net` (Plik > Nowy > Inny).

2. Kliknij przycisk **Konsola debugowania** > **CMD**.

3. Przejdź do `D:\home\site\wwwroot`, a następnie przeciągnij plik package.json **wwwroot** folderu na górze strony.  
    Można również przekazać pliki do aplikacji funkcji w inny sposób. Aby uzyskać więcej informacji, zobacz [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). 

4. Po przekazaniu pliku package.json, uruchom `npm install` w **konsoli zdalne wykonywanie kodu Kudu**.  
    Ta akcja pobiera pakiety określonej w pliku package.json i ponowne uruchomienie aplikacji funkcji.

Po zainstalowaniu pakietów, należy ich importowania do funkcji przez wywołanie metody `require('packagename')`, jak w poniższym przykładzie:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Należy zdefiniować `package.json` pliku w katalogu głównym aplikacji funkcji. Definiowanie pliku umożliwia wszystkich funkcji aplikacji i udostępniać te same pakiety pamięci podręcznej, które zapewnia najlepszą wydajność. Jeśli wystąpi konflikt wersji, można rozwiązać go przez dodanie `package.json` pliku w folderze określoną funkcję.  

## <a name="environment-variables"></a>Zmienne środowiskowe
Aby uzyskać wartość zmiennej środowiskowej lub wartość ustawienia aplikacji, należy użyć `process.env`, jak pokazano w `GetEnvironmentVariable` funkcji:

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
## <a name="considerations-for-javascript-functions"></a>Zagadnienia dotyczące funkcji JavaScript

Podczas pracy z funkcji JavaScript, należy pamiętać o zagadnień opisane w poniższych sekcjach.

### <a name="choose-single-vcpu-app-service-plans"></a>Wybierz jeden vCPU planów usługi aplikacji

Podczas tworzenia aplikacji funkcji, który używa plan usługi aplikacji, zaleca się wybrania planu jednym vCPU, a nie planu z wielu Vcpu. Obecnie funkcji działa funkcji JavaScript wydajniej na maszynach wirtualnych jednym vCPU i przy użyciu większe maszyny wirtualne nie tworzy ulepszenia obniżenie wydajności. Jeśli to konieczne, można ręcznie skalować w poziomie przez dodanie więcej wystąpień maszyny Wirtualnej w jednym vCPU, lub można włączyć automatycznego skalowania. Aby uzyskać więcej informacji, zobacz [skalowanie liczby wystąpień ręcznie lub automatycznie](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Obsługa języka typeScript i CoffeeScript
Ponieważ wsparcia bezpośredniego jeszcze nie istnieje maszynie lub CoffeeScript kompilowanie automatycznie za pomocą środowiska uruchomieniowego, wsparcie takie musi do obsługi poza środowisko uruchomieniowe, w czasie wdrażania. 

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure funkcje wyzwalaczy i powiązań](functions-triggers-bindings.md)

