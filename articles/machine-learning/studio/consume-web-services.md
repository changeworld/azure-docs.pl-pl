---
title: Jak korzystać z usługi sieci Web Azure Machine Learning | Dokumentacja firmy Microsoft
description: Po wdrożeniu usługi uczenia maszynowego, usługę sieci Web RESTFul, który ma zostać udostępnione mogą być używane jako usługa w czasie rzeczywistym żądanie odpowiedź lub jako usługę wykonywania wsadowego.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.openlocfilehash: b89fb0fbb499fa06c9e56f02937b1c586efde9b6
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833385"
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Jak korzystać z usługi Azure Machine Learning w sieci Web

Po wdrożenia usługi Azure Machine Learning model predykcyjny jako usługę sieci Web, można użyć interfejsu API REST przesyła dane do pobrania prognoz. Możesz wysłać dane w czasie rzeczywistym lub w trybie wsadowym.

Można znaleźć więcej informacji na temat sposobu tworzenia i wdrażania usługi Machine Learning w sieci Web przy użyciu usługi Machine Learning Studio, w tym miejscu:

* Samouczek dotyczący sposobu tworzenia eksperymentu w usłudze Machine Learning Studio, zobacz [Tworzenie pierwszego eksperymentu](create-experiment.md).
* Aby uzyskać więcej informacji na temat wdrażania usługi sieci Web, zobacz [wdrażania usługi Machine Learning Web](publish-a-machine-learning-web-service.md).
* Aby uzyskać więcej informacji na temat usługi Machine Learning, odwiedź [Centrum dokumentacji uczenia maszynowego](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Przegląd
W usłudze Azure Machine Learning w sieci Web aplikacji zewnętrznej komunikuje się z modelem oceniania uczenia maszynowego przepływu pracy w czasie rzeczywistym. Wywołanie usługi Machine Learning Web zwraca wyniki prognozowania do aplikacji zewnętrznej. Aby wywołać usługi Machine Learning w sieci Web, należy przekazać klucz interfejsu API, który jest tworzony podczas wdrażania prognozowania. Usługa Machine Learning w sieci Web jest oparta na REST, wybór popularnych Architektura programowania projektów sieci Web.

Usługa Azure Machine Learning udostępnia dwa typy usług:

* Odpowiedź na żądania usługi (RR) — krótki czas oczekiwania, wysoce skalowalna usługa, która zapewnia interfejs do modeli bezstanowych, utworzeniu i wdrożeniu z Machine Learning Studio.
* Usługa partia zadań wykonywania (BES) — asynchronicznego usługi tego wyniki w partii rekordów danych.

Aby uzyskać więcej informacji na temat usługi Machine Learning w sieci Web, zobacz [wdrażania usługi Machine Learning Web](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Pobierz klucz autoryzacji usługi Azure Machine Learning
Podczas wdrażania eksperymentu klucze interfejsu API są generowane przez usługę sieci Web. Klucze można pobrać z kilku lokalizacjach.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>W portalu usługi sieci Web Microsoft Azure Machine Learning
Zaloguj się do [usługi sieci Web Microsoft Azure Machine Learning](https://services.azureml.net) portalu.

Aby pobrać klucz interfejsu API usługi sieci Web uczenie nowe maszyny:

1. W portalu usługi sieci Web systemu Azure Machine Learning kliknij **usług sieci Web** menu u góry.
2. Kliknij usługę sieci Web, dla których chcesz pobrać klucza.
3. W górnym menu, kliknij przycisk **Consume**.
4. Skopiuj i Zapisz **klucz podstawowy**.

Aby pobrać klucz interfejsu API usługi klasycznego Machine Learning w sieci Web:

1. W portalu usługi sieci Web systemu Azure Machine Learning kliknij **usług sieci Web klasycznego** menu u góry.
2. Kliknij usługę sieci Web, z którym pracujesz.
3. Kliknij punkt końcowy, dla którego chcesz pobrać klucza.
4. W górnym menu, kliknij przycisk **Consume**.
5. Skopiuj i Zapisz **klucz podstawowy**.

### <a name="classic-web-service"></a>Klasycznym usługi sieci Web
 Można również pobrać klucza dla usługi sieci Web klasycznego z Machine Learning Studio.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. W usłudze Machine Learning Studio, kliknij przycisk **usług sieci WEB** po lewej stronie.
2. Kliknij usługę sieci Web. **Klucz interfejsu API** znajduje się na **pulpitu NAWIGACYJNEGO** kartę.

## <a id="connect"></a>Połączyć się z usługą Machine Learning w sieci Web
Można nawiązać połączenia z usługą Machine Learning w sieci Web przy użyciu języka programowania, która obsługuje żądania HTTP i odpowiedzi. Przykłady można wyświetlić w języku C#, Python i R ze strony pomocy usługi Machine Learning w sieci Web.

**Maszyny pomocy Learning API** Machine Learning API pomocy jest tworzona podczas wdrażania usługi sieci Web. Zobacz [uczenie maszynowe Azure wskazówki — wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md).
Pomoc Machine Learning API zawiera szczegółowe informacje o prognozowania usługi sieci Web.

1. Kliknij usługę sieci Web, z którym pracujesz.
2. Kliknij punkt końcowy, dla którego chcesz wyświetlić strona pomocy interfejsu API.
3. W górnym menu, kliknij przycisk **Consume**.
4. Kliknij przycisk **strona pomocy interfejsu API** w obszarze żądanie-odpowiedź lub wykonywania wsadowego usługi punktów końcowych.

**Do widoku interfejsu API uczenia maszynowego Pomoc dla usługi sieci Web nowy**

W [portalu usługi Azure Machine Learning Web](https://services.azureml.net/):

1. Kliknij przycisk **usług sieci WEB** w górnym menu.
2. Kliknij usługę sieci Web, dla których chcesz pobrać klucza.

Kliknij przycisk **Użyj usługi sieci Web** można pobrać identyfikatorów URI dla żądania Reposonse i usługi wykonywania wsadowego i przykładowy kod w języku C#, R i Python.

Kliknij przycisk **interfejsu API programu Swagger** można pobrać struktury Swagger interfejsy API wywoływanych z podany identyfikator URI na podstawie dokumentacji.

### <a name="c-sample"></a>Przykład C#
Aby połączyć się z usługą Machine Learning w sieci Web, należy użyć **HttpClient** przekazywanie ScoreData. ScoreData zawiera FeatureVector, n wymiarowej wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie usługi Machine Learning przy użyciu klucza interfejsu API.

Aby połączyć się z usługą Machine Learning w sieci Web, **Microsoft.AspNet.WebApi.Client** musi być zainstalowany pakiet NuGet.

**Instalowania Microsoft.AspNet.WebApi.Client NuGet w programie Visual Studio**

1. Publikowanie pobierania zestawu danych z UCI: 2 dla dorosłych klasy dataset usługi sieci Web.
2. Kliknij pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.
3. Wybierz **Microsoft.AspNet.WebApi.Client Install-Package**.

**Aby uruchomić przykładowy kod**

1. Publikowanie "przykład 1: pobrać zestaw danych z UCI: osoba dorosła 2 klasy dataset" eksperymentu, część pobieranie próbek uczenia maszynowego.
2. Przypisz apiKey przy użyciu klucza z usługi sieci Web. Zobacz **Pobierz klucz autoryzacji usługi Azure Machine Learning** powyżej.
3. Przypisz serviceUri o identyfikatorze URI żądania.

**Oto jak będzie wyglądać wykonać żądanie.**
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
                // if you are calling this code from the UI thread of an ASP.Net application.
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

                    // Print the headers - they include the requert ID and the timestamp,
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

### <a name="python-sample"></a>Przykładowe Python
Aby połączyć się z usługą Machine Learning w sieci Web, należy użyć **urllib2** biblioteki dla języka Python 2.X i **urllib.request** biblioteki dla języka Python 3.X. Przekazuje ScoreData, zawierającą FeatureVector, n wymiarowej wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie usługi Machine Learning przy użyciu klucza interfejsu API.

**Aby uruchomić przykładowy kod**

1. Wdrażanie "przykład 1: pobrać zestaw danych z UCI: osoba dorosła 2 klasy dataset" eksperymentu, część pobieranie próbek uczenia maszynowego.
2. Przypisz apiKey przy użyciu klucza z usługi sieci Web. Zobacz **Pobierz klucz autoryzacji usługi Azure Machine Learning** na początku części tego artykułu.
3. Przypisz serviceUri o identyfikatorze URI żądania.

**Oto jak będzie wyglądać wykonać żądanie.**
```python
import urllib2 # urllib.request for Python 3.X
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

# "urllib.request.Request(uri, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Przykładowe R

Aby połączyć się z usługą sieci Web Machine Learning, należy użyć **RCurl** i **rjson** biblioteki w celu wysłania żądania i przetwarzanie zwrócona odpowiedź w formacie JSON. Przekazuje ScoreData, zawierającą FeatureVector, n wymiarowej wektor liczbowe funkcje reprezentuje ScoreData. Uwierzytelnianie usługi Machine Learning przy użyciu klucza interfejsu API.

**Oto jak będzie wyglądać wykonać żądanie.**
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

# Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>Przykład JavaScript

Aby połączyć się z usługą sieci Web Machine Learning, należy użyć **żądania** pakietu npm w projekcie. Ponadto `JSON` obiektu dane i przeanalizować wyniku. Instalowanie przy użyciu `npm install request --save`, lub Dodaj `"request": "*"` do pliku package.json w obszarze `dependencies` i uruchom `npm install`.

**Oto jak będzie wyglądać wykonać żądanie.**
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