---
title: Korzystanie z usługi sieci web
titleSuffix: ML Studio (classic) - Azure
description: Po wdrożeniu usługi uczenia maszynowego z usługi Azure Machine Learning Studio (klasyczna) usługa sieci Web RESTFUL może być zużywana jako usługa odpowiedzi żądania w czasie rzeczywistym lub jako usługa wykonywania wsadowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: b97fe6e55e2c36b6f101071e702952f529146281
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631661"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Jak korzystać z usługi sieci Web usługi Azure Machine Learning Studio (klasycznej)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Po wdrożeniu modelu predykcyjnego usługi Azure Machine Learning Studio (klasycznego) jako usługi sieci Web można użyć interfejsu API REST, aby wysłać go do danych i uzyskać prognozy. Dane można wysyłać w czasie rzeczywistym lub w trybie wsadowym.

Więcej informacji na temat tworzenia i wdrażania usługi sieci Web usługi machine learning przy użyciu usługi Machine Learning Studio (klasycznej) można znaleźć tutaj:

* Aby zapoznać się z samouczkiem na temat tworzenia eksperymentu w programie Machine Learning Studio (klasycznym), zobacz [Tworzenie pierwszego eksperymentu](create-experiment.md).
* Aby uzyskać szczegółowe informacje na temat wdrażania usługi sieci Web, zobacz [Wdrażanie usługi usługi uczenia maszynowego .](deploy-a-machine-learning-web-service.md)
* Aby uzyskać więcej informacji na temat uczenia maszynowego w ogóle, odwiedź [Centrum dokumentacji uczenia maszynowego](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Omówienie
Dzięki usłudze azure machine learning web aplikacja zewnętrzna komunikuje się z modelem oceniania przepływu pracy uczenia maszynowego w czasie rzeczywistym. Wywołanie usługi sieci Web uczenia maszynowego zwraca wyniki prognozowania do aplikacji zewnętrznej. Aby wywołać usługę sieci Web uczenia maszynowego, należy przekazać klucz interfejsu API, który jest tworzony podczas wdrażania prognozowania. Usługa sieci Web uczenia maszynowego jest oparta na REST, popularnym wyborze architektury dla projektów programowania sieci web.

Usługa Azure Machine Learning Studio (klasyczna) ma dwa typy usług:

* Usługa żądania odpowiedzi (RRS) — usługa o małym opóźnieniu, wysoce skalowalna usługa, która zapewnia interfejs do modeli bezstanowych utworzonych i wdrożonych z usługi Machine Learning Studio (classic).
* Usługa wykonywania wsadowego (BES) — usługa asynchroniza, która ocenia partię dla rekordów danych.

Aby uzyskać więcej informacji na temat usług sieci Web usługi uczenia [maszynowego, zobacz Wdrażanie usługi usługi uczenia maszynowego w sieci Web](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Pobierz klucz autoryzacji
Podczas wdrażania eksperymentu klucze interfejsu API są generowane dla usługi sieci Web. Klucze można pobrać z kilku lokalizacji.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Z portalu usług usługi azure usługi uczenia maszynowego platformy Microsoft Azure
Zaloguj się do portalu [usług azure machine learning web services.](https://services.azureml.net)

Aby pobrać klucz interfejsu API dla nowej usługi sieci Web uczenia maszynowego:

1. W portalu usług azure machine learning web services kliknij menu w górnej części **usługi sieci Web.**
2. Kliknij usługę sieci Web, dla której chcesz pobrać klucz.
3. W górnym menu kliknij pozycję **Konsumuj**.
4. Skopiuj i zapisz **klucz podstawowy**.

Aby pobrać klucz interfejsu API dla klasycznej usługi sieci Web uczenia maszynowego:

1. W portalu usług azure machine learning web services kliknij polecenie **Klasyczne usługi sieci Web** w górnym menu.
2. Kliknij usługę sieci Web, z którą pracujesz.
3. Kliknij punkt końcowy, dla którego chcesz pobrać klucz.
4. W górnym menu kliknij pozycję **Konsumuj**.
5. Skopiuj i zapisz **klucz podstawowy**.

### <a name="classic-web-service"></a>Klasyczna usługa sieci Web
 Można również pobrać klucz dla klasycznej usługi sieci Web z machine learning studio (klasyczny).

#### <a name="machine-learning-studio-classic"></a>Usługa Machine Learning Studio (klasyczna)
1. W programie Machine Learning Studio (klasycznym) kliknij pozycję **USŁUGI SIECI WEB** po lewej stronie.
2. Kliknij usługę sieci Web. **Klucz interfejsu API** znajduje się na karcie **DASHBOARD.**

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Łączenie się z usługą sieci Web usługi uczenia maszynowego
Można połączyć się z usługą sieci Web uczenia maszynowego przy użyciu dowolnego języka programowania, który obsługuje żądanie HTTP i odpowiedzi. Przykłady w językach C#, Python i R można wyświetlić na stronie pomocy usługi sieci Web usługi uczenia maszynowego.

**Interfejs API uczenia maszynowego – pomoc** Pomoc interfejsu API usługi Machine Learning jest tworzona podczas wdrażania usługi sieci Web. Zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).
Pomoc interfejsu API uczenia maszynowego zawiera szczegółowe informacje na temat usługi sieci Web przewidywania.

1. Kliknij usługę sieci Web, z którą pracujesz.
2. Kliknij punkt końcowy, dla którego chcesz wyświetlić stronę pomocy interfejsu API.
3. W górnym menu kliknij pozycję **Konsumuj**.
4. Kliknij **stronę pomocy interfejsu API** w punktach końcowych żądanie odpowiedzi lub wykonanie wsadowe.

**Aby wyświetlić pomoc interfejsu API usługi Machine Learning dla nowej usługi sieci Web**

W portalu [usług azure machine learning usług sieci Web:](https://services.azureml.net/)

1. W górnym menu kliknij polecenie **USŁUGI SIECI WEB.**
2. Kliknij usługę sieci Web, dla której chcesz pobrać klucz.

Kliknij **przycisk Użyj usługi sieci Web,** aby uzyskać identyfikatory URI dla usług żądania odpowiedzi i wykonywania wsadowego oraz przykładowego kodu w językach C#, R i Python.

Kliknij **przycisk Interfejs API swagger,** aby uzyskać dokumentację opartą na swagger dla interfejsów API wywoływanych z dostarczonych identyfikatorów URI.

### <a name="c-sample"></a>Próbka języka C#
Aby połączyć się z usługą sieci Web uczenia maszynowego, należy użyć **httpclient** przekazywania ScoreData. ScoreData zawiera FeatureVector, n-wymiarowy wektor funkcji liczbowych, który reprezentuje ScoreData. Uwierzytelniać się w usłudze uczenia maszynowego za pomocą klucza interfejsu API.

Aby połączyć się z usługą sieci Web usługi uczenia maszynowego, należy zainstalować pakiet **Microsoft.AspNet.WebApi.Client** NuGet.

**Zainstaluj microsoft.aspNet.WebApi.Client NuGet w programie Visual Studio**

1. Publikowanie zestawu danych Pobieranie z usługi sieci Web zestawu danych UCI: Adult 2 klasy.
2. Kliknij pozycję **Narzędzia** > **NuGet Package Manager** > **Package Manager Console**.
3. Wybierz **pozycję Install-Package Microsoft.AspNet.WebApi.Client**.

**Aby uruchomić przykładowy kod**

1. Opublikuj eksperyment "Przykładowy 1: Pobierz zestaw danych z zestawu danych UCI: Adult 2 class dataset", część kolekcji próbek uczenia maszynowego.
2. Przypisywanie apiKey z kluczem z usługi sieci Web. Zobacz **Pobierz klucz autoryzacji** powyżej.
3. Przypisywanie serviceUri za pomocą identyfikatora URI żądania.

**Oto, jak będzie wyglądać pełna prośba.**
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

### <a name="python-sample"></a>Przykład języka Python
Aby połączyć się z usługą sieci Web usługi Machine Learning, użyj biblioteki **urllib2** dla języka Python 2.X i biblioteki **urllib.request** dla języka Python 3.X. Przekażesz ScoreData, który zawiera FeatureVector, n-wymiarowy wektor funkcji liczbowych, który reprezentuje ScoreData. Uwierzytelniać się w usłudze uczenia maszynowego za pomocą klucza interfejsu API.

**Aby uruchomić przykładowy kod**

1. Wdrażanie eksperymentu "Przykładowy 1: Pobierz zestaw danych z zestawu danych UCI: Adult 2 class dataset", część kolekcji próbek uczenia maszynowego.
2. Przypisywanie apiKey z kluczem z usługi sieci Web. Zobacz Pobierz klucz **autoryzacji** sekcji na początku tego artykułu.
3. Przypisywanie serviceUri za pomocą identyfikatora URI żądania.

**Oto, jak będzie wyglądać pełna prośba.**
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

### <a name="r-sample"></a>Próbka R

Aby połączyć się z usługą sieci Web uczenia maszynowego, użyj bibliotek **RCurl** i **rjson,** aby zażądać i przetworzyć zwróconą odpowiedź JSON. Przekażesz ScoreData, który zawiera FeatureVector, n-wymiarowy wektor funkcji liczbowych, który reprezentuje ScoreData. Uwierzytelniać się w usłudze uczenia maszynowego za pomocą klucza interfejsu API.

**Oto, jak będzie wyglądać pełna prośba.**
```r
library("RCurl")
library("rjson")

# Accept TLS/SSL certificates issued by public Certificate Authorities
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

### <a name="javascript-sample"></a>Przykład javascript

Aby połączyć się z usługą sieci Web uczenia maszynowego, użyj pakietu **npm żądania** w projekcie. Obiekt służy `JSON` również do formatowania danych wejściowych i analizowania wyniku. Zainstaluj `npm install request --save`za pomocą `"request": "*"` programu lub dodaj `dependencies` do `npm install`pliku package.json w obszarze i uruchom program .

**Oto, jak będzie wyglądać pełna prośba.**
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
