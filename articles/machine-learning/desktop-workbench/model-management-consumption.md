---
title: Użycie usługi sieci web w usłudze Azure Zarządzanie modelami usługi Machine Learning | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano kroki i pojęcia zaangażowane w korzystanie z usług sieci web wdrażane za pomocą zarządzania modelami w usłudze Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 4a49ccff68003cf7b81a7d945176992a2893d1ac
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972635"
---
# <a name="consuming-web-services"></a>Korzystanie z usług internetowych
Po wdrożeniu modelu jako usługi sieci web w czasie rzeczywistym można przesyła dane i uzyskiwać prognozy z różnych platform i aplikacji. Usługa sieci web w czasie rzeczywistym uwidacznia interfejs API REST w celu uzyskania prognozy. Umożliwia wysyłanie danych do usługi sieci web w formacie jednego lub wielu wierszy można pobrać co najmniej jeden prognozy w danym momencie.

Za pomocą [usługi Azure Machine Learning w sieci Web](model-management-service-deploy.md), aplikacja zewnętrzna synchronicznie komunikuje się z model predykcyjny przez wywołania HTTP POST na adres URL usługi. Aby wywołać usługę sieci web, aplikacja kliencka musi określić klucz interfejsu API, który jest tworzony podczas wdrożenia przewidywanie i dane żądania są umieszczane w treści żądania POST.

> [!NOTE]
> Należy pamiętać, że klucze interfejsu API są dostępne tylko w trybie wdrożenia klastra. Usługi sieci web w lokalnych nie ma kluczy.

## <a name="service-deployment-options"></a>Opcje wdrażania usługi
Usługi Machine Learning w sieci Web platformy Azure można wdrażać zarówno w środowisku produkcyjnym, jak i testowania scenariuszy, w klastrach opartych na chmurze i lokalnych stacji roboczych przy użyciu aparatu platformy docker. Funkcje modelu predykcyjnego w obu przypadkach pozostaje taki sam. Wdrożenie oparte na klastrze oferuje skalowalne i wydajne rozwiązanie oparte na usłudze Azure Container Service, chociaż wdrożenie lokalne mogą być używane do debugowania. 

Interfejsu wiersza polecenia platformy Azure Machine Learning i interfejsu API udostępnia wygodne poleceń do tworzenia i zarządzania compute środowisk na potrzeby wdrożeń usługi przy użyciu ```az ml env``` opcji. 

## <a name="list-deployed-services-and-images"></a>Listy wdrożonych usług i obrazów
Możesz wyświetlić listę aktualnie wdrożonych usług i obrazy Docker przy użyciu interfejsu wiersza polecenia ```az ml service list realtime -o table```. Należy pamiętać, że to polecenie jest zawsze działa w kontekście bieżącego środowiska obliczeniowego. Nie spowoduje to pokazanie usług, które są wdrażane w środowisku, które nie jest ustawione jako bieżący. Aby ustawić Użyj środowiska ```az ml env set```. 

## <a name="get-service-information"></a>Uzyskaj informacje o usłudze
Po pomyślnym wdrożeniu usługi sieci web Użyj następującego polecenia, aby uzyskać adres URL usługi i inne szczegóły wywołanie punktu końcowego usługi. 

```
az ml service usage realtime -i <web service id>
```

To polecenie wyświetla adres URL usługi, nagłówki żądania wymagane, adresu URL struktury swagger i przykładowe dane do wywoływania usługi, jeśli podano schematu interfejsu API usługi w czasie wdrażania.

Można testować usługę bezpośrednio z poziomu interfejsu wiersza polecenia, bez tworzenia żądania HTTP, wprowadzając przykładowe polecenie interfejsu wiersza polecenia przy użyciu danych wejściowych:

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Pobierz klucz interfejsu API usługi
Aby uzyskać klucz usługi sieci web, użyj następującego polecenia:

```
az ml service keys realtime -i <web service id>
```
Podczas tworzenia żądania HTTP, Użyj klucza w nagłówku autoryzacji: "Autoryzacja": "Bearer <key>"

## <a name="get-the-service-swagger-description"></a>Pobierz opis struktury Swagger usługi
Jeśli podano schematu interfejsu API usługi, punkt końcowy usługi może narazić dokumentu Swagger ```http://<ip>/api/v1/service/<service name>/swagger.json```. Dokument struktury Swagger może służyć do automatycznego generowania klienta usługi i zapoznaj się z oczekiwanych danych wejściowych i inne szczegóły dotyczące usługi.

## <a name="get-service-logs"></a>Pobierz dzienniki usługi
Aby zrozumieć zachowanie usługi i diagnozowanie problemów, istnieje kilka sposobów, aby pobrać dzienniki usługi:
- Polecenia interfejsu wiersza polecenia ```az ml service logs realtime -i <service id>```. To polecenie działa w tryby lokalne i klastra.
- Jeśli rejestrowanie usługi zostało włączone we wdrożeniu, dzienniki usługi również będą wysyłane do AppInsight. Polecenia interfejsu wiersza polecenia ```az ml service usage realtime -i <service id>``` zawiera adres URL AppInsight. Należy pamiętać, że dzienniki AppInsight mogą być opóźnione o 2 do 5 minut.
- Dzienniki klastra można wyświetlić w konsoli Kubernetes, która jest połączona po ustawieniu bieżącego środowiska klastra za pomocą ```az ml env set```
- Dzienniki platformy docker lokalne są dostępne dzienniki aparatu docker, gdy usługa jest uruchomiona lokalnie.

## <a name="call-the-service-using-c"></a>Wywoływanie usługi przy użyciu języka C#
Wysłać żądanie z poziomu aplikacji Konsolowej C# za pomocą adresu URL usługi. 

1. W programie Visual Studio Utwórz nową aplikację konsoli: 
    * W menu, kliknij przycisk, a plik -> Nowy -> Projekt
    * W ramach programu Visual Studio C#, kliknij przycisk Windows klasy pulpit, a następnie wybierz aplikację konsoli.
2. Wprowadź `MyFirstService` jako nazwę projektu, kliknij przycisk OK.
3. W odwołaniach projektu równa odwołania `System.Net`, i `System.Net.Http`.
4. Kliknij pozycję Narzędzia -> Menedżer pakietów NuGet -> Konsola Menedżera pakietów, a następnie zainstaluj **Microsoft.AspNet.WebApi.Client** pakietu.
5. Otwórz **Program.cs** pliku i Zastąp kod następującym kodem:
6. Aktualizacja `SERVICE_URL` i `API_KEY` parametrów przy użyciu informacji z usługi sieci web.
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
Wyślij żądanie do usługi internetowej czasu rzeczywistego przy użyciu języka Python. 

1. Skopiuj poniższy przykładowy kod do nowego pliku języka Python.
2. Zaktualizuj parametry danych, adres url i api_key. Dla usług sieci web w lokalnych Usuń nagłówka "Authorization".
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
