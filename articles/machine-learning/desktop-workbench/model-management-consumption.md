---
title: Korzystania z usług sieci web Azure Machine Learning Model zarządzania | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano kroki i koncepcje zaangażowane w korzystanie z usług sieci web wdrażane za pomocą modelu zarządzania w usłudze Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: f87f865ef6d2c3403903a1bdcc402c01c3e9f939
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831993"
---
# <a name="consuming-web-services"></a>Korzystanie z usług sieci web
Gdy należy wdrożyć model jako usługę sieci web w czasie rzeczywistym, możesz przesyła dane i uzyskać prognoz z różnych platform i aplikacji. Usługa sieci web czasu rzeczywistego udostępnia interfejs API REST dla pobierania prognoz. Można wysyłać dane do usługi sieci web w formacie jednego lub wielu wierszy można pobrać co najmniej jeden prognoz naraz.

Z [usługi sieci Web Azure Machine Learning](model-management-service-deploy.md), zewnętrzną aplikacją synchronicznie komunikuje się z modelu predykcyjnego za wywołania HTTP POST na adres URL usługi. Aby wywołać usługi sieci web, aplikacja kliencka musi określić klucz interfejsu API, który jest tworzony podczas wdrażania Prognozowanie i umieszczanie danych żądania w treści żądania POST.

Należy pamiętać, że klucze interfejsu API są dostępne tylko w trybie wdrożenia klastra. Usługi sieci web w lokalnej nie ma kluczy.

## <a name="service-deployment-options"></a>Opcje wdrażania usługi
Azure Machine Learning Web services można wdrożyć klastry oparte na chmurze dla scenariuszy zarówno produkcyjnych i testowych i lokalnych stacji roboczych przy użyciu aparatu docker. Funkcjonalność model predykcyjny w obu przypadkach pozostaje taki sam. Wdrożenia na podstawie klastra zapewniają skalowalność i wydajność rozwiązania oparte na usługi kontenera platformy Azure, podczas lokalnego wdrożenia może służyć do debugowania. 

Interfejsów API i interfejsu wiersza polecenia Azure Machine Learning zapewnia wygodny poleceń do tworzenia i zarządzania obliczeniowe środowiska w przypadku wdrożeń usługi przy użyciu ```az ml env``` opcji. 

## <a name="list-deployed-services-and-images"></a>Lista jest wdrożona usług i obrazów
Można wyświetlić listę aktualnie wdrożonych usług i obrazy usługi Docker za pomocą polecenia interfejsu wiersza polecenia ```az ml service list realtime -o table```. Należy pamiętać, że to polecenie zawsze działa w kontekście bieżącego środowiska obliczeniowego. Czy pokazuj usług, które są wdrażane w środowisku, które nie jest ustawiony na bieżącej. Aby ustawić Użyj środowiska ```az ml env set```. 

## <a name="get-service-information"></a>Uzyskiwanie informacji o usłudze
Po pomyślnym wdrożeniu usługi sieci web Użyj następującego polecenia, aby uzyskać adres URL usługi i inne szczegóły wywoływania punktu końcowego usługi. 

```
az ml service usage realtime -i <web service id>
```

To polecenie wyświetla się adres URL usługi, nagłówki żądania wymagane, adresu URL struktury swagger i przykładowe dane do wywoływania usługi, jeśli podano schematu interfejsu API usługi w czasie wdrażania.

Można testować usługę bezpośrednio z poziomu interfejsu wiersza polecenia, bez tworzenia żądania HTTP, wprowadzając przykład polecenia interfejsu wiersza polecenia z danych wejściowych:

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Pobierz klucz interfejsu API usługi
Aby uzyskać klucz usługi sieci web, użyj następującego polecenia:

```
az ml service keys realtime -i <web service id>
```
Podczas tworzenia żądania HTTP, Użyj klucza w nagłówku autoryzacji: "Autoryzacja": "Bearer <key>"

## <a name="get-the-service-swagger-description"></a>Pobrania opisu usługi programu Swagger
Jeśli podano schematu interfejsu API usługi, punkt końcowy usługi może narazić dokumentu Swagger ```http://<ip>/api/v1/service/<service name>/swagger.json```. Dokumentu Swagger może służyć do automatycznego generowania klienta usługi i Eksploruj oczekiwane dane wejściowe i inne szczegółowe informacje o usłudze.

## <a name="get-service-logs"></a>Pobierz dzienniki usługi
Aby zrozumieć zachowania usługi i diagnozowania problemów, istnieje kilka sposobów, aby pobrać dzienniki usługi:
- Polecenia interfejsu wiersza polecenia ```az ml service logs realtime -i <service id>```. To polecenie działa zarówno w przypadku klastra, jak i lokalnego trybów.
- Jeśli rejestrowanie usługi zostało włączone w wdrożenia, dzienniki usługi również będą wysyłane do AppInsight. Polecenia interfejsu wiersza polecenia ```az ml service usage realtime -i <service id>``` zawiera adres URL AppInsight. Należy pamiętać, że dzienniki AppInsight mogą być opóźnione przez 2-5 minut.
- Dzienniki klastra można wyświetlić za pomocą konsoli Kubernetes podłączoną po ustawieniu w bieżącym środowisku klastra z ```az ml env set```
- Docker lokalne dzienniki są dostępne dzienniki aparatu docker, gdy usługa jest uruchomiona lokalnie.

## <a name="call-the-service-using-c"></a>Wywołania tej usługi przy użyciu języka C#
Adres URL usługi umożliwia wysłanie żądania z poziomu aplikacji Konsolowej C#. 

1. W programie Visual Studio Utwórz nową aplikację konsoli: 
    * W menu, kliknij przycisk, plik -> Nowy -> projektu
    * W programie Visual Studio C#, kliknij system Windows Desktop klasy, a następnie wybierz aplikację konsoli.
2. Wprowadź _MyFirstService_ jako nazwę projektu, kliknij przycisk OK.
3. W odwołaniach projektu odwołań do zestawu _System.Net_, i _System.Net.Http_.
4. Kliknij pozycję Menedżer pakietów NuGet -> Narzędzia -> Konsola Menedżera pakietów, a następnie zainstalowanie pakietu Microsoft.AspNet.WebApi.Client.
5. Otwórz plik Program.cs i Zastąp kod następującym kodem:
6. Aktualizacja _SERVICE_URL_ i _API_KEY_ parametrów o informacje z usługi sieci web.
7. Uruchom projekt.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Wywoływanie usługi sieci web przy użyciu języka Python
Użyj języka Python, aby wysłać żądania do usługi sieci web w czasie rzeczywistym. 

1. Skopiuj poniższy przykładowy kod do nowego pliku Python.
2. Zaktualizuj dane, adres url i api_key parametrów. Dla usług sieci web w lokalnej Usuń nagłówka "Authorization".
3. Uruchom kod. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, body, headers=headers)
resp.text
```
