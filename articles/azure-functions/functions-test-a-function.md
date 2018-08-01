---
title: Testowanie usługi Azure Functions | Dokumentacja firmy Microsoft
description: Testowanie funkcji platformy Azure przy użyciu programu cURL, Node.js i narzędzia Postman.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure funkcji, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bezserwerowa testowania
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05c88c8938580666ce99f7cae46dc69cda3c3776
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344702"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie do testowania kodu w usłudze Azure Functions

W tym temacie przedstawiono różne sposoby, aby przetestować funkcje, w tym przy użyciu następujących podejść:

+ Narzędzia oparte na protokole HTTP, takich jak nawet przeglądarki sieci web opartej na sieci web wyzwalaczy programu cURL i narzędzia Postman
+ Eksplorator usługi Azure Storage, aby przetestować wyzwalacze oparte na usłudze Azure Storage
+ Karta testu w portalu usługi Azure Functions
+ Funkcji wyzwalanej przez czasomierz
+ Testowanie aplikacji lub framework

Wszystkie te metody testowania, użyj funkcji wyzwalacza HTTP, który akceptuje dane wejściowe przez parametr ciągu zapytania lub treści żądania. Tę funkcję, należy utworzyć w pierwszej sekcji.

## <a name="create-a-function-for-testing"></a>Tworzenie funkcji do testowania
Dla większości po ukończeniu tego samouczka używamy nieco zmodyfikowaną wersję szablonu funkcji HttpTrigger JavaScript, która jest dostępna podczas tworzenia funkcji. Jeśli potrzebujesz, aby uzyskać pomoc przy tworzeniu funkcji, zapoznaj się z tym [samouczek](functions-create-first-azure-function.md). Wybierz **HttpTrigger - JavaScript** szablonu podczas tworzenia funkcji testowej w [Azure Portal].

Domyślny szablon funkcji jest zasadniczo funkcji "hello world", która ponownie funkcją nazwy na podstawie żądania treści lub zapytania parametr typu ciąg, `name=<your name>`.  Zaktualizujemy kod można również podać nazwę i adres jako zawartość JSON w treści żądania. Następnie funkcja zwraca te z powrotem do klienta, jeśli jest dostępna.   

Zaktualizuj następujący kod, który będziemy używać do testowania funkcji:

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>Testowanie funkcji za pomocą narzędzi
Poza portalem Azure istnieją różne narzędzia, które można użyć w celu wyzwolenia funkcji do testowania. Obejmują one testowania narzędzi (oparty na interfejsie użytkownika i polecenia wiersz), uzyskać dostęp do narzędzi usługi Azure Storage i nawet przeglądarki sieci web prostego protokołu HTTP.

### <a name="test-with-a-browser"></a>Testowanie za pomocą przeglądarki
Przeglądarka sieci web jest to prosty sposób funkcji wyzwalacza za pośrednictwem protokołu HTTP. Możesz użyć przeglądarki dla żądań GET, które nie wymagają ładunku treści i użyj tylko zapytania ciąg parametrów.

Aby przetestować tę funkcję wcześniej zdefiniowaną, skopiuj **adres Url funkcji** z poziomu portalu. Ma następującą postać:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Dołącz `name` parametr ciągu zapytania. Użyć rzeczywistej nazwy dla `<Enter a name here>` symbol zastępczy.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Wklej adres URL w przeglądarce, a użytkownik powinna pojawić się odpowiedź podobna do następującej.

![Zrzut ekranu Chrome kartach przeglądarki za pomocą odpowiedzi testu](./media/functions-test-a-function/browser-test.png)

W tym przykładzie jest przeglądarki Chrome, która otacza zwracanego ciągu w formacie XML. Inne przeglądarki pokazywać tylko wartości ciągu.

