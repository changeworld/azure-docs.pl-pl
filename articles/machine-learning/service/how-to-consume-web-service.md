---
title: Jak używać wdrożeń usług internetowych — usługa Azure Machine Learning
description: Informacje o sposobie korzystania z usługi sieci web utworzone przez wdrożenie model usługi Azure Machine Learning. Wdrażanie modelu usługi Azure Machine Learning tworzy usługi sieci web, który uwidacznia interfejs API REST. Można utworzyć klientów dla tego interfejsu API przy użyciu preferowanego języka programowania. W tym dokumencie, jak uzyskać dostęp do interfejsu API przy użyciu języka Python i C#.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: larryfr
ms.date: 10/30/2018
ms.openlocfilehash: 2af954f14f7113a1f6214bf5e9235933312bbf02
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51347714"
---
# <a name="how-to-consume-an-azure-ml-model-deployed-as-a-web-service"></a>Jak korzystać z modelu usługi uczenie Maszynowe Azure wdrożyć jako usługę sieci web

Wdrażanie modelu usługi Azure Machine Learning w postaci usługi sieci web tworzy interfejs API REST. Można wysyłać dane do tego interfejsu API i odbierać prognozowania zwracane przez model. W tym dokumencie, Dowiedz się, jak utworzyć klientów przy użyciu usługi sieci web C#, Go, Java i Python.

Usługi sieci web jest tworzona podczas wdrażania obrazu do wystąpienia kontenera platformy Azure, usługi Azure Kubernetes Service lub Project Brainwave (Tablice bramek programowane). Obrazy są tworzone na podstawie zarejestrowane modele i plik oceniania. Identyfikator URI używany do uzyskiwania dostępu do usługi sieci web można pobrać przy użyciu [zestawu SDK usługi Azure Machine Learning](https://docs.microsoft.com/en-us/python/api/overview/azure/ml/intro?view=azure-ml-py). Jeśli włączono uwierzytelnianie umożliwia także zestaw SDK można pobrać klucze uwierzytelniania.

Jest ogólny przepływ pracy podczas tworzenia klienta, który korzysta z uczenia Maszynowego usługi sieci web:

1. Użyj zestawu SDK, aby uzyskać informacje o połączeniu
1. Określ typ żądania danych używanego przez model
1. Utwórz aplikację, która wywołuje usługę sieci web

## <a name="connection-information"></a>Informacje o połączeniu

> [!NOTE]
> Zestaw SDK usługi Azure Machine Learning można uzyskać informacji usługi sieci web. To jest zestaw SDK języka Python. Gdy jest używany do pobierania informacji o usługach sieci web, można użyć dowolnego języka, można utworzyć klienta dla usługi.

Informacje o połączeniu dla usługi sieci web mogą być pobierane przy użyciu zestawu SDK usługi Azure Machine Learning. [Azureml.core.Webservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) klasa udostępnia informacje niezbędne do utworzenia klienta. Następujące `Webservice` właściwości, które są przydatne podczas tworzenia aplikacji klienckiej:

* `auth_enabled` — Jeśli włączono uwierzytelnianie `True`; w przeciwnym razie `False`.
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

* Możesz użyć `Webservice.list` można pobrać listy wdrożonych usług sieci web dla modeli w obszarze roboczym. Można dodać filtry, aby zawęzić listę informacje zwrócone. Aby uzyskać więcej informacji o tym, co może zostać wykonane filtrowanie, zobacz [Webservice.list](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#list) dokumentację referencyjną.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Jeśli znasz nazwę wdrożonej usługi, możesz utworzyć nowe wystąpienie klasy `Webservice` i podaj nazwę obszaru roboczego i usługi jako parametry. Nowy obiekt zawiera informacje o wdrożonej usługi.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-key"></a>Klucz uwierzytelniania

Klucze uwierzytelniania są tworzone automatycznie, gdy jest włączone uwierzytelnianie dla wdrożenia.

* Uwierzytelnianie jest __domyślnie__ podczas wdrażania __usługi Azure Kubernetes Service__.
* Uwierzytelnianie jest __domyślnie wyłączone__ podczas wdrażania __usłudze Azure container Instances__.

Aby kontrolować uwierzytelniania, należy użyć `auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie jest włączone, można użyć `get_keys` metodę, aby pobrać klucz podstawowy i pomocniczy uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli konieczne jest ponowne wygenerowanie klucza, użyj [ `service.regen_key` ](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#regen-key).

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

Na przykład modelu w [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) przykład oczekuje tablicy 10 kolejnych liczb. Skrypt oceniania w tym przykładzie tworzy tablicę Numpy z żądania i przekazuje je do modelu. Poniższy przykład przedstawia dane, które oczekuje na tę usługę:

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

## <a name="call-the-service-c"></a>Wywołania tej usługi (C#)

W tym przykładzie przedstawiono sposób użycia C# wywołać usługę sieci web utworzone na podstawie [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) przykładu:

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

W tym przykładzie pokazano, jak za pomocą języka Go do wywoływania usługi sieci web utworzone na podstawie [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) przykładu:

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

W tym przykładzie pokazano, jak używać języka Java w celu wywoływania usługi sieci web utworzone na podstawie [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) przykładu:

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

W tym przykładzie pokazano, jak używać języka Python do wywoływania usługi sieci web utworzone na podstawie [szkolenie w notesie](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) przykładu:

```python
import requests
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
headers = { 'Content-Type':'application/json' }
# If authentication is enabled, set the authorization header
headers['Authorization']=f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers = headers)
print(resp.text)

```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, że zawarto informacje dotyczące tworzenia klienta we wdrożonym modelu, jak [wdrażanie modelu na urządzeniu usługi IoT Edge](how-to-deploy-to-iot.md).