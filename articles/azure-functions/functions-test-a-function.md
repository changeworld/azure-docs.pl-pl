---
title: Testowanie usługi Azure Functions | Dokumentacja firmy Microsoft
description: Testowanie funkcji platformy Azure przy użyciu Postman, cURL i Node.js.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: ''
tags: ''
keywords: Azure funkcji, funkcji, przetwarzania zdarzeń, elementów webhook, dynamiczne obliczeń, niekorzystającą architektury, testowanie
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41796a8cdde0756e5157ba276463a56b07679d04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23838954"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie do testowania kodu w usługi Azure Functions

W tym temacie przedstawiono różne sposoby, aby przetestować funkcje, w tym o korzystaniu z następujących podejść:

+ Narzędzia oparte na protokole HTTP, takie jak cURL, Postman, a nawet przeglądarki sieci web opartych na sieci web wyzwalacze
+ Azure Eksploratora usługi Storage, aby przetestować wyzwalacze oparte na usłudze Azure Storage
+ Karta testu w portalu Azure Functions
+ Funkcja wyzwalany przez czasomierz
+ Testowanie aplikacji lub struktury

Tych metod testowych używana jest funkcja wyzwalacza HTTP, który akceptuje dane wejściowe za pomocą parametru ciągu zapytania lub treści żądania. W pierwszej sekcji tworzenia tej funkcji.

## <a name="create-a-function-for-testing"></a>Utwórz funkcję do testowania
Większość tego samouczka używamy nieco zmodyfikowaną wersję szablonu funkcji HttpTrigger JavaScript, która jest dostępna podczas tworzenia funkcji. Jeśli potrzebujesz, aby uzyskać pomoc przy tworzeniu funkcji, przejrzyj to [samouczek](functions-create-first-azure-function.md). Wybierz **HttpTrigger - JavaScript** szablonu podczas tworzenia funkcji testu w [portalu Azure].

Domyślny szablon funkcji jest zasadniczo funkcji "hello world", która zwraca ponownie nazwę z żądania treści lub zapytania parametru ciągu, `name=<your name>`.  Będziemy informować kod można również określić nazwę i adres jako zawartość JSON w treści żądania. Następnie funkcja zwraca te wstecz do klienta, jeśli jest dostępna.   

Aktualizacja funkcji z następującym kodem, który zostanie wykorzystany do testowania:

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

## <a name="test-a-function-with-tools"></a>Testowanie funkcji z narzędzia
Poza portalem Azure istnieją różne narzędzia, które służy do wyzwalania funkcji do testowania. Obejmują one testowanie narzędzia (zarówno interfejsu użytkownika i polecenia wiersza), narzędzia dostępu do usługi Azure Storage i nawet przeglądarki sieci web prostego protokołu HTTP.

### <a name="test-with-a-browser"></a>Testowanie przy użyciu przeglądarki
Przeglądarki sieci web to prosty sposób funkcje wyzwalaczy za pośrednictwem protokołu HTTP. Można użyć przeglądarki dla żądania GET, które nie wymagają ładunku treści i użyj tylko zapytania parametry.

Aby przetestować funkcję zdefiniowanego wcześniej, skopiuj **adres Url funkcji** z portalu. Ma następujący format:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Dołącz `name` parametr ciągu zapytania. Użyć rzeczywistej nazwy dla `<Enter a name here>` symbolu zastępczego.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Wklej adres URL do przeglądarki, a następnie należy pobrać odpowiedzi podobny do następującego.

![Zrzut ekranu Chrome karty przeglądarki z odpowiedzią testu](./media/functions-test-a-function/browser-test.png)

W tym przykładzie jest przeglądarka Chrome, który opakowuje zwracany ciąg w formacie XML. Inne przeglądarki pokazywać tylko wartość ciągu.

