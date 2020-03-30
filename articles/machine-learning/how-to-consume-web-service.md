---
title: Tworzenie klienta dla modelu wdrożonego jako usługa sieci Web
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z usługi sieci web, która została wygenerowana, gdy model został wdrożony za pomocą modelu usługi Azure Machine Learning. Usługa sieci web udostępnia interfejs API REST. Utwórz klientów dla tego interfejsu API przy użyciu wybranego języka programowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: a86b8ddb59719db9bdaffea44aecd5428ad16834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282668"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Korzystanie z modelu usługi Azure Machine Learning wdrożonego jako usługa sieci web
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Wdrażanie modelu usługi Azure Machine Learning jako usługi sieci web tworzy interfejs API REST. Można wysłać dane do tego interfejsu API i odbierać prognozowania zwrócone przez model. W tym dokumencie dowiedz się, jak utworzyć klientów dla usługi sieci web przy użyciu języka C#, Go, Java i Python.

Usługę sieci web można utworzyć podczas wdrażania obrazu w wystąpieniach kontenerów platformy Azure, usłudze Azure Kubernetes lub tablicach bramy programowalnych w terenie (FPGA). Obrazy są tworzę obrazy z zarejestrowanych modeli i pliki oceniania. Pobieranie identyfikatora URI używanego do uzyskiwania dostępu do usługi sieci web przy użyciu [sdk usługi Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Jeśli uwierzytelnianie jest włączone, można również użyć SDK, aby uzyskać klucze uwierzytelniania lub tokeny.

Ogólny przepływ pracy do tworzenia klienta korzystającego z usługi sieci web uczenia maszynowego jest:

1. Użyj sdk, aby uzyskać informacje o połączeniu.
1. Określ typ danych żądania używanych przez model.
1. Utwórz aplikację, która wywołuje usługę sieci web.

> [!TIP]
> Przykłady w tym dokumencie są tworzone ręcznie bez użycia specyfikacji OpenAPI (Swagger). Jeśli włączono specyfikację OpenAPI dla wdrożenia, można użyć narzędzi, takich jak [swagger-codegen](https://github.com/swagger-api/swagger-codegen) do tworzenia bibliotek klienta dla usługi.

## <a name="connection-information"></a>Informacje o połączeniu

> [!NOTE]
> Użyj zestawu SDK usługi Azure Machine Learning, aby uzyskać informacje o usłudze sieci web. To jest SDK języka Python. Można użyć dowolnego języka, aby utworzyć klienta dla usługi.

Klasa [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) zawiera informacje potrzebne do utworzenia klienta. Następujące `Webservice` właściwości są przydatne do tworzenia aplikacji klienckiej:

* `auth_enabled`- Jeśli uwierzytelnianie za `True`pomocą klucza jest włączone, ; w `False`przeciwnym razie , .
* `token_auth_enabled`- Jeśli uwierzytelnianie tokenu jest włączone, `True`; w `False`przeciwnym razie , .
* `scoring_uri`- Adres INTERFEJSU API REST.
* `swagger_uri`- Adres specyfikacji OpenAPI. Ten identyfikator URI jest dostępny, jeśli włączono automatyczne generowanie schematu. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

Istnieją trzy sposoby pobierania tych informacji dla wdrożonych usług sieci web:

* Podczas wdrażania modelu `Webservice` obiekt jest zwracany z informacjami o usłudze:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Można użyć `Webservice.list` do pobrania listy wdrożonych usług sieci web dla modeli w obszarze roboczym. Można dodać filtry, aby zawęzić listę zwróconych informacji. Aby uzyskać więcej informacji na temat tego, co można filtrować, zobacz dokumentację referencyjną [Webservice.list.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py)

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Jeśli znasz nazwę wdrożonej usługi, możesz utworzyć nowe `Webservice`wystąpienie programu i podać nazwę obszaru roboczego i usługi jako parametry. Nowy obiekt zawiera informacje o wdrożonej usłudze.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Zabezpieczona usługa internetowa

Jeśli wdrożona usługa internetowa została zabezpieczona przy użyciu certyfikatu TLS/SSL, można użyć [protokołu HTTPS](https://en.wikipedia.org/wiki/HTTPS) do nawiązania połączenia z usługą przy użyciu identyfikatora URI oceniania lub swaggera. Protokół HTTPS pomaga zabezpieczyć komunikację między klientem a usługą sieci web, szyfrując komunikację między nimi. Szyfrowanie używa [zabezpieczeń warstwy transportu (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS jest czasami nadal określane jako *Secure Sockets Layer* (SSL), który był poprzednikiem TLS.

> [!IMPORTANT]
> Usługi sieci Web wdrożone przez usługę Azure Machine Learning obsługują tylko usługę TLS w wersji 1.2. Podczas tworzenia aplikacji klienckiej, upewnij się, że obsługuje tę wersję.

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie usługi sieci web za pośrednictwem usługi Azure Machine Learning za pomocą protokołu TLS.](how-to-secure-web-service.md)

### <a name="authentication-for-services"></a>Uwierzytelnianie dla usług

Usługa Azure Machine Learning oferuje dwa sposoby kontrolowania dostępu do usług sieci web.

|Metoda uwierzytelniania|Aci|AKS|
|---|---|---|
|Klucz|Domyślnie wyłączone| Domyślnie włączone|
|Token| Niedostępne| Domyślnie wyłączone |

Podczas wysyłania żądania do usługi, która jest zabezpieczona za pomocą klucza lub tokenu, użyj __nagłówka Autoryzacja,__ aby przekazać klucz lub token. Klucz lub token musi być `Bearer <key-or-token>`sformatowany jako , gdzie `<key-or-token>` jest klucz lub wartość tokenu.

#### <a name="authentication-with-keys"></a>Uwierzytelnianie za pomocą kluczy

Po włączeniu uwierzytelniania dla wdrożenia, automatycznie utworzyć klucze uwierzytelniania.

* Uwierzytelnianie jest domyślnie włączone podczas wdrażania w usłudze Azure Kubernetes.
* Uwierzytelnianie jest domyślnie wyłączone podczas wdrażania w wystąpieniach kontenera platformy Azure.

Aby kontrolować uwierzytelnianie, należy użyć tego parametru `auth_enabled` podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie jest włączone, `get_keys` można użyć tej metody do pobrania podstawowego i pomocniczego klucza uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli chcesz ponownie wygenerować [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)klucz, użyj programu .

#### <a name="authentication-with-tokens"></a>Uwierzytelnianie za pomocą tokenów

Po włączeniu uwierzytelniania tokenu dla usługi sieci web użytkownik musi dostarczyć token JWT usługi Azure Machine Learning do usługi sieci web, aby uzyskać do niego dostęp. 

* Uwierzytelnianie tokenu jest domyślnie wyłączone podczas wdrażania w usłudze Azure Kubernetes.
* Uwierzytelnianie tokenu nie jest obsługiwane podczas wdrażania w wystąpieniach kontenera platformy Azure.

Aby kontrolować uwierzytelnianie `token_auth_enabled` tokenu, należy użyć parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie tokenu jest włączone, można użyć `get_token` metody, aby pobrać token okaziciela i że czas wygaśnięcia tokenów:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Musisz zażądać nowego tokenu po czasie `refresh_by` tokenu. 

## <a name="request-data"></a>Żądania danych

Interfejs API REST oczekuje, że treść żądania będzie dokumentem JSON o następującej strukturze:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Struktura danych musi odpowiadać oczekiwaniom skryptu i modelu oceniania w usłudze. Skrypt oceniania może zmodyfikować dane przed przekazaniem go do modelu.

Na przykład model w [Train w przykładzie notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) oczekuje tablicy 10 liczb. Skrypt oceniania w tym przykładzie tworzy tablicę Numpy z żądania i przekazuje ją do modelu. W poniższym przykładzie przedstawiono dane, na które oczekuje ta usługa:

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

Aby uzyskać informacje na temat włączania obsługi danych binarnych w usłudze, zobacz [Dane binarne](how-to-deploy-and-where.md#binary).

### <a name="cross-origin-resource-sharing-cors"></a>Współużytkowy przydział zasobów między źródłami (CORS)

Aby uzyskać informacje na temat włączania obsługi cors w usłudze, zobacz [Udostępnianie zasobów między źródłami](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Zadzwoń do usługi (C#)

W tym przykładzie pokazano, jak używać języka C# do wywołania usługi sieci web utworzonej na podstawie [train w przykładzie notesu:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

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

Zwracane wyniki są podobne do następującego dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Zadzwoń do usługi (Go)

W tym przykładzie pokazano, jak używać Go do wywołania usługi sieci web utworzonej na podstawie [train w przykładzie notesu:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

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

Zwracane wyniki są podobne do następującego dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Zadzwoń do usługi (Java)

W tym przykładzie pokazano, jak używać języka Java do wywoływania usługi sieci web utworzonej na podstawie przykładu [Train within notebook:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
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

Zwracane wyniki są podobne do następującego dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Wywołanie usługi (Python)

W tym przykładzie pokazano, jak używać języka Python do wywoływania usługi sieci web utworzonej na podstawie train [w przykładzie notesu:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

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

Zwracane wyniki są podobne do następującego dokumentu JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Korzystanie z usługi z poziomu usługi Power BI

Usługa Power BI obsługuje korzystanie z usług Azure Machine Learning w celu wzbogacenia danych w usłudze Power BI o prognozy. 

Aby wygenerować usługę sieci Web obsługiwaną do użycia w usłudze Power BI, schemat musi obsługiwać format wymagany przez usługę Power BI. [Dowiedz się, jak utworzyć schemat obsługiwany przez program Power BI](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script).

Po wdrożeniu usługi sieci web jest zużywalna z przepływów danych usługi Power BI. [Dowiedz się, jak korzystać z usługi azure machine learning w usłudze Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić architekturę referencyjną do oceniania w czasie rzeczywistym modeli języka Python i uczenia głębokiego, przejdź do [centrum architektury platformy Azure.](/azure/architecture/reference-architectures/ai/realtime-scoring-python)
