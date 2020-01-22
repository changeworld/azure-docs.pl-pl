---
title: Korzystanie z usługi sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Po wdrożeniu usługi Machine Learning z poziomu Azure Machine Learning Studio (klasycznego) usługa sieci Web RESTFul może być używana jako usługa żądania-odpowiedzi w czasie rzeczywistym lub jako usługa wykonywania wsadowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 7626714812b44119099344b52fe7506989555a57
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314304"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Jak korzystać z usługi sieci Web Azure Machine Learning Studio (klasycznej)

Po wdrożeniu modelu predykcyjnego Azure Machine Learning Studio (klasyczny) jako usługi sieci Web można użyć interfejsu API REST do wysyłania danych IT i uzyskiwania prognoz. Możesz wysłać dane w czasie rzeczywistym lub w trybie wsadowym.

Więcej informacji na temat sposobu tworzenia i wdrażania usługi sieci Web Machine Learning przy użyciu Machine Learning Studio (klasyczny) można znaleźć tutaj:

* Aby zapoznać się z samouczkiem dotyczącym tworzenia eksperymentu w Machine Learning Studio (klasyczny), zobacz [Tworzenie pierwszego eksperymentu](create-experiment.md).
* Aby uzyskać więcej informacji na temat wdrażania usługi sieci Web, zobacz [wdrażanie usługi Machine Learning w sieci Web](deploy-a-machine-learning-web-service.md).
* Aby uzyskać więcej informacji na temat usługi Machine Learning ogólnie rzecz biorąc, odwiedź stronę [Centrum dokumentacji usługi Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Przegląd
Za pomocą usługi Azure Machine Learning w sieci Web aplikacji zewnętrznej komunikowanie się z modelem oceniania przepływu pracy usługi Machine Learning w czasie rzeczywistym. Wywołanie usługi Machine Learning w sieci Web zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi Machine Learning w sieci Web polega na przekazaniu klucza interfejsu API, który jest tworzony podczas wdrożenia przewidywanie. Usługa Machine Learning w sieci Web jest oparta na interfejsu REST — popularnej architektury w projektach programistycznych w sieci web.

Azure Machine Learning Studio (klasyczny) ma dwa typy usług:

* Usługa Request-Response (RR) — małe opóźnienia i wysoce skalowalna usługa udostępniająca interfejs do modeli bezstanowych utworzonych i wdrożonych z Machine Learning Studio (klasyczny).
* Usługa wykonywania wsadowego Service (BES) — asynchroniczna Usługa przeznaczona do oceniania partii rekordów danych.

Aby uzyskać więcej informacji na temat usługi Machine Learning w sieci Web, zobacz [wdrażanie usługi Machine Learning w sieci Web](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Pobieranie klucza autoryzacji
Podczas wdrażania eksperymentu klucze interfejsu API są generowane przez usługę sieci Web. Klucze można pobrać z kilku lokalizacjach.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Z poziomu portalu usług sieci Web Microsoft Azure Machine Learning
Zaloguj się do [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net) portalu.

Aby pobrać klucz interfejsu API usługi nowe Machine Learning w sieci Web:

1. W portalu usług sieci Web Azure Machine Learning, kliknij przycisk **usług sieci Web** menu u góry.
2. Kliknij usługę sieci Web, dla którego chcesz pobrać klucza.
3. W górnym menu, kliknij przycisk **zużywania**.
4. Skopiuj i Zapisz **klucz podstawowy**.

Aby pobrać klucz interfejsu API usługi sieci Web Machine Learning:

1. W portalu usług sieci Web Azure Machine Learning, kliknij przycisk **klasycznych usług sieci Web** menu u góry.
2. Kliknij usługę sieci Web, z którym pracujesz.
3. Kliknij punkt końcowy, dla którego chcesz pobrać klucza.
4. W górnym menu, kliknij przycisk **zużywania**.
5. Skopiuj i Zapisz **klucz podstawowy**.

### <a name="classic-web-service"></a>Klasyczne usługi sieci Web
 Możesz również pobrać klucz dla klasycznej usługi sieci Web z Machine Learning Studio (klasyczny).

#### <a name="machine-learning-studio-classic"></a>Usługa Machine Learning Studio (klasyczna)
1. W Machine Learning Studio (klasyczny) kliknij pozycję **usługi sieci Web** po lewej stronie.
2. Kliknij opcję usługi sieci Web. **Klucz interfejsu API** znajduje się na **pulpit NAWIGACYJNY** kartę.

## <a id="connect"></a>Łączenie z usługą Machine Learning w sieci Web
Możesz połączyć się z usługą Machine Learning w sieci Web, za pomocą dowolnego języka programowania, który obsługuje żądania HTTP i odpowiedzi. Możesz wyświetlić przykłady w C#, Python i R ze strony pomocy usługi Machine Learning w sieci Web.

**Usługi Machine Learning API Pomocy** pomocy interfejsu API usługi Machine Learning jest tworzona, gdy wdrażasz usługę sieci Web. Zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).
Pomoc interfejsu API usługi Machine Learning zawiera szczegóły dotyczące prognoz usługi sieci Web.

1. Kliknij usługę sieci Web, z którym pracujesz.
2. Kliknij punkt końcowy, dla którego chcesz wyświetlić na stronie pomocy interfejsu API.
3. W górnym menu, kliknij przycisk **zużywania**.
4. Kliknij przycisk **strona pomocy interfejsu API** w odpowiedzi na żądanie lub wykonywanie wsadowe punktów końcowych.

**Widok interfejsu API usługi Machine Learning Pomoc dla usługi sieci Web nowy**

W [usługi Azure Machine Learning Web Services Portal](https://services.azureml.net/):

1. Kliknij przycisk **usług sieci WEB** w górnym menu.
2. Kliknij usługę sieci Web, dla którego chcesz pobrać klucza.

Kliknij pozycję **Użyj usługi sieci Web** , aby pobrać identyfikatory URI dla usług żądanie-odpowiedź i wykonywanie wsadowe oraz C#przykładowego kodu w, R i Python.

Kliknij przycisk **interfejsu API struktury Swagger** pobrania struktury Swagger dla interfejsów API o nazwie z podane identyfikatory URI na podstawie dokumentacji.

### <a name="c-sample"></a>Przykładowy języka C#
Aby połączyć się z usługą Machine Learning w sieci Web, użyj **HttpClient** przekazywanie ScoreData. ScoreData zawiera FeatureVector, n wymiarowy wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

Aby nawiązać połączenie z usługą Machine Learning w sieci Web, **Microsoft.AspNet.WebApi.Client** musi być zainstalowany pakiet NuGet.

**Instalowania Menedżera Microsoft.AspNet.WebApi.Client NuGet w programie Visual Studio**

1. Publikowanie zestawu pobierania danych z UCI: 2 treści dla dorosłych klasy dataset usługi sieci Web.
2. Kliknij pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.
3. Wybierz **Microsoft.AspNet.WebApi.Client Install-Package**.

**Aby uruchomić przykładowy kod**

1. Publikowanie "przykład 1: Pobierz zestaw danych z UCI: zawartość dla dorosłych 2 klasy w zestawie danych" eksperyment i jest częścią usługi Machine Learning pobieranie próbek.
2. Przypisz apiKey przy użyciu klucza z usługi sieci Web. Zobacz **Pobieranie klucza autoryzacji** powyżej.
3. Przypisz serviceUri o identyfikatorze URI żądania.

**Oto, jak będzie wyglądać kompletne żądanie.**
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
Aby połączyć się z usługą Machine Learning w sieci Web, użyj **urllib2** biblioteki dla języka Python 2.X i **urllib.request** biblioteki dla języka Python 3.X. Zostaną spełnione ScoreData, zawierającą FeatureVector, n wymiarowy wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

**Aby uruchomić przykładowy kod**

1. Wdrażanie "przykład 1: Pobierz zestaw danych z UCI: zawartość dla dorosłych 2 klasy w zestawie danych" eksperyment i jest częścią usługi Machine Learning pobieranie próbek.
2. Przypisz apiKey przy użyciu klucza z usługi sieci Web. Zobacz sekcję **Pobieranie klucza autoryzacji na** początku tego artykułu.
3. Przypisz serviceUri o identyfikatorze URI żądania.

**Oto, jak będzie wyglądać kompletne żądanie.**
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

Aby nawiązać połączenie usługi internetowej Machine Learning, należy użyć **RCurl** i **rjson** biblioteki, aby wysłać żądanie i przetworzyć zwrócona odpowiedź w formacie JSON. Zostaną spełnione ScoreData, zawierającą FeatureVector, n wymiarowy wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie w usłudze Machine Learning przy użyciu klucza interfejsu API.

**Oto, jak będzie wyglądać kompletne żądanie.**
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

Aby nawiązać połączenie usługi internetowej Machine Learning, należy użyć **żądania** pakietu npm w projekcie. Ponadto `JSON` obiekt do formatu dane wejściowe i przeanalizować wyniki. Instalowanie przy użyciu `npm install request --save`, lub Dodaj `"request": "*"` do pliku package.json w obszarze `dependencies` i uruchom `npm install`.

**Oto, jak będzie wyglądać kompletne żądanie.**
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
