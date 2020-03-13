---
title: Korzystanie z usługi sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Po wdrożeniu usługi Machine Learning z poziomu Azure Machine Learning Studio (klasycznego) usługa sieci Web RESTFul może być używana jako usługa żądania-odpowiedzi w czasie rzeczywistym lub jako usługa wykonywania wsadowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: de395f7167f0ab7d7c6429c14d5efce46831b576
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218234"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Jak korzystać z usługi sieci Web Azure Machine Learning Studio (klasycznej)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Po wdrożeniu modelu predykcyjnego Azure Machine Learning Studio (klasyczny) jako usługi sieci Web można użyć interfejsu API REST do wysyłania danych IT i uzyskiwania prognoz. Możesz wysłać dane w czasie rzeczywistym lub w trybie wsadowym.

Więcej informacji na temat sposobu tworzenia i wdrażania usługi sieci Web Machine Learning przy użyciu Machine Learning Studio (klasyczny) można znaleźć tutaj:

* Aby zapoznać się z samouczkiem dotyczącym tworzenia eksperymentu w Machine Learning Studio (klasyczny), zobacz [Tworzenie pierwszego eksperymentu](create-experiment.md).
* Aby uzyskać szczegółowe informacje na temat wdrażania usługi sieci Web, zobacz [wdrażanie usługi sieci web Machine Learning](deploy-a-machine-learning-web-service.md).
* Aby uzyskać więcej informacji na temat ogólnie Machine Learning, odwiedź [centrum dokumentacji Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Omówienie
Za pomocą usługi Azure Machine Learning w sieci Web aplikacji zewnętrznej komunikowanie się z modelem oceniania przepływu pracy usługi Machine Learning w czasie rzeczywistym. Wywołanie usługi Machine Learning w sieci Web zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi Machine Learning w sieci Web polega na przekazaniu klucza interfejsu API, który jest tworzony podczas wdrożenia przewidywanie. Usługa Machine Learning w sieci Web jest oparta na interfejsu REST — popularnej architektury w projektach programistycznych w sieci web.

Azure Machine Learning Studio (klasyczny) ma dwa typy usług:

* Usługa Request-Response (RR) — małe opóźnienia i wysoce skalowalna usługa udostępniająca interfejs do modeli bezstanowych utworzonych i wdrożonych z Machine Learning Studio (klasyczny).
* Usługa wykonywania wsadowego Service (BES) — asynchroniczna Usługa przeznaczona do oceniania partii rekordów danych.

Aby uzyskać więcej informacji na temat Machine Learning usług sieci Web, zobacz [wdrażanie usługi sieci web Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Pobieranie klucza autoryzacji
Podczas wdrażania eksperymentu klucze interfejsu API są generowane przez usługę sieci Web. Klucze można pobrać z kilku lokalizacjach.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Z poziomu portalu usług sieci Web Microsoft Azure Machine Learning
Zaloguj się do portalu [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net) .

Aby pobrać klucz interfejsu API usługi nowe Machine Learning w sieci Web:

1. W portalu usług sieci Web Azure Machine Learning kliknij pozycję **usługi sieci Web** w górnym menu.
2. Kliknij usługę sieci Web, dla którego chcesz pobrać klucza.
3. W górnym **menu kliknij pozycję**Użyj.
4. Skopiuj i Zapisz **klucz podstawowy**.

Aby pobrać klucz interfejsu API usługi sieci Web Machine Learning:

1. W portalu usług sieci Web Azure Machine Learning kliknij pozycję **klasyczne usługi sieci Web** w górnym menu.
2. Kliknij usługę sieci Web, z którym pracujesz.
3. Kliknij punkt końcowy, dla którego chcesz pobrać klucza.
4. W górnym **menu kliknij pozycję**Użyj.
5. Skopiuj i Zapisz **klucz podstawowy**.

### <a name="classic-web-service"></a>Klasyczne usługi sieci Web
 Możesz również pobrać klucz dla klasycznej usługi sieci Web z Machine Learning Studio (klasyczny).

#### <a name="machine-learning-studio-classic"></a>Usługa Machine Learning Studio (klasyczna)
1. W Machine Learning Studio (klasyczny) kliknij pozycję **usługi sieci Web** po lewej stronie.
2. Kliknij opcję usługi sieci Web. **Klucz interfejsu API** znajduje się na karcie **pulpit nawigacyjny** .

## <a id="connect"></a>Nawiązywanie połączenia z usługą sieci Web Machine Learning
Możesz połączyć się z usługą Machine Learning w sieci Web, za pomocą dowolnego języka programowania, który obsługuje żądania HTTP i odpowiedzi. Możesz wyświetlić przykłady w C#, Python i R ze strony pomocy usługi Machine Learning w sieci Web.

**Pomoc interfejsu API Machine Learning** Pomoc interfejsu API Machine Learning jest tworzona podczas wdrażania usługi sieci Web. Zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).
Pomoc interfejsu API usługi Machine Learning zawiera szczegóły dotyczące prognoz usługi sieci Web.

1. Kliknij usługę sieci Web, z którym pracujesz.
2. Kliknij punkt końcowy, dla którego chcesz wyświetlić na stronie pomocy interfejsu API.
3. W górnym **menu kliknij pozycję**Użyj.
4. Kliknij pozycję **Pomoc interfejsu API** w obszarze punkty końcowe odpowiedzi na żądanie lub wykonanie wsadowe.

**Aby wyświetlić Pomoc interfejsu API Machine Learning dla nowej usługi sieci Web**

W [portalu Azure Machine Learning Web Services](https://services.azureml.net/):

1. Kliknij pozycję **usługi sieci Web** w górnym menu.
2. Kliknij usługę sieci Web, dla którego chcesz pobrać klucza.

Kliknij pozycję **Użyj usługi sieci Web** , aby pobrać identyfikatory URI dla usług żądanie-odpowiedź i wykonywanie wsadowe oraz C#przykładowego kodu w, R i Python.

Kliknij pozycję **interfejs API struktury Swagger** , aby uzyskać dokumentację opartą na strukturze interfejsów API wywoływaną z dostarczonych identyfikatorów URI.

### <a name="c-sample"></a>Przykładowy języka C#
Aby nawiązać połączenie z usługą sieci Web Machine Learning, użyj **HttpClient** Pass ScoreData. ScoreData zawiera FeatureVector, n wymiarowy wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

Aby nawiązać połączenie z usługą sieci Web Machine Learning, należy zainstalować pakiet NuGet **Microsoft. ASPNET. WebApi. Client** .

**Zainstaluj pakiet NuGet Microsoft. AspNet. WebApi. Client w programie Visual Studio**

1. Publikowanie zestawu pobierania danych z UCI: 2 treści dla dorosłych klasy dataset usługi sieci Web.
2. Kliknij pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.
3. Wybierz polecenie **install-package Microsoft. ASPNET. WebApi. Client**.

**Aby uruchomić przykładowy kod**

1. Publikowanie "przykład 1: Pobierz zestaw danych z UCI: zawartość dla dorosłych 2 klasy w zestawie danych" eksperyment i jest częścią usługi Machine Learning pobieranie próbek.
2. Przypisz apiKey przy użyciu klucza z usługi sieci Web. Zobacz **Pobieranie klucza autoryzacji** powyżej.
3. Przypisz serviceUri o identyfikatorze URI żądania.

**Oto, jak będzie wyglądać pełne żądanie.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Przykład środowiska Python
Aby nawiązać połączenie z usługą sieci Web Machine Learning, użyj biblioteki **urllib2** dla języka Python 2. x i **urllib. Request** Library dla języka Python 3. x. Zostaną spełnione ScoreData, zawierającą FeatureVector, n wymiarowy wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

**Aby uruchomić przykładowy kod**

1. Wdrażanie "przykład 1: Pobierz zestaw danych z UCI: zawartość dla dorosłych 2 klasy w zestawie danych" eksperyment i jest częścią usługi Machine Learning pobieranie próbek.
2. Przypisz apiKey przy użyciu klucza z usługi sieci Web. Zobacz sekcję **Pobieranie klucza autoryzacji na** początku tego artykułu.
3. Przypisz serviceUri o identyfikatorze URI żądania.

**Oto, jak będzie wyglądać pełne żądanie.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Przykładowy języka R

Aby nawiązać połączenie z usługą sieci Web Machine Learning, użyj bibliotek **RCurl** i **rjson** , aby wykonać żądanie i przetworzyć zwróconą odpowiedź JSON. Zostaną spełnione ScoreData, zawierającą FeatureVector, n wymiarowy wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

**Oto, jak będzie wyglądać pełne żądanie.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>Przykład JavaScript

Aby nawiązać połączenie z usługą sieci Web Machine Learning, Użyj pakietu **Request** npm w projekcie. Użyjesz również obiektu `JSON`, aby sformatować dane wejściowe i przeanalizować wynik. Zainstaluj program przy użyciu `npm install request --save`lub Dodaj `"request": "*"` do pliku Package. JSON w obszarze `dependencies` i uruchom `npm install`.

**Oto, jak będzie wyglądać pełne żądanie.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```
