---
title: Utwórz klienta, aby korzystał z wdrożonej usługi sieci Web
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak korzystać z usługi sieci Web, która została wygenerowana, gdy model został wdrożony z modelem Azure Machine Learning. Usługa sieci Web uwidacznia interfejs API REST. Tworzenie klientów dla tego interfejsu API przy użyciu wybranego języka programowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: a007e3adb72148cfde1590e996f7df9082159445
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840500"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Korzystanie z modelu usługi Azure Machine Learning, wdrożyć jako usługę sieci web

Wdrażanie modelu usługi Azure Machine Learning w postaci usługi sieci web tworzy interfejs API REST. Można wysyłać dane do tego interfejsu API i odbierać prognozowania zwracane przez model. W tym dokumencie dowiesz się, jak tworzyć klientów dla usługi sieci Web za C#pomocą, go, Java i Python.

Usługa sieci Web jest tworzona podczas wdrażania obrazu do Azure Container Instances, usługi Azure Kubernetes lub tablic opartych na programowalnych polach (FPGA). Tworzysz obrazy z zarejestrowanych modeli i plików oceniania. Pobrano identyfikator URI służący do uzyskiwania dostępu do usługi sieci Web przy użyciu [zestawu SDK Azure Machine Learning](https://aka.ms/aml-sdk). Jeśli włączono uwierzytelnianie umożliwia także zestaw SDK można pobrać klucze uwierzytelniania.

Ogólny przepływ pracy służący do tworzenia klienta korzystającego z usługi sieci Web Machine Learning to:

1. Użyj zestawu SDK, aby uzyskać informacje o połączeniu.
1. Określ typ danych żądania używanych przez model.
1. Utwórz aplikację, która wywołuje usługę sieci Web.

> [!TIP]
> Przykłady w tym dokumencie są tworzone ręcznie bez użycia specyfikacji OpenAPI (Swagger). Jeśli została włączona Specyfikacja OpenAPI dla danego wdrożenia, można użyć narzędzi, takich jak [Swagger-codegen](https://github.com/swagger-api/swagger-codegen) , aby utworzyć biblioteki klienckie dla usługi.

## <a name="connection-information"></a>informacje o połączeniu

> [!NOTE]
> Użyj zestawu SDK Azure Machine Learning, aby uzyskać informacje o usłudze sieci Web. To jest zestaw SDK języka Python. Możesz użyć dowolnego języka, aby utworzyć klienta dla usługi.

Klasa [Azure. Core. WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) zawiera informacje potrzebne do utworzenia klienta. Następujące `Webservice` właściwości są przydatne podczas tworzenia aplikacji klienckiej:

* `auth_enabled`-Jeśli jest włączone `True`uwierzytelnianie klucza; `False`w przeciwnym razie.
* `token_auth_enabled`-Jeśli uwierzytelnianie tokenu jest włączone, `True`w przeciwnym razie `False`,.
* `scoring_uri` Adres interfejsu API REST.


Istnieją trzy sposoby, aby pobrać te informacje dotyczące wdrożonymi usługami sieci web:

* Po wdrożeniu modelu, `Webservice` obiekt jest zwracany za pomocą informacji o usłudze:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* Możesz użyć `Webservice.list` można pobrać listy wdrożonych usług sieci web dla modeli w obszarze roboczym. Można dodać filtry, aby zawęzić listę informacje zwrócone. Aby uzyskać więcej informacji o tym, co można filtrować, zobacz dokumentację dotyczącą usługi [WebService. list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) .

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Jeśli znasz nazwę wdrożonej usługi, możesz utworzyć nowe wystąpienie programu `Webservice`i podać nazwę obszaru roboczego i usługi jako parametry. Nowy obiekt zawiera informacje o wdrożonej usługi.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-for-services"></a>Uwierzytelnianie dla usług

Azure Machine Learning zapewnia dwa sposoby kontroli dostępu do usług sieci Web. 

|Metoda uwierzytelniania|ACI|AKS|
|---|---|---|
|Klucz|Domyślnie wyłączone| Włączona domyślnie|
|Token| Niedostępna| Domyślnie wyłączone |
#### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Po włączeniu uwierzytelniania dla wdrożenia automatycznie tworzone są klucze uwierzytelniania.

* Uwierzytelnianie jest domyślnie włączone podczas wdrażania w usłudze Azure Kubernetes Service.
* Podczas wdrażania programu w celu Azure Container Instances uwierzytelnianie jest domyślnie wyłączone.

Aby kontrolować uwierzytelnianie, użyj `auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie jest włączone, można użyć `get_keys` metodę, aby pobrać klucz podstawowy i pomocniczy uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli konieczne jest ponowne wygenerowanie klucza, użyj [ `service.regen_key` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).


#### <a name="authentication-with-tokens"></a>Uwierzytelnianie przy użyciu tokenów

Po włączeniu uwierzytelniania tokenów dla usługi sieci Web użytkownik musi podać Azure Machine Learning token JWT do usługi sieci Web, aby uzyskać do niej dostęp. 

* Uwierzytelnianie tokenu jest domyślnie wyłączone podczas wdrażania w usłudze Azure Kubernetes Service.
* Uwierzytelnianie tokenu nie jest obsługiwane w przypadku wdrażania do Azure Container Instances.

Aby kontrolować uwierzytelnianie tokenu, użyj `token_auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli jest włączone uwierzytelnianie tokenu, można użyć `get_token` metody, aby pobrać token okaziciela i czas wygaśnięcia tokenów:

```python
token, refresh_by = service.get_tokens()
print(token)
```

> [!IMPORTANT]
> Po upływie `refresh_by` czasu tokenu trzeba będzie zażądać nowego tokenu. 

## <a name="request-data"></a>Żądanie danych

Interfejs API REST oczekuje, że treść żądania jako dokument JSON o następującej strukturze:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Struktury danych wymaga do dopasowania, jakie oceniania skrypt i modelu w oczekiwanej usługi. Skrypt oceniania może modyfikować danych przed przekazaniem go do modelu.

Na przykład modelu w [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) przykład oczekuje tablicy 10 kolejnych liczb. Skrypt oceniania w tym przykładzie tworzy tablicę numpy z żądania i przekazuje ją do modelu. Poniższy przykład przedstawia dane, które oczekuje na tę usługę:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
``` 

Usługa sieci web może akceptować wiele zestawów danych w jednym żądaniu. Zwraca dokument JSON zawierający tablicę odpowiedzi.

### <a name="binary-data"></a>Dane binarne

Jeśli model akceptuje dane binarne, takie jak obraz, należy zmodyfikować `score.py` plik używany dla danego wdrożenia do akceptowania żądań HTTP raw. Oto przykład `score.py` , który akceptuje dane binarne:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `azureml.contrib` Przestrzeń nazw często zmienia się, ponieważ pracujemy nad ulepszeniem usługi. W związku z tym wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.
>
> Jeśli musisz przetestować to w lokalnym środowisku programistycznym, możesz zainstalować składniki w `contrib` przestrzeni nazw za pomocą następującego polecenia:
> 
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="call-the-service-c"></a>Wywołania tej usługi (C#)

W tym przykładzie przedstawiono sposób użycia C# wywołać usługę sieci web utworzone na podstawie [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) przykładu:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Wywoływanie usługi (Przejdź do)

W tym przykładzie pokazano, jak za pomocą języka Go do wywoływania usługi sieci web utworzone na podstawie [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) przykładu:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Wywoływanie usługi (Java)

W tym przykładzie pokazano, jak używać języka Java w celu wywoływania usługi sieci web utworzone na podstawie [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) przykładu:

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key
        String key = "<your key>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Wywoływanie usługi (Python)

W tym przykładzie pokazano, jak używać języka Python do wywoływania usługi sieci web utworzone na podstawie [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) przykładu:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Korzystanie z usługi z Power BI

Power BI obsługuje użycie usług sieci Web Azure Machine Learning do wzbogacania danych w Power BI z przewidywaniami. 

Aby wygenerować usługę sieci Web obsługiwaną do użycia w Power BI, schemat musi obsługiwać format wymagany przez Power BI. [Dowiedz się, jak utworzyć schemat obsługiwany przez Power BI](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#example-script-with-dictionary-input-support-consumption-from-power-bi).

Po wdrożeniu usługi sieci Web jest ona zużywana z Power BI przepływów danych. [Dowiedz się, jak korzystać z usługi sieci web Azure Machine Learning z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).