W portalu **dzienniki** okno dane wyjściowe podobne do następującego jest rejestrowane podczas wykonywania funkcji:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Testowanie przy Postman
Zalecanym narzędziem do testowania większość funkcji jest Postman, która integruje się z przeglądarki Chrome. Aby zainstalować Postman, zobacz [uzyskać Postman](https://www.getpostman.com/). Postman zapewnia kontrolę nad dużo więcej atrybutów żądania HTTP.

> [!TIP]
> Użyj protokołu HTTP, testowanie narzędzie, które jest najbardziej Ci. Poniżej przedstawiono niektóre alternatyw Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Łapy](https://luckymarmot.com/paw)  
>
>

Testowanie funkcji z treści żądania w Postman:

1. Uruchom Postman z **aplikacji** przycisk w lewym górnym rogu okna przeglądarki Chrome.
2. Kopiowanie z **adres Url funkcji**i wklej go do Postman. Obejmuje on parametr ciągu zapytania kod dostępu.
3. Zmień metodę HTTP **POST**.
4. Kliknij przycisk **treści** > **raw**, i Dodaj treści żądania JSON podobny do następującego:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Kliknij przycisk **wysyłania**.

Na poniższej ilustracji przedstawiono testowanie echo prosty przykład funkcji, w tym samouczku.

![Zrzut ekranu Postman interfejsu użytkownika](./media/functions-test-a-function/postman-test.png)

W portalu **dzienniki** okno dane wyjściowe podobne do następującego jest rejestrowane podczas wykonywania funkcji:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Testowanie przy cURL z wiersza polecenia
Często podczas testowania oprogramowania, nie jest konieczne wyglądały żadnego dalej niż wiersza polecenia do debugowania aplikacji. Nie różni się z testowaniem funkcje się to. Należy pamiętać, że narzędzie cURL jest dostępny domyślnie w systemach opartych na systemie Linux. W systemie Windows, należy najpierw pobrać i zainstalować [narzędzie cURL](https://curl.haxx.se/).

Aby przetestować funkcję zdefiniowanego wcześniej, skopiuj **adres URL funkcji** z portalu. Ma następujący format:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

To jest adres URL do wyzwalania funkcji. To sprawdzić za pomocą polecenia cURL w wierszu polecenia, aby GET (`-G` lub `--get`) żądania dotyczącego funkcji:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

W tym przykładzie określonego wymaga parametru ciągu zapytania, które mogą być przekazywane jako danych (`-d`) w polecenia cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Uruchom polecenie i wyświetlone następujące dane wyjściowe funkcji w wierszu polecenia:

![Dane wyjściowe zrzut ekranu wiersza polecenia](./media/functions-test-a-function/curl-test.png)

W portalu **dzienniki** okno dane wyjściowe podobne do następującego jest rejestrowane podczas wykonywania funkcji:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Test wyzwalania obiektów blob przy użyciu Eksploratora usługi Storage
Funkcja wyzwalacza obiektu blob można testować przy użyciu [Eksploratora usługi Storage Azure](http://storageexplorer.com/).

1. W [portalu Azure] dla funkcji aplikacji, Utwórz funkcję wyzwalacza języka C#, F # lub JavaScript obiektu blob. Ustaw ścieżkę do monitorowania do nazwy kontenerze obiektu blob. Na przykład:

        files
2. Kliknij przycisk  **+**  przycisk, aby wybrać lub utworzyć konto magazynu, którego chcesz użyć. Następnie kliknij pozycję **Utwórz**.
3. Utwórz plik tekstowy z następującym tekstem i zapisz go:

        A text file for blob trigger function testing.
4. Uruchom [Eksploratora usługi Storage Azure](http://storageexplorer.com/)i podłącz do kontenera obiektów blob na koncie magazynu monitorowane.
5. Kliknij przycisk **przekazać** można przekazać pliku tekstowego.

    ![Zrzut ekranu przedstawiający Eksploratora usługi Storage](./media/functions-test-a-function/azure-storage-explorer-test.png)

Domyślny kod funkcja wyzwalacza obiektu blob raportów przetwarzanie obiektów blob w dziennikach:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Testowanie funkcji w funkcjach
Azure Functions w portalu jest umożliwiają testowanie HTTP i funkcje czasomierza wyzwolone. Można również utworzyć funkcje do wyzwolenia inne funkcje, które są testowania.

### <a name="test-with-the-functions-portal-run-button"></a>Test z portalu przycisk Uruchom funkcji
Portal zawiera **Uruchom** przycisk, którego można używać w celu wykonywania niektórych ograniczone testowania. Musisz dostarczyć treść żądania przy użyciu przycisku, ale nie można podać parametrów ciągu zapytania lub zaktualizować nagłówki żądania.

Testowanie funkcji wyzwalacza HTTP utworzony wcześniej przez dodanie ciągu JSON, podobny do następującego **treść żądania** pola. Następnie kliknij przycisk **Uruchom** przycisku.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

W portalu **dzienniki** okno dane wyjściowe podobne do następującego jest rejestrowane podczas wykonywania funkcji:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Test z wyzwalacza bazującego na czasomierzu
Niektóre funkcje nie można przetestować odpowiednio przy użyciu narzędzi wspomniano powyżej. Rozważmy na przykład funkcja wyzwalacza kolejki, która jest uruchamiana, gdy komunikat jest upuścić na [magazynu kolejek Azure](../storage/queues/storage-dotnet-how-to-use-queues.md). Zawsze można napisać kod, aby porzucić komunikatu do kolejki, a na przykład w projekcie konsoli znajduje się w dalszej części tego artykułu. Istnieje jednak innej metody można użyć bezpośrednio sprawdzający funkcji.  

Można użyć wyzwalacza bazującego na czasomierzu skonfigurowane z kolejką powiązania wyjściowego. Czy kod wyzwalacza czasomierza następnie zapisywać wiadomości testowe do kolejki. W tej sekcji przedstawiono przykład.

Aby uzyskać więcej szczegółowych informacji o używaniu powiązania z usługi Azure Functions, zobacz [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Tworzenie kolejki wyzwalacza do testowania
Aby zademonstrować takie podejście, najpierw utworzymy funkcja wyzwalacza kolejki chcemy, aby przetestować kolejki o nazwie `queue-newusers`. Ta funkcja przetwarza nazwy i adresu informacji upuścić na kolejki magazynu dla nowego użytkownika.

> [!NOTE]
> Jeśli używasz innej kolejki, upewnij się, że nazwa używana odpowiada [nazewnictwo kolejek i metadanych](https://msdn.microsoft.com/library/dd179349.aspx) reguły. W przeciwnym razie wystąpi błąd.
>
>

1. W [portalu Azure] dla funkcji aplikacji, kliknij przycisk **nową funkcję** > **QueueTrigger - C#**.
2. Wprowadź nazwę kolejki monitorowanych przez funkcję kolejki:

        queue-newusers
3. Kliknij przycisk  **+**  przycisk, aby wybrać lub utworzyć konto magazynu, którego chcesz użyć. Następnie kliknij pozycję **Utwórz**.
4. Pozostaw to okno przeglądarki w portalu otwarty, więc można monitorować wpisów dziennika dla kodu szablonu domyślnego kolejki funkcji.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Tworzenie wyzwalacza bazującego na czasomierzu można usunąć wiadomości w kolejce
1. Otwórz [portalu Azure] w nowym oknie przeglądarki, a następnie przejdź do aplikacji funkcji.
2. Kliknij przycisk **nową funkcję** > **TimerTrigger - C#**. Wprowadź wyrażenie cron, aby ustawić częstotliwość kodu czasomierza testów funkcji kolejki. Następnie kliknij pozycję **Utwórz**. Jeśli chcesz testów do uruchomienia co 30 sekund, można użyć następujących [wyrażenie CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Kliknij przycisk **integracji** kartę nowego wyzwalacza czasomierza.
4. W obszarze **dane wyjściowe**, kliknij przycisk **+ nowe dane wyjściowe**. Następnie kliknij przycisk **kolejki** i **wybierz**.
5. Uwaga nazwa używana dla **obiekt kolejki wiadomości**. Możesz użyć tego kodu funkcji czasomierza.

        myQueue
6. Wprowadź nazwę kolejki wysyłania wiadomości:

        queue-newusers
7. Kliknij przycisk  **+**  przycisk, aby wybrać poprzednio używany z wyzwalaczem kolejki konta magazynu. Następnie kliknij przycisk **Save** (Zapisz).
8. Kliknij przycisk **opracowanie** kartę wyzwalacza czasomierza.
9. Tak długo, jak użyć tej samej kolejki wiadomości nazwę obiektu przedstawiona wcześniej, można użyć poniższego kodu dla funkcji czasomierza C#. Następnie kliknij przycisk **Save** (Zapisz).

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

W tym momencie funkcja czasomierza C# wykonuje co 30 sekund, jeśli użyto wyrażenia cron przykład. W dziennikach funkcji czasomierza raport Każde wykonanie:

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

## <a name="test-a-function-with-code"></a>Testowanie funkcji z kodu
Może być konieczne do utworzenia zewnętrznych aplikacji lub framework, aby przetestować funkcji.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Testowanie funkcji wyzwalacza HTTP o kodzie: Node.js
Aplikacja Node.js można użyć do wykonania żądania HTTP, aby przetestować funkcję.
Upewnij się ustawić:

* `host` w opcjach żądanie do hosta funkcji aplikacji.
* Nazwę funkcji w `path`.
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

W portalu **dzienniki** okno dane wyjściowe podobne do następującego jest rejestrowane podczas wykonywania funkcji:

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
Wspomniano wcześniej przetestowanie wyzwalacz kolejki przy użyciu kodu można usunąć wiadomości z kolejki. Poniższy przykładowy kod jest oparty na kodzie C# przedstawionych w [Rozpoczynanie pracy z magazynem kolejek Azure](../storage/queues/storage-dotnet-how-to-use-queues.md) samouczka. Kod dla innych języków jest również dostępna z tego łącza.

Aby przetestować ten kod w aplikacji konsoli, należy:

* [Konfigurowanie parametrów połączenia magazynu w pliku app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Przekaż `name` i `address` jako parametry do aplikacji. Na przykład `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Ten kod akceptuje nazwę i adres dla nowego użytkownika jako argumenty wiersza polecenia w czasie wykonywania.)

Przykład C# kodu:

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

[portalu Azure]: https://portal.azure.com