W portalu **dzienniki** okno dane wyjściowe podobne do następujących jest rejestrowane podczas wykonywania funkcji:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Testowanie za pomocą narzędzia Postman
Zalecanym narzędziem do testowania większość funkcji jest narzędzia Postman, którą można zintegrować z przeglądarki Chrome. Aby zainstalować narzędzia Postman, zobacz [uzyskać Postman](https://www.getpostman.com/). Postman zapewnia kontrolę nad wiele atrybutów więcej żądania HTTP.

> [!TIP]
> Korzystanie z protokołu HTTP, narzędzia, które jest najbardziej Ci testowanie. Poniżej przedstawiono kilka alternatyw narzędzia postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Rozwiązania paw](https://luckymarmot.com/paw)  
>
>

Aby przetestować funkcję z treści żądania w narzędziu Postman:

1. Uruchom narzędzie Postman z **aplikacje** przycisku w lewym górnym rogu okna przeglądarki Chrome.
2. Kopiuj swoje **adres Url funkcji**i wklej go do narzędzia Postman. Obejmuje on parametr ciągu zapytania kod dostępu.
3. Zmień metodę HTTP na **WPIS**.
4. Kliknij przycisk **treści** > **pierwotne**, i Dodaj treści żądania JSON podobne do następujących:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Kliknij przycisk **wysyłania**.

Na poniższej ilustracji przedstawiono testowanie echo prosty przykład funkcji w ramach tego samouczka.

![Zrzut ekranu narzędzia Postman interfejsu użytkownika](./media/functions-test-a-function/postman-test.png)

W portalu **dzienniki** okno dane wyjściowe podobne do następujących jest rejestrowane podczas wykonywania funkcji:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Testowanie za pomocą programu cURL z wiersza polecenia
Często podczas testowania oprogramowania, nie jest niezbędne do wyszukiwania dowolnego dalej niż wiersza polecenia, aby pomóc w debugowaniu aplikacji. To nie różni się za pomocą testowania funkcji. Należy pamiętać, że narzędzie cURL jest dostępna domyślnie na komputerach z systemem Linux. W Windows, musisz najpierw pobrać i zainstalować [narzędzie cURL](https://curl.haxx.se/).

Aby przetestować tę funkcję, która wcześniej zdefiniowaliśmy, skopiuj **adres URL funkcji** z poziomu portalu. Ma następującą postać:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

To jest adres URL do wyzwalania funkcji. Testować tę aplikację przy użyciu polecenia cURL w wierszu polecenia umożliwiają GET (`-G` lub `--get`) żądania względem funkcji:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

W tym przykładzie określonego wymaga parametru ciągu zapytania, które mogą być przekazywane jako dane (`-d`) w poleceniu programu cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Uruchom polecenie, a zobaczysz następujące dane wyjściowe funkcji w wierszu polecenia:

![Dane wyjściowe zrzut ekranu wiersza polecenia](./media/functions-test-a-function/curl-test.png)

W portalu **dzienniki** okno dane wyjściowe podobne do następujących jest rejestrowane podczas wykonywania funkcji:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Testowanie wyzwalacz obiektu blob przy użyciu Eksploratora usługi Storage
Można przetestować funkcji wyzwalacza obiektu blob przy użyciu [Eksploratora usługi Azure Storage](http://storageexplorer.com/).

1. W [Azure Portal] dla aplikacji funkcji, tworzenie funkcji wyzwalacza obiektów blob języka C#, F # lub JavaScript. Ustaw ścieżkę do monitorowania do nazwy kontenera obiektów blob. Na przykład:

        files
2. Kliknij przycisk **+** przycisk, aby wybrać lub utworzyć konto magazynu, którego chcesz użyć. Następnie kliknij pozycję **Utwórz**.
3. Utwórz plik tekstowy z następującym tekstem i zapisz go:

        A text file for blob trigger function testing.
4. Uruchom [Eksploratora usługi Azure Storage](http://storageexplorer.com/)i nawiąż połączenie z kontenera obiektów blob na koncie magazynu, które są monitorowane.
5. Kliknij przycisk **przekazywanie** można przekazać pliku tekstowego.

    ![Zrzut ekranu przedstawiający Eksploratora usługi Storage](./media/functions-test-a-function/azure-storage-explorer-test.png)

Domyślny obiekt blob wyzwalacza funkcji kod zgłasza przetwarzania obiektów blob w dziennikach:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Testowanie funkcji w obrębie funkcji
Usługa Azure Functions w portalu jest przeznaczona do umożliwiają testowanie protokołu HTTP i funkcji wyzwalanej czasomierzem. Można również utworzyć functions do wyzwalania innych funkcji, które testujesz.

### <a name="test-with-the-functions-portal-run-button"></a>Testowanie za pomocą przycisku Uruchom w portalu funkcji
Portal zawiera **Uruchom** przycisku, który można użyć w celu niektóre ograniczenia testowania. Możesz podać treść żądania, korzystając z przycisku, ale nie można podać parametry ciągu zapytania lub zaktualizować nagłówki żądania.

Testowanie funkcji wyzwalacza HTTP, utworzony wcześniej przez dodanie podobny do następującego ciągu JSON **treść żądania** pola. Następnie kliknij przycisk **Uruchom** przycisku.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

W portalu **dzienniki** okno dane wyjściowe podobne do następujących jest rejestrowane podczas wykonywania funkcji:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Testowanie za pomocą wyzwalacza czasomierza
Niektóre funkcje nie odpowiednio przetestować za pomocą narzędzi wymienionych wcześniej. Na przykład, należy wziąć pod uwagę kolejki funkcji wyzwalacza, który jest uruchamiany, gdy komunikat jest porzucany do [Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md). Zawsze można napisać kod, aby porzucić komunikatu do kolejki, a w dalszej części tego artykułu znajduje się na przykład w projekcie konsoli. Istnieje jednak innym podejściem, których można użyć, sprawdzająca funkcje bezpośrednio.  

Można użyć wyzwalacza czasomierza skonfigurowany z kolejką powiązania danych wyjściowych. Ten kod wyzwalacza czasomierza może następnie zapisywać wiadomości testowe do kolejki. W tej sekcji przedstawiono przykład.

Aby uzyskać więcej szczegółowych informacji na temat korzystania z powiązań z usługą Azure Functions, zobacz [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Tworzenie wyzwalacza kolejki do testowania
Aby zademonstrować takie podejście, najpierw utworzymy kolejki funkcji wyzwalacza, który chcemy sprawdzić, czy kolejka o nazwie `queue-newusers`. Ta funkcja przetwarza nazwa i informacje o adresie upuszczonego do kolejki magazynu dla nowego użytkownika.

> [!NOTE]
> Jeśli użyto nazwy innej kolejki, upewnij się, że nazwa używana jest zgodny z [nazewnictwo kolejek i metadanych](https://msdn.microsoft.com/library/dd179349.aspx) reguły. W przeciwnym razie wystąpi błąd.
>
>

1. W [Azure Portal] dla aplikacji funkcji kliknij **nową funkcję** > **QueueTrigger — C#**.
2. Wprowadź nazwę kolejki, które mają być monitorowani przez funkcję kolejki:

        queue-newusers
3. Kliknij przycisk **+** przycisk, aby wybrać lub utworzyć konto magazynu, którego chcesz użyć. Następnie kliknij pozycję **Utwórz**.
4. Zamykaj tego okna przeglądarki w portalu, aby można było monitorować wpisów dziennika dla kodu szablonu domyślnego kolejki funkcji.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Tworzenie wyzwalacza czasomierza, aby porzucić komunikatu w kolejce
1. Otwórz [Azure Portal] w nowym oknie przeglądarki i przejdź do aplikacji funkcji.
2. Kliknij przycisk **nową funkcję** > **TimerTrigger — C#**. Wprowadź wyrażenie narzędzia cron, aby ustawić częstotliwość kodu czasomierz sprawdza funkcję kolejki. Następnie kliknij pozycję **Utwórz**. Jeśli chcesz, aby test, aby uruchomić co 30 sekund, należy używać następującej [wyrażenie CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Kliknij przycisk **integracja** kartę nowego wyzwalacza czasomierza.
4. W obszarze **dane wyjściowe**, kliknij przycisk **+ nowe dane wyjściowe**. Następnie kliknij przycisk **kolejki** i **wybierz**.
5. Uwaga nazwa używana dla **obiekt komunikatu kolejki**. Możesz użyć tego w kodzie funkcji czasomierza.

        myQueue
6. Wprowadź nazwę kolejki, w którym komunikat jest wysyłany:

        queue-newusers
7. Kliknij przycisk **+** przycisk, aby wybrać konto magazynu używane wcześniej z wyzwalaczem kolejki. Następnie kliknij przycisk **Save** (Zapisz).
8. Kliknij przycisk **programowanie** karcie wyzwalacza czasomierza.
9. Poniższy kod służy do funkcja języka C# czasomierza, tak długo, jak używać tej samej kolejki komunikatów nazwę obiektu przedstawionej wcześniej. Następnie kliknij przycisk **Save** (Zapisz).

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

W tym momencie funkcja czasomierza języka C# wykonuje co 30 sekund, jeśli użyto przykładu wyrażenia cron. Dzienniki funkcji czasomierza zgłosić każdego wykonania:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

W oknie przeglądarki dla funkcji kolejki można wyświetlić każdego przetwarzanego komunikatu:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Testowanie funkcji przy użyciu kodu
Może być konieczne Utwórz zewnętrznych aplikacji lub framework, aby przetestować funkcje.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Testowanie funkcji wyzwalacza HTTP z kodem: Node.js
Aby wykonać żądanie HTTP, aby przetestować funkcję, można użyć aplikacji Node.js.
Upewnij się ustawić:

* `host` Za pomocą opcji żądania do hosta aplikacji funkcji.
* Twoja nazwa funkcji w `path`.
* Kod dostępu (`<your code>`) w `path`.

Przykład kodu:

```javascript
var http = require("http");

var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";

var nameBodyJSON = {
    name : "Wes testing with Node.JS code",
    address : "Dallas, T.X. 75201"
};

var bodyString = JSON.stringify(nameBodyJSON);

var options = {
  host: "functions841def78.azurewebsites.net",
  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
  method: "POST",
  headers : {
      "Content-Type":"application/json",
      "Content-Length": Buffer.byteLength(bodyString)
    }    
};

callback = function(response) {
  var str = ""
  response.on("data", function (chunk) {
    str += chunk;
  });

  response.on("end", function () {
    console.log(str);
  });
}

var req = http.request(options, callback);
console.log("*** Sending name and address in body ***");
console.log(bodyString);
req.end(bodyString);
```


Dane wyjściowe:

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

W portalu **dzienniki** okno dane wyjściowe podobne do następujących jest rejestrowane podczas wykonywania funkcji:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Testowanie funkcji wyzwalacza kolejki z kodem: C# #
Wspomniano wcześniej, możesz przetestować wyzwalacz kolejki przy użyciu kodu, aby porzucić komunikatu w kolejce. Poniższy przykład kodu jest oparty na kodzie języka C# znajdujące się w [wprowadzenie do usługi Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md) samouczka. Kod dla innych języków jest również dostępna z tego linku.

Aby przetestować kod w aplikacji konsoli, musisz mieć:

* [Konfigurowanie parametrów połączenia magazynu w pliku app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Przekaż `name` i `address` jako parametry do aplikacji. Na przykład `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Ten kod przyjmuje nazwę i adres dla nowego użytkownika jako argumenty wiersza polecenia podczas wykonywania.)

Kod przykładowy języka C#:

```cs
static void Main(string[] args)
{
    string name = null;
    string address = null;
    string queueName = "queue-newusers";
    string JSON = null;

    if (args.Length > 0)
    {
        name = args[0];
    }
    if (args.Length > 1)
    {
        address = args[1];
    }

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference(queueName);

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    if (name != null)
    {
        if (address != null)
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

W oknie przeglądarki dla funkcji kolejki można wyświetlić każdego przetwarzanego komunikatu:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Azure Portal]: https://portal.azure.com
